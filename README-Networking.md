# Networking in Container Management

## Overview

//TODO

Kubernetes uses the [Container Networking Interface](https://github.com/containernetworking/cni) (CNI), a Cloud Native Computing Foundation project, consists of a specification and libraries for writing plugins to configure network interfaces in Linux containers, along with a number of supported plugins. CNI concerns itself only with network connectivity of containers and removing allocated resources when the container is deleted. Because of this focus, CNI has a wide range of support and the specification is simple to implement.

* [Kubernetes Networking Model](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

## Tools

[Plug-ins](https://github.com/containernetworking/cni#3rd-party-plugins)

**Weave Net**
  - Network partition management - mesh architecture & gossip protocol
  - Decentralized architecture; not reliant on external configuration and storage
  - Enterprise-grade Security - Encrypted network
    - Encryption between nodes with minimal performance loss
    - Distributed virtual firewall
    - Network Policy Controller
    - Container-to-container access rules
  - Service Discovery
    - micro DNS at each node, DNS lookups always local
  - Multicast Networking - UDP multcast even if underlying network doesn't support it