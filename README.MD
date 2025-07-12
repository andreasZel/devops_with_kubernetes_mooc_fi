# Exercises Τable of Contents

# Personal Notes

## Microservices

Microservices are small, autonomous services that work together.

The top 3 reasons for using microservices are:

1. `Zero-downtime independent deployability`. If we have multiple small micoservices that each have a seperate functionality, we can easily just **update only this individual microservice** rather that **the whole application**. This **reduces the dowtime, or eliminates the downtime** of the application, because only the specific microservice needs to be redeployed.

2. `Isolation of data and of processing around that data`. Each microservice manages its own data and business logic. There is no shared database; each service owns its data and is responsible for its consistency and behavior.

   - So we have **increased security** as data is used only where is needed
   - We **avoid tight coupling** because there is no dependencies between the services
   - We **can scale up** easily because we don't need to redeploy the whole application

3. `Use microservices to reflect the organizational structure`. Basically it can help an organisation to **maintain** and **structure** different apps, because it can assign teams to handle different microservices. When the **Organisation scales**, it is easy to assign and distribute workload on new members by assigning them new or existing microservice.

## Kubernetes

Kubernetes is greek for ""**Κυβερνήτης**"". It basically takes care of **orchestrating/distributing different processes on different machines**.

If we didn't have it, all of that would be done manually. That means we have to

1.  decide the most optimal distribution of the processes
2.  decide if they live in VMs or actual machines
3.  update the whole setup if we scale up with a new process

More officially:

""`In essence, Kubernetes is the sum of all the bash scripts and best practices that most system administrators would cobble together over time, presented as a single system behind a declarative set of APIs.`"

— Kelsey Hightower

### How kubernetes operates

First of all we have to move from a **"Monolith"** or a **single process model application** to one using **microservices**

Kubernetes then uses three main abstract terms:

1. **POD**: a block that can contain one or more containers, kubernetes sees the POD, not the containers themselves ![PODS](./assets/PODS.png)Pods are like containers of containers. It is used so that containers can share **Network** and **Storage**.  It's very much like how you have used containers to define environments for a single process. So when a pod is deleted, the containers will stop running and the data will be lost. 
2. **NODE**: a group of **PODS** ![NODES](./assets/NODES.png)
3. **CLUSTER**: a group machines, in case of kubernetes, **NODES** that work together, overseen by a _MASTER NODE_ ![CLUSTERS](./assets/CLUSTERS.png)

We put in place those clusters using a **.yml** file that is called **DEPLOYMENT**.

Then from our **container registry**, kubernetes pulls the containers and propagates them in each pod automatically based on **optimisation** of resources and **avoidance of single failure point**.

This makes **scalability** and **maintenance** easier.

![OPTIMISATION](./assets/OPTIMISATION.png)
![MAINTENANCE](./assets/MAINTENANCE.png)

### Using Kubernetes

We can use a lightweight Kubernetes distribution called **K3s**.

To create clusters for it we use **Docker** and mainly a tool called **K3D**.

The reason for using k3d is that it enables us to create a cluster without worrying about virtual machines or physical machines.

** This \_requires installing **kubectl** a commandline tool\_ **.

## Using K3D

A cluster is a group of machines, nodes, that work together - in this case, they are part of a Kubernetes cluster.

Kubernetes cluster can be of any size - a single node cluster would consist of one machine that hosts the Kubernetes **control-plane (exposing API and maintaining the cluster)** and that cluster can then be expanded with up to 5000 nodes total, as of Kubernetes v1.18.

So a node can be:

1. **"server node"**, nodes **with control-plane**
2. **"agent node"**, nodes **without** that role.

To create a cluser we use:

```bash
k3d cluster create -a <<number of nodes>>
```

with `docker ps`, we can see that **two agent nodes** are created with **one server node as well**.

We also see that port **6443** is opened to **"k3d-k3s-default-serverlb"**, a useful "load balancer" proxy, that'll **redirect a connection to 6443 into the server node**, and that's how we can access the contents of the cluster.

