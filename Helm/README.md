
# My Helm Chart

## What is Helm?

Helm is a package manager for Kubernetes that simplifies the deployment and management of applications on Kubernetes clusters. It allows you to define, install, and upgrade even the most complex Kubernetes applications using a simple command-line interface.

## Why Use Helm?

- **Simplified Deployment**: Helm packages applications into charts, which are easy to install and manage.
- **Version Control**: Helm allows you to manage different versions of your applications, making it easy to roll back to a previous version if needed.
- **Configuration Management**: Helm charts can be customized with values files, allowing you to manage configurations easily.
- **Dependency Management**: Helm can manage dependencies between different applications, ensuring that all required components are installed together.
- **Community Support**: Helm has a large community and a rich ecosystem of pre-built charts available in repositories.
















###  Create a Directory for Helm
  ```
mkdir helm

```
```
cd helm

```
###  Download the Helm Installation Script
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

```
Explanation:
The curl command is used to transfer data from or to a server.
The flags -fsSL mean:
-f: Fail silently on server errors.
-s: Silent mode (don't show progress).
-S: Show an error message if it fails.
-L: Follow redirects.
The -o get_helm.sh option specifies the output filename for the downloaded script.
The URL points to the Helm installation script for version 3.
Result:
The script get_helm.sh is downloaded into the helm directory.


 ##  Change Permissions of the Script
```
chmod 700 get_helm.sh

```
The chmod command changes the file mode (permissions) of a file.
The 700 permission means:
7: Read, write, and execute permissions for the owner.
0: No permissions for the group.
0: No permissions for others.

The script get_helm.sh is now executable by the owner.

Run the Helm Installation Script
```
./get_helm.sh
```

The ./ indicates that you want to execute a script located in the current directory.

Helm is installed successfully, and you see a message indicating the installation was completed.

 
### Check Helm Version
```
helm version
```
The helm version command checks the installed version of Helm.
Displays the version of Helm installed, for example, v3.17.2, along with other build information.

 
 
 ### Create a New Helm Chart
```
helm create apache-helm
```
The helm create command generates a new Helm chart.
apache-helm is the name of the new chart.


A new directory named apache-helm is created, containing the necessary files and directories for a Helm chart, including Chart.yaml, templates, and values.yaml.


### Navigate to the Chart Directory
```
cd apache-helm
```

You are now in the apache-helm directory, where you can modify the chart files.

 ####   View Chart Structure
```
tree
```
The tree command displays the directory structure in a tree-like format.

Shows the files and directories created for the Helm chart, including:
Chart.yaml: Contains metadata about the chart.
charts: A directory for dependencies.
templates: Contains Kubernetes manifest templates.
values.yaml: Default configuration values for the chart.


#####   Edit the Service Template
```
vim templates/service.yaml
```
edit service.yaml
add this target port 
```
targetPort: {{ .Values.target.port }}
```

###   View and Edit Values File
```
cat values.yaml
```


The cat command displays the contents of the specified file.

Shows default values for the chart, including:

replicaCount: Number of replicas for the application.
image: Container image settings (repository, pull policy, tag).
service: Service settings (type, port).


Modify Values

Action: Change the following values in values.yaml:
Set replicaCount to 2.
Change image.repository to httpd.
Change image.tag to 2.4.


These changes configure the application to use the HTTPD image and set the number of replicas to 2.


###  Package the Helm Chart
```
helm package apache-helm
```

The helm package command packages the Helm chart into a .tgz file.

A file named apache-helm-0.1.0.tgz is created in the current directory.

 
 ### Install the Helm Chart
```
helm install dev-apache apache-helm
```

The helm install command deploys the Helm chart.

dev-apache is the name of the release, and apache-helm is the chart being installed.

The chart is deployed, and you see a success message indicating the release has been created.

 
 ###  Check Pods
```
kubectl get pods
```
The kubectl get pods command lists all the pods in the current namespace (default).

Displays the pods created by the dev-apache release, showing their status (e.g., Running).


###   Check Services
```
kubectl get svc
```

The kubectl get svc command lists all the services in the current namespace (default).
Shows the service created for the dev-apache release, including its type and cluster IP.
### Check Deployments
```bash

kubectl get deployment
```
The kubectl get deployment command lists all deployments in the current namespace (default).
Displays the deployment created for the dev-apache release, showing its status and number of replicas.

 
 ###  Uninstall the Helm Release
```bash

helm uninstall dev-apache

