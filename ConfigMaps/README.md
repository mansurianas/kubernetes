
# ConfigMaps in Kubernetes

## Overview

ConfigMaps are a Kubernetes resource used to store non-confidential configuration data in key-value pairs. They allow you to separate configuration artifacts from container images, making it easier to manage and update application settings without needing to rebuild or redeploy your containers.

## Key Features

- **Decoupling Configuration from Code**: ConfigMaps enable you to keep your application code and configuration separate. This separation allows for easier updates and management of configuration data.

- **Dynamic Configuration**: ConfigMaps can be updated independently of the application. When a ConfigMap is updated, the changes can be reflected in the application without requiring a redeployment.

- **Multiple Consumption Methods**: ConfigMaps can be consumed in various ways, including:
  - As environment variables in containers.
  - As command-line arguments to containers.
  - As files in a volume mounted to a container.













##  Step 1: Create a ConfigMap

```yml
# configMap.yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-config-map
  namespace: mysql
data:
  MYSQL_DATABASE: devops


```


```
kubectl apply -f configMap.yml

```

```
kubectl get configmaps -n mysql
```

how to use the ConfigMap in a StatefulSet for a MySQL deployment.

```yml
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
              value: root

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









