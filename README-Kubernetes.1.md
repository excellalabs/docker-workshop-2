
## What is Kubernetes?

Kubernetes runs and manages containerized applications on a cluster. It is great at making multiple servers (i.e. kernels) look like one, so you can deploy and manage deployed containers.

*Basic things we can ask Kubernetes to do*

* Start x number of containers using a given image and place an internal load balancer in front of these containers. 

* When needing to scale, we can easily grow our cluster and/or add containers. We can replace containers with a new version of our image, and keep processing requests during the upgrade.

*Other things that Kubernetes can do for us:*

* Basic autoscaling
* Blue/green deployment, canary deployment
* Long running services, but also batch (one-off) jobs
* Overcommit our cluster and evict low-priority jobs
* Run services with stateful data (databases etc.)
* Fine-grained access control defining what can be done by whom on which resources
* Integrating third party services (service catalog)
* Automating complex tasks (operators)

## General concepts

Kubernetes uses persistent objects to manage its state, including:

  * what containerized applications are running, on which nodes
  * resources available to those applications
  * policies around how those applications behave (i.e. restart, upgrades, fault-tolerance)

Objects are a **record of intent** meaning once created, Kubernetes works to take this **desired state** to **actual state**. 

  *DIVE DEEPER*

  All objects have a *spec* and *status* field to record this state. They are created, deleted and updated through the Kubernetes (typically through the *kubectl* CLI), and the Kubernetes Control Plane manaages an object's actual state to match your supplied desired state.

**Kubernetes Control Plane**

The Control Plane consists of the follow processes. It carries out the work of getting the cluster to the desired state, via a collection of processes running on your cluster that respond to object changes.

  **Kubernetes Master**  

  The master is responsible to achieving the desired state of your cluster through these processes:

  - *kube-apiserver*, which *validates and configures objects*
  
  - *kube-controller-manager*, which *watches the state of the system through kube-apiserver (with control loops), and make necessary changes* though controllers
    
    I.E: node, replication, endpoints, service account and token controllers)
  
  - *kube-scheduler*, which *watches for newly created pods that aren't assigned to a node yet, and assigns them*
  
    This is based on individual and collective resource requirements, quality of service requirements, hardware/software/policy constraints, affinity and anti-affinity specifications, data locality, inter-workload interference, deadlines, and so on

  **Kubernetes non-master nodes**
    
  - kubelet, which communicates with the Kubernetes Master.
  - kube-proxy, a network proxy which reflects Kubernetes networking services on each node.

**Basic object types**

* Pod
* Service
* Deployment
* Volume
* Namespace

**Controllers**

They build on objects and provider additional functionality and convenience.

* ReplicaSet
* Deployment
* StatefulSet
* DaemonSet
* Job

## Common Objects

* Manifest (configuration) files:  Written in YAML or JSON, these files describe the desired state of your application in terms of Kubernetes API objects. A file can include one or more API object descriptions (manifests).

* Pods: This is the basic unit for all of the workloads you run on Kubernetes. It is a wrapper around a group of one or more containers with shared storage, network and a specification for how to run. It represents processes that would run on the same server in the pre-container world. Pods do act like a single server.

    The motivations for pods include 

    - simplfying application deployment and management
    - resource sharing and communication.

    Containers in a pod:

    - should be very tightly coupled
    - run on the same node and share Linux namespaces and cgroups
    - share a network namespace, communicate via localhost within the pod and share an IP address and port space
    - can communicate using standard inter-process communications like SystemV semaphores or POSIX shared memory
    - not recommended to put multiple application tiers in the same pod, as they often are scaled and distributed independently

    Colocation (co-scheduling), shared fate (e.g. termination), coordinated replication, resource sharing, and dependency management are handled automatically for containers in a pod.

    Common uses of multi-container pods are sidecar containers (helpers to the main container, often an application container):

    - content management systems, file and data loaders, local cache managers, etc.
    - log and checkpoint backup, compression, rotation, snapshotting, etc.
    - data change watchers, log tailers, logging and monitoring adapters, event publishers, etc.
    - proxies, bridges, and adapters
    - controllers, managers, configurators, and updaters

* Deployment:  The most common way of running X copies (Pods) of your application. Supports rolling updates to your container images.

* Service: By itself, a Deployment can’t receive traffic. Setting up a Service is one of the simplest ways to configure a Deployment to receive and loadbalance requests. Depending on the type of Service used, these requests can come from external client apps or be limited to apps within the same cluster. A Service is tied to a specific Deployment using label  selection.
  - Services have a cluster-wide IP, DNS name and port

*Additional concepts*

* Master - normally doesn't run containers, but the key Kubernetes services
* Nodes - non-master servers that run containers (in pods)
* Namespaces - logical grouping of cluster for use with multiple users or projects
* Labels - K/V pair for categorizing objects such as pods
* DaemonSets - ensures all (or some) nodes run a copy of a pod (i.e. for running a logs collection daemon on every node)
* Secrets - a safer place to store secrets than pods
* Volumes - share data between containers, persistent storage; beefier than Docker volumes, tied to life of 
* kubectl - main CLI tool
* kubeadm - CLI tool for managing Kubernetes clusters

[NEXT](README.2.md)