The port on our machine that is mapped to 6443 is randomly choosen. We could opted out of the load balancer with `k3d cluster create -a 2 --no-lb`.

### kubeconfig

A `kubeconfig` file will be created, this is used to organize information about clusters, users, namespaces, and authentication mechanisms.

To access it we use:

```bash
k3d kubeconfig get k3s-default
```

we can use `kubectl` to modify the contents like:

```bash
kubectl config use-context k3d-k3s-default
```

In general `kubectl` will read **kubeconfig** from the location in **KUBECONFIG** environment value or by default from **~/.kube/config** and use the information to connect to the cluster.

we can acess the cluster with:

```bash
kubectl cluster-info
```

to **start/stop** we use 

```bash
k3d cluster stop #stop cluster
k3d cluster start #start cluster
```

and to delete `k3d cluster delete`.

## Deployment

By default kubernetes uses `Registries` such as Dockerhub to have access to our images.

We can use local images with `k3d image import <image-name>` and alter `deploymen`'s **imagePullPolicy** from **Always** to **IfNotPresent** or **Never**. The deployment can be edited after creation with `kubectl edit deployment <deployment-name>`.

If we do want to deploy from a Repo we can use:

### Create Deployment
```bash
kubectl create deployment <deployment-name> --image=<user/image-name>
```

This action created a few things for us to look at

- a deployment resource
- a pod resource

In Kubernetes, all entities that exist are called **objects**. We can list all objects of a resource with `kubectl get RESOURCE`.

so for pods:

```bash 
kubectl get pods
```

A `deployment` resource takes care of the deployment. It's a way to tell Kubernetes what container you want, how they should be running, and how many of them should be running.

### Delete Deployment

to delete it we use:

```bash
kubectl delete deployment <deployment-name>
```

### ReplicaSets

When we create a deployment a `ReplicaSet` object is created, it is used to tell how many replicas of a Pod we want, so It will delete or create Pods until the number of Pods we want is running. 

ReplicaSets are managed by Deployments, there is need to define or modify them manually. If we want to manage the number of replicas, we can edit the Deployment and it will take care of modifying the ReplicaSet.

to view them we do as we did with pods:

```bash
kubectl get deployments
```

to see the output we use:

```bash
kubectl logs -f <pod-name>
```

A helpful list for other commands from docker-cli translated to kubectl is available here https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/

## Configuration: update image and scale up

There are two methods to make changes to our deployment.

1. **Imperative: Execute Commands**:
  we can always
   - scale up with:
     ```bash
      kubectl scale deployment/<deployment-name> --replicas=<number-of-replicas>
     ```
   - alter image with:
     ```bash
      kubectl set image deployment/<deployment-name> <container-name>=<image>
     ```
