## Workshop: Kubernetes Basics

We'll go through the core operations with Kubernetes, which include:

1. Setting up a cluster
1. Prepare an application to deploy
1. Create a deployment
    - scale
1. Create a service
    
## Setup: Set up a Kubernetes cluster

**Kubernetes Cluster Overview**

The master is the machine where the control plane components run, including etcd (the cluster database) and the API server (which the kubectl CLI communicates with).

To initialize the master, first choose the pod network plugin you want and check if it requires any parameters to be passed to kubeadm while initializing the cluster.

kubeadm init will first run a series of pre-checks to ensure that the machine is ready to run Kubernetes. It will expose warnings and exit on errors. It will then download and install the cluster database and control plane components.

[kubeadm](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/) is a toolkit that helps you bootstrap a best-practice Kube cluster in an easy, reasonably secure and extensible way. It's aim to to set up a minimum viable cluster that passes the [Kubernetes Conformance tests](https://kubernetes.io/blog/2017/10/software-conformance-certification/). 

1. Go to [Play with K8's](https://labs.play-with-k8s.com/) and log in. The servers have the Docker and Kubernetes tools installed but Kubernetes is not initialized.

1. Try listing Kubernetes resources with

    ```bash
    kubectl get all
    ```

    You'll see it errors since there is no Kubernetes cluster initialized.

1. Initialize cluster master node using `kubeadm`:

    1. **Run:**

        ```bash
        kubeadm init --apiserver-advertise-address $(hostname -i)
        ````

        Watch for *Your Kubernetes master has initialized successfully!*

    1. Check the status with `kubectl get componentstatus`

1. **Join another node to cluster**

    1. Copy the outputted block `kubeadm join...` from the init commnad
    
    1. Go into another node's terminal and run that command to join it to the cluseter

    1. See that it joined, `kubtcrl get nodes`

    1. Describe a node with `kubectl describe nodes node2`

1. **Initialize networking using Weave Net:**

    `kubeadm` by design does not install a networking solution for you, which means you have to install a third-party CNI-compliant networking solution yourself using `kubectl apply`. It expects to be pointed to a machine to work on. 

    Run: 
    
    ```bash
    kubectl apply -n kube-system -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
    ```

1. Inspect the Kubernetes cluster. Kubernetes functionality is delivered as a series of Kubernetes services (containing pods, containing containers). 

    1. Try to list the running containers via Kubernetes pods: `kubectl get pods`. Nothing shows up because it defaults in the default namespace, with has no pods. 

    1. List all namespaces: `kubectl get namespaces`

    1. Get the pods from the namespace with the Kubernetes services: `kubectl -n kube-system get pods` and you will see the pods that deliver the containers with the Kubernetes functionality.

    1. Change the default namespace with 
    
        ```
        kubectl config set-context my-context --namespace=mystuff
        kubectl config use-context my-context
        ```

    1. List services with `kubectl get services`

## Exercise: Basic deployment

1. We containered an app in Part 1, so we'll quickly spin that up and deploy it. Run:

    ```bash
    git clone https://github.com/excellalabs/docker-workshop-1
    ```

1. Change into the docker-workshop 1 directory and run `docker-compose up`

When it says it's serving, you should see a link for `80` next to the local IP, which you can click on for the public url. Add `/api/values` on the end and viola!

Just like that you have an app running online. We could leave it like this, but it would not be very stable. There are many additional things to cover when getting it production-ready.

### Need for an orchestrator 

- If the server reboots or Docker restarts, the container and app will shut down and not come back up. 
- We also have to consider how we will monitor the health of the service and its logs, among other services deployed.  
- We don't have a straightforward way to manage the scaling, deployment and communication of multiple containers across machines.

- Wrap containers with extra layer(s) for additional services such as self-healing, logging, deployment management, etc.

## Exercise: Create a deployment from a simgle image using kubectl

A deployment manages the desired state, such as for specifying the number of pods. The *reconciliation loop* makes the desired state the actual state.

The `kubectl run` command can create a deployment, which creates a pod (along with a ReplicaSet) with your container(s).

1. Createa a serivce:

    1. Run an alpine image with a ping commnad via a pod: `kubectl run pingpong --image alpine ping 8.8.8.8`

    1. Run `kubectl get all`. The results show:

        - we created deploy/pingpong - the deployment that we just created, a high-level construct which allows scaling, rolling updates, rollbacks, multiple deployments can be used together to implement a canary deployment, delegates pods management to replica sets

        - our deployment created rs/pingpong-xxxx - a replica set created by the deployment, a low-level construct, makes sure that a given number of identical pods are running, allows scaling, rarely used directly
        
        - the replica set created po/pingpong-yyyy - a pod created by the replica sets
    
1. Run `kubectl logs deploy/pingpong --tail 1 --follow` to see the output.

1. Scale by creating more copies of the pod: `kubectl scale deploy/pingpong --replicas 8`

1. Go to node2 and do `docker ps` to see all the containers running there.

1. Clean up your pods, `kubectl delete deploy/pingpong`

## Overview: Kubernetes Services

A service is a stable address for a pod/bunch of pods, used to connect to our pods. `kube-dns` will then allow us to resolve it by name. Services expose your pod externally.

`ClusterIP` - a virtual IP address is allocated for the service (in an internal, private range) this IP address is reachable only from within the cluster (nodes and pods) our code can connect to the service using the original port number

`NodePort` - a port is allocated for the service (by default, in the 30000-32768 range) that port is made available on all our nodes and anybody can connect to it our code must be changed to connect to that new port number

`LoadBalancer` - external load balancer allocated for the service

`ExternalName` the DNS entry managed by kube-dns will just be a CNAME

### Exercise: Run service with open port with kubectl

1. Start some elasticsearch containers, `kubectl run elastic --image=elasticsearch:2 --replicas=4`

1. Watch them being started, `kubectl get pods -w`

1. Create a ClusterIP service for the API port: `kubectl expose deploy/elastic --port 9200`

1. Look at IP address allocated: `kubectl get svc`

1. Get the IP address of the service: `IP=$(kubectl get svc elastic -o go-template --template '{{ .spec.clusterIP }}')`

1. Send some requests: `curl http://$IP:9200/`

1. Clean up, `kubectl delete deploy/elastic`

## Manifest files

### Exercise: Create a deployment from a manifest file

1. Create an nginx deployment & service via `Kubernetes spec`:

    This points to a spec describing a Nginx deployment and can be run via `apply`. Run: 

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/website/master/content/cn/docs/user-guide/nginx-app.yaml
    ```

    You can now navigate to the page the Nginx is serving.

1. Display info about deployment: `kubectl describe deployment my-nginx`

1. List the pods created by the deployment: `kubectl get pods -l app=nginx`

1. Display information about a pod: `kubectl describe pod <pod-name>`

## Next steps to production...

* Building images in a registry
* set up automated builds of container images from the code repo
* set up a CI pipeline using these container images
* set up a CD pipeline (for staging/QA) using these images
* Set up logging and monitoring

## Next Steps

* [Deploy a stateful app in Kubernetes - MySQL](https://kubernetes.io/docs/tasks/run-application/run-single-instance-stateful-application/#deploy-mysql)
* [Scenario exercises](https://www.katacoda.com/courses/kubernetes)
* [Kubernetes for Beginners interactive training](https://training.play-with-kubernetes.com/)
* [Kubernetes Cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet)
* [Kubernetes: Up and Running](http://shop.oreilly.com/product/0636920043874.do)