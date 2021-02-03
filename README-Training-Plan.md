# Kubernetes Curricula

Follow through the appropriate curricula - for a developer or admin and the fundamentals for both, and each checkbox item should be completed. It starts with core knowledge, supplemented by hands-on learning. The CKAD or CKA for developers and admins respectively are good measures to conclude with, as they require real coding, however these curricula are for gaining a professional foundation of Kubernetes, beyond just passing the exams (For just passing the exams, use just the mentioned video or its CKAD nad CKS counterparts, Kubernetes Docs, and practice).
 
The curricula contained here are:

* Kubernetes Fundamentals (for all)
* Developers' Curriculum
* Admins' Curriculum

# Kubernetes Fundamentals (developers & admins)

- [ ] [Initial overview and architecture](https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes)
- [ ] Kubernetes: Up and Running (book)
- [ ] [CKA/CDAD video - fundamentals, first 7 lessons](https://learning.oreilly.com/videos/certified-kubernetes-administrator/9780136677482/9780136677482-CKA1_02_05_07)
- [ ] Read thru [Kubernetes docs](https://kubernetes.io/docs/)
- [ ] Review all terms & familiarize with core [add-ons, vendors, community members](https://www.cncf.io/projects/)
- [ ] [Hands-On Workshop Deploying to Kubernetes](README-Kubernetes.2.md)
- [ ] [Set up Minikube for lightweight local clusters](https://dzone.com/articles/minikube-cloud-code-vscode)

Additional Resources:
- Kubernetes Best Practices (book)
- [Kubernetes Exam Simulator](https://killer.sh/)

# Developers 

Ensure you understand the below core topics, and can do/have done the hands-on items.
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
- [ ] [Tutorials](https://kubernetes.io/docs/tutorials/) for the following: 
    - [ ] Configure a multi-service app to deploy in k8s cluster
    - [ ] Set up application secrets, load balancers, rate-limiters, auto-scaling
- [ ] CI/CD: set up Argo for CD/CD; Flux for cross-cluster GitOps
- Extend:
    - [ ] Create CRDs to extend functionality, api aggregation and web hooks
    - [ ] Create operator
- [ ] Auto-scaling pods
- [ ] Planning an implementation & implementation details

# Admins

## Core topics

Ensure you understand the below core topics, and can do/have done the hands-on items. Also go through the rest of the following video (or equivalent content), [CKA/CDAD video - fundamentals, lessons 8 through end](https://learning.oreilly.com/videos/certified-kubernetes-administrator/9780136677482/9780136677482-CKA1_02_05_07).

* [ ] Flavors you can use - public, on-prem, managed, local
* [ ] Core knowledge of Linux, each major piece, including networking, TLS and containerization concepts
* [ ] Creating a Kubernetes cluster - node, networking, etc requirements
* [ ] Managing Pods and Deployments
    - API access and commands
    - Running Pods using Deployments
    - Managing Storage
    - Managing Pod Networking
* [ ] Managing Kubernetes Clusters
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
- [ ] Scale nodes go through cross-node pod networking
- [ ] Set up CNI, CSI
- [ ] Create operator
- multi-node and multi-cluster
    - [ ] create multi-node clusters locally (i.e. vagrant/ansible, etc)
    - [ ] try kubernetes the hard way
- [ ] Planning an implementation & implementation details
