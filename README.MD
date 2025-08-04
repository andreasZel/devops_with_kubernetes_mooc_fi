# Exercises Τable of Contents

## Chapter 2

[Exercise 1.1](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.1) 
[Exercise 1.2](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.2) 
[Exercise 1.3](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.3) 
[Exercise 1.4](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.4) 
[Exercise 1.5](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.5) 
[Exercise 1.6](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.6) 
[Exercise 1.8](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.8) 
[Exercise 1.9](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.9) 
[Exercise 1.10](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.10) 
[Exercise 1.11](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.11) 
[Exercise 1.12](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.12) 
[Exercise 1.13](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.13) 
[Exercise 1.7](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/1.7) 

## Chapter 3

[Exercise 2.1](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/2.1) 
[Exercise 2.2](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/2.2) 
[Exercise 2.3](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/2.3) 
[Exercise 2.4](https://github.com/andreasZel/devops_with_kubernetes_mooc_fi/tree/2.4) 


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

ReplicaSets are managed by Deployments, there is no need to define or modify them manually. If we want to manage the number of replicas, we can edit the Deployment and it will take care of modifying the ReplicaSet.

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

  ### Using a Pod 
  We can use a pod described with a `.yaml` file to test for connectivity or status of pods.

  *A generic container we can use is `busybox`. It has basic unix functionalities.*

  When we are done, we can delete the pod and it will be gone forever. So this cannot be used in production, because unlike **Deployment**, kubernetes won't spin up a new pod in it's place.

  We can create a pod like:

  ```docker
  apiVersion: v1
  kind: Pod
  metadata:
    name: my-busybox
    labels:
      app: my-busybox
  spec:
    containers:
    - image: busybox
      command:
        - sleep
        - "3600"
      imagePullPolicy: IfNotPresent
      name: busybox
    restartPolicy: Always
  ```

# Networking

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

### 1. Service Resource (NodePort)

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

### 2. Ingress (Incoming Network Access Resource)

`Ingress` differentiates from Service Resource, because it works on **Application Layer (7)** while Service works on **Transport Layer (4)** of the `OSI Model`.

Ingress can work by itself for **Routing** or in combination with a load balancer.

It is implemented using different kinds of **controllers**, k3d provides [Traefik](https://traefik.io/traefik) by default, but we can use other ones like [Istio](https://istio.io/) and [Nginx Ingress Controller](https://github.com/kubernetes/ingress-nginx).

#### Use an Ingress resource:

1. `ClusterIP` Service.
 
 To use this resource we need an `Internal IP` that can be accessed from the cluster. To do this we create a `ClusterIP` type Service.

The yml file looks like so:

```yml
apiVersion: v1
kind: Service
metadata:
  name: hashresponse-svc # Name of service
spec:
  type: ClusterIP
  selector:
    app: hashresponse # This is the app as declared in the deployment.
  ports:
    - port: 2345 # let TCP Traffic from 
      protocol: TCP
      targetPort: 3000 # let TCP Traffic to
```

2. Actual `Ingress Service`:

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dwk-material-ingress # Name of Service 
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: hashresponse-svc # Route all traffic to this port
            port:
              number: 2345 
```

Then apply both with: 

```bash
kubectl apply -f manifest
```

### 3. Gateway API

`Gateway API` is an evolving **set of resources and configurations** designed to **manage network traffic within** Kubernetes **clusters**.

## DNS Within Kubernetes

Kubernetes includes a **DNS service**. 

`Containers` in a `pod share the network`.

For communication `between Pods`, a `Service` (like ClusterIp) is used as they expose the Pods as a network service.

Alternatively each **Pod** has an **IP created by Kubernetes**.

We can get the **ip of the service** using the **get** command:

```bash
kubectl get svc

# output

#NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
#todo-backend-svc   ClusterIP   10.43.89.182   <none>        2345/TCP   2d1h
```

and use `exec` command to check for the status of the pod:

```bash
kubectl exec -it my-busybox -- wget -qO - http://10.43.89.182:2345
```

or use the **ip of pod it'self**:

```bash
kubectl describe pod <pod-name>

# output

#Name:             todo-backend-dep-84fcdff4cc-2x9wl
#Namespace:        default
#Priority:         0
#Service Account:  default
#Node:             k3d-k3s-default-agent-0/192.168.176.5
#Start Time:       Mon, 08 Apr 2024 23:27:00 +0300
#Labels:           app=todo-backend
#                  pod-template-hash=84fcdff4cc
#Annotations:      <none>
#Status:           Running
#IP:               10.42.0.63
```

then use it with `exec` command as well, but with the ip used in the container:

```bash
kubectl exec -it my-busybox wget -qO - http://10.42.0.63:3000
```

finally, we can use the `svc created`, so if we defined a ClusterIP service for **todo-backend** as **todo-backend-svc**, we can use it as:

```bash
kubectl exec -it my-busybox -- wget -qO http://todo-backend-svc:2345
```

# Storage

Like Docker, kubernetes uses `Volumes`to **persist** data. 

There are many kinds of Volumes in Kubernetes:

1. `emptyDir volumes`: are shared filesystems **inside a pod**.
   - This means that ${{\color{red} \ their \ lifecycle \ is \ tied \ to \ a \ pod\ !}}$. When the pod is destroyed the data is lost.
   - Moving the pod from another node will destroy the contents of the volume as the space is reserved from the node the pod is running on. 

   So, emptyDir Volumes are:
    - Bad ❌: for Backing up a database
    - Good ✅: To use as a **cache** as it *persists* between container restarts 
    - Good ✅: To **Share files** between two containers in a pod

   ### Share data between two containers (in the same pod):
   The way to add multiple containers within a pod is described in the **deployment**.
   
   We define:
   - A volume, in this case type `emptyDir`
   - `VolumeMounts` in each container with the path mount path 

   ```bash
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: images-dep
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: images
      template:
        metadata:
          labels:
            app: images
        spec:
          volumes: # Define volume
            - name: shared-image
              emptyDir: {}
          containers:
            - name: image-finder
              image: jakousa/dwk-app3-image-finder:b7fc18de2376da80ff0cfc72cf581a9f94d10e64
              volumeMounts: # Mount volume
              - name: shared-image
                mountPath: /usr/src/app/files
            - name: image-response
              image: jakousa/dwk-app3-image-response:b7fc18de2376da80ff0cfc72cf581a9f94d10e64
              volumeMounts: # Mount volume
              - name: shared-image
                mountPath: /usr/src/app/files
   ``` 

2. `persistent volumes`

    A **Persistent Volume (PV)** is a **cluster-wide resource**, that represents a piece of storage in the cluster. So the lifecycle is not tide to a POD.

    > PVs are bound to a POD, this ensures that the PV resource is exclusively used by the pod it's bound to

    Persistent Volumes can be backed by various types of storage such as local disk, NFS, cloud storage, etc. 

    ### Backing up Data

    When using a cloud provider, such as Google Kubernetes Engine, it is **the cloud provider that takes care of backing storage** and the **Persistent Volumes** that we can use.

    If we run our **own cluster** or use a **local cluster such as k3s** for development, we need to **take care of the storage system and Persistent Volumes ourselfs**.

    #### K3s Persistent Volumes

    An easy option that we can use with **K3s** is a **local PersistentVolume** that uses **a path in a cluster node as the storage**. This solution ties the volume to a particular node and `if the node becomes unavailable, the storage is not usable`, so it's **not suitable for production!**.


    ### Steps to create/use PVs (in k3d)

    1. `Create the actual path` in the node (k3d is a container). We can do it with **docker exec**

        ```bash
        docker exec k3d-k3s-default-agent-0 mkdir -p /tmp/kube
        ```
    
    2. `Define a PV .yml file`, so that kubernetes knows how to create the PV and **which nodes are goint to use it**:

        ```bash
          ## persistentvolume.yaml
          #-------------------------
          apiVersion: v1
          kind: PersistentVolume
          metadata:
            name: example-pv
          spec:
            storageClassName: my-example-pv # this is the name we are using later to claim this volume
            capacity:
              storage: 1Gi # Could be e.q. 500Gi. Small amount is to preserve space when testing locally
            volumeMode: Filesystem # This declares that it will be mounted into pods as a directory
            accessModes:
            - ReadWriteOnce
            local:
              path: /tmp/kube
            nodeAffinity: ## This is only required for local, it defines which nodes can access it
              required:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: kubernetes.io/hostname
                    operator: In
                    values:
                    - k3d-k3s-default-agent-0
        ```

    3. `Create a Persistent Volume Claim (PVC)`, Which is a **request for the storage**.
    
        PVC is a way to define the Requirements to be satisfied by a PV. Kubernetes finds a PV satisfying the needs or creates one, then binds it to the PVC.

        Once bound, the PersistentVolumeClaim is **"locked"** and can only be used **by one Pod** (depending on the access mode specified). This ensures that the PV resource is **exclusively used by the pod it's bound to**.

        If there is no suitable Persistent Volume available, the PVC will stay in the "Pending" state, waiting for a suitable PV. 
        
        So, 

        > PVs -> physical volume (the actual storage in our infrastructure).
        PVCs -> the means by which pods claim this storage for their use.

        We can create one like so: 

        ```bash
        # persistentvolumeclaim.yaml
        #-------------------
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: image-claim # name of the volume claim, this will be used in the deployment
        spec:
          storageClassName: my-example-pv # this is the name of the persistent volume we are claiming
          accessModes:
            - ReadWriteOnce
          resources:
            requests:
              storage: 1Gi
        ```

    4. `Use the PV and PVC` in the `deployment`:

        ```bash
        # ...
        spec:
          volumes:
            - name: shared-image
              persistentVolumeClaim: # we add PVC
                claimName: image-claim
          containers:
            - name: image-finder
              image: jakousa/dwk-app3-image-finder:b7fc18de2376da80ff0cfc72cf581a9f94d10e64
              volumeMounts:
              - name: shared-image # use as before
                mountPath: /usr/src/app/files
            - name: image-response
              image: jakousa/dwk-app3-image-response:b7fc18de2376da80ff0cfc72cf581a9f94d10e64
              volumeMounts:
              - name: shared-image # use as before
                mountPath: /usr/src/app/files
        ```

        📝 Note:
        
         Always apply the PVs and PVCs first! This is done by:
         ```bash
          kubectl apply -f persistentVolumesDir -f manifest
         ```
# Namespaces

`Namespaces` are used to keep `resources Seperated`. 

1. Seperate environments

*A common way to use it is to separate environments such as production, testing, staging*.

2. Utilize limited Resources

But a company could also benefit if only uses 1 cluster. Multiple projects can use namespaces to `split the cluster into virtual clusters`, one for each project.
        
## Communication using namespaces

`DNS entry` for services `includes the namespace` so we can still have projects communicate with each other if needed through `service.namespace address`.

This means that we can use it as:
> http://{service-name}.{namespace}
        
## Create a namespace

We can create a namespace imperative and declerative

1. with the imperative way we use:
   ```bash
   kubectl create namespace example-namespace
   ``` 
2. with declerative we add it to our `deployment` or `any other Service` .yml file:
```docker
# ...
metadata:
  namespace: example-namespace
  name: example
# ...
```
 \
we can also set the namespace to be used by default with: 

```bash
kubectl config set-context --current --namespace=<name>
```

or use the  `kubens` tools. 

It provides `kubectx` command, that let us change cluster we are using.

And `change the default namespace easily`.

> !! ⚠️ IMPORTANT !!

Namespaces should be kept separate. Critical software should be in a seperated cluster.

An administrator should set a `ResourceQuota` for that namespace, so that we can safely run anything there.

## Listing namespaces 

we can get namespaces using the `-n` flag:

```bash
kubectl get pods -n kube-system
```

or use `--all-namespaces`:

```bash
kubectl get all --all-namespaces
```

# Labels

Labels help:

1. in **seperating applications** from others inside a **namespace**.
2. **grouping** different resources together.
3. **Configure pods** to be in a **wanted Node** with `nodeSelector`, an example would be *nodes* known to have *higher speed network*. 

## Adding a label

labels can be added per namespace. So we can have **same labels in different namespaces**. We can add them using:

```bash 
# great importance pod
kubectl label pod hashgenerator-dep-7b9b88f8bf-lvcv4 importance=great  
```

## listing labels

we can list labeled resources by:

```bahs
kubectl get pod -l importance=great
```

## Configure Nodes with `NodeSelector`

we can pick which nodes to assign a pod using `NodeSelector`. For example an excellent Quality Network:

```yml 
# deployment decleration
    ...
    spec:
      containers:
        - name: hashgenerator
          image: jakousa/dwk-app1:b7fc18de2376da80ff0cfc72cf581a9f94d10e64
      nodeSelector:
        networkquality: excellent
```

if we don't have any nodes qualified as "excellent", the pod will be in statur "PENDING", if we add one then the pod will run into it:

```bash
kubectl label nodes k3d-k3s-default-agent-1 networkquality=excellent
```

> !! ⚠️ IMPORTANT !!

By default kubernetes will use different strategies to avoid high network latency such as [**affinity** and **anti-affinity**](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity).

`NodeSelector` is best suited for cases such as picking nodes based to if they use SSD or HDD. Other tasks are better handled by Kubernetes, **with our touch of configuration of rules**.

# Secrets and ConfigMaps Services

`Secrets` are for **sensitive information** that are given to **containers on runtime**.

`ConfigMaps` are quite much like secrets but they may contain **any kind of configurations**.

## Secrets:

   Secrets are **Services**. We need to define a .yml file. We need to give:
   1. a name to the service to be used
   2. a name to the variable used in the container

   so, a **yml file** example would look like this:
   ```yml
    apiVersion: v1
    kind: Secret
    metadata:
      name: pixabay-apikey # Name of Service
    data:
      # Name of key 
      API_KEY: aHR0cDovL3d3dy55b3V0dWJlLmNvbS93YXRjaD92PWRRdzR3OVdnWGNR
   ```

   and we can use it in the **deployment**, either with

   1. **longer** form definition

   ```yml
    # ...
    containers:
      - name: imageagain
        env:
          - name: API_KEY # ENV name passed to container
            valueFrom:
              secretKeyRef:
                name: pixabay-apikey
                key: API_KEY # ENV name in the secret
   ```

   2. or the **shorter**, where we define a {secret-service-name}-{secret-variable}:

   ```yml
    # ...
    containers:
      - name: imageagain
        envFrom:
          - secretRef:
              name: pixabay-apikey
   ```

### Secret encoding, decoding

There are multiple solutions for secret management depending on the platform. 

Cloud service providers may have their own solution, like **Google Cloud Secret Manager** or **AWS Secrets Manager**. For a Kubernetes native solution, we could use **SealedSecrets**.

One approach used in the module is using:

1. [`SOPS`](https://github.com/getsops/sops), a **tool made by mozilla** to **edit, manage and store** encypted secrets (it can handle yaml files, so docker compose files as well).

2. [`age`](https://github.com/FiloSottile/age), a tool dedicated to encryption alone.

So, `SOPS` will **manage the keys** and `age` will handle the **encyption-decryption**.

First we create a key:

```bash
age-keygen -o key.txt
# Public key: age17mgq9ygh23q0cr00mjn0dfn8msak0apdy0ymjv5k50qzy75zmfkqzjdam4
```

public and secret keys are in the **key.txt** file.

then we encypt the .yaml file, we can add a **regex** with the **--encrypted-regex** flag, meaning only the values matched with this regex will be encypted:

```bash
sops --encrypt \
  --age age17mgq9ygh23q0cr00mjn0dfn8msak0apdy0ymjv5k50qzy75zmfkqzjdam4 \
  --encrypted-regex '^(data)$' \
  secret.yaml > secret.enc.yaml
```

The secret.enc.yaml could look like this:

```yml
apiVersion: v1
kind: Secret
metadata:
  name: pixabay-apikey
data:
  API_KEY: ENC[AES256_GCM,data:geKXBLn4kZ9A2KHnFk4RCeRRnUZn0DjtyxPSAVCtHzoh8r6YsCUX3KiYmeuaYixHv3DRKHXTyjg=,iv:Lk290gWZnUGr8ygLGoKLaEJ3pzGBySyFJFG/AjwfkJI=,tag:BOSX7xJ/E07mXz9ZFLCT2Q==,type:str]
sops:
  kms: []
  gcp_kms: []
  azure_kv: []
  hc_vault: []
  age:
    - recipient: age17mgq9ygh23q0cr00mjn0dfn8msak0apdy0ymjv5k50qzy75zmfkqzjdam4
      enc: |
        -----BEGIN AGE ENCRYPTED FILE-----
        YWdlLWVuY3J5cHRpb24ub3JnL3YxCi0+IFgyNTUxOSBDczBhbGNxUkc4R0U0SWZI
        OEVYTEdzNUlVMEY3WnR6aVJ6OEpGeCtJQ1hVCjVSbDBRUnhLQjZYblQ0UHlneDIv
        UmswM2xKUWxRMHZZQjVJU21UbDNEb3MKLS0tIGhOMy9lQWx4Q0FUdVhoVlZQMjZz
        dDEreFAvV3Nqc3lIRWh3RGRUczBzdXcKh7S4q8qp5SrDXLQHZTpYlG43vLfBlqcZ
        BypI8yEuu18rCjl3HJ+9jbB0mrzp60ld6yojUnaggzEaVaCPSH/BMA==
        -----END AGE ENCRYPTED FILE-----
  lastmodified: "2021-10-29T12:20:40Z"
  mac: ENC[AES256_GCM,data:qhOMGFCDBXWhuildW81qTni1bnaBBsYo7UHlv2PfQf8yVrdXDtg7GylX9KslGvK22/9xxa2dtlDG7cIrYFpYQPAh/WpOzzn9R26nuTwvZ6RscgFzHCR7yIqJexZJJszC5yd3w5RArKR4XpciTeG53ygb+ng6qKdsQsvb9nQeBxk=,iv:PZLF3Y+OhtLo+/M0C0hqINM/p5K94tb5ZGc/OG8loJI=,tag:ziFOjWuAW/7kSA5tyAbgNg==,type:str]
  pgp: []
  encrypted_regex: ^(data)$
  version: 3.7.1
```

Now we can safely save the encrypted file. Then when we need to use it we can **decrypt** the file using:

```bash 
sops --decrypt secret.enc.yaml | kubectl apply -f -
```

this way we don't create the plain **secret.yaml** file, we **just pipe it to the apply command**. Or we can create it and then apply it:

```bash
export SOPS_AGE_KEY_FILE=$(pwd)/key.txt

sops --decrypt secret.enc.yaml > secret.yaml
```

# ConfigMaps

`ConfigMaps` are similar but the **data doesn't have to be encoded and is not encrypted**.

For example we can define a ConfigMap with a file and contents we can use, so changing the file in the node, we can change the env variables in our container:

1. define the ConfigMap Service:

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-configmap
data:
  serverconfig.txt: |
    maxplayers=12
    difficulty=2
```

and use it directly as volume, no need for PV or PVC:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: game-server
spec:
  containers:
    - name: game
      image: mygame/server:latest
      volumeMounts:
        - name: config-volume
          mountPath: /etc/game/
          readOnly: true
  volumes:
    - name: config-volume
      configMap:
        name: example-configmap
```