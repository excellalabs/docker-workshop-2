# Getting started deploying with Docker using Kubernetes

In this workshop, we will take a containerized application, and go through basic steps to deploy it.

We'll be using Kubernetes since it has become the de facto container orchestrator.

Following is a brief overview of container management and Kubernetes concepts. It is just to review for the workshop. Slides for an overview of container orchestration are [here](https://drive.google.com/open?id=150b-NPLbwBCTcecGoVon2LYWCD6aVh0Y)

Then we'll review Kubernetes concepts as we go through the workshop exercises.

This workshop can be self-guided.

## Using an orchestrator

No longer are applications always made up a a single process or a small number of processes across a handful of servers. They are being decomposed into individual services for a number of benefits. Different work comes from this, such as:

- Need to manage many services spread across many servers, in such a way that effectively utilizes resources
- Need automation in deploying containers to servers, configuring them, supervising and failure-handling

Kubernetes abstracts away the hardware infrastructure and can expose a cluster of servers as a single enormous computational resource, so you don't need to know about what's underneath. It's becoming the standard way to run distributed apps on premises and in the cloud.

We will set up Kubernetes and use it to deploy instead of Docker directly.

## Agenda

1. Setting up a cluster
1. Deploy a containerized application
    1. Create a deployment
    1. Create a service
    1. Scale our application
    1. Deploy application declaratively
1. Update the application

First, a quick review of Kubernetes...

[NEXT](README-Kubernetes.1.md)