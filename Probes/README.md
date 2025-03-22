# Kubernetes Probes

In Kubernetes, **probes** are used to check the health and readiness of applications running in containers. They help ensure that your applications are functioning properly and can handle incoming traffic. There are three main types of probes:

## 1. Liveness Probes

- **Definition**: Checks if your application is still running. If it fails, Kubernetes will restart the container.
- **Purpose**: Useful for applications that might get stuck. Restarting the container helps recover the application.
- **Example**: A web server that stops responding can be checked by a liveness probe.
- **Configuration Example**:
  ```yaml
  livenessProbe:
    httpGet:
      path: /healthz
      port: 8080
    initialDelaySeconds: 30
    periodSeconds: 10

  ```

## 2.  Readiness Probes

### Definition
Readiness probes are used to check if your application is ready to handle traffic. If a readiness probe fails, Kubernetes will stop sending traffic to that container until it passes the probe again.

### Purpose
The primary purpose of readiness probes is to ensure that only healthy containers receive traffic, especially during the startup phase of an application. This is crucial for applications that may take some time to initialize, such as databases or services that require loading data.

### Example
For instance, a database that takes time to initialize can be monitored using a readiness probe to ensure it is ready to accept connections before traffic is routed to it.

### Configuration Example
Here is an example of how to configure a readiness probe in a Kubernetes deployment YAML file:

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5

```


##  3 . Startup Probes

### Definition
Startup probes are similar to liveness probes, but they specifically check if an application has started successfully. If a startup probe fails, Kubernetes will restart the container.

### Purpose
The primary purpose of startup probes is to handle applications that take a long time to start. By using startup probes, you can prevent Kubernetes from prematurely restarting containers that are still in the process of starting up.

### Example
For instance, a web application that takes time to load dependencies can be monitored using a startup probe to ensure it has fully started before traffic is routed to it.

### Configuration Example
Here is an example of how to configure a startup probe in a Kubernetes deployment YAML file:

```yaml
startupProbe:
  httpGet:
    path: /start
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5


```
# Kubernetes Deployment for Node-Phontiqe Application

This document provides a step-by-step explanation of deploying the Node-Phontiqe application using Kubernetes.

## Step 1: Navigate to the Application Directory

```bash
cd node-phontiqe
```

## Step 2: List the Files in the Directory

```
ubuntu@LAPTOP-7E5UNQJ4:~/k8s-production/node-phontiqe$ ls

```

ubuntu@LAPTOP-7E5UNQJ4:~/k8s-production/node-phontiqe/k8s$ cat deployment.yml


```yml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: nodejs-deployment
  namespace: nodejs
  labels:
    app: nodejs-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nodejs-app
  template:
    metadata:
      labels:
        app: nodejs-app
    spec:
      containers:
        - name: nodejs-app
          image: lucky0111/nodejs:latest
          ports:
           - containerPort: 3000
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
            limits:
              cpu: 200m
              memory: 256Mi
          livenessProbe:
            httpGet:
              path: /
              port: 3000
            initialDelaySeconds: 30
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /
              port: 3000
            initialDelaySeconds: 30
            periodSeconds: 10


```
```
 kubectl apply -f deployment.yml
```

```

kubectl apply -f service.yml
```

```
kubectl get pods -n ndoejs

```
```
 kubectl get pods -n nodejs
```

```
kubectl describe pods nodejs-deployment-79979f9f97-9xpjr -n nodejs


```


```
Name:             nodejs-deployment-79979f9f97-9xpjr
Namespace:        nodejs
...
Status:           Running
...
Liveness:     http-get http://:3000/ delay=30s timeout=1s period=10s #success=1 #failure=3
Readiness:    http-get http://:3000/ delay=30s timeout=1s period=10s #success=1 #failure=3
...

```
```
 kubectl port-forward service/nodejs-service -n nodejs 3000:3000 --address=0.0.0.0

```
