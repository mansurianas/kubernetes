# Kubernetes Ingress Overview

## Introduction

Kubernetes Ingress is a key resource that manages external access to services within a Kubernetes cluster. It provides HTTP and HTTPS routing to services based on defined rules. Ingress allows you to expose multiple services under a single IP address, simplifying access management and resource utilization.

## Key Components

1. **Ingress Resource**: 
   - Defines the rules for routing external HTTP/S traffic to the appropriate services based on the request's host and path.

2. **Ingress Controller**: 
   - A component that implements the Ingress resource and manages traffic routing based on the defined rules. Common Ingress controllers include NGINX, Traefik, and HAProxy.

3. **Annotations**: 
   - Key-value pairs that provide additional configuration options for the Ingress resource. They can be used to customize the behavior of the Ingress controller.
  

##  Step 1: Create Namespace

First, create a namespace for the Node.js application.

```yml
# namespace.yml
apiVersion: v1
kind: Namespace
metadata:
  name: nodejs

```

```
kubectl apply -f namespace.yml

```

## Step 2: Create Deployment

```yml
# deployment.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs-deployment
  namespace: nginx
  labels:
    app: nodejs
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nodejs
  template:
    metadata:
      name: nodejs-pod
      namespace: nodejs
      labels:
        app: nodejs
    spec:
      containers:
        - name: nodejs
          image: anas011/nodejs:v1
          ports:
            - containerPort: 3000


```

```
kubectl apply -f deployment.yml
```

## Step 3: Create Service


```yml
# service.yml
kind: Service
apiVersion: v1
metadata:
  name: nodejs-service
  namespace: nginx
spec:
  type: NodePort
  selector:
    app: nodejs
  ports:
    - protocol: TCP
      nodePort: 30003
      port: 3000 # port in the service
      targetPort: 3000 # port in the pod


```

```
kubectl apply -f service.yml
```

## Step 4: Verify Deployment and Service

```
kubectl get deployment -n nginx
kubectl get svc -n nginx

```
## Step 5: Create NGINX Service

```yml
# nginx-service.yml
kind: Service
apiVersion: v1
metadata:
  name: nginx-service
  namespace: nginx
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      nodePort: 30001
      port: 80 # port in the service
      targetPort: 80 # port in the pod


```

```
kubectl apply -f nginx-service.yml

```

##  Step 6: Deploy Ingress Controller


```
kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
```



```
kubectl get ns
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```


##  Step 7: Create Ingress Resource




```yml
# ingress.yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
   name: nginx-node-ingress
   namespace: nginx
  
spec:
  rules:
    - http:
        paths:
          - pathType: Prefix
            path: /nginx
            backend:
              service:
                name: nginx-service
                port:
                  number: 80
          - pathType: Prefix
            path: /
            backend:
               service:
                 name: nodejs-service
                 port:
                   number: 3000


```

```
kubectl apply -f ingress.yml
```
```
kubectl get ingress -n nginx
```

To access the ingress controller, set up port forwarding:

```
sudo -E kubectl port-forward service/ingress-nginx-controller -n ingress-nginx 8087:80 --address=0.0.0.0

```


Accessing http://localhost:8087/nginx does not work as expected, you may need to adjust the ingress annotations to ensure proper routing.


To resolve this, you can add the following annotation to your ingress resource:


```
annotations:
  nginx.ingress.kubernetes.io/rewrite-target: /
```
This annotation tells the NGINX ingress controller to rewrite the incoming request path. With this configuration, when you access http://localhost:8087/nginx, the request will be rewritten to /, allowing it to reach the NGINX service correctly.

#  ingress.yml with annotation 

```yml
# ingress.yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
   name: nginx-node-ingress
   namespace: nginx
   annotations:
     nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - http:
        paths:
          - pathType: Prefix
            path: /nginx
            backend:
              service:
                name: nginx-service
                port:
                  number: 80
          - pathType: Prefix
            path: /
            backend:
               service:
                 name: nodejs-service
                 port:
                   number: 3000


```
```
kubectl apply -f ingress.yml
```


Now, when you access http://localhost:8087/nginx, you should see the "Welcome to NGINX" page, indicating that the request has been successfully routed to the NGINX service.












