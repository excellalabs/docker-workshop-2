# Kubernetes Curricula

Follow through the appropriate curricula - for a developer or admin and the fundamentals for both, and each checkbox item should be completed. It starts with core knowledge, supplemented by hands-on learning. The CKAD or CKA for developers and admins respectively are good measures to conclude with, as they require real coding.
 
The curricula contained here are:

* Kubernetes Fundamentals (for all)
* Developers' Curriculum
* Admins' Curriculum

# Kubernetes Fundamentals (developers & admins)

- [ ] Initial overview and architecture:
    - https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes
    - Basic content - Up and Running (book), or free edX class https://www.edx.org/course/introduction-to-kubernetes
- [ ] Kubernetes: Up and Running (book)
- [ ] CKA/CDAD video that covers walkthrus of all fundamentals
- Review all terms & familiarize with core add-ons, vendors, community members
- [ ] Kubernetes Best Practices (book)
- [ ] [Hands-On Workshop Deploying to Kubernetes]()

# Developers 

## Core topics

* [ ] Core Concepts
    - API primitives
    - Create and configure basic pods
* [ ] Configuration
    - ConfigMaps, SecurityContexts
    - Define application's resource requirements
    - Create and consume Secrets
    - Understand ServiceAccoints
* [ ] Multi-Container Pods - patterns such as ambassador, adapter, sidecar
* [ ] Observability 
    - LivenessProbes and ReadinessProbes
    - Container logging
    - How to monitor apps
    - How to debug
* [ ] Pod Design
    - Working with Deployments and rolling updates and rollbacks
    - Jobs and CronJobs
    - Using Labels, Selectors, Annotations
* [ ] Services & Networking - using Services objects nad NetworkPolicies
* [ ] State Persistence - PersistentVolumeClaims

## Hands-on 

- [ ] Configure 
    - [ ] Helm, registry
    - [ ] fluentd for logging, jaeger for tracing, tracing with zipkin
    - [ ] Service Mesh set up and configure (Linkerd or Envoy/Istio)
- [ ] Set up local development workflow via Cloud Code > Skaffold
- [ ] Create & deploy microservice-based app in helm chart
    - [ ] Ingress, Service/Endpoint strategies 
- [ ] Configure a multi-service app to deploy in k8s cluster
    - [ ] set up application secrets, load balancers, rate-limiters, auto-scaling
- [ ] CI/CD: set up Argo for CD/CD; Flux for cross-cluster GitOps
- Extend:
    - [ ] Create CRDs to extend functionality, api aggregation and web hooks
    - [ ] Create operator
- [ ] Auto-scaling pods
- [ ] Planning an implementation & implementation details

# Admins

## Core topics

* [ ] Flavors you can use - public, on-prem, managed, local
* [ ] Creating a Kubernetes cluster [admin] - node, networking, etc requirements
* [ ] Managing Pods and Deployments
    - API access and commands
    - Running Pods using Deployments
    - Managing Storage
    - Managing Pod Networking
* [ ] Managing Kubernetes Clusters [admin]
    - Managing API Object Specifications, API extension, CRs
    - Managing scheduling
    - Managing security settings - API access, auth, authorization modes, security contexts, accounts
    - Managing networking - CNI, plugings, Kubernetes DNS, managing network policies, managing kubernetes networking
    - Managing cluster nodes & the etcd database
    - Logging, monitoring, and troubleshooting (including pod states, resource logs)
    - Performance tuning of kubernetes and etcd
    - Backups - etcd, etc
    - Performance tuning and configuration of Ingress Controller
    - Cluster upgrades, app & data migration across clusters 

## Hands-on

- [ ] How to organize app in kubernetes objects - labels, pods, services, etc    
- [ ] Standard patterns for infrastructure architecture
-
- [ ] Scale nodes go through cross-node pod networking
- [ ] Set up CNI, CSI
- [ ] Create operator
- multi-node and multi-cluster
    - [ ] create multi-node clusters locally (i.e. vagrant/ansible, etc)
    - [ ] try kubernetes the hard way
- [ ] Planning an implementation & implementation details
