# Getting started deploying with Docker using Kubernetes

In this workshop, we will take a containerized application, and go through basic steps to deploy it.

We'll be using Kubernetes since it has become the de facto container orchestrator.

Following is a brief overview of container management and Kubernetes concepts. It is just to review for the workshop. Slides for an overview of container orchestration are [here](https://drive.google.com/file/d/1yaUfuSBkgzl9s7KcGLNogJX2if5w04ri/view?usp=sharing)

Then we'll review Kubernetes concepts as we go through the workshop exercises.

## Using an orchestrator

//TODO is this redundant to slides? 

No longer are applications always made up a a single process or a small number of processes across a handful of servers. They are being decomposed into individual services for a number of benefits. Different work comes from this, such as:

- Need to manage many services spread across many servers, in such a way that effectively utilizes resources
- Need automation in deploying containers to servers, configuring them, supervision and failure-handling

Kubernetes abstracts away the hardware infrastructure and can expose a cluster of servers as a single enormous computational resource, so you don't need to know about what's underneath. It's becoming the standard way to run distributed apps on premises and in the cloud.

We will set up Kubernetes and use it to deploy instead of Docker directly.

All orchestrators share core concepts around wrapping containers for more management, i.e.:

- Wrap containers with configuration for orchestration management. (ECS has task definition, Kubernetes has spec, Swarm uses additions to Compose).
    
    - Simplify communication between containers
    - Specify how to deploy containers for app across nodes
    - Set how app will scale on a container level
    - Set resource usage of containers

[Next - Kubernetes](README-Kubernetes.1.md)