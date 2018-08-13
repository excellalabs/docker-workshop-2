# Networking in Container Management

## Overview
Kubernetes uses CNI as an interface between network providers and Kubernetes networking.  The [Container Networking Interface](https://github.com/containernetworking/cni) (CNI), a Cloud Native Computing Foundation project, consists of a specification and libraries for writing plugins to configure network interfaces in Linux containers, along with a number of supported plugins. CNI concerns itself only with network connectivity of containers and removing allocated resources when the container is deleted. Because of this focus, CNI has a wide range of support and the specification is simple to implement.

* [Kubernetes Networking Model & CNI Plug-ins](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

**CNI Reference Plugins**

**bridge**: Creates a bridge, adds the host and the container to it.
**ipvlan**: Adds an ipvlan interface in the container.
**loopback**: Set the state of loopback interface to up.
**macvlan**: Creates a new MAC address, forwards all traffic to that to the container.
**ptp**: Creates a veth pair.
**vlan**: Allocates a vlan device.

**CNI-Genie from Huawei**

CNI-Genie is a CNI plugin that enables Kubernetes to simultaneously have access to different implementations of the Kubernetes network model in runtime. This includes any implementation that runs as a CNI plugin, such as Flannel, Calico, Romana, Weave-net

![With CNI Genie](https://raw.githubusercontent.com/Huawei-PaaS/CNI-Genie/master/docs/multiple-cni-plugins/what-cni-genie.png)

## CNI 3rd party Plug-ins Overview

**Weave-net**
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
  - 
**Flannel**

**Calico**

**Romana**