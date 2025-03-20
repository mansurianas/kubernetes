# DaemonSet in Kubernetes

A **DaemonSet** is a special feature in Kubernetes that ensures a specific type of Pod (a group of containers) runs on every node (computer) in your cluster. This is particularly useful for services that need to be active on all nodes, such as logging tools, monitoring tools, or network helpers.

## Key Features of DaemonSets

- **One Pod per Node**: A DaemonSet guarantees that there is one Pod running on each node. If you add a new node to your cluster, the DaemonSet will automatically start a Pod on that new node.

- **Node Selection**: You can choose which nodes should run the Pods. This can be done using specific labels or rules that you set up.

- **Automatic Updates**: If you need to change something in the DaemonSet, Kubernetes will automatically update the Pods on all the nodes for you.

- **Resource Management**: You can set limits on how much CPU and memory each Pod can use, helping to manage resources effectively.

## Use Cases for DaemonSets

- **Log Collection**: You can use a DaemonSet to run a logging tool (like Fluentd) on every node to gather logs from applications running there.

- **Monitoring**: You can run monitoring tools (like Prometheus Node Exporter) on each node to collect performance data about the nodes.

- **Network Proxies**: You can deploy a network helper (like Envoy) on every node to manage and direct network traffic.

- **Storage Daemons**: You can run storage management tools (like Ceph) on each node to handle distributed storage.

## How DaemonSets Work

When you create a DaemonSet, Kubernetes performs the following steps:

1. **Pod Template**: You create a template that describes what the Pod should look like, including which container image to use and what resources it needs.

2. **Node Selection**: Kubernetes checks which nodes in the cluster match the criteria you set for running the Pods.

3. **Pod Scheduling**: Kubernetes then places a Pod on each of the eligible nodes based on your rules.

4. **Monitoring**: Kubernetes continuously monitors the Pods created by the DaemonSet. If a Pod crashes or if a new node is added, Kubernetes ensures that there is still one Pod running on each eligible node.

---

```yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nginx-daemonset
  namespace: nginx
  labels:
    app: nginx
spec:
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


kubectl apply -f nginx-daemonset.yml


kubectl get daemonsets -n nginx

kubectl get pods  -n nginx  -o wide 