```
The helm uninstall command removes the specified release from the cluster.
The dev-apache release is uninstalled, and you see a success message.



###  Install Again with Namespace
```bash

helm install dev-apache apache-helm -n dev-apache --create-namespace
```
This command installs the Helm chart in a new namespace called dev-apache.
The -n flag specifies the namespace, and --create-namespace creates the namespace if it does not exist.
The chart is deployed in the dev-apache namespace, and you see a success message.

 
 ###  Upgrade the Release
```bash
helm upgrade prd-apache ./apache-helm -n prd-apache
```


The helm upgrade command updates an existing release with a new chart version.
prd-apache is the name of the release, and ./apache-helm specifies the chart to upgrade to.


The release is upgraded, and you see a success message indicating the upgrade was successful.

```

ubuntu@LAPTOP-7E5UNQJ4:~/k8s-production/helm$ kubectl get p
ods -n dev-apache
NAME                                      READY   STATUS    RESTARTS   AGE
dev-apache-apache-helm-58dd8c6f59-kc9g9   1/1     Running   0          2m20s
dev-apache-apache-helm-58dd8c6f59-lxjvr   1/1     Running   0          2m20s

```
```

ubuntu@LAPTOP-7E5UNQJ4:~/k8s-production/helm$ kubectl get pods -n prd-apache
NAME                                      READY   STATUS    RESTARTS   AGE
prd-apache-apache-helm-59dbfcb7cd-874xk   1/1     Running   0          56s
prd-apache-apache-helm-59dbfcb7cd-c8hnt   1/1     Running   0          56s
```

## now i want to increase replicas  

1. change the chart version
   ```
   vim apache-helm/Chart.yaml
   ```
   ```
change the app version 1.16.0 to 1.16.1
```
Open values.yaml to change replicas
```
vim apache-helm/values.yaml
```
# Change replicaCount: 2 to replicaCount: 3   

2.
 ```
    
ubuntu@LAPTOP-7E5UNQJ4:~/k8s-production/helm$  helm package
  apache-helm
Successfully packaged chart and saved it to: /home/ubuntu/k8s-production/helm/apache-helm-0.1.0.tgz

```
3.
   helm upgrade
prd-apache ./apache-helm -n prd-apache

```
```
Release "prd-apache" has been upgraded. Happy Helming!
NAME: prd-apache
LAST DEPLOYED: Tue Mar 25 17:17:35 2025
NAMESPACE: prd-apache
STATUS: deployed
REVISION: 2
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace prd-apache -l "app.kubernetes.io/name=apache-helm,app.kubernetes.io/instance=prd-apache" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace prd-apache $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace prd-apache port-forward $POD_NAME 8080:$CONTAINER_PORT
```

```
```
```
kubectl get pods -n dev-apache
```

result 
```
NAME                                      READY   STATUS    RESTARTS   AGE
dev-apache-apache-helm-58dd8c6f59-kc9g9   1/1     Running   0          10m
dev-apache-apache-helm-58dd8c6f59-lxjvr   1/1     Running
```
```
```
```
kubectl get pods -n  prd-apache
```

```
```
```
NAME                                      READY   STATUS    RESTARTS   AGE
prd-apache-apache-helm-5867645b75-bftpr   1/1     Running   0          75s
prd-apache-apache-helm-5867645b75-mc995   1/1     Running   0          78s
prd-apache-apache-helm-5867645b75-nn6m4   1/1     Running 
```

we can see in prd-apche 3 pods and in dev-apche 2 pods 



   
###  Rollback the Release
```
helm rollback prd-apache 1 -n prd-apache
```
result
```
Rollback was a success! Happy Helming!
```

The helm rollback command reverts a release to a previous revision.
prd-apache is the name of the release, and 1 specifies the revision number to roll back to.

The release is rolled back successfully, and you see a success message.
Final Verification


Check Pods in dev-apache Namespace:
```
kubectl get pods -n dev-apache

```
result :  2 pod


Check Pods in prd-apache Namespace:
```
kubectl get pods -n prd-apache
```
result:  2 pods

```

```

# Now do same for Node js app 



## Step 1: Create a Directory for the Node.js App
```bash

helm create node-js-app

```
The helm create command generates a new Helm chart.
node-js-app is the name of the new chart being created.


Result:
A new directory named node-js-app is created, containing the necessary files and directories for a Helm chart, including Chart.yaml, templates, and values.yaml.


##  Step 2: Navigate to the Node.js App Directory
```bash

cd node-js-app
```

