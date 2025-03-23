

# Kubernetes RBAC (Role-Based Access Control)

RBAC (Role-Based Access Control) is a method for regulating access to resources in a Kubernetes cluster. It allows you to define roles and permissions for users, groups, or service accounts, ensuring that only authorized entities can perform specific actions.

## Key Components

### 1. Service Accounts
- **Definition**: Special accounts for applications running in pods.
- **Purpose**: Enable pods to interact with the Kubernetes API.

### 2. User Accounts
- **Definition**: Accounts for human users needing access to the cluster.
- **Purpose**: Allow users to perform actions on cluster resources.

### 3. Roles
- **Definition**: Define a set of permissions within a specific namespace.
- **Example**: A role can allow a user to read and write pods in the "development" namespace.

### 4. Cluster Roles
- **Definition**: Similar to roles but apply across the entire cluster.
- **Purpose**: Useful for managing permissions for cluster-wide resources.

### 5. Role Binding
- **Definition**: Binds a role to a user or service account within a specific namespace.
- **Purpose**: Grants permissions defined in that role.

### 6. Cluster Role Binding
- **Definition**: Binds a cluster role to a user or service account.
- **Purpose**: Grants permissions across the entire cluster.




#### example
```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: my-namespace
  name: multi-group-role
rules:
- apiGroups: [""]  # Core API group
  resources: ["pods", "services"]
  verbs: ["get", "list", "create", "update", "delete"]

- apiGroups: ["apps"]  # Apps API group
  resources: ["deployments", "statefulsets"]
  verbs: ["get", "list", "create", "update", "delete"]

- apiGroups: ["rbac.authorization.k8s.io"]  # RBAC API group
  resources: ["roles", "rolebindings"]
  verbs: ["get", "list", "create", "update", "delete"]

- apiGroups: ["batch"]  # Batch API group
  resources: ["jobs", "cronjobs"]
  verbs: ["get", "list", "create", "update", "delete"]



```
```
kubectl auth whoami
```

Outcome:
Username is kubernetes-admin.
This command shows the current user context. In this case, you are logged in as kubernetes-admin, which typically has cluster-admin privileges.


```
kubectl auth can-i get pods
```
Outcome: Yes.

The kubernetes-admin user has permission to get pods across all namespaces.




```
kubectl apply -f namespace.yml
```
```
kubectl auth can-i get pods -n apache
```
Outcome: Yes.
 The kubernetes-admin user can still get pods in the newly created apache namespace.



Create Role

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: apache-manager
  namespace: apache
rules:
  - apiGroups: ["","apps","rbac.authorization.k8s.io","batch"]
    resources: ["deployments","pods","services"]
    verbs: ["get","apply","delete","watch","create","patch"]


```


Create Service Account

```yaml
kind: ServiceAccount
apiVersion: v1
metadata:
   name: apache-user
   namespace: apache
```


Service account apache-user created.
 This creates a service account that can be used by pods to interact with the Kubernetes API.
 
Verify ServiceAccounts
Check ServiceAccounts:
```
kubectl get serviceaccounts -n apache
```



## Verify Roles and RoleBinding
To cross-verify the RBAC setup, you can perform a series of checks and actions to ensure that the roles and permissions are applied correctly. Here’s how you can do it:

Check Roles:
```
kubectl get roles -n apache
```


Check RoleBindings:
```
kubectl get rolebindings -n apache
```


######################  Check Permissions as Service Account

```bash
kubectl auth can-i get pods -n apache --as=apache-user

```
 No.
 The service account apache-user does not have any permissions by default. Therefore, it cannot get pods.




 
###   Create Role Binding

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: apache-manager-rolebinding
  namespace: apache
subjects:
- kind: User
  name: apache-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: apache-manager
  apiGroup: rbac.authorization.k8s.io


```



Outcome: Role binding created.
This binds the apache-manager role to the apache-user service account, granting it the permissions defined in the role.
Check Permissions Again as Service Account



```bash
kubectl auth can-i get pods -n apache --as=apache-user

```
Outcome: Yes.

 After the role binding, the apache-user service account now has the permissions to get pods in the apache namespace.




Check Permissions for Other Resources

Get HPA:

```bash
kubectl auth can-i get hpa --as=apache-user -n apache
```
Outcome: No.
 The apache-manager role does not include permissions for Horizontal Pod Autoscalers (HPA), so the service account cannot access them.


 
Get Services:

```bash
kubectl auth can-i get svc --as=apache-user -n apache
```
Outcome: Yes.
 The role includes permissions for services, so the service account can access them.\

 
Delete Pods:

```bash
kubectl auth can-i delete pods --as=apache-user -n apache
```
Outcome: Yes.
 The role allows the service account to delete pods, hence the permission check returns yes.


 
List Pods:

```bash
kubectl auth can-i list pods --as=apache-user -n apache

```

Outcome: No.
 The role does not explicitly allow listing pods, which is a different permission from getting them. The service account cannot list pods.

 
##### Summary of Permissions


Yes: Indicates that the user or service account has the required permissions to perform the action.
No: Indicates that the user or service account does not have the required permissions, either due to lack of role binding or insufficient permissions defined in the role.




