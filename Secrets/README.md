# Secrets in Kubernetes

## Overview

Secrets are a Kubernetes resource used to store sensitive information, such as passwords, OAuth tokens, SSH keys, and other confidential data. Secrets allow you to manage sensitive information separately from your application code and configuration, ensuring that sensitive data is not exposed in your source code or container images.

## Key Features

- **Decoupling Sensitive Data from Code**: Secrets enable you to keep sensitive information separate from your application code. This separation helps to prevent accidental exposure of sensitive data in version control systems.

- **Base64 Encoding**: Secrets are stored in base64-encoded format, which provides a basic level of obfuscation. However, it is important to note that base64 encoding is not encryption, and additional security measures should be taken to protect sensitive data.

- **Multiple Consumption Methods**: Secrets can be consumed in various ways, including:
  - As environment variables in containers.
  - As command-line arguments to containers.
  - As files in a volume mounted to a container.

- **Access Control**: Kubernetes provides fine-grained access control for Secrets using Role-Based Access Control (RBAC). You can specify which users or service accounts have access to specific Secrets.


## Step 1: Create a Secret

Secrets in Kubernetes are used to store sensitive information, such as passwords. In this case, we are creating a Secret to store the MySQL root password.



```yml
# secrets.yml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
  namespace: mysql
data:
  MYSQL_ROOT_PASSWORD: cm9vdA0=  # base64 encoded for "root"



```

```
kubectl apply -f secrets.yml

```

```
kubectl get secret -n mysql

```

## Step 2: Create a StatefulSet

A StatefulSet is used to manage stateful applications in Kubernetes. In this case, we are deploying a MySQL database with a StatefulSet.

```yml
# statefulsets.yml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql-statefulsets
  namespace: mysql
spec:
  serviceName: mysql-service
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:8.0
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                   name: mysql-secret
                   key: MYSQL_ROOT_PASSWORD
            - name: MYSQL_DATABASE
              valueFrom:
                configMapKeyRef:
                  name: mysql-config-map
                  key: MYSQL_DATABASE
          volumeMounts:
            - name: mysql-data
              mountPath: /var/lib/mysql
  volumeClaimTemplates:
    - metadata:
        name: mysql-data
      spec:
        accessModes: [ "ReadWriteOnce" ]
        resources:
          requests:
            storage: 1Gi



```


```
kubectl apply -f statefulsets.yml


```

```
kubectl get pods -n mysql
```

# Step 3: Access the MySQL Database


```
kubectl exec -it mysql-statefulsets-1 -n mysql -- bash
``