The cd command stands for "change directory."
This command changes the current working directory to the node-js-app directory you just created.
Result:
You are now in the node-js-app directory.

##   Step 3: List the Contents of the Node.js App Directory
```bash

ls
```
The ls command lists the contents of the current directory.
Result:
Displays the files and directories in node-js-app, including Chart.yaml, charts, templates, and values.yaml.

## Step 4: View the Directory Structure
```bash

tree
```
The tree command displays the directory structure in a tree-like format.
Result:
Shows the files and directories created for the Helm chart, including:
Chart.yaml: Contains metadata about the chart.
charts: A directory for dependencies.
templates: Contains Kubernetes manifest templates.
values.yaml: Default configuration values for the chart.


## Step 5: Open the Chart.yaml File
```
vim Chart.yaml
```

Explanation:
The vim command opens the specified file in the Vim text editor.
Chart.yaml contains metadata about the Helm chart.
Result:
You can modify the chart's metadata, such as its name, version, and description.


###  Step 6: Change the Target Port in the Service Template
```
vim templates/service.yaml
```


Explanation:
This command opens the service.yaml file in the Vim text editor for editing.
Result:
You can modify the service configuration for the application, specifically the targetPort.


##  Step 7: Modify the Target Port
Action: Add the following line in the service.yaml file:
```
targetPort: {{ .Values.service.targetPort }}
```

Explanation:
This line sets the targetPort for the service to use the value defined in values.yaml.
Result:
The service will now correctly reference the targetPort defined in the values.yaml file.

##  Step 8: Open the Values.yaml File
```
vim values.yaml

```


Explanation:
This command opens the values.yaml file in the Vim text editor for editing.
Result:
You can modify default values for the chart, including the image repository, tag, and ports.

##  Step 9: Modify Values in values.yaml
Action: Change the following values in values.yaml:
```yaml

image:
  repository: lucky0111/nodejs
  pullPolicy: IfNotPresent
  tag: "latest"

service:
  port: 3000
  targetPort: 3000

```
This sets the container image to lucky0111/nodejs, the pull policy to IfNotPresent, and the ports for the service.
Result:
The application will use the specified Node.js image and ports.

##  Step 10: Package the Helm Chart
```
helm package node-js-app
```

The helm package command packages the Helm chart into a .tgz file.
Result:
A file named node-js-app-0.1.0.tgz is created in the current directory.


##  Step 11: Install the Node.js App Helm Chart
```
helm install dev-node-js-app node-js-app -n dev-node --create-namespace
```
This command installs the Helm chart in a new namespace called dev-node.
dev-node-js-app is the name of the release, and node-js-app is the chart being installed.
Result:
The application is deployed in the Kubernetes cluster, and you receive a confirmation message with deployment details.


## Step 12: Check the Status of the Pods
```
kubectl get pods -n dev-node
```


This command retrieves the list of pods in the dev-node namespace.
Result:
Displays the status of the dev-node-js-app pod, confirming it is running.


##  Step 13: Check the Status of the Services
```
kubectl get svc -n dev-node
```

This command retrieves the list of services in the dev-node namespace.
Result:
Displays the service dev-node-js-app, showing its type, cluster IP, and ports.

## Step 14: Port Forward to Access the Application
```
kubectl port-forward svc/dev-node-js-app 3000:3000 -n dev-node --address=0.0.0.0 &
```

This command forwards traffic from your local machine's port 3000 to the service's port 3000 in the Kubernetes cluster.
The --address=0.0.0.0 option allows access from any IP address.
Result:
The application is now accessible via http://localhost:3000.

##  Step 15: Access the Application in the Browser
Action: Open a web browser and navigate to http://localhost:3000.
Explanation:
This allows you to view the Node.js application running in your Kubernetes cluster.
Result:
You can see your Node.js app running successfully.

