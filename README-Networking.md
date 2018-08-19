# Networking in Container Management

## Overview
Kubernetes approaches networking somewhat differently than Docker does by default. There are 4 distinct networking problems to solve:

  - Highly-coupled container-to-container communications: this is solved by pods and localhost communications.
  - Pod-to-Pod communications
  - Pod-to-Service communications: done using services.
  - External-to-Service communications: done using services.

Pods can be treated much like VMs or physical hosts from the perspectives of port allocation, naming, service discovery, load balancing, application configuration, and migration.  This means that you do not need to explicitly create links between pods and you almost never need to deal with mapping container ports to host ports.  Containers are addressed with routable IP addresses that are on the subnet of the external network.  

This model is not only less complex overall, but it is principally compatible with the desire for Kubernetes to enable low-friction porting of apps from VMs to containers. If your job previously ran in a VM, your VM had an IP and could talk to other VMs in your project. This is the same basic model.

***Kubernetes imposes the following fundamental requirements*** on any networking implementation (barring any intentional network segmentation policies):
  - all containers can communicate with all other containers without NAT
  - all nodes can communicate with all containers (and vice-versa) without NAT
  - the IP that a container sees itself as is the same IP that others see it as

## CNI Interfaces

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
Try it out: `kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"`
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

**Flannel**

Try it out: `kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml`
  - Focused on networking. For network policy, other projects such as Calico can be used
  -  Main advantage of this model is that it removes the port mapping complexities that come from sharing a single host IP
  - Does not control how containers are networked to the host, only how the traffic is transported between hosts
  - Service Discovery
    - Runs a small, single binary agent called `flanneld` on each host, and is responsible for allocating a subnet lease to each host out of a larger, preconfigured address space
    - uses either the Kubernetes API or etcd directly to store the network configuration, the allocated subnets, and any auxiliary data (such as the host's public IP)

**Calico**

Try it out: `kubectl apply -f \
https://docs.projectcalico.org/v3.2/getting-started/kubernetes/installation/rbac.yaml`

  - Enables networking and network policy
  - Secure component communication: can configure etcd to encrypt its communications with TLS and require clients to present certificates signed by the etcd certificate authority.
  - Can configure BGP peers, IP Pools, use of IPV6 and IPVS, IP-in-IP, Node IP and Subnet, etc.

**Romana**

Try it out: `kubectl apply -f https://raw.githubusercontent.com/romana/romana/master/containerize/specs/romana-kubeadm.yml`

  - Network and security automation solution for cloud native applications
  - Automates the creation of isolated cloud native networks and secures applications using microsegmentation and enforces access control policies on all endpoints, wherever they run
  - Enables fine-grained access control policies to be applied to all application traffic
  - Network agnostic and runs on layer 2 LANs and VLANs as well as IP fabrics running VXLANs, layer 3 networks and public cloud networks like Amazon’s VPC
  - Only CNI provider that lets HA clusters split across zones apply policy to native VPC networks. Route aggregation avoids VPC route limitations, enabling native VPC networking on large clusters