## SETUP: Create a Kubernetes cluster

**Kubernetes Cluster Overview**

The master is the machine where the control plane components run, including etcd (the cluster database) the API server (which the kubectl CLI communicates with) and the controller manager.

To initialize the master, first choose the pod network plugin you want and check if it requires any parameters to be passed to kubeadm while initializing the cluster.

kubeadm init will first run a series of pre-checks to ensure that the machine is ready to run Kubernetes. It will expose warnings and exit on errors. It will then download and install the cluster database and control plane components.

[kubeadm](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/) is a toolkit that helps you bootstrap a best-practice Kube cluster in an easy, reasonably secure and extensible way. It's aim to to set up a minimum viable cluster that passes the [Kubernetes Conformance tests](https://kubernetes.io/blog/2017/10/software-conformance-certification/).

## **EXERCISE: Set up the cluster**

1. Go to [Play with K8's](https://labs.play-with-k8s.com/) and log in. The servers have the Docker and Kubernetes tools installed but Kubernetes is not initialized.

    *NOTE: [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) is a great way to play with much of the Kubernetes functionality locally in a single node.*

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

1. Join another node to cluster

    1. Copy the outputted block `kubeadm join...` from the init commnad

    1. Go into another node's terminal and run that command to join it to the cluseter

    1. See that it joined, `kubectl get nodes`

    1. Describe a node with `kubectl describe nodes node2`

1. Initialize networking using Weave Net:

    `kubeadm` by design does not install a networking solution for you, which means you have to install a third-party CNI-compliant networking solution yourself using `kubectl apply`. It expects to be pointed to a machine to work on. 

    Run:

    ```bash
    kubectl apply -n kube-system -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
    ```

    [Read more about Networking Solutions](README-Networking.md)

1. Inspect the Kubernetes cluster. Kubernetes functionality is delivered as a series of Kubernetes services (containing pods, containing containers).

    1. Try to list the running containers via Kubernetes pods: `kubectl get pods`. Nothing shows up because it defaults in the default namespace, with has no pods. 

    1. List all namespaces: `kubectl get namespaces`

    1. Get the pods from the namespace with the Kubernetes services: `kubectl -n kube-system get pods` and you will see the pods that deliver the containers with the Kubernetes functionality.

    1. Now check out all the objects in the kube-system namespace on a default Kubernetes cluster by typing `kubectl -n kube-system get all`

## **EXERCISE: Basic deployment**

1. We containered an app in Part 1, so we'll quickly spin that up and deploy it. Run:

    ```bash
    git clone https://github.com/excellalabs/docker-workshop-1
    ```

1. Change into the docker-workshop 1 directory and run `docker-compose up`

When it says it's serving, you should see a link for `80` next to the local IP, which you can click on for the public url. Add `/api/values` on the end and viola!

Just like that you have an app running online. We could leave it like this, but it would not be very stable. There are many additional things to cover when getting it production-ready.

### Need for an orchestrator

- If the server reboots or Docker restarts, the container and app will shut down and not come back up.
- We don't have a straightforward way to manage the scaling, deployment and communication of multiple containers across machines.

*Wrap containers with extra layer(s) for additional services such as self-healing, logging, deployment management, etc.*

## Object management

`kubectl <operation> <object> <resource name> <optional flags>`

### Imperative vs declarative

| Management technique | Operates on | Recommended environment |	Supported writers |	Learning curve |
|---|---|---|---|---|
| Imperative commands | Live objects | Development projects | 1+ | Lowest |
| Imperative object configuration	| Individual files	| Production projects	| 1	| Moderate |
| Declarative object configuration	| Directories of files |	Production projects	| 1+	| Highest |

### Imperative Commands

`kubectl run nginx --image nginx`

This creates a Deployment. Do the same thing using a different syntax:

`kubectl create deployment nginx --image nginx`

### Declarative Commands

Create the objects defined in a configuration file:

`kubectl create -f nginx.yaml`

Delete the objects defined in two configuration files:

`kubectl delete -f nginx.yaml -f redis.yaml`

Update the objects defined in a configuration file by overwriting the live configuration:

`kubectl replace -f nginx.yaml`

## Kubernetes Services

A service is a stable address for a pod/bunch of pods, used to connect to our pods. `kube-dns` will then allow us to resolve it by name. Services give your pods a reliable networking endpoint, and expose your pod(s) externally.

The get an:

- IP address
- DNS name
- Port

`ClusterIP` - a virtual IP address is allocated for the service (in an internal, private range) this IP address is reachable only from within the cluster (nodes and pods) our code can connect to the service using the original port number. This is the default.

`NodePort` - wraps ClusterIP with a port that is allocated for the service (by default, in the 30000-32768 range). That port is made available on all nodes that can be connected to outside the cluster.

`LoadBalancer` - integrates NodePort with external load balancer

`ExternalName` - the DNS entry managed by kube-dns will just be a CNAME

## **EXERCISE: Expose an app via a Service**

1. Start some elasticsearch containers, `kubectl run elastic --image=elasticsearch:2 --replicas=4`

1. Watch them being started, `kubectl get pods -w`

1. Create a ClusterIP Service for the API port: `kubectl expose deploy/elastic --port 9200`

1. Look at IP address allocated: `kubectl get svc`

1. Get the IP address of the service: `IP=$(kubectl get svc elastic -o go-template --template '{{ .spec.clusterIP }}')`

1. Send some requests: `curl http://$IP:9200/`

## **EXERCISE: Rolling update**

We're going to update the elasticsearch image our containers are using, in this case just to a newer version, but it would work the same way if you updated code in your app and had a new version of an image to apply.

1. `kubectl set image deployment/elastic elastic=elasticsearch:5`

1. Clean up, `kubectl delete deploy/elastic`

## Kubernetes Deployments

A quick note about *Replication Controller*. They manage the desired state, such as the number of pods. Deployments take this to a higher level, and wrap *RelicaSets* to provide the above plus:

- Rolling updates
- Seamless rollbacks

You update the existing deployment and apply it, and Kubernetes creates a new ReplicaSet and spins up new pods and in conjunction removing the old. It keeps the old replica set revision, so if a rollback is needed it can easily spin up pods within there and remove the new ones.

## **EXERCISE: Create a deployment from a simple image using kubectl**

The `kubectl run` command creates a deployment, apart of which creates a pod (along with a ReplicaSet) with your container(s).

In general, users shouldn’t need to create pods directly. They should almost always use controllers even for singletons, such as Deployments.

1. Create a Deployment:

    1. Run an alpine image with a ping commnad via a pod: `kubectl run pingpong --image alpine ping 8.8.8.8`

    1. Run `kubectl get all`. The results show:

        - we created deploy/pingpong - the deployment that we just created, a high-level construct which allows scaling, rolling updates, rollbacks, multiple deployments can be used together to implement a canary deployment, delegates pods management to replica sets

        - our deployment created rs/pingpong-xxxx - a replica set created by the deployment, a low-level construct, makes sure that a given number of identical pods are running, allows scaling, rarely used directly

        - the replica set created po/pingpong-yyyy - a pod created by the replica sets

1. Run `kubectl logs deploy/pingpong --tail 1 --follow` to see the output.

1. Scale by creating more copies of the pod: `kubectl scale deploy/pingpong --replicas 8`

1. Go to node2 and do `docker ps` to see all the containers running there.

1. Clean up your pods, `kubectl delete deploy/pingpong`

## Manifest files

### EXERCISE: Deploy an app via a manifest file

1. Create an nginx deployment & service via `Kubernetes manifest`:

    1. Take a look at the following manifest file. This is a manifest file describing an Nginx deployment and can be run via `apply`.

        ```
        apiVersion: v1
        kind: Service
        metadata:
          name: my-nginx-svc
          labels:
            app: nginx
            zone: prod
            version: v1
        spec:
          type: NodePort
          ports:
          - port: 80
          selector:
            app: nginx
        ---
        apiVersion: apps/v1beta1
        kind: Deployment
        metadata:
          name: my-nginx
        spec:
          replicas: 3
          template:
            metadata:
              labels:
                app: nginx
            spec:
                containers:
                - name: nginx
                image: nginx:1.7.9
                ports:
                - containerPort: 80
        ```



    1. Run it directly from this file:

        ```bash
         kubectl apply -f https://raw.githubusercontent.com/excellalabs/docker-workshop-2/master/examples/deployment-service-pod-nginx.yaml
        ```

    You can now navigate to the page the Nginx is serving.

1. Display info about deployment: `kubectl describe deployment my-nginx`

1. List the pods created by the deployment via a label: `kubectl get pods -l app=nginx`

1. Display information about a pod: `kubectl describe pod <pod-name>`

## Next steps to production...

* Building images in a registry
* set up automated builds of container images from the code repo
* set up a CI pipeline using these container images
* set up a CD pipeline (for staging/QA) using these images
* Set up logging and monitoring

## Next Steps

* [Getting Started with Kubernetes - 2 hour course. Great architecture overview.](https://www.pluralsight.com/courses/getting-started-kubernetes)
* [Kubernetes: Up and Running - great book that goes deep](http://shop.oreilly.com/product/0636920043874.do)
* [Kubernetes Docs - Concepts, Tasks, Tutorials](https://kubernetes.io/docs/concepts/)
* [Deploy a stateful app in Kubernetes - MySQL](https://kubernetes.io/docs/tasks/run-application/run-single-instance-stateful-application/#deploy-mysql)
* [Scenario exercises](https://www.katacoda.com/courses/kubernetes)
* [Kubernetes for Beginners interactive training](https://training.play-with-kubernetes.com/)
* [Kubernetes Cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet)
* [Minikube for local playing](https://kubernetes.io/docs/tasks/tools/install-minikube/)