![Screenshot 2025-03-25 182233](https://github.com/user-attachments/assets/a708af7a-a307-4130-9226-5f8b12d4cc21)






# Deploying MongoDB Using Helm
This document explains how to deploy MongoDB using Helm, a package manager for Kubernetes, and how to manage the deployment effectively.

## Step 1: Install MongoDB Helm Chart
```bash

helm install mongodb-helm oci://registry-1.docker.io/bitnamicharts/mongodb -n mongo --create-namespace
```
helm install: This command is used to install a Helm chart.
mongodb-helm: This is the name you are giving to the release of the MongoDB chart.
oci://registry-1.docker.io/bitnamicharts/mongodb: This specifies the location of the MongoDB chart in an OCI-compliant registry (Docker Hub in this case).
-n mongo: This flag specifies the namespace where the MongoDB release will be deployed. If the namespace does not exist, it will be created due to the --create-namespace flag.
Result:
The output shows:
```
Pulled: registry-1.docker.io/bitnamicharts/mongodb:16.4.10
Digest: sha256:c0be02e3e5fc22ae85b9ec6d9855c98613837c7d1e2c0c8186ab5fe0cfdbb7d8
NAME: mongodb-helm
LAST DEPLOYED: Tue Mar 25 19:22:31 2025
NAMESPACE: mongo
STATUS: deployed
REVISION: 1
```
This indicates that the MongoDB chart has been successfully pulled and deployed in the mongo namespace.
Use Case:
Deploying MongoDB in a Kubernetes environment allows for scalable and manageable database solutions. MongoDB is a popular NoSQL database used for various applications.
## Step 2: Deployment Notes
Output Notes:
The output provides important information about the deployment:
```
CHART NAME: mongodb
CHART VERSION: 16.4.10
APP VERSION: 8.0.6
```
It also includes instructions on how to access the MongoDB service:

MongoDB can be accessed on the following DNS name(s) and ports from within your cluster:
mongodb-helm.mongo.svc.cluster.local

Accessing MongoDB:
To get the root password, run:
 ```
export MONGODB_ROOT_PASSWORD=$(kubectl get secret --namespace mongo mongodb-helm -o jsonpath="{.data.mongodb-root-password}" | base64 -d)
```

This command retrieves the root password for MongoDB from the Kubernetes secret created during the installation.
Connecting to MongoDB:
To connect to your database, create a MongoDB client container:
```
kubectl run --namespace mongo mongodb-helm-client --rm --tty -i --restart='Never' --env="MONGODB_ROOT_PASSWORD=$MONGODB_ROOT_PASSWORD" --image docker.io/bitnami/mongodb:8.0.6-debian-12-r0 --command -- bash
```

This command runs a temporary MongoDB client pod that allows you to connect to the MongoDB instance.
Result:
You can then run the following command inside the client pod to connect to MongoDB:
```
mongosh admin --host "mongodb-helm" --authenticationDatabase admin -u $MONGODB_ROOT_USER -p $MONGODB_ROOT_PASSWORD
```

Use Case:
These commands are useful for accessing and managing your MongoDB database directly from the Kubernetes environment.
## Step 3: Check Pod Status
```
kubectl get pods -n mongo
```

Explanation:
This command checks the status of the pods in the mongo namespace.
Result:
Initially, the pod is in the Init:0/1 state, indicating it is initializing. After a few seconds, the pod transitions to Running, indicating that the MongoDB server is up and running.
Use Case:
Monitoring the status of your application to ensure it is running correctly.
## Step 4: Execute Commands Inside the MongoDB Pod
```
kubectl exec -it mongodb-helm-6675c4b99d-4jdcp -n mongo -- bash
```

Explanation:
This command allows you to execute a command inside the specified pod (mongodb-helm-6675c4b99d-4jdcp) in the mongo namespace.
The -it flags allow you to interact with the pod's terminal.
Result:
You enter the shell of the MongoDB pod, where you can run commands.
Example Command:
```
mongosh
```

This command starts the MongoDB shell, allowing you to interact with the MongoDB database.
Use Case:
This is useful for performing database operations, running queries, and managing your MongoDB instance directly.

## Step 5: List Helm Repositories
```
helm repo list
```

Explanation:
This command lists all the Helm repositories that you have configured in your Helm client.
Result:
The output shows:
```
NAME    URL
bitnami https://charts.bitnami.com/bitnami
```
This indicates that the Bitnami repository is configured and available for use.
## Step 6: List Helm Releases
```
helm list
```
Explanation:
This command lists all the Helm releases in the default namespace.
Result:
The output shows:
```
NAME    NAMESPACE       REVISION        UPDATED                                    STATUS          CHART              APP VERSION
```
If you specify the namespace:
```
helm list -n mongo
```

Result:
You will see the release details for mongodb-helm in the mongo namespace, including its status and version.
## Step 7: Uninstall the MongoDB Release
```
helm uninstall mongodb-helm -n mongo
```

Explanation:
This command uninstalls the release named mongodb-helm from your Kubernetes cluster.
Result:
The message release "mongodb-helm" uninstalled confirms that the release has been successfully removed.
Use Case:
This is useful for cleaning up resources when you no longer need the MongoDB application or want to redeploy it with different configurations.

