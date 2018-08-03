# Getting started with deploying with Docker

In this workshop, we will take a containerized application, and go through basic steps to deploy it.

We'll be using Kubernetes since it has become the de facto container orchestrator.

An overview of container orchestration could be given here, and here are [slides for that](https://drive.google.com/file/d/1yaUfuSBkgzl9s7KcGLNogJX2if5w04ri/view?usp=sharing)

### Part 1: Basic deployment

1. Navigate to [Play with K8s](https://labs.play-with-k8s.com/), which gives us servers temporarily that we can set up in a Kubernetes cluster. It has Docker and Kubernetes tools installed already.

1. To use the app we containerized in Part 1, we'll quickly spin up the hello world application we containerized. Run:

    ```bash
    git clone https://github.com/excellalabs/docker-workshop-1
    ```

1. Change into the docker-workshop 1 directory and run `docker-compose up`

When it says it's serving, you should see a link for `80` next to the local IP, which you can click on for the public url. Add `/api/values` on the end and viola!

Just like that you have an app running online. We could leave it like this, but it would not be very stable. There are many additional things to cover when getting it production-ready.
    
- If the server reboots or Docker restarts, the container and app will shut down and not come back up. 
- We also have to consider how we will monitor the health of the service and its logs, among other services deployed.  
- We don't have a straightforward way to manage the scaling, deployment and communication of multiple containers across machines.

### Using an orchestrator

No longer are applications always made up a a single process or a small number of processes across a handful of servers. They are being decomposed into microservices for a number of benefits. Different work comes from this, such as:

- Need to manage many services spread across many servers, in such a way that effectively utilizes resources
- Need automation in deploying containers to servers, configuring them, supervision and failure-handling

Kubernetes abstracts away the hardware infrastructure and exposes your whole datacenter as a single enormous computational resource. You don't need to know about the servers underneath. It's becoming the standard way to run distributed apps on premises and in the cloud.

We will set up Kubernetes and use it to deploy instead of Docker directly.

All orchestrators share core concepts around wrapping containers for more management, i.e.:

- Wrap containers with configuration for orchestration management. (ECS has task definition, Kubernetes has spec, Swarm uses additions to Compose).
    - Simplify communucation between containers, I.E. give one IP address to all related containers
    - Specify how to deploy containers for app across nodes
    - Set how app will scale on a container level
    - Set resource usage of containers

- Wrap containers with extra layer(s) for additional services such as self-healing, logging, deployment management, etc.

### What is Kubernetes?

Kubernetes is a container management system. It runs and manages containerized applications on a cluster.

*Basic things we can ask Kubernetes to do*

Start x number of containers using a given image and place an internal load balancer in front of these containers. When needing to scale, we can easily grow our cluster and add containers. We can replace containers with a new version of our image, and keep processing requests during the upgrade as we update containers one at a time.

*Other things that Kubernetes can do for us:*

* Basic autoscaling
* Blue/green deployment, canary deployment
* Long running services, but also batch (one-off) jobs
* Overcommit our cluster and evict low-priority jobs
* Run services with stateful data (databases etc.)
* Fine-grained access control defining what can be done by whom on which resources
* Integrating third party services (service catalog)
* Automating complex tasks (operators)

*Kubernetes concepts:*

* Master - normally doesn't run containers, but the key Kubernetes services
* Nodes - non-master servers that run containers (in pods)
* Namespaces - logical grouping of cluster for use with multiple users or projects
* Labels - K/V pair for categorizing objects such as pods
* Pods - wraps container(s)
* DaemonSets - ensures all (or some) nodes run a copy of a pod (i.e. for running a logs collection daemon on every node)
* Deployments - manages the desired state, i.e. specify number of pods
* Services - allows external pod communication
* Secrets - a safer place to store secrets than pods
* Volumes - share data between containers, persistent storage; beefier than Docker volumes, tied to life of 
* kubectl - main CLI tool
* kubeadm - CLI tool for managing Kubernetes clusters

