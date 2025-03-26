# Service Mesh 

A **Service Mesh** is a dedicated infrastructure layer that manages service-to-service communication in a microservices architecture. It provides features such as traffic management, security, observability, and reliability without requiring changes to the application code. In Kubernetes (K8s), a service mesh can help manage the complexities of microservices communication.

## Key Features of a Service Mesh

- **Traffic Management**: Control the flow of traffic between services, including load balancing, traffic splitting, and retries.
- **Security**: Implement security features such as mutual TLS (mTLS) for secure communication between services, as well as authentication and authorization policies.
- **Observability**: Collect telemetry data, such as metrics and logs, to monitor and debug microservices.
- **Reliability**: Enhance the reliability of service-to-service communication through features like circuit breaking and retries.

## Popular Service Mesh Implementations

- **Istio**: One of the most popular service meshes, providing a rich set of features for managing microservices.
- **Linkerd**: A lightweight service mesh focused on simplicity and performance, making it easy to deploy and manage.
- **Consul**: A service mesh that integrates with HashiCorp's Consul for service discovery and configuration, providing a robust solution for microservices.


Service meshes are essential for managing the complexities of microservices communication in modern cloud-native applications. By providing a dedicated infrastructure layer, they enable developers to focus on building applications while ensuring secure, reliable, and observable service interactions.













## Step 1: Download Istio

#### Download the Installation File

You can download Istio manually from the Istio release page or use the following command to automatically download and extract the latest release for Linux or macOS:
```yml
curl -L https://istio.io/downloadIstio | sh -
```
#### Navigate to the Istio Package Directory

Change to the directory of the downloaded Istio package. For example, if the package is istio-1.25.0:
```yml
cd istio-1.25.0
```
#### Contents of the Installation Directory

The installation directory contains:
samples/: Sample applications.
bin/: Contains the istioctl client binary.
Move istioctl to a Global Path

To run istioctl from any directory, move it to /usr/local/bin:
```yml
sudo mv bin/istioctl /usr/local/bin
```

## Step 2: Install Istio

#### Install Istio Components

Use the following command to install Istio with a specific configuration file:
```yml
istioctl install -f samples/bookinfo/demo-profile-no-gateways.yaml -y
```
You should see messages indicating that Istio core and Istiod have been installed successfully.

####  Label the Default Namespace for Sidecar Injection

#### To enable automatic injection of Envoy sidecar proxies, label the default namespace:
```yml


kubectl label namespace default istio-injection=enabled
```

The namespace will be labeled, allowing Istio to manage traffic for applications deployed in this namespace.

## Step 3: Install the Kubernetes Gateway API CRDs

#### Check and Install Gateway API CRDs
Ensure that the Gateway API Custom Resource Definitions (CRDs) are installed:
```yml
kubectl get crd gateways.gateway.networking.k8s.io &> /dev/null || \
{ kubectl kustomize "github.com/kubernetes-sigs/gateway-api/config/crd?ref=v1.2.1" | kubectl apply -f -; }
```

 The CRDs for gateways and routes will be created if they do not already exist.
## Step 4: Deploy the Sample Application

#### Deploy the Bookinfo Application

Use the following command to deploy the Bookinfo application:
```yml
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml

```
 Services and deployments for the Bookinfo application will be created.
Check the Status of Pods

#### Verify that the pods are running:
```yml
kubectl get pods
```
 You should see the status of the pods. Initially, some may be in Init or Pending state.
## Step 5: Port Forwarding to Access the Application

#### Port Forward to Access the Product Page
Use the following command to forward the port for the product page service:
```yml
kubectl port-forward svc/productpage 9080:9080
```

 You should be able to access the product page at http://localhost:9080/productpage.
![Screenshot 2025-03-27 003000](https://github.com/user-attachments/assets/b9df6321-fc12-4188-a54e-3443d1907618)



 
## Step 6: Create an Ingress Gateway

#### Deploy the Gateway Configuration

To expose the application externally, apply the gateway configuration:
```yml
kubectl apply -f samples/bookinfo/gateway-api/bookinfo-gateway.yaml
```

 The gateway and HTTP route resources will be created.

 
### Annotate the Gateway

Change the service type to ClusterIP:
```yml
kubectl annotate gateway bookinfo-gateway networking.istio.io/service-type=ClusterIP --namespace=default

```

 The gateway will be annotated successfully.
 
#### Check the Gateway Status

Verify that the gateway is set up correctly:
```yml
kubectl get gateway
```
: You should see the gateway listed with its details.

## Step 7: Access the Application through the Gateway


#### Port Forward the Gateway Service
Forward the port for the gateway service:
```yml
kubectl port-forward svc/bookinfo-gateway-istio 8083:80


```
Open your browser and navigate to http://localhost:8080/productpage to view the Bookinfo application.


You should be able to access the application at http://localhost:8083/productpage.
![Screenshot 2025-03-27 004018](https://github.com/user-attachments/assets/0b7a0b3e-95f2-487d-81a3-e25d40c0a2fd)



##  Step 8: Send Requests to Generate Trace Data

#### Send Requests to the Product Page
To generate trace data, send 100 requests:
```yml
for i in $(seq 1 100); do curl -s -o /dev/null "http://localhost:8083/productpage"; done
```
This will help in generating telemetry data for monitoring.


##  Step 9: Deploy Telemetry Tools


####  Install Kiali, Prometheus, Grafana, and Jaeger

Deploy the telemetry tools:
```yml
kubectl apply -f samples/addons
```

All the necessary telemetry tools will be deployed.


###### Check the Rollout Status of Kiali

Ensure Kiali is deployed successfully:
```yml
kubectl rollout status deployment/kiali -n istio-system
```
You should see a message indicating that the Kiali deployment has rolled out successfully.

##  Step 10: Access the Kiali Dashboard


#### **Open the Kiali Dashboard**


Use the following command to open the Kiali dashboard:
```yml
istioctl dashboard kiali
```
A URL will be provided (e.g., http://localhost:20001/kiali). Open this URL in your browser to view the Kiali dashboard.

#### to see  -  again Send Requests to the Product Page
To generate trace data, send 100 requests:
```yml
for i in $(seq 1 100); do curl -s -o /dev/null "http://localhost:8083/productpage"; done
```
![Screenshot 2025-03-27 005904](https://github.com/user-attachments/assets/402a13b1-05bf-46bd-b502-868d2047779a)
![Screenshot 2025-03-27 010019](https://github.com/user-attachments/assets/b8439875-af90-4f10-ae86-f749b89419a4)
![Screenshot 2025-03-27 010042](https://github.com/user-attachments/assets/7cd38d1b-a95c-469b-a1e5-b14b14130de5)
![Screenshot 2025-03-27 010145](https://github.com/user-attachments/assets/8481fa6e-99c7-4dbf-a721-e1923c3666d4)


You have successfully installed Istio, deployed the Bookinfo application, set up an ingress gateway, and accessed the Kiali dashboard for monitoring. The Kiali dashboard provides insights into the service mesh, including traffic graphs, application health, and service configurations.
