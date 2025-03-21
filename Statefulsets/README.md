# StatefulSets in Kubernetes

StatefulSets are a Kubernetes resource designed to manage stateful applications. Unlike Deployments, which are suitable for stateless applications, StatefulSets provide guarantees about the ordering and uniqueness of pods, making them ideal for applications that require stable identities, persistent storage, and ordered deployment and scaling.

## Key Features of StatefulSets

- **Stable Network Identity**: Each pod in a StatefulSet has a unique, stable network identity that persists across rescheduling. This is crucial for applications that rely on stable hostnames.

- **Ordered Deployment and Scaling**: Pods in a StatefulSet are created, updated, and deleted in a specific order. This ensures that the application can start and stop in a controlled manner.

- **Persistent Storage**: StatefulSets can be associated with persistent storage volumes, allowing data to persist even if the pod is rescheduled or restarted.

## When to Use StatefulSets

You should consider using StatefulSets for applications that require:

- **Stable, unique network identifiers** (e.g., databases, clustered applications).
- **Ordered deployment and scaling** (e.g., when one pod must be ready before another starts).
- **Persistent storage** that needs to be retained across pod restarts.





## Step 1: Create Namespace


```yml
# namespace.yml
kind: Namespace
apiVersion: v1
metadata:
  name: mysql


```
```
kubectl apply -f namespace.yml
```

##  Step 2: Create MySQL Service (headless)


```yml
# service.yml
kind: Service
apiVersion: v1
metadata:
  name: mysql-service
  namespace: mysql
spec:
  clusterIP: None
  selector:
    app: mysql
  ports:
    - name: mysql
      port: 3306
      targetPort: 3306
      protocol: TCP

```

```
kubectl apply -f service.yml

```


## Step 3: Create MySQL StatefulSet


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
              value: devops


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
````

output:

```
NAME                   READY   STATUS    RESTARTS   AGE
mysql-statefulsets-0   1/1     Running   0          2m4s
mysql-statefulsets-1   1/1     Running   0          2m3s
mysql-statefulsets-2   1/1     Running   0          60s

```


####  Step 5: Access MySQL

```
kubectl exec -it mysql-statefulsets-0 -n mysql -- bash
```

```
mysql -u root -p
```
enter password - root


```
show databases;
```
```
+--------------------+
| Database           |
+--------------------+
| devops             |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.15 sec)


```

exit


###  Step 6: Delete a Pod

```
kubectl delete pod mysql-statefulsets-0 -n mysql
```


```
kubectl get pods -n mysql

```

##  You should see that the pod mysql-statefulsets-0 is recreated automatically, maintaining the same name due to the StatefulSet's management of state.




   
