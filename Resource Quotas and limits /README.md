# Kubernetes Resource Management

This document provides an overview of resource management in Kubernetes, focusing on Resource Limits and Resource Quotas.

## Resource Limit

### Definition
A Resource Limit is a setting that defines the maximum amount of resources (CPU, memory) that a specific container or pod can use. It is applied at the level of individual containers or pods.

### Purpose
Resource limits are used to ensure that a single container or pod does not consume excessive resources, which could affect the performance of other containers or pods running on the same node.

### Components
Resource limits can be set for:
- **CPU limits**: The maximum amount of CPU that a container or pod can use.
- **Memory limits**: The maximum amount of memory that a container or pod can use.


write this in deployment.yml
```yml
resources:
        requests:
          cpu: 100m
          memory: 128Mi
        limits:
          cpu: 200m
          memory: 256Mi
```


deployment.yml


```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: nginx
  labels:
    app: nginx

spec:
  replicas: 3
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
          resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 200m
                memory: 256Mi

          volumeMounts:
            - mountPath: /var/www/html
              name: my-volume
      volumes:
          - name: my-volume
            persistentVolumeClaim:
                claimName: local-pvc


```


```
kubectl apply -f namespace.yml
```
```
kubectl apply -f persistentVolume.yml

```

This command creates or updates the persistent volume local-pv, which provides storage for your application.

  ```
kubectl get pv -n nginx
```

```
kubectl apply -f persistentVolumeClaim.yml
```

```
kubectl apply -f deployment.yml
```
```
kubectl get deployment -n nginx
```
```
kubectl get pods -n nginx -o wide
```
```
kubectl describe pod nginx-deployment-7fbd6db447-25zvp -n nginx
```



# Resource Quota

### Definition
A Resource Quota is a mechanism that limits the total amount of resources (CPU, memory, storage, etc.) that can be consumed by all the resources (pods, services, etc.) in a specific namespace. It is used to ensure fair resource distribution among different teams or applications within a cluster.

### Purpose
Resource quotas are primarily used to prevent a single team or application from consuming all the resources in a namespace, which could lead to resource starvation for other applications.

### Components
Resource quotas can limit various resources, including:
- **CPU**: The total amount of CPU that can be requested by all pods in the namespace.
- **Memory**: The total amount of memory that can be requested by all pods in the namespace.
- **Persistent Volume Claims (PVCs)**: The total number of PVCs that can be created in the namespace.
- **Number of Pods, Services, etc.**: The total number of pods, services, and other resources that can be created in the namespace.


```
vim resource-quota.yml
```
```yml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: my-quota
  namespace: nginx
spec:
  hard:
    requests.cpu: "4"                # Total CPU requests for all pods in the namespace
    requests.memory: "8Gi"           # Total memory requests for all pods in the namespace
    limits.cpu: "4"                  # Total CPU limits for all pods in the namespace
    limits.memory: "8Gi"             # Total memory limits for all pods in the namespace
    pods: "10"                       # Maximum number of pods that can be created in the namespace
    persistentvolumeclaims: "5"      # Maximum number of PVCs that can be created in the namespace
```

```
kubectl apply -f resource-quota.yml
```

```
resourcequota/my-quota created
```


```
kubectl get resourcequota -n ngin
```

result:
```
NAME       AGE   REQUEST
                        LIMIT
my-quota   35s   persistentvolumeclaims: 1/5, pods: 3/10, requests.cpu: 300m/4, requests.memory: 384Mi/8Gi   limits.cpu: 600m/4, limits.memory: 768Mi/8Gi
```

```
kubectl describe resourcequota my-quota -n nginx

```
```
Name:                   my-quota
Namespace:              nginx
Resource                Used   Hard
--------                ----   ----
limits.cpu              600m   4
limits.memory           768Mi  8Gi
persistentvolumeclaims  1      5
pods                    3      10
requests.cpu            300m   4
requests.memory          384Mi  8Gi

```



Delete the ResourceQuota:


```


kubectl delete resourcequota my-quota -n nginx


```








