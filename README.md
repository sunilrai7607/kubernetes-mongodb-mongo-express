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
  > is responsible for implementing a form of virtual IP for Services of type other than ExternalName
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

What is Service in kubernetes ?
> An abstract way to expose an application running on a set of Pods as a network service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP/NodePort/LoadBalancer/ExternalName
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
      # Optional field - LoadBalancer
      # By default and for convenience, the Kubernetes control plane will allocate a port from a range (default: 30000-32767)
      nodePort: 30007
```
```markdown
Note: A Service can map any incoming port to a targetPort. By default and for convenience, the targetPort is set to the same value as the port field.
```
### Kubernetes ServiceTypes:
Kubernetes ServiceTypes allow you to specify what kind of Service you want. The default is ClusterIP.
- **ClusterIP:** 
    > Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default ServiceType.
- **NodePort:** 
    > Exposes the Service on each Node's IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You'll be able to contact the NodePort Service, from outside the cluster, 
      by requesting _"NodeIP:NodePort"_      
- **LoadBalancer:** 
    > Exposes the Service externally using a cloud provider's load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.
- **ExternalName:** 
    > Maps the Service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record with its value. No proxying of any kind is set up.

### Kubernetes Ingress vs OpenShift Route
Although pods and services have their own IP addresses on Kubernetes, these IP addresses are only reachable within the Kubernetes cluster and not accessible to the outside clients. The Ingress object in Kubernetes, although still in beta, is designed to signal the Kubernetes platform that a certain service needs to be accessible to the outside world and it contains the configuration needed such as an externally-reachable URL, SSL, and more.

Creating an ingress object should not have any effects on its own and requires an ingress controller on the Kubernetes platform in order to fulfill the configurations defined by the ingress object.

Here at Red Hat, we saw the need for enabling external access to services before the introduction of ingress objects in Kubernetes, and created a concept called Route for the same purpose (with additional capabilities such as splitting traffic between multiple backends, sticky sessions, etc). Red Hat is one of the top contributors to the Kubernetes community and contributed the design principles behind Routes to the community which heavily influenced the Ingress design.

When a Route object is created on OpenShift, it gets picked up by the built-in HAProxy load balancer in order to expose the requested service and make it externally available with the given configuration. It’s worth mentioning that although OpenShift provides this HAProxy-based built-in load-balancer, it has a pluggable architecture that allows admins to replace it with NGINX (and NGINX Plus) or external load-balancers like F5 BIG-IP.              