2. **Declarative: configuration with YAML**:
  we can make a .yml file to define how things should run, rather than change. 
  
  To do that we need:
  
  1. To create a `manifest` directory
  2. Create a .yml file
     An example could be the following:

   ```kubernetes
   apiVersion: apps/v1
   kind: Deployment
   metadata:
      name: hashgenerator-dep
   spec:
      replicas: 1
      selector:
         matchLabels:
            app: hashgenerator
      template:
         metadata:
            labels:
               app: hashgenerator
         spec:
            containers:
            - name: hashgenerator
              image: jakousa/dwk-app1:b7fc18de2376da80ff0cfc72cf581a9f94d10e64
   ```
   this tells three main things:

   1. **kind: Deployment** declares what kind, or type the created object has
   2. **name: hashgenerator-dep** gives a name as metadata
   3. **replicas: 1** declaring that there should be one pod to run the containers
   4. **container** that is from, is named
   `hashgenerator` with image `dwk-app1:b7fc18de2376da80ff0cfc72cf581a9f94d10e64`

   ### Apply
   to use it we have to `apply`:

   ```bash
   kubectl apply -f manifests/<yml file>
   ```

   ### Delete

   To delete it we use:

   ```bash
   kubectl delete -f manifests/<yml file>
   ```
   
   #### Versioning rather deleting
   
   ** **Instead of deleting it we can use a manifest from the interner and update it when we want to** **

   to do so, we just update the tag in the deployments. Kubernetes will see to update it when we tell it. 

   so a typical workflow would be:

   ```bash
   # build and push image to dockerhub
   docker build -t <image>:<new_tag>
   docker push <image-name>:tag

   # update the deployment, by altering image tag in .yml and then
   kubectl apply -f manifests/deployment.yaml
   ```

   ### Use same updated image tag

   If we want to use the same image tag because we updated it, instead of reapplying the yml (we cant update the image of deloyment because it has same tag) we can use:

   ```bash
   kubectl rollout restart deployment <deployment-name>
   ``` 

   ## Anti-patterns

   1. We should generally avoid deleting resources, so that kubernetes knows what and when to update.
   2. It's better to be declerative rather that imperative in our approaches, so .yml files are preffered. 

   ## Debugging

   To debug there are several options:

   A common bug i found using **k3d** is `outdated docker`. 
   
   Also, if errors persist with k3d cluster network, try creating with `localhost binding` to avoid host.docker.internal issues with:

   ```bash
   k3d cluster create k3s-default --api-port 127.0.0.1:6443
   ```

   In general tho, we can:

   1. use commands: 
      - `kubectl describe` which can tell us most of everything we need to know about any resource
      - `kubectl logs` with which we can follow the logs of our possibly broken software
      - `kubectl delete` which will simply delete the resource and in some cases, like with pods in deployment, a new one will be automatically released
   2. use an IDE like [Lens](https://k8slens.dev/) to view everything in a dashboard or [FreeLens](https://github.com/freelensapp/freelens), v1.3.2 currently worked in windows

## Port Forwarding

We can us `port-forward` to forward a local port to a pod. For example if we have a service (in a POD) that runs on port 3000, we can use:

```bash
kubectl port-forward <pod-name> 3000:3000
```

**!! `We don't need to expose the port in our Dockerfile if we use Docker`, kubernetes ignores it when creating the pods in K3D**

## External Connections

To achieve an external connection in kubernetes we can use 3 ways:

1. A `Service` resource, more precisely a `NodePort`
2. An `Ingress` resource
3. A `Gateway API`

### Initial Steps

To open the connection to extrenal resources we have to open some ports our nodes. Following the k3d documentation we can achieve that using:


```bash
# load balancer has 80 port by default
# agent ports should be 30000-32767
k3d cluster create --port <local-port>:<agent-port(30000-32767)>@agent:0 -p <local-port>:80@loadbalancer --agents 2
```

### Services 

Service resources are essential for managing the application's accessibility, ensuring that it can be reached by connections originating both outside the cluster and from within. 

These resources handle the routing and load balancing necessary to maintain seamless communication with the application, regardless of the dynamic nature of pod creation and termination.

### Service Resource (NodePort)

 **NodePorts** are simply ports that are opened by Kubernetes **to all of the nodes** and the service will handle requests in that port. 
 
 *NodePorts are not flexible and require you to assign a different port for every application. As such NodePorts are not used in production but are helpful to know about.*

 To create the service we:

1. Declare that we want a Service
2. Declare which port to listen to
3. Declare the application where the request should be directed to
4. Declare the port where the request should be directed to

The service.yml should look like:
 ```yml
apiVersion: v1
kind: Service
metadata:
  name: hashresponse-svc
spec:
  type: NodePort
  selector:
    app: hashresponse # This is the app as declared in the deployment.
  ports:
    - name: http
      nodePort: 30080 # This is the port that is available outside. Value for nodePort can be between 30000-32767
      protocol: TCP
      port: 1234 # This is a port that is available to the cluster, in this case it can be ~ anything
      targetPort: 3000 # This is the target port
 ```

 then we apply it in the deployment

 ```bash
kubectl apply -f manifests/service.yaml
 ```

