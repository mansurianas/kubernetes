# Kubernetes Persistent Volume and Persistent Volume Claim Setup

This document provides a step-by-step guide to setting up Persistent Volumes (PV), Persistent Volume Claims (PVC), and Deployments in Kubernetes.

## Overview

- **Persistent Volume (PV)**: A piece of storage in the Kubernetes cluster that is provisioned by an administrator or dynamically provisioned using Storage Classes. It exists independently of any Pod that uses it.

- **Persistent Volume Claim (PVC)**: A request for storage by a user. It specifies the size and access modes required.

- **Storage Class**: A way to describe the types of storage available in a Kubernetes cluster, allowing for dynamic provisioning of PVs.

- **Deployment**: A Kubernetes resource that manages a set of replicas of a Pod, ensuring that the desired number of Pods are running at all times.

##  Create a Persistent Volume (PV)

Create a YAML file named `persistentVolume.yml` with the following content:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
  namespace: nginx
  labels:
    app: local
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: local-storage
  hostPath:
    path: /mnt/data



```


```
kubectl apply -f persistentVolume.yml

persistentvolume/local-pv created

```

## Create a YAML file named persistentVolumeClaim.yml with the following content:

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: local-pvc
  namespace: nginx
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: local-storage


```

```
kubectl apply -f persistentVolumeClaim.yml

persistentvolumeclaim/local-pvc created
```

```
kubectl get pv
kubectl get pvc -n nginx

```


## Create a YAML file named deployment.yaml with the following content:

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
          volumeMounts:
            - mountPath: /var/www/html
              name: my-volume
      volumes:
        - name: my-volume
          persistentVolumeClaim:
            claimName: local-pvc


```


```
kubectl apply -f deployment .yaml

```
```

kubectl get pods -n nginx
```
```
kubectl describe pod <pod-name> -n nginx
```

```
kubectl exec -it <pod-name> -n nginx -- /bin/bash

```
```
cd /var/www/html
ls
```

## If you need to delete the Deployment while retaining the data in the Persistent Volume, you can do so with:

kubectl delete deployment nginx-deployment -n nginx



## If you want to clean up all resources created, you can delete the PVC and PV:

```
kubectl delete pvc local-pvc -n nginx
kubectl delete pv local-pv



```
