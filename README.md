# kubernetes-mongodb-mongo-express

Deploy mongo and mongo express into Kubernetes and basic understanding of Kubernetes

### What is kubernetes ?

> Kubernetes is open-source-container-orchestration system for automatating computer application deployment, scaling and management.

### What are the important key componenets in Kubernetes?

- Cluster:
  > A Kubernetes cluster is a set of nodes that run containerized applications. Containerized applications packages an app with its dependences
  > and some necessary services.
  > Each cluster also has a master (control plane) that manages the nodes and pods (more on pods below) of the cluster
  > Kubernetes clustes allow containers to run across multiple machines and environments: _virtual, phyical, cloud-based, and on-premises._
- Node: 
  > A Node is single machine in a cluster. A Node is a worker machine in Kubernetes and may be either a virtual or a physical machine, depending on the cluster. Each Node is managed by the Master.
- Pod: 
  > Pods are the smallest, most basic deployable objects in Kubernetes. A Pod represents a single instance of a running process in your cluster. Pods contain one or more containers, such as Docker containers. When a Pod runs multiple containers, the containers are managed as a single entity and share the Pod's resources.
- MasterNode:
  > Master Node controls the state of the cluster. The master node is the origin for all task assignments.
  It coordinate processes such as scheduling, scaling, cluster state, updates
  - kube-apiserver:
    > Exposes a REST interface to all Kubernetes resources. Serves as the front end of the Kubernetes control plane. like UI, CLI etc
    > It is a kind of cluster gateway. Client intract with API server to Update/Query node. It acts as a gatekeepr for authentication.
    ```commandline
    Some Request -> API Server -> validates Request -> other processes.. -> Pod (Docker)
    ```          
  - controller-manager:
    > Detects cluster state changes, like pods are down so it will schedule for new pods
    ```commandline
    controller-Manager -> Scheduler -> Kubelete.
    ```                          
  - kube-scheduler:
    > scheduler has all the information about all nodes, it based on requirement or resources need, scheduler decide to which node need to install.
    ```commandline
    Some Request -> API Server -> validates Request -> scheduler -> Pod (Scheduler decide where to put the pods)
    ```    
  - ETCD Server:
    > It's kind of Cluster brain where value store into Key Values. Cluster changes get stored in the key value store, like how many nodes,pods, CPU size, max
    > capacity etc. It's important because Controller-Manager, Scheduler sue the ETCD data to perform operation. Like what resources are available and did the cluster
    > state change.
- WorkerNode

  - _Kubectl_: 
  > Interacts with both - the runtime container and node. It give instruction to continer runtime to pull image from docker and run into pod.
  - _kube-proxy_: 
  > Node to Node communication. Manages network connectivity and maintains network rules across nodes. Implements the Kubernetes Service concept across every node in a given cluster.
  - _RunTime container like docker/rkt_: 
  > Container runtime need to install very node,




```commandline
kubectl get pods -n kube-system
NAME READY STATUS RESTARTS AGE
etcd-minikube 1/1 Running 5 223d
kube-apiserver-minikube 1/1 Running 5 223d
kube-controller-manager-minikube 1/1 Running 5 223d
kube-proxy-XXXX 1/1 Running 5 223d
kube-scheduler-minikube 1/1 Running 5 223d
```
Some Request -> API Server -> validates Request -> other processes.. -> Pod (Docker)

How to start minikube ?

> minikube start

How to delete pods ?
```commandline
kubectl delete pods <pod> --grace-period=0 --force
```

how to delete deployment
```commandline
kubectl delete deployment <name-of-deployment>
```
Exposed to external IP
```commandline
 minikube service mongo-express-service
 |-----------|-----------------------|-------------|---------------------------|
 | NAMESPACE | NAME | TARGET PORT | URL |
 |-----------|-----------------------|-------------|---------------------------|
 | default | mongo-express-service | 8081 | http://<ip-address>:30000 |
 |-----------|-----------------------|-------------|---------------------------|
```
kubectl apply commands in order
```commandline
kubectl apply -f mongo-secret.yaml
kubectl apply -f mongo-deployment.yaml
kubectl apply -f mongo-service.yaml
kubectl apply -f mongo-configmap.yaml 
kubectl apply -f mongo-express-deployment.yaml
kubectl apply -f mongo-service.yaml
```
kubectl get commands
```commandline
kubectl get pod
NAME                                 READY   STATUS    RESTARTS   AGE
mongo-deveployment-xxxxxx-xxxxx   1/1     Running   0          3h2m
mongo-express-xxxx-xxxx           1/1     Running   0          158m

kubectl get pod --watch
kubectl get pod -o wide # Give the Internal IP address associated with Pod
kubectl get service
kubectl get secret
kubectl get all | grep mongodb

```

Kubectl debugging commands
```commandline
kubectl describe pod mongodb-deployment-xxxxxx
kubectl describe service mongodb-service
kubectl logs mongo-express-xxxxxx
```
URL to external service in minikube
```commandline
minikube service mongo-express-service
```
Mongo express running on kubernetes 

![Optional Text](image/OAuth-Flow.png)

![Optional Text](./image/mongo-express.png)