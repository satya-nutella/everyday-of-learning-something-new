## Pods

- k8s allows us to deploy containerized applications across a set of machines configured as worker nodes inside a cluster.
- We don't directly deploy containers to k8s
- Containers are encapsulated in a k8s object called a Pod
- Pod => single instance of an application
- Pod => smallest object we can create in k8s
- Applications are deployed to k8s as containers contained within a pod
- Scaling:  
   ❌ deploy another container inside the same pod
   ✅ deploy application container inside a new pod

   If more scale is required, we can also increase the # of workers

## Pods and Containers

- Pods usually have 1-1 relationship with the containers
- To scale up, we deploy another pod
  To scale down, we delete a pod containing a container

## Multi-container Pods

- rare to have multi-container pods
- scenario => helper container does supporting task for the application container
- helper + application container share the same fate
- helper + application share the same network space (can talk over localhost) and can also share the same storage space

## Why Pods?

Consider how the same could be achieved with only Docker:

- call `docker run` multiple times
- establish network connectivity between containers
- if helper containers, we need to link those to application containers manually
- maintain a map for shared network, volumes and also for application => helper
- manually observe application state. If app dies, helper should also be killed

Kubernetes does all this for you and a Pod is a healthy abstraction over this "map". Application is equipped for architectural changes and scale!

## Deploying a Pod

```
kubectl run busybox --image busybox
```

1. k8s pulls the image specified from a repository (default: Dockerhub)
2. k8s creates a Pod and deploys the pulled image as container within that Pod

Get all pods:

```
kubectl get pods
```

## YAML in K8S

In k8s, definition YAML files are used as inputs to create k8s objects (Pods, Deployments, ReplicaSets, etc). The general syntax of a k8s definition file is:


| Kind       | Version |
| ---------- | ------- |
| Pod        | v1      |
| Service    | v1      |
| ReplicaSet | apps/v1 |
| Deployment | apps/v1 |

```yaml
apiVersion: # api version used to create an object. eg. v1 / apps/v1
kind: # k8s object to create. eg. Pods, ReplicaSet, Service, Deployment, etc
metadata: # should container specific fields understandable by k8s
  name: # name of the object
  labels: # set of key value pairs for filtering/identification. This can have any key-value pairs as you need
spec: # actual object configuration. Varies as per the object
```

## Pods as YAML

```yaml
# pod-definition.yaml

apiVersion: v1
kind: Pod
metadata:
  name: mongodb-pod
  labels:
    app: mongodb
    type: database
 spec:
   containers: # List of containers within the Pod. Usually, we will only have one unless a multi-container pod
     - name: mongodb-container # Name of the container
       image: mongo # Container image
```

To deploy the Pod, run:

```
kubectl create -f pod-definition.yaml
```

To describe the pod once deployed inside a cluster:

```
kubectl describe pod mongodb-pod
```
