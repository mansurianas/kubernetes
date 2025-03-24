## kubernetes dashboard

Step 1: Navigating to the Kubernetes Production Directory
You started by navigating to the k8s-production directory where you have your Kubernetes configuration files.

```bash
cd k8s-production

```

Step 2: Creating a Dashboard Directory
You created a new directory named dashboard to store the configuration files related to the Kubernetes Dashboard.

```
mkdir dashboard
```

Step 3: Deploying the Kubernetes Dashboard
You applied the recommended YAML configuration for the Kubernetes Dashboard from the official GitHub repository. This command creates various resources necessary for the Dashboard to function.

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

```



Step 4: Creating an Admin User
You created a YAML file named dashboard-admin-user.yml to define a ServiceAccount and a ClusterRoleBinding for an admin user. This allows you to access the Dashboard with admin privileges.

The content of dashboard-admin-user.yml should look like this:

```yaml
kind: ServiceAccount
apiVersion: v1
metadata:
  name: admin-user
  namespace: kubernetes-dashboard

---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user-binding
  namespace: kubernetes-dashboard
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin

```


You applied this configuration to create the ServiceAccount and ClusterRoleBinding.

```
kubectl apply -f dashboard-admin-user.yml
```

Step 5: Generating a Token for the Admin User
You generated a token for the admin-user ServiceAccount, which you will use to log in to the Kubernetes Dashboard.

```
kubectl -n kubernetes-dashboard create token admin-user
```
Step 6: Navigating to the Nginx Directory
You navigated to the nginx directory to deploy Nginx-related resources.

```
cd nginx
```

Step 7: Creating a Namespace for Nginx
You applied the namespace.yml file to create a namespace for Nginx.

```
kubectl apply -f namespace.yml
```

Step 8: Deploying Persistent Volume and Claim
You applied the persistentVolume.yml and persistentVolumeClaim.yml files to create a persistent volume and a claim for Nginx.
```
kubectl apply -f persistentVolume.yml
kubectl apply -f persistentVolumeClaim.yml


kubectl apply -f pod.yml

```


Step 9: Deploying Nginx Deployment, CronJob, DaemonSet, and ReplicaSet
You created various resources for Nginx, including a Deployment, CronJob, DaemonSet, and ReplicaSet by applying their respective YAML files.

```
kubectl apply -f deployment.yml
kubectl apply -f cron-job.yml
kubectl apply -f daemonsets.yml
kubectl apply -f replicaset.yml

```

Step 10: Starting the Kubernetes Proxy


You started the Kubernetes proxy to access the Dashboard through your browser.

```
kubectl proxy --address=0.0.0.0  & 
```
Step 11: Accessing the Dashboard
You opened the Dashboard in your browser using the provided URL and logged in using the token generated earlier.

```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

```

![Screenshot 2025-03-24 102510](https://github.com/user-attachments/assets/af1ccde5-93fa-4c3c-a31e-8ec7997a84ca)

kubernetes dashboard 
![Screenshot 2025-03-24 111150](https://github.com/user-attachments/assets/1ee8b0c5-f333-4d52-89b4-ea88343303c6)

