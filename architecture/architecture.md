# Kubernetes (K8s) Architecture

## Components of Kubernetes Architecture

![Kubernetes Architecture](https://github.com/user-attachments/assets/0223ef23-6783-48e4-ae6b-004e50a18d74)

### 1. Control Plane (Master Node)
The control plane is the brain of the Kubernetes cluster, responsible for managing and orchestrating the entire system. It includes several key components:

#### a. API Server
- The main entry point for all requests to the Kubernetes cluster.
- When you want to interact with your cluster, your request goes through the API server.
- **Functions**:
  - **Authentication**: Verifies who you are (like a security guard checking IDs).
  - **Authorization**: Checks what actions you are allowed to perform (using Role-Based Access Control).
  - **Admission Controller**: Validates and can modify requests before they are processed. There are two types:
    - **Mutating Admission Controller**: Can change requests.
    - **Validating Admission Controller**: Checks requests for compliance without making changes.

![Admission Controller](https://github.com/user-attachments/assets/13a6a4b1-49f9-4f6f-9d36-7aebae6b8454)

#### b. etcd
- A distributed key-value store that holds all the configuration data and state information for the cluster.
- Crucial for maintaining the cluster's state and is designed to be fault-tolerant.
- Data is not encrypted, and no fixed schema is defined.

#### c. Kube Scheduler
- Responsible for assigning Pods (the smallest deployable units in Kubernetes) to nodes based on resource availability and constraints.
- Acts like an event planner, ensuring the desired number of Pods are running.
- When you request a container to be run, the scheduler decides which node in your cluster should run it, considering resource availability and other constraints.

#### d. Kube Controller Manager
- Runs various controllers that monitor the state of the cluster and make adjustments as needed.
- Responsible for ensuring the desired state of the cluster is maintained.

#### e. Cloud Controller Manager
- Manages cloud-specific resources, such as load balancers and storage, allowing Kubernetes to interact with cloud infrastructure.

### 2. Worker Nodes
Worker nodes are where the containerized applications actually run. Each worker node has three main components:

#### a. Kubelet
- An agent that runs on each worker node, ensuring that Pods are running and healthy.
- Communicates with the API server to receive instructions and report the status of the Pods.

#### b. Kube Proxy
- Manages network communication for the Pods, handling traffic routing and ensuring that network policies are enforced.
- Acts as a network proxy that provides networking and load balancing capabilities for Pods in the cluster.
- **Functions**:
  - Uses iptables for networking.
  - Runs as a DaemonSet, installed on every worker node.
  - Creates IP tables and IP mapping.
  - **Cluster IP**: Private IP.
  - **RFC1918 CIDR Range**: (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) - Not available for public use, used internally only.

#### c. Container Runtime Interface (CRI)
- Allows Kubernetes to use different container runtimes (like Docker or containerd) to manage containers.
- Container runtimes are responsible for booting up the containers.
- Examples include:
  - **containerd**
  - **CRI-O**
  - **Rocket**
- A CRI can manage multiple Pods on a single node.

### 3. Networking and Storage
- **Container Network Interface (CNI)**: Manages networking for Pods, allowing for complex network configurations and policies.
- **Container Storage Interface (CSI)**: Standardizes storage management across different storage providers, enabling dynamic volume creation and deletion.

## Tools for Managing Kubernetes
- **kubectl**: A command-line tool used to interact with Kubernetes clusters. It allows you to deploy, manage, and scale applications.
- **Minikube**: A tool that creates a single-node Kubernetes cluster on your local machine, ideal for learning and development.
- **KIND (Kubernetes IN Docker)**: A tool for running Kubernetes clusters in Docker containers, primarily used for testing and development.
- **Kubeadm**: A tool that simplifies the process of setting up a production-ready Kubernetes cluster.

## Conclusion
Kubernetes architecture is designed to provide a robust and scalable platform for managing containerized applications. Understanding its components and how they interact is crucial for effectively deploying and managing applications in a Kubernetes environment. Each part of the architecture plays a vital role in ensuring that applications run smoothly and efficiently.
