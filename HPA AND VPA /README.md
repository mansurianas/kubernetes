# Kubernetes Autoscaling Overview

Kubernetes provides several tools to automatically adjust the resources of your applications based on demand. Here’s a simple explanation of three key autoscaling components:

## Horizontal Pod Autoscaler (HPA)

- **Functionality**: HPA automatically increases or decreases the number of pods in a deployment based on the current load.
- **Use Case**: For example, if your application experiences high traffic, HPA can add more pods to handle the increased demand.

## Vertical Pod Autoscaler (VPA)

- **Functionality**: VPA adjusts the resource limits (CPU and memory) of your existing pods.
- **Use Case**: If a pod needs more resources due to increased workload, VPA can automatically update its resource requests and limits to ensure optimal performance.

## KEDA (Kubernetes Event-driven Autoscaling)

- **Functionality**: KEDA allows you to scale your applications based on external events or metrics, such as messages in a queue or HTTP requests.
- **Use Case**: It works alongside HPA to provide more granular scaling based on specific triggers, making it ideal for event-driven architectures.

These tools help ensure that your applications run efficiently and can handle varying loads without manual intervention.



# Kubernetes HPA  Installation Guide

In this guide, we will demonstrate how to deploy the Horizontal Pod Autoscaler (HPA) and Vertical Pod Autoscaler (VPA) controllers in a Minikube or KIND cluster. HPA will automatically scale the number of pods based on CPU utilization, while VPA will adjust the CPU and memory resources within existing pod containers, effectively scaling capacity vertically.

## Prerequisites

1. **Create a Virtual Machine**:
   - Create 1 virtual machine on AWS with the following specifications:
     - **Instance Type**: t2.medium
     - **CPU**: 2
     - **RAM**: 4GB

