## What is a Pod in Kubernetes?

A **Pod** is the smallest and simplest  deployable unit  that can create and manage in Kubernetes. It represents a single instance of a running process in your cluster. Pods can contain one or more containers, which are usually tightly coupled applications that need to share resources.

### Key Features of Pods:

- **Single or Multiple Containers**: A Pod can run a single container or multiple containers that need to work together. For example, a web server and a helper program that pushes logs to a logging service can run in the same Pod.

- **Shared Networking**: All containers in a Pod share the same IP address and port space, which allows them to communicate easily with each other.

- **Shared Storage**: Pods can also share storage volumes, allowing containers to access the same data.

### Practical Example: Creating a Pod

Here’s a simple YAML example to create a Pod that runs an Nginx web server.

#### Nginx Pod YAML Manifest

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80

```

## Managing Pods in Kubernetes

### Creating a Pod

To create a Pod defined in a YAML file, use the following command:

```bash
kubectl apply -f nginx-pod.yml
```
Performing a Dry Run
If you want to see what would happen if you applied the configuration without actually creating or modifying any resources, you can perform a dry run:

```bash
kubectl apply -f nginx-pod.yml --dry-run=client
```
TO list all pods

```bash
kubectl get pods -n nginx



