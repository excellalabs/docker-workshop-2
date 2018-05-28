# docker-workshop-ii

Getting started with deploying with Docker

### Setup

https://github.com/excellalabs/docker-workshop-2


1. Clone this sample container-based app, `git clone https://github.com/dockersamples/dockercoins`

1. Initializes cluster master node:

The master is the machine where the control plane components run, including etcd (the cluster database) and the API server (which the kubectl CLI communicates with).

To initialize the master, first choose the pod network plugin you want and check if it requires any parameters to be passed to kubeadm while initializing the cluster. Pick one of the machines you previously installed kubeadm on, and run:

kubeadm init will first run a series of prechecks to ensure that the machine is ready to run Kubernetes. It will expose warnings and exit on errors. It will then download and install the cluster database and control plane components.

[kubeadm](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/) - a toolkil that helps you bootstrap a best-practice Kube cluster in an easy, reasonably secure and extensible way. It's aim to to set up a minimum viable cluster that passes the [Kubernetes Conformance tests](https://kubernetes.io/blog/2017/10/software-conformance-certification/). 

Run: `kubeadm init --apiserver-advertise-address $(hostname -i)`

Watch for *Your Kubernetes master has initialized successfully!*

1. Take `kubeadm join...` from output, and run in other node to join

1. Initialize cluster networking:

`kubeadm` by design does not install a networking solution for you, which means you have to install a third-party CNI-compliant networking solution yourself using `kubectl apply`. It expects to be pointed to a machine to work on Run:

`kubectl apply -n kube-system -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"`

1. (Optional) Create an nginx deployment:

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/website/master/content/cn/docs/user-guide/nginx-app.yaml`

1. Run a pod

    1. Run an alpine image with a ping commnad via a pod: `kubectl run pingpong --image alpine ping 8.8.8.8`

    1. Run `kubectl get all`. The results show:

        we created deploy/pingpong - the deployment that we just created, a high-level construct which allows scaling, rolling updates, rollbacks, multiple deployments can be used together to implement a canary deployment, delegates pods management to replica sets

        our deployment created rs/pingpong-xxxx - a replica set created by the deployment, a low-level construct, makes sure that a given number of identical pods are running, allows scaling, rarely used directly
        
        the replica set created po/pingpong-yyyy - a pod created by the replica sets
    
1. Run `kubectl logs deploy/pingpong --tail 1 --follow` to see the output.

1. Scale by creating more copies of the pod: `kubectl scale deploy/pingpong --replicas 8`

1. See the resilience provided by the deployment, which will keep the number of replicas requested.

    1. In one terminal, run: `kubectl get pods -w`

    1. Watch the output of that while running this in another terminal: `kubectl delete pod pingpong-xxxx` filling in `xxxx` with a pod identifier.

1. Clean up your pods, `kubectl delete deploy/pingpong`

1. **Create a service** 

A service is a stable address for a pod/bunch of pods, used to connect to our pods. `kube-dns` will then allow us to resolve it by name. Different types of services include,

`ClusterIP` - a virtual IP address is allocated for the service (in an internal, private range) this IP address is reachable only from within the cluster (nodes and pods) our code can connect to the service using the original port number

`NodePort` - a port is allocated for the service (by default, in the 30000-32768 range) that port is made available on all our nodes and anybody can connect to it our code must be changed to connect to that new port number

`LoadBalancer` - external load balancer allocated for the service

`ExternalName` the DNS entry managed by kube-dns will just be a CNAME

1. **Run service with open port** 

    1. Start some elasticsearch containers, `kubectl run elastic --image=elasticsearch:2 --replicas=4`

    1. Watch them being started, `kubectl get pods -w`

    1. Create a ClusterIP service for the API port: `kubectl expose deploy/elastic --port 9200`

    1. Look at IP address allocated: `kubectl get svc`

    1. Get the IP address of the service: `IP=$(kubectl get svc elastic -o go-template --template '{{ .spec.clusterIP }}')`

    1. Send some requests: `curl http://$IP:9200/`

    1. Clean up, `kubectl delete deploy/elastic`


//todo: what do you do next when deployed? Monitoring, logging management?


### What is Kubernetes?

Kubernetes is a container management system. It runs and manages containerized applications on a cluster.

*Basic things we can ask Kubernetes to do*

Start x number of containers using a given image and place an internal load balancer in front of these containers. When needing to scale, we can easily grow our cluster and add containers. We can replace my containers with a new version of our image, and keep processing requests during the upgrade as we update containers one at a time.

*Other things that Kubernetes can do for us*

* Basic autoscaling
* Blue/green deployment, canary deployment
* Long running services, but also batch (one-off) jobs
* Overcommit our cluster and evict low-priority jobs
* Run services with stateful data (databases etc.)
* Fine-grained access control defining what can be done by whom on which resources
* Integrating third party services (service catalog)
* Automating complex tasks (operators)

#### Deploy an app

1. Create a deployment: `kubectl run hello-node --image=hello-node:v1 --port=8080`

1. View deployments, etc: `kubectl get [deployments | pods | events]`. View config: `kubectl config view`

1. Create a service to expose Pod outside Kubernetes virtual networK: `kubectl expose deployment hello-node --type=LoadBalancer`

1. View service: `kubectl get services`

1. Service is accessible on Minikube via `minikube service [SERVICE NAME]`

1. View logs: `kubectl logs [POD NAME]`

1. Update app: Rebuild image and then run `kubectl set image deployment/hello-node hello-node=hello-node:v2`

1. Clean up: to delete the service and deployment run: 

    ```sh
    kubectl delete service hello-node
    kubectl delete deployment hello-node
    ```

#### Deploy from Deployment Object

1. Deployments can be specified in a yaml file, see `/sample-kube-deployment-nginx.yaml`. You can run this deployment like this: 

    `kubectl apply -f sample-kube-deployment-nginx.yaml`

1. Display info about deployment: `kubectl describe deployment nginx-deployment`

1. List the pods created by the deployment: `kubectl get pods -l app=nginx`

1. Display information about a pod: `kubectl describe pod <pod-name>`
