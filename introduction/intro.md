# Kubernetes (K8s) Notes


- **Developed by Google**: Kubernetes, commonly referred to as K8s, was developed by Google, with predecessors Borg and Omega for managing large-scale data processing.
- **Open Source**: Open-sourced in 2014 and maintained by the Cloud Native Computing Foundation (CNCF).
- **Purpose**: Automates the deployment, scaling, and management of containerized applications.

## Key Features
- **Framework for Distributed Systems**: Provides a framework to run distributed systems resiliently.
- **Declarative Configuration**: Users define the desired state of their applications and infrastructure, and Kubernetes continuously works to maintain that state.

## YAML (YAML Ain't Markup Language)
- **Human-Readable**: YAML is a human-readable data serialization format used for configuration files.
- **Case Sensitive**: Relies on indentation for structure.
- **Quotes**: Special characters like @ and # require quotes.
- **Extensions**: Files can have `.yaml` or `.yml` extensions.

### Structure
- **Lists**: Represented by a single hyphen (-).
- **Documents**: Start with three hyphens (---).

## Advantages of Kubernetes in Addressing Docker Limitations

1. **Ephemeral Nature of Docker**:
   - **Solution**: Kubernetes provides Pods, which are the smallest deployable units. If a container fails, the Pod remains, ensuring that the application remains available.

2. **Single Host Issue**:
   - **Solution**: Kubernetes can create a cluster of machines (virtual or physical) to run containers, allowing for better resource utilization and redundancy.

3. **Auto-Scaling**:
   - **Solution**: Kubernetes provides Horizontal Pod Autoscaler (HPA) and Cluster Autoscaler, enabling applications to scale automatically based on demand.

4. **Auto-Healing**:
   - **Solution**: Kubernetes automatically replaces and reschedules containers from failed Pods, ensuring high availability and reliability.

5. **Enterprise-Level Standards**:
   - **Solution**: Kubernetes offers features like load balancing, service discovery, and secret management, making it suitable for enterprise applications.

## Disadvantages of Kubernetes

1. **High Resource Requirements**:
   - Kubernetes can be resource-intensive, requiring significant CPU and memory overhead, which may not be suitable for smaller applications or environments.

2. **Complexity**:
   - The learning curve for Kubernetes can be steep, especially for teams new to container orchestration. Managing a Kubernetes cluster requires a good understanding of its architecture and components.

3. **Security Concerns**:
   - Kubernetes introduces additional security considerations, such as managing access controls, securing the API server, and ensuring that Pods are isolated from each other. Misconfigurations can lead to vulnerabilities.

4. **Operational Overhead**:
   - Running and maintaining a Kubernetes cluster can require significant operational effort, including monitoring, logging, and troubleshooting.
