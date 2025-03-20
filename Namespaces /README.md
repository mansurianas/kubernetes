# Kubernetes Namespaces

A **namespace** in Kubernetes is akin to a virtual cluster within a physical cluster. It enables you to organize and manage resources effectively in a Kubernetes environment. You can think of namespaces as a way to create multiple environments (such as development, testing, and production) within the same cluster, ensuring that they do not interfere with one another.

## Why Use Namespaces?

- **Isolation**: Namespaces provide a means to isolate resources. For instance, you can run two applications with the same name in different namespaces without any conflict.
  
- **Resource Management**: You can apply resource quotas and limits to namespaces, helping to manage the CPU and memory usage of each application.
  
- **Access Control**: Different permissions can be set for different namespaces, allowing you to control who can access what resources.
  
- **Organization**: Namespaces help maintain organization, especially in large teams or projects, by categorizing resources effectively.

## Managing Namespaces in Kubernetes

You can manage namespaces in Kubernetes using both command-line commands and YAML manifest files. Below are examples of how to create, list, and delete namespaces using both methods.

### Using `kubectl` Commands

1. **Create a Namespace**:  
   To create a namespace, use the following command:
   ```bash
   kubectl create ns nginx

   ```
To list all namespaces, use:

```bash
kubectl get ns


```
# Kubernetes Namespace Management

## Deleting a Namespace
To delete a namespace you created, use the following command:

```bash
kubectl delete ns nginx

```
Namespace YAML Manifest

```yml
apiVersion: v1
kind: Namespace
metadata:
  name: nginx


```
TO create 

```bash
kubectl create -f namespace.yml
```

```
To delete

```bash
 
kubectl create -f namespace.yml

```

When to Use create vs apply
create: Use this command to create a resource from scratch. If the resource already exists, the command will fail. Use create when you are sure the resource does not exist yet.

Example:
```bash
kubectl create -f namespace.yml

```
apply: Use this command to create or update a resource. If the resource already exists, apply will update it with the configuration in the YAML file. Use apply when you want to ensure that the resource matches the YAML file configuration, whether creating it for the first time or updating it.

Example:

```bash 
kubectl apply -f namespace.yml
