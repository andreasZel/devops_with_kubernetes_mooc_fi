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
4. A `Load Balancer Service`

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
It is used in combination with a cloud provider, it is desribed better in the [Gateway API in GKE](#gateway-api-in-gke) section.

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

### 4. Load Balancer Service

We can also use a load balancer as follows: 

```yml
apiVersion: v1
kind: Service
metadata:
  name: seedimage-svc
spec:
  type: LoadBalancer # This should be the only unfamiliar part
  selector:
    app: seedimage
  ports:
    - port: 80
      protocol: TCP
      targetPort: 3000
```

It's the same as ingress, it will add to cost in the end but we get 
reliability.

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

## StatefulSets

  In kubernetes Deployment creates replicas of pods. When scaling and read-write data, PVs can be troublesome.

  `StatefulSets` solve that problem by ensuring an **identical** pod will replace the one that died. If it is scaled **each copy will have it's own storage**. It's stateless applications.

  They are ideal in cases we want the data to be saved within the application, not outside. 

  One key advantage of using StatefulSets is that they ensure data safety by not deleting the associated volumes when the StatefulSet is deleted.

  ### using StatefulSets

  To use StatefulSets, we need a **"headless Service"** over it. Basically a **ClusterIP** Service that let's us *access pods directly*, without reverse proxy or load balancing. 
  
  This can be achieved using `ClusteIP: None`. For example, let's say we want a Redis and an application to use it:

  ```yml
    ## Cluster IP (headless Service)
    apiVersion: v1
    kind: Service
    metadata:
      name: redis-svc
      labels:
        app: redis
    spec:
      ports:
      - port: 6379
        name: web
      clusterIP: None
      selector:
        app: redisapp
  ```

  ```yml
    # statefulset with Redis and app using it
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: redis-stset
    spec:
      serviceName: redis-svc
      replicas: 2
      selector:
        matchLabels:
          app: redisapp
      template:
        metadata:
          labels:
            app: redisapp
        spec:
          containers:
            - name: redisfiller
              image: jakousa/dwk-app5:54203329200143875187753026f4e93a1305ae26
            - name: redis
              image: redis:5.0
              ports:
                - name: web
                  containerPort: 6379
              volumeMounts:
                - name: redis-data-storage
                  mountPath: /data
      volumeClaimTemplates:
        - metadata:
            name: redis-data-storage
          spec:
            accessModes: ["ReadWriteOnce"]
            storageClassName: local-path
            resources:
              requests:
                storage: 100Mi
  ```

  The difference between Deployment, is that we use `volumeClaimTemplate` to claim it's own volume in each pod. This means that we don't need a PVC, it is created automatically.

  We can ping or nslookup the redis-svc thanks to ClusterIP. If we now delete a pod, it a new **one identical will be created** even with the **same name**.

> !! ✅ TIP !!

  we can combine files, eg. the statefulSet.yml and the ClusterIP, as with any resource:

  ```yml
    apiVersion: apps/v1
    kind: StatefulSet
    metadata:
      name: redis-stset
    spec:
      serviceName: redis-svc
      replicas: 2
      selector:
        matchLabels:
          app: redisapp
      # more rows
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: redis-svc
      labels:
        app: redis
    spec:
      ports:
      - port: 6379
        name: web
      clusterIP: None
      selector:
        app: redisapp
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

# Jobs and CronJobs Resources

## Jobs Resources

`Jobs Resources` are resources used to run workloads that are not continuous services, 
but are supposed to **run from start to end**. 

Jobs have **status** in order for them to be **monitored** and to know when they are finished.

Jobs can be **configured** so that they **run multiple instances of the same task** 
1. concurrently.
2. **sequentially**.
3. **until a set number of successful completions** have been achieved.

These are very nice for **backups**. 

We can create a `bash sript` that takes an **env URL variable**, the db url and saves a backup 
using pgdump:

```bash
#!/usr/bin/env bash
set -e

if [ $URL ]
then
  pg_dump -v $URL > /usr/src/app/backup.sql

  echo "Not sending the dump actually anywhere"
  # curl -F ‘data=@/usr/src/app/backup.sql’ https://somewhere
fi
```

here we just save it locally, but as we see in the comments, we can **send the backup** as a form file upload (field named **data**) to the URL **https://somewhere** or **whatever service holds the backups**.

An outside of the cluster service is used for safety. If cluster is damaged, the backups will be available.

Then we create an image that uses this file as:

```yml
  FROM alpine:latest

  # Install bash and postgresql client tools (pg_dump)
  RUN apk add --no-cache bash postgresql-client

  # Copy the backup script into the image
  COPY backup.sh /usr/local/bin/backup.sh

  # Make sure the script is executable
  RUN chmod +x /usr/local/bin/backup.sh

  # Set the backup script as the container entrypoint
  ENTRYPOINT ["/usr/local/bin/backup.sh"]
```

We have to use this image in a `Job Resource`:

```yml
  apiVersion: batch/v1
  kind: Job
  metadata:
    name: backup
  spec:
    template:
      spec:
        containers:
        - name: backup
          image: <our-image>
          env:
            - name: URL
              value: "postgres://postgres:example@postgres-svc:5432/postgres"
        restartPolicy: Never # This time we'll run it only once
```

`restartPolicy: Never` just runs it once, but **we can force it to retry for a number of times**
by defining `backoffLimit`.

Now we can create a statefulset postgres resource with a headles Service on top and take a backup from it:

```yml
apiVersion: v1
kind: Service
metadata:
  name: postgres-svc
  labels:
    app: postgres
spec:
  ports:
  - port: 5432
    name: web
  clusterIP: None
  selector:
    app: postgres
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres-ss
spec:
  serviceName: postgres
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
          image: postgres:13.0
          ports:
            - name: postgres
              containerPort: 5432
          env:
          - name: POSTGRES_PASSWORD
            value: "example"
          volumeMounts:
            - name: data
              mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        storageClassName: local-path
        resources:
          requests:
            storage: 100Mi
```

## CronJobs Resources

CronJobs are similar to Jobs but they run on schedule. 

# Monitoring the cluster

To monitor our cluster we will install some tools. These will be `Promethius` **for monitoring the cluster**, `Graphala` **to view the data**. 

## `Helm`

  To monitor our cluster we need to install some packages. So first we need
  to install a **kubernetes package manager**: `Helm`.

  To install it we download the binary file, save it in our pc and add it to 
  the **path**.

  ### Charts

  Helm uses a packaging format called `charts`. These are used to define dependencies of an application.

  ### Repos

  `Repos` in Helm are **collections of charts** stored in one place, accessible via a URL

  To add a repo we use:

  ```bash
  helm repo add <custom-name> <repo-url>
  ```

  Then to update the cache with the latest verion we use:

  ```bash
  helm repo update
  ```

  1. (add repo) So we can add `Promitheus` repo by:

  ```bash
  helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
  helm repo add stable https://charts.helm.sh/stable
  helm repo update
  ```

  2. (install) we can now install the `kube-prometheus-stack`, preferrably in a different namespace to be distinct `prometheus`:

  ```bash
  kubectl create namespace prometheus

  helm install prometheus-community/kube-prometheus-stack --generate-name --namespace prometheus
  ```

  This adds a lot of extra custom resources that are not in kubernetes by default.

  We can use `helm list -n prometheus` to view all our resources.
  
  And `helm delete [name]` to delete them.

## Graphala

We can now add graphala. We first need to find it in helm:

```bash
kubectl get po -n prometheus
#...
NAME                                                              READY   STATUS    RESTARTS
kube-prometheus-stack-1602180058-grafana-59cd48d794-4459m         2/2     Running   0          53s
```

Then we can port forward it to port 3000 and access it from browser:

```bash
kubectl -n prometheus port-forward kube-prometheus-stack-1602180058-grafana-59cd48d794-4459m 3000
```

the default credentials are:

> admin / prom-operator

## Loki

In graphala we can monitor our logs. We can achieve that by adding 
the **log aggregation system** `Loki`.

1. (add repo) we add the graphana helm-charts:
    ```bash
    helm repo add grafana https://grafana.github.io/helm-charts

    helm repo update
    ```

2. (install) we can now install the `loki-stack`, preferrably in a different namespace to be distinct `loki-stack`:

    ```bash
    kubectl create namespace loki-stack

    # image 2.9.3 is old but lightweight, newer one is very heavy for a mid laptop
    helm upgrade --install loki --namespace=loki-stack grafana/loki-stack --set loki.image.tag=2.9.3
    ```

we can now see the resources running with:

```bash
kubectl get all -n loki-stack

#---

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
  service/loki            ClusterIP   10.43.170.68   <none>        3100/TCP   18m
  service/loki-headless   ClusterIP   None           <none>        3100/TCP   18m
```

we can see that it's running on 3100.
 
So in `graphal`, if we navigate to **Connections** > **choose Data Sources**  and then Add new data source,
we can add `loki` in **http://loki.loki-stack:3100**.

To view the logs, we choose the desired container or whatever we want and press **live** and **initiate query**.
Any logs will be added there.

This is done because this version of `loki-stack` comes with `Promtail`, an agent which ships the contents of local logs to a private Grafana Loki instance or Grafana Cloud.

Promptail will be deprecated soon and need to be replaced by `Grafana Alloy` in combination with `grafana/loki`.

# Cloud Solutions

We can manage our own kubernetes clusters in case we have the necessary hardware for it or we are a private organisation such as
a univercity. The problem comes with managing it, an improper setup can add up to the bill, so in most case `Serverless` Solution is 
the best approach.

*Serverless, meaning, not actually worring about the actual hardware, just use servers of another provider*. There are different high-tier environment for serverless workloads, such as **Google Cloud Run** or **AWS Fargate**, but we'll use `Google Kubernetes Engine`.

A cheap option would be **5 nodes in 1 zonal cluster using 1 vCPU each**. This would come around 200$ a month, with more cost added with load balancers.

To use Google Kubernetes Engine:

1. we'll have to [create an account](https://cloud.google.com/kubernetes-engine?hl=el).
2. Install the [Google Cloud SDK](https://cloud.google.com/sdk/docs/install).
3. On the [resources page](https://console.cloud.google.com/cloud-resource-manager) we can create a new project.
4. We can now log in from CLI and use the project we created:
    ```bash
    # login
    gcloud auth login
    
    #...

    # set project
    gcloud config set project [project-name]
    ```
5. enable the KCE API from the page or by typing:
    ```bash
    y
    gcloud services enable container.googleapis.com
    ```
6. Install the `gke-gcloud-auth-plugin` from [here](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl#install_plugin)
7. We can now create a cluster with `gcloud container clusters create`. The command accepts a [zone](https://cloud.google.com/about/locations/) and a [GKE version](https://cloud.google.com/kubernetes-engine/docs/release-schedule), for example :
   ```bash
    gcloud container clusters create dwk-cluster --zone=europe-west1-b --cluster-version=latest --disk-size=32 --num-nodes=3 --machine-type=e2-medium
   ```

   we created a cluster with name: dwk-cluster, in zone europe-west1-b, disk size 32 gb, 3 nodes and a machine type of e2-medium 2 vCPUs and 2 GB RAM per node.

After that we can observe our cluster using `kubectl cluster-info`.

## Applying resources from github

We can use `https://raw.githubusercontent.com/` to apply our files directly from github, we can do it as bellow:

```bash
# after repo we have to specify commit key
kubectl apply -f https://raw.githubusercontent.com/andreasZel/devops_with_kubernetes_1_1_Log_Output/fd9e072b39d69535cb892c1623879d334f3ecee6/porsgresServices/StatefullSetPorsgres.yml
```

> !! Sometimes there are errors with postgres statefulset, we can use `subPath: postgres` to avoid it, and don't add a storage class. After that we remove the storage class and the statefulset and reapply to fix it

## Troubleshooting

We can use different methods to troubleshoot. One is the `get <resource-type> <resource-name> -n <namespace>` and another is the 
`describe <resource-type> <resource-name> -n <namespace>` if the resource restarts or is unavailable.

Using tools as `freelens` is also vey helpful.

## Deleting the cluster 

To avoid using resources we can delete the cluster using:

```bash
gcloud container clusters delete dwk-cluster --zone=europe-west1-b
```

## Ingress in GKE 

Services are quite simple. They allow us to define a layer 4 (L4) load balancer on Google Cloud Services.

Layer 4 here means the layer in OSI model. In other words, TCP/UDP traffic. For advanced traffic rules, we need to use a layer 7 (L7) load balancers which can be created either by an Ingress, or by Gateway.

To use a Load balancer in GKE we have to define 1 clusterIP resource and 1 load balancer resource for each port wanted, or define them in the dame .yml file, for ingress a different approach is required.

In GKE to use ingress we have to use a `NodePort` service as well. 
Even though it is NodePort, GKE does not expose it outside the cluster.

## Gateway API in GKE

The `Gateway API` is a **set of resources and standards** that allow us to define **how external traffic should be routed to services within our Kubernetes cluster**.

At the top is **GatewayClass**, which is a resource within the GatewayAPI that defines a type of Gateway.

It specifies the underlying infrastructure or controller that will be used to implement the Gateway. 

The **GatewayClass** resources are **defined by infrastructure providers**. The list of options provided by Google Kubernetes Engine can be seen [here](https://cloud.google.com/kubernetes-engine/docs/concepts/gateway-api).

![gateway api image](./assets/GATEWAY-API.png)

### How to use a Gateway API in GKE

First we need to enable it in the cluster:

```bash
gcloud container clusters update dwk-cluster --location=europe-west1-b --gateway-api=standard
```

Then we need 3 .yaml files:

1. A `Gateway Resource`, `gateway.yaml`, which defines where and how the load balancers listen for traffic to our cluster:
   ```yaml
    apiVersion: gateway.networking.k8s.io/v1beta1
    kind: Gateway
    metadata:
      name: my-gateway
    spec:
      gatewayClassName: gke-l7-global-external-managed
      listeners:
      - name: http
        protocol: HTTP
        port: 80
        allowedRoutes:
          kinds:
          - kind: HTTPRoute
   ```
   we can see that it's a type of Gateway, it picks a GatewayClass following the [Google recommendations](https://cloud.google.com/kubernetes-engine/docs/concepts/gateway-api#route) and says that our cluster accepts HTTP traffic to port 80
2. A `HTTPRoute resource`, `route.yaml` describes the routing rules
    ```yml
    apiVersion: gateway.networking.k8s.io/v1beta1
    kind: HTTPRoute
    metadata:
      name: my-route
    spec:
      parentRefs:
      - name: my-gateway
      rules:
      - matches:
        - path:
            type: PathPrefix
            value: /
        backendRefs:
        - name: seedimage-svc
          port: 80
    ```
    more or less the same as an ingress.
3. A `ClusterIp Resource` to open the port:
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: seedimage-svc
    spec:
      type: ClusterIP
      selector:
        app: seedimage
      ports:
        - port: 80
          protocol: TCP
          targetPort: 3000
    ```

Then we can see where we can access it using `describe`:

```bash
kubectl get gateway my-gateway

# Output:

# NAME         CLASS         ADDRESS          PROGRAMMED   AGE
# my-gateway   gke-l7-gxlb   35.227.224.141   True         106m
```

## Deployment Pipeline

There are several tools that help with kubernetes deployment like [Kustomize](https://github.com/kubernetes-sigs/kustomize) and [Helmsman](https://github.com/mkubaczyk/helmsman). These allow as to apply multiple yml files with one command. This can be used in cobination with `github actions` to deploy our solutions.

### Kustomise 

`Kustomise` is built in to kubernetes! To use it we have to create a `kustomization.yml` file where we define our resources:

An example would be:

```yml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - manifests/deployment.yaml
  - manifests/service.yaml
images:
  - name: PROJECT/IMAGE_COLORCONTENT
    newName: jakousa/colorcontent
    newTag: v1.2.3
```

Then we apply with the `-k` flag:

```bash
kubectl apply -k .
```

we can also use the `kubectl kustomize .` to view our created yml file to the screen. In our example,
we used *PROJECT/IMAGE_COLORCONTENT* as a var that points to **jakousa/colorcontent**.

Now, in our service we can use *PROJECT/IMAGE_COLORCONTENT* as the image name and it will be **updated automatically** based on tag!

So in our case is **v1.2.3**, if we don't specify, it will be the **latest one**.

> A useful tool that can be used is
> [Kubernetes Kustomize Cheat Sheet](https://itnext.io/kubernetes-kustomize-cheat-sheet-8e2d31b74d8f), providing additional documentation.

## Github Actions

We can define a github action to build our images each time we push a new change in our repo.

We can use the [Google Artifacts Registry](https://console.cloud.google.com/artifacts) repo instead of `dockerhub`. 

To do so, we need to add a `service account`. We can do it as:

1. In GKE navigate to `IAM & ADMIN`/`Service Accounts`
2. Add a new account with the following **roles**:
  - **Kubernetes Engine Service Agent**: Gives Kubernetes Engine account access to manage cluster resources. Includes access to service accounts.
  - **Storage Admin**: Grants full control of buckets and objects.
  - **Artifact Registry Administrator**: Administrator access to create and manage repositories.
  - **Artifact Registry Create-on-Push Repository Administrator**: Access to manage artifacts in repositories, as well as create new repositories on push.
3. Generate a key:
  - Either from the **UI**, by downloading the **JSON**
  - or by typing:
    ```bash
    gcloud iam service-accounts keys create ./private-key.json
    ```
4. Create a **Google Artifact Registry** with a docker repo
5. Create the **workflow**:
    - create a dir **.github/workflows** and add a **main.yml** file
    - add two `secrets`, one for the **project_id** and one for the **key**. The key needs to have the **entire JSON file content**.
    
    The workflow needs to 
      1. checkout in the branch
      2. authenticate to GKE with the key we got
      3. setup the artifact registry
      4. get creadentials for our cluster
      5. build and push each image seperately, with a new tag
      6. setup Kustomize with new variables
    
    The whole action can be described as follows:
    ```yml
    name: Release application

    on:
      push:
        branches:
          - main  

    env:
      PROJECT_ID: ${{ secrets.GKE_PROJECT }}
      GKE_CLUSTER: dwk-cluster
      GKE_ZONE: europe-west1-b
      REGISTRY: europe-west1-docker.pkg.dev
      REPOSITORY: my-repository # Artifact registry for images
      BRANCH: ${{ github.ref_name }}

      # image names
      IMAGE_SERVER: zelhs/project_server
      IMAGE_TODO: zelhs/todoapp
      IMAGE_RANDOM: zelhs/random-article

    jobs:
      build-publish-deploy:
        name: Build, Publish and Deploy
        runs-on: ubuntu-latest

        steps:
          - name: Checkout
            uses: actions/checkout@v4

          - uses: google-github-actions/auth@v2
            with:
              credentials_json: '${{ secrets.GKE_SA_KEY }}'

          - name: 'Set up Cloud SDK'
            uses: google-github-actions/setup-gcloud@v2

          - run: gcloud --quiet auth configure-docker $REGISTRY

          - name: 'Get GKE credentials'
            uses: 'google-github-actions/get-gke-credentials@v2'
            with:
              cluster_name: '${{ env.GKE_CLUSTER }}'
              project_id: '${{ env.PROJECT_ID }}'
              location: '${{ env.GKE_ZONE }}'

          # Form image tags
          - name: 'Form image names'
            run: |
              echo "IMAGE_TAG_SERVER=$REGISTRY/$PROJECT_ID/$REPOSITORY/$IMAGE_SERVER:$BRANCH-$GITHUB_SHA" >> $GITHUB_ENV
              echo "IMAGE_TAG_TODO=$REGISTRY/$PROJECT_ID/$REPOSITORY/$IMAGE_TODO:$BRANCH-$GITHUB_SHA" >> $GITHUB_ENV
              echo "IMAGE_TAG_RANDOM=$REGISTRY/$PROJECT_ID/$REPOSITORY/$IMAGE_RANDOM:$BRANCH-$GITHUB_SHA" >> $GITHUB_ENV

          # Build & Push Server
          - name: Build & Push project_server
            run: |
              docker build -t $IMAGE_TAG_SERVER .
              docker push $IMAGE_TAG_SERVER

          # Build & Push todoApp
          - name: Build & Push todoApp
            run: |
              docker build -t $IMAGE_TAG_TODO ./todoApp
              docker push $IMAGE_TAG_TODO

          # Build & Push createRandomTodo
          - name: Build & Push createRandomTodo
            run: |
              docker build -t $IMAGE_TAG_RANDOM ./createRandomTodo
              docker push $IMAGE_TAG_RANDOM

          # Deploy
          - name: Set up Kustomize
            uses: imranismail/setup-kustomize@v2.1.0

          - name: Deploy
            run: |-
              kustomize edit set image PROJECT/IMAGE_SERVER=$IMAGE_TAG_SERVER
              kustomize edit set image PROJECT/IMAGE_TODO=$IMAGE_TAG_TODO
              kustomize edit set image PROJECT/IMAGE_RANDOM=$IMAGE_TAG_RANDOM
              kustomize build . | kubectl apply -f -
              kubectl rollout status deployment project_server
              kubectl rollout status deployment todoapp
              kubectl rollout status deployment random-article
    ```
    we can see that env vars were used in combination with the secrets. 

    Image tags were also auto updated using:

    ```yml
    IMAGE_TAG_SERVER=$REGISTRY/$PROJECT_ID/$REPOSITORY/$IMAGE_SERVER:$BRANCH-$GITHUB_SHA

    # output would be something like: 
    europe-west1-docker.pkg.dev/dwk-gke-12312/myregistry/zelhs/project_server:main-c00adaefda1f7169
    ```
## Good practices Branch - Namespace

It's a good practice to seperate each branch as it's own namespace. We can achieve this using actions such as:

```yml
kustomize edit set namespace ${GITHUB_REF#refs/heads/}
```

`GITHUB_REF` will give us something like **refs/heads/main**. By adding `#` remove the shortest prefix match. So 
the path would look like **main**, if it would be **refs/heads/feat/main** it would give **feat/main** and so forth.

We need to make sure the namespace exist first, so well try to create it first:

```yml
kubectl create namespace ${GITHUB_REF#refs/heads/} || true
```

and use it with:

```yml 
kubectl config set-context --current --namespace=${GITHUB_REF#refs/heads/}
```

it uses it in context for all the following instructions.

All together it should look like:

```yml
  # ...
- name: Deploy
  run: |-
    kubectl create namespace ${GITHUB_REF#refs/heads/} || true
    kubectl config set-context --current --namespace=${GITHUB_REF#refs/heads/}
    kustomize edit set namespace ${GITHUB_REF#refs/heads/}
    kustomize edit set image PROJECT/IMAGE=$IMAGE_TAG
    kustomize build . | kubectl apply -f -
    kubectl rollout status deployment $SERVICE
    kubectl get services -o wide
```

### Domain Names

The next step is to seperate to different environments using
[domain names](https://cloud.google.com/kubernetes-engine/docs/tutorials/configuring-domain-name-static-ip) like we'd have "www.example.com" as the production and e.g. "featx.example.com" as the featx branch.

# Scaling 

There are two primary types of scaling: `horizontal scaling` and `vertical scaling`.

1. `Vertical scaling` involves increasing the resources available to **a single pod or node**, such as adding **more CPU or memory**. This approach enhances the capacity of individual units.

2. `Horizontal scaling`, which is more commonly discussed, involves increasing the **number of pods or nodes**. This method distributes the load across multiple units, enhancing the system's ability to handle higher traffic and providing redundancy.

## Horizontal Scaling: Scaling Pods (HorizontalPodAutoscaler)

We might want to increase the number of pods according to network traffic, like when we have more request in an application than the number of requests that can be processed.

we can enforce some **limits** in our deployments with `limits:`. We can setup limit for `CPU` and `memory`, this is enforced by limiting the resources by `throttling` for CPU and memory limits are enforced by the kernel with **out of memory (OOM) kills**.

an example would be:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cpushredder-dep
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cpushredder
  template:
    metadata:
      labels:
        app: cpushredder
    spec:
      containers:
        - name: cpushredder
          image: jakousa/dwk-app7:e11a700350aede132b62d3b5fd63c05d6b976394
          resources:
          # limits
            limits:
              cpu: "150m"
              memory: "100Mi"
```

The suffix of the CPU limit "m" stands for "thousandth of a core". Thus 150m equals 15% of a single CPU core (150/1000=0,15).

we can scale up using a new resource, a `HorizontalPodAutoscaler`. 

We define **minimum replicas**, **maximum replica count** and the **target CPU Utilization** as well as the **target deployment**. 

If the CPU utilization exceeds the target then an additional replica is created until the max number of replicas.

```yml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: cpushredder-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: cpushredder-dep
  minReplicas: 1
  maxReplicas: 6
  targetCPUUtilizationPercentage: 50
```

By default it will take 300 seconds to scale down. We can set this up by altering HorizontalPodAutoscaler:

```yml
#...
behavior:
  scaleDown:
    stabilizationWindowSeconds: 30
```

## Horizontal Scaling: Scaling Nodes (cluster autoscaling)

To enable node scaling, we have to update our cluster using `--enable-autoscaling` flag, in 
combination with `--min-nodes` and `--max-nodes` flags for minimum and maximum ammount of nodes.

An example with min 1 and max 5 would be:

```bash
gcloud container clusters update dwk-cluster --zone=europe-north1-b --enable-autoscaling --min-nodes=1 --max-nodes=5
```

Cluster autoscaling may disrupt pods by moving them around as the number of nodes increases or decreases. So we can use
`PodDisruptionBudget` resource to tell kubernetes minAvailable or/and maxAvailable number of nodes.

with this setup:

```yml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
  name: example-app-pdb
spec:
  maxUnavailable: 50%
  selector:
    matchLabels:
      app: example-app
```

we tell kubernetes that no more than half of the pods can be unavailable.

## Vertical Scaling (VerticalPodAutoscaler)

We can also scale pods vertically with `VerticalPodAutoscaler`, utilizing more cpu and memory when needed.

## Safer Deployment

We ideally want the application to work during and after an update. So
instead of updating avery pod at the same time, we update once at a time.

There are two main approaches:

1. `Rolling update`
2. `Canary release`

### 1. Rolling update

Kubernetes automatically initiates a rolling update when we change an image,
, every pod is updates sequentially. If we decide to push an image that does not work, the update will automatically stop.

That does not take into accoint if the actual application works, for that we can use `ReadinessProbe` resource.

#### ReadinessProbe

We can use `ReadinessProbe`to use an endpoint thats checks for the application's helth. We actually have to create the endpoint ourselfs, returning 200 for healthy and 500 not healthy.

A typical readinessProbe inside a deployment.yml would look like:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flaky-update-dep
spec:
  replicas: 4
  selector:
    matchLabels:
      app: flaky-update
  template:
    metadata:
      labels:
        app: flaky-update
    spec:
      containers:
        - name: flaky-update
          image: mluukkai/dwk-app8:v1
          readinessProbe:
            initialDelaySeconds: 10 # Initial delay until the readiness is tested
            periodSeconds: 5 # How often to test
            httpGet:
               path: /healthz
               port: 3541
```

if it does return 500 the update won't continue.



