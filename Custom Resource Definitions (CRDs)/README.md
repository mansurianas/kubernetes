# Custom Resource Definitions (CRDs) in Kubernetes

## What is a CRD?

A Custom Resource Definition (CRD) is a way to define a new resource type in Kubernetes. Once you create a CRD, you can use the Kubernetes API to manage instances of that resource type, just like you would with built-in resources such as Pods, Services, and Deployments.

## Why Use CRDs?

1. **Extensibility**: 
   - CRDs allow you to add your own resource types to Kubernetes, enabling you to tailor the platform to your specific needs.

2. **API Integration**: 
   - You can interact with your custom resources using the Kubernetes API, allowing for seamless integration with existing Kubernetes tools and workflows.

3. **Declarative Configuration**: 
   - You can manage your resources using YAML files, just like built-in resources. This makes it easy to define, deploy, and manage your custom resources in a consistent manner.
  






#### Creating a New Directory for CRDs
```
mkdir crd
```

 Navigating to the CRD Directory
```
cd crd
```

#####  Creating a Custom Resource Definition (CRD)
```
vim bca-crd.yml

```

```yml
kind: CustomResourceDefinition  # Specifies the type of Kubernetes resource being defined
apiVersion: apiextensions.k8s.io/v1  # API version for the CustomResourceDefinition
metadata:  # Metadata about the CRD
    name: bcabatches.ait.com  # Unique name for the CRD, following the format <plural>.<group>
spec:  # Specification of the CRD
  group: ait.com  # The API group for the custom resource
  names:  # Names for the custom resource
     plural: bcabatches  # Plural name used in URLs
     singular: bcabatch  # Singular name used for individual resources
     kind: BcaBatch  # The kind of the custom resource
     shortNames:  # Short names for the custom resource
       - batches  # Short name for the plural form
       - ait  # Another short name for the custom resource
  scope: Namespaced  # Indicates that the custom resource is namespaced
  versions:  # Versions of the custom resource
    - name: v1  # Version name
      served: true  # Indicates that this version is served by the API
      storage: true  # Indicates that this version is the storage version
      schema:  # Schema definition for the custom resource
        openAPIV3Schema:  # OpenAPI v3 schema definition
          type: object  # The type of the resource is an object
          properties:  # Properties of the custom resource
            spec:  # The spec field contains the desired state of the resource
              type: object  # The type of the spec field is an object
              properties:  # Properties of the spec object
                name:  # Name property
                  type: string  # The type of the name property is a string
                  description: "this is the name of the bca batch"  # Description of the name property
                duration:  # Duration property
                   type: string  # The type of the duration property is a string
                   description: "this is duration of bca batch"  # Description of the duration property
                mode:  # Mode property
                  type: string  # The type of the mode property is a string
                  description: "this is mode of batch eg. live/recorded"  # Description of the mode property
                platform:  # Platform property
                  type: string  # The type of the platform property is a string
                  description: "this is platform of batch"  # Description of the platform property




```




Applying the CRD
```
kubectl apply -f bca-crd.yml
```

without comment
```yml
kind:  CustomResourceDefinition
apiVeresion: apiextensions.k8s.io/v1
metadata:
    name:  bcabatches.ait.com
spec:
  group: ait.com
  names:
     plural: bcabatches
     singular: bcabatch
     kind: BcaBatch
     shortNames:
       - batches
       - ait
  scope: Namespaced
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                name:
                  type: string
                  description: "this is the name of the bca batch"
                duration:
                   type: string
                   description: "this is duration of bca batch"
                mode:
                  type:  string
                  description: "this is mode of batch eg. live/recorded"
                platform:
                  type: string
                  description: "this is platform of batch"


```






```
kubectl apply -f bca-crd.yml

```



customresourcedefinition.apiextensions.k8s.io/bcabatches.ait.com created

#####    Listing All CRDs
```
kubectl get crd
```
```
Result:


NAME                                                  CREATED AT
bcabatches.ait.com                                    2025-03-25T07:45:01Z
verticalpodautoscalercheckpoints.autoscaling.k8s.io   2025-03-19T02:26:55Z
verticalpodautoscalers.autoscaling.k8s.io             2025-03-19T02:26:55Z
```


###  Creating a Custom Resource Instance

vim bca-cr.yml
```yml
apiVersion: ait.com/v1
kind: BcaBatch
metadata:
   name: batch-2025
spec:
   name: batch bca 2025
   duration : 3 year from 20222
   platform: ait campus
   mode: live
```

Applying the Custom Resource Instance
```
kubectl apply -f bca-cr.yml
```
Result:
```
bcabatch.ait.com/batch-2025 created
```

 Listing All Instances of the Custom Resource
```
kubectl get bcabatches
```
```
Result:

NAME         AGE
batch-2025   17s
```

 Copying the Custom Resource Instance File
```
cp bca-cr.yml bca-cr2.yml

```
This command creates a copy of the custom resource instance file for further modifications.

 Modifying and Applying the Second Custom Resource Instance
```
vim bca-cr2.yml
```

```
kubectl apply -f bca-cr2.yml
```
```

Result:

bcabatch.ait.com/batch-2026 created
```
After modifying the second instance file, you create another custom resource instance named batch-2026.

 Listing All Instances Again
```
kubectl get bcabatches
```
```

Result:


NAME         AGE
batch-2025   2m6s
batch-2026   4s
```


 Describing the First Custom Resource Instance

```
kubectl describe bcabatch/batch-2025
```

Result:

```
Name:         batch-2025
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  ait.com/v1
Kind:         BcaBatch
Metadata:
  Creation Timestamp:  2025-03-25T07:51:29Z
  Generation:          1
  Resource Version:    186986
  UID:                 85a0e030-3d04-4310-8e34-b1c64e193bec
Spec:
  Duration:  3 year from 20222
  Mode:      live
  Name:      batch bca 2025
  Platform:  ait campus
Events:      <none>

```

 Describing the Second Custom Resource Instance
```
kubectl describe bcabatch/batch-2026
```

Result:
```

Name:         batch-2026
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  ait.com/v1
Kind:         BcaBatch
Metadata:
  Creation Timestamp:  2025-03-25T07:53:31Z
  Generation:          1
  Resource Version:    187202
  UID:                 43bb662a -1dab-4d22-8c8a-7f5d91fbb2a9
Spec:
  Duration:  3 year from 2023
  Mode:      live
  Name:      batch bca 2026
  Platform:  ait campus
Events:      <none>
```
