## Taints and Tolerations in Kubernetes

### Taints

**Definition**: Taints are a way to mark a node so that it can repel certain pods. When a node has a taint, it will not accept any pods that do not have a matching toleration.

**Purpose**: Taints are used to control which pods can be scheduled on which nodes. This is useful for:
- Preventing certain pods from being scheduled on specific nodes.
- Managing resources by isolating workloads.

**Example**: If you have a node that should only run high-priority workloads, you can taint that node to repel all other pods.

**Taint Format**: A taint consists of three parts:
- **Key**: A label key.
- **Value**: A label value.
- **Effect**: What happens to pods that do not tolerate the taint. Common effects are:
  - `NoSchedule`: Pods that do not tolerate the taint will not be scheduled on the node.
  - `PreferNoSchedule`: Kubernetes will try to avoid scheduling pods that do not tolerate the taint, but it is not guaranteed.
  - `NoExecute`: Pods that do not tolerate the taint will be evicted from the node.



**Example Command to Add a Taint**:
```bash
kubectl taint nodes <node-name> key=value:NoSchedule


```


## Tolerations in Kubernetes

### Definition
Tolerations are applied to pods and allow them to be scheduled on nodes with matching taints. They "tolerate" the taint, meaning the pod can be scheduled on that node.

### Purpose
Tolerations are used to enable specific pods to be scheduled on nodes that have certain taints. This is useful for:
- Allowing specific workloads to run on dedicated nodes.
- Managing resource allocation effectively.

### Example
If a node is tainted to repel all pods except those that are high-priority, you can add a toleration to those high-priority pods.

### Toleration Format
A toleration consists of:
- **Key**: The key of the taint to tolerate.
- **Value**: The value of the taint to tolerate.
- **Effect**: The effect of the taint to tolerate (e.g., `NoSchedule`).

### Example Toleration in a Pod Spec
Here is an example of how to define a toleration in a pod specification:

```yaml
tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"




```


Taint a Node: Use the following command to taint a specific node. Replace <node-name> with the name of your node.

```
kubectl taint nodes <node-name> dedicated=high-priority:NoSchedule
```
```
kubectl taint node kind-worker2  prod=
true:NoSchedule
```
# toleration 

```
kubectl taint node kind-worker2  prod=
true:NoSchedule-

```

## Create a Pod with a Toleration

```yml
apiVersion: v1
kind: Pod
metadata:
  name: high-priority-pod
spec:
  containers:
    - name: nginx
      image: nginx:latest
  tolerations:
    - key: "dedicated"
      operator: "Equal"
      value: "high-priority"
      effect: "NoSchedule"

      
```
```
kubectl apply -f high-priority-pod.yml
```

```
kubectl get pods
```
```
kubectl describe pod high-priority-pod
```


