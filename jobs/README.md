# Understanding Jobs in Kubernetes

A **Job** in Kubernetes is a resource that allows you to run a specific task or batch process until it completes successfully. Unlike a Deployment, which is designed to keep running continuously (like a web server), a Job is meant to run a task to completion and then stop.

## Key Features of Jobs

- **Completion**: Jobs are designed to ensure that a specified number of Pods successfully complete their tasks. Once the task is finished, the Job will terminate.

- **Parallelism**: You can control how many Pods run in parallel to complete the task. This is useful for speeding up processing when you have multiple tasks to perform simultaneously.

- **Retries**: If a Pod fails, the Job controller will automatically create a new Pod to replace it, up to a specified limit.

## Use Cases

Jobs are commonly used for tasks such as:

- Database backups
- Data migrations
- Batch processing of files
- Running scripts that need to complete once

## Job Configuration Parameters

- **Completion**: This parameter specifies how many successful Pods are required to consider the Job complete. For example, if you set `completions: 5`, the Job will ensure that 5 Pods successfully complete their tasks.

- **Parallelism**: This parameter controls how many Pods can run at the same time. For example, if you set `parallelism: 3`, up to 3 Pods will run concurrently.



```yml
kind: Job
apiVersion: batch/v1
metadata:
  name: demo-job
  namespace: nginx
spec:
  completions: 1
  parallelism: 1
  template:
    metadata:
      name: demo-job-pod
      labels:
        app: batch-task
    spec:
      containers:
        - name: batch-container
          image: busybox:latest
          command: ["sh", "-c", "echo Hello Dosto! && sleep 10"]
      restartPolicy: Never

```


Create the Namespace (if it doesn't exist)

Before creating the Job, ensure that the `nginx` namespace exists. If it doesn't, create it using the following command:

```bash
kubectl create namespace nginx


```
```bash
kubectl apply -f demo-job.yaml
```

```bash
kubectl get jobs -n nginx

```

You should see output similar to this:


NAME        COMPLETIONS   DURATION   AGE
demo-job    1/1           5s         1m



To see the Pods created by the Job, run:


```bash

kubectl get pods -n nginx

```
check the logs of the Pod created by the Job:

```bash
kubectl logs <pod-name> -n nginx

```
Replace <pod-name> with the actual name of the Pod (e.g., demo-job-pod-xxxxx). You should see the output:

```
Hello Dosto!


