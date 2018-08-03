# Getting started deploying with Docker using Kubernetes

In this workshop, we will take a containerized application, and go through basic steps to deploy it.

We'll be using Kubernetes since it has become the de facto container orchestrator.

An overview of container orchestration could be given here, and here are [slides for that](https://drive.google.com/file/d/1yaUfuSBkgzl9s7KcGLNogJX2if5w04ri/view?usp=sharing)

## Using an orchestrator

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

## What is Kubernetes?

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

[Next](README.2.md)
