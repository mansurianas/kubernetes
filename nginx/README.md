# Deploying Nginx on Kubernetes

This guide provides an overview of deploying an Nginx web server on Kubernetes, including the roles of Pods, Services, Deployments, and Namespaces. It also includes example manifest files and deployment steps.

## Overview

### 1. Role of a Kubernetes Pod

A Kubernetes Pod is the smallest deployable unit in Kubernetes and represents a single instance of a running process. When deploying Nginx, a Pod encapsulates the Nginx container along with shared networking and storage resources.

### 2. Purpose of a Kubernetes Service

A Kubernetes Service provides a stable network endpoint for accessing Nginx Pods. It enables load balancing, service discovery, and ensures availability even if Pods are rescheduled or scaled.

### 3. Enhancements of a Kubernetes Deployment

A Kubernetes Deployment manages the lifecycle of Nginx Pods. It allows for declarative updates, scaling, and self-healing by maintaining a desired number of replica Pods based on a defined configuration.

### 4. Using Namespaces

A Namespace provides a virtual cluster environment within a physical cluster. It helps in organizing and isolating resources, making it easier to manage and monitor Nginx-related components separately.

## Manifest File Examples

### 1. Nginx Pod Manifest in "nginx" Namespace

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: nginx
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80


```


### 2. Nginx Service Manifest in "nginx" Namespace

```yaml
apiVersion: v1
kind: Service
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
      port: 80        # port in the service
      targetPort: 80  # port in the pod


```

### 3. Nginx Deployment Manifest in "nginx" Namespace

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: nginx
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80

```


## Nginx Namespace Deployment Steps

 **Create the "nginx" Namespace:**

   ```sh
   kubectl create namespace nginx

## Apply the Nginx Pod, Service, and Deployment YAMLs within the "nginx" Namespace:

```sh
kubectl apply -f pod.yaml -n nginx
kubectl apply -f service.yaml -n nginx
kubectl apply -f deployment.yaml -n nginx
