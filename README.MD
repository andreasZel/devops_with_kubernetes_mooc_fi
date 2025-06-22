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

1. **POD**: a block that can contain one or more containers, kubernetes sees the POD, not the containers themselves ![PODS](./assets/PODS.png)
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