2. **Setup Minikube**:
   - Follow the [Minikube setup guide](https://minikube.sigs.k8s.io/docs/start/) to install Minikube on your virtual machine.

3. **Ensure Metrics Server is Installed**:
   - The Metrics Server is required to enable HPA. If it is not already installed, you can enable it using the following command:

   ```bash
   minikube addons enable metrics-server


```
Check Minikube Status:
```
Verify that your Minikube cluster is running:


```
minikube status

```
Get Nodes:

List the nodes in your Minikube cluster:

```
kubectl get nodes
```

#  If Using a KIND Cluster
If you are using a KIND (Kubernetes IN Docker) cluster, follow these steps to install the Metrics Server:

Install Metrics Server:

Apply the Metrics Server components:


```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releas
```

Edit the Metrics Server Deployment:

Edit the Metrics Server deployment to add security bypass options:
```
kubectl -n kube-system edit deployment metrics-server

```
Under container.args, add the following lines:


```yaml

- --kubelet-insecure-tls
- --kubelet-preferred-address-types=InternalIP,Hostname,ExternalIP

```
Restart the Deployment:

Restart the Metrics Server deployment to apply the changes:
```
kubectl -n kube-system rollout restart deployment metrics-server

```
Verify Metrics Server is Running:

Check if the Metrics Server pod is running:


```
kubectl get pods -n kube-system


```
Check Node Metrics:

Use the following command to see the metrics for your nodes:


```
kubectl top nodes

```



## Step 1: Navigate to the Apache Directory

```bash
cd apache
```

Create Namespace
```yml
kind: Namespace
apiVersion: v1
metadata:
  name: apache
```

Create Service

```yml
kind: Service
apiVersion: v1
metadata:
    name: apache-service
    namespace: apache
spec:
  selector:
      app: apache
  ports:
    - protocol: TCP
      port: 80 # exposed port in the cluster
      targetPort: 80 # container port
  type: ClusterIP


```


Create Deployment


```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
  namespace: apache
spec:
  replicas: 1
  selector:
    matchLabels:
      app: apache
  template:
    metadata:
      labels:
        app: apache
    spec:
      containers:
        - name: apache
          image: httpd:latest
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: "100m"
              memory: "128Mi"
            limits:
              cpu: "200m"
              memory: "256Mi"

```
```
kubectl apply -f namespace.yml -f service.yml -f deployment.yml
```


Access the Service via DNS
You can also access the service through DNS:

```
curl http://apache-service.apache.svc.cluster.local
```


 Port Forwarding to Access the Service
Port Forward the Service

```
sudo -E kubectl port-forward service/apache-service -n apache 83:80 --addres
```



Now, if you navigate to http://localhost:83 in your browser, it should display the Apache server.  ---  it's work write on browser




####  Scale the Deployment
Attempt to Scale the Deployment

```
kubectl scale deployment apache-deployment -n apache --replicas=3
```
```

kubectl get pods -n apache
```
output
```
NAME                                READY   STATUS    RESTARTS   AGE
apache-deployment-b8f7fbcc5-2vklf   1/1     Running   0          0s
apache-deployment-b8f7fbcc5-rbvq6   1/1     Running   0          0s
apache-deployment-b8f7fbcc5-s2lwt   1/1     Running   0          6m4s
```


##  this is not autoscaling  for autoscaling we use hpa/vpa 


now we do hpa 


## Step 1: Create HPA Configuration

### Open HPA Configuration File

```bash
vim hpa.yml

``

```yml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: apache-hpa
  namespace: apache
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: apache-deployment
  minReplicas: 1
  maxReplicas: 5
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 5

```


Apply the HPA Configuration
```
kubectl apply -f hpa.yml
```

```
kubectl get hpa -n apache

```
result :
```
NAME         REFERENCE                      TARGETS       MINPODS   MAXPODS   REPLICAS   AGE
apache-hpa   Deployment/apache-deployment   cpu: 1%/5%   1         5         1          10m

```
Note: The output shows that the current CPU utilization is 1% with a target of 5%.


Perform Load Testing
Run Load Generator Pod

```
kubectl run -i --tty load-generator --image=busybox --namespace=apache --
```


Generate Load Using wget

```
while true; do wget -q -O- http://apache-service.apache.svc.cluster.local; done

```
 This command continuously sends requests to the Apache service to generate load.


```
kubectl get pods -n apache

```
```

NAME                                READY   STATUS    RESTARTS   AGE
apache-deployment-b8f7fbcc5-gr94c   1/1     Running   0          3m52s
apache-deployment-b8f7fbcc5-s2lwt   1/1     Running   0          28m
apache-deployment-b8f7fbcc5-vjzzx   1/1     Running   0          3m52s
apache-deployment-b8f7fbcc5-w9vxd   1/1     Running   0          3m52s
apache-deployment-b8f7fbcc5-w9zj5   1/1     Running   0          3m37s
load-generator                      1/1     Running   0          5m45s

```

Check HPA Status Again
```
kubectl get hpa -n apache
```
result 
```


NAME         REFERENCE                      TARGETS       MINPODS   MAXPODS   REPLICAS   AGE
apache-hpa   Deployment/apache-deployment   cpu: 25%/5%   1         5         5          10m

```
Note: The output indicates that the CPU utilization has increased to 25%, prompting the HPA to scale up to 5 replicas.


Stop Load Testing

Stop Load Generator


To stop the load generator, you can exit the interactive shell by typing exit or pressing Ctrl + D.

Check HPA After Stopping Load
```
kubectl get hpa -n apache

```
result

```
NAME         REFERENCE                      TARGETS      MINPODS   MAXPODS   REPLICAS   AGE
apache-hpa   Deployment/apache-deployment   cpu: 1%/5%   1         5         4          17m

```
Note: The CPU utilization has dropped back to 1%, and the HPA has scaled down to 4 replicas.


# Deleting and Recreating a Deployment in Kubernetes

This document outlines the steps taken to delete an existing deployment and recreate it in the `apache` namespace.
  just give idea when fist time create only create one pod  after  then it create multiple replicas


  ## Step 1: Delete the Existing Deployment

### Command to Delete Deployment

```bash
kubectl delete deployment apache-deployment -n apache
```


```
kubectl get pods -n apache

```

```

NAME             READY   STATUS    RESTARTS      AGE
load-generator   1/1     Running   1 (14m ago)   21m

```
Note: The output shows that the load-generator pod is still running, but there are no pods from the deleted deployment.


Recreate the Deployment
```
kubectl apply -f deployment.yml
```

Check Pods After Recreating Deployment
```
kubectl get pods -n apache
```
```
NAME                                READY   STATUS    RESTARTS      AGE
apache-deployment-b8f7fbcc5-l8nzk   1/1     Running   0             8s
load-generator                      1/1     Running   1 (14m ago)   22m
```


Note: The output shows that the apache-deployment pod is now running successfully.


```
kubectl apply -f hpa.yml
```

Check Pods After Applying HPA

```
kubectl get pods -n apache
```

Check Pods Multiple Times
```
kubectl get pods -n apache
```


```
NAME                                READY   STATUS    RESTARTS      AGE
apache-deployment-b8f7fbcc5-2cxh8   1/1     Running   0             31s
apache-deployment-b8f7fbcc5-fps7h   1/1     Running   0             31s
apache-deployment-b8f7fbcc5-hvt6l   1/1     Running   0             16s
apache-deployment-b 8f7fbcc5-l8nzk   1/1     Running   0             85s
apache-deployment-b8f7fbcc5-s62ql   1/1     Running   0             31s
load-generator                      1/1     Running   1 (15m ago)   23m
```
Note: The output shows multiple replicas of the apache-deployment pod are running successfully along with the load-generator pod.



now create for phontiqe app

# Node.js Application Deployment (Phontiqe) on Kubernetes

This guide outlines the steps to deploy a Node.js application named Phontiqe on a Kubernetes cluster, including creating a namespace, deployment, and Horizontal Pod Autoscaler (HPA).

## Step 1: Navigate to the Node.js Phontiqe Directory

```bash
cd node-phontiqe

```
```
cd k8s

```

vim deployment.yml
```

```yml
kind: Deployment
apiVersion: apps/v1
metadata:
  name: nodejs-deployment
  namespace: nodejs
  labels:
    app: nodejs-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nodejs-app
  template:
    metadata:
      labels:
        app: nodejs-app
    spec:
      containers:
        - name: nodejs-app
          image: lucky0111/nodejs:latest
          ports:
           - containerPort: 3000
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
            limits:
              cpu: 200m
              memory: 256Mi
          livenessProbe:
            httpGet:
              path: /
              port: 3000
            initialDelaySeconds: 30
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /
              port: 3000
            initialDelaySeconds: 30
            periodSeconds: 10
```
vim hpa.yml

```yml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nodejs-hpa
  namespace: nodejs
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nodejs-deployment
  minReplicas: 1
  maxReplicas: 5
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 5


```

kubectl apply -f namespace.yml -f deployment.yml


```

kubectl get pods -n nodejs
```
```
NAME                                 READY   STATUS    RESTARTS   AGE
nodejs-deployment-79979f9f97-xrk94   0/1     Running   0          18s
```

note : only one pod

now apply hpa 

```
kubectl apply -f hpa.yml
```

This command creates the Horizontal Pod Autoscaler for the Node.js deployment, allowing it to automatically scale based on CPU utilization.


```
kubectl get hpa -n nodejs
```
```
kubectl get pods -n nodejs
```

```
NAME                                 READY   STATUS    RESTARTS   AGE
nodejs-deployment-79979f9f97-2kx8k   1/1     Running   0
 110s
nodejs-deployment-79979f9f97-9n6rr   1/1     Running   0
 110s
nodejs-deployment-79979f9f97-gdt58   1/1     Running   0
 97s
nodejs-deployment-79979f9f97-srv4b   1/1     Running   0
 110s
nodejs-deployment-79979f9f97-xrk94   1/1     Running   0
 36m

```


here pods are increased 





#  Setting Up Vertical Pod Autoscaler (VPA) in Kubernetes



Step 1: Clone the VPA Repository
To begin, clone the Vertical Pod Autoscaler repository from GitHub:

```bash
git clone https://github.com/kubernetes/autoscaler.git

```
Step 2: Navigate to the VPA Directory
Change your current directory to the Vertical Pod Autoscaler folder:

```bash
cd autoscaler/vertical-pod-autoscaler

```


Step 3: Run the Installation Script

Execute the installation script to set up the necessary components for VPA in your Kubernetes cluster:

```bash
./hack/vpa-up.sh
```

Step 4: Verify the VPA Pods
After the installation is complete, verify that the VPA components are running in the kube-system namespace:

```bash
kubectl get pods -n kube-system

```


This command will list all the pods in the kube-system namespace, including those related to the Vertical Pod Autoscaler.  




# Managing Vertical Pod Autoscaler (VPA) in Kubernetes

This document outlines the steps taken to create and manage a Vertical Pod Autoscaler (VPA) for an Apache deployment in the `apache` namespace.

## Step 1: Create VPA Configuration

### Open VPA Configuration File

```yml
vim vpa.yml

kind: VerticalPodAutoscaler
apiVersion: autoscaling.k8s.io/v1
metadata:
  name: apache-vpa
  namespace: apache
spec:
  targetRef:
    name: apache-deployment
    apiVersion: apps/v1
    kind: Deployment
  updatePolicy:
    updateMode: "Auto"


```
```
kubectl get hpa -n apche


```
```
kubectl get hpa -n apche
```
```
kubectl apply -f vpa.yml
```
```
kubectl get vpa -n apache
```

```
NAME         MODE   CPU   MEM       PROVIDED   AGE
apache-vpa   Auto   25m   262144k   True       15m
```

```

NAME         MODE   CPU   MEM       PROVIDED   AGE
apache-vpa   Auto   25m   262144k   True       15m


```
Note: The output shows that the VPA is in Auto mode, with a CPU recommendation of 25m and memory of 262144k



```

sudo -E kubectl port-forward service/apache-service -n apache 83:80 --address=0.0.0.0 &

```

```
kubectl run -i --tty load-generator --image=busybox --namespace=apache -- /bin/sh

```

```
while true; do wget -q -O- http://apache-service.apache .svc.cluster.local; done

```
this command continuously sends requests to the Apache service, generating load to test the autoscaling behavior.


kubectl top pod -n apache




```
kubectl get vpa -n apache

```
```
NAME         MODE   CPU   MEM       PROVIDED   AGE
apache-vpa   Auto   163m   262144k   True       13m

```
Note: The output indicates that the CPU recommendation has increased to 163m, reflecting the load generated by the load generator.

