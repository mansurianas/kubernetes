## Node Affinity in Kubernetes

Node Affinity is a set of rules used by the Kubernetes scheduler to determine on which nodes a pod can be scheduled based on node labels. It allows you to constrain which nodes your pods are eligible to be scheduled on, based on labels assigned to the nodes.

### Types of Node Affinity

There are two types of Node Affinity:

1. **RequiredDuringSchedulingIgnoredDuringExecution**: This type of affinity is mandatory. If the node does not meet the specified criteria, the pod will not be scheduled on that node.

2. **PreferredDuringSchedulingIgnoredDuringExecution**: This type of affinity is preferred but not mandatory. The scheduler will try to place the pod on a node that meets the criteria, but if no such node is available, the pod can still be scheduled on other nodes.

















# Configuring Node Affinity in Kubernetes

This document outlines the steps taken to configure Node Affinity for a deployment in Kubernetes. Node Affinity allows you to constrain which nodes your pods can be scheduled on based on labels assigned to the nodes.

## Step 1: Label Your Nodes

### Check Existing Nodes

```bash
kubectl get nodes

```

```
NAME           STATUS   ROLES    AGE   VERSION
node1          Ready    <none>   10d   v1.21.0
node2          Ready    <none>   10d   v1.21.0
```



Label a Node
```bash
kubectl label nodes node1 disktype=ssd
```
output
```
node/node1 labeled
```



node-affinity-deployment.yml

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-affinity-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: node-affinity-app
  template:
    metadata:
      labels:
        app: node-affinity-app
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: disktype
                    operator: In
                    values:
                      - ssd
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```
this deployment configuration specifies that the pods should only be scheduled on nodes with the label disktype=ssd.


```
kubectl apply -f node-affinity-deployment.yml

```
```

kubectl get pods
```


```
NAME                                READY   STATUS    RESTARTS   AGE
node-affinity-app-<id>              1/1     Running   0          1m
node-affinity-app-<id>              1/1     Running   0          1m
node-affinity-app-<id>              1/1     Running   0          1m


```
```
kubectl describe pod node-affinity-app-<id>

```

output:

```
Name:         node-affinity-app-<id>
Namespace:    default
Node:         node1/<node-ip>
...
Affinity:
  Node Affinity:
    Required During Scheduling Ignored During Execution:
      Node Selector Term 0:
        Match Expressions:
          disktype in [ssd]

```

Note: The output confirms that the pod is scheduled on node1, which has the label disktype=ssd.

```
kubectl delete deployment node-affinity-app
```









