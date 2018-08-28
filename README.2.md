## SETUP: Create a Kubernetes cluster

**Kubernetes Cluster Overview: kubeadm & kubectl**

The master is the machine where the control plane components run, including etcd (the cluster database) the API server (which the kubectl CLI communicates with) and the controller manager.

To initialize the master, first choose the pod network plugin you want and check if it requires any parameters to be passed to kubeadm while initializing the cluster.

kubeadm init will first run a series of pre-checks to ensure that the machine is ready to run Kubernetes. It will expose warnings and exit on errors. It will then download and install the cluster database and control plane components.

[kubeadm](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/) is a toolkit that helps you bootstrap a best-practice Kube cluster in an easy, reasonably secure and extensible way. It's aim to to set up a minimum viable cluster that passes the [Kubernetes Conformance tests](https://kubernetes.io/blog/2017/10/software-conformance-certification/).

kubectl is the main CLI you use to interface with the Kubernetes API and do most operations.

## **EXERCISE: Set up the cluster**

1. Go to [Play with K8's](https://labs.play-with-k8s.com/) and log in. The servers have the Docker and Kubernetes tools installed but Kubernetes is not initialized.

    *NOTE: [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) is a great way to play with much of the Kubernetes functionality locally in a single node.*

    *If Play with K8s is overloading/not working, you can use kubernetes.io's [interactive tutorial sandbox](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-interactive/) which already has a cluster set up.*


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

    1. Go back to the master node and see the new node that joined, `kubectl get nodes`

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

## Object management

`kubectl <operation> <object> <resource name> <optional flags>`

(Some operations include: create, get, delete, describe, rollout. Some objects include namespaces/ns, services/svc, deployments/deploy. replicaset/rs. pod)

### Imperative vs declarative

You want to to use declarative format (yaml) for production, so you can describe and version your infrastructure.

| Management technique | Operates on | Recommended environment |	Supported writers |	Learning curve |
|---|---|---|---|---|
| Imperative commands | Live objects | Development projects | 1+ | Lowest |
| Imperative object configuration	| Individual files	| Production projects	| 1	| Moderate |
| Declarative object configuration	| Directories of files |	Production projects	| 1+	| Highest |

### Imperative Commands

`kubectl run nginx --image nginx`

This creates a Deployment.

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

## **EXERCISE: Deploy an app via a Deployment**

1. Start some hello world containers, `kubectl run docker-hello-api --image=wyntuition/docker-hello-api:1 --replicas=3`

    The `kubectl run` command creates a deployment, apart of which creates a pod (along with a ReplicaSet) with your container(s).

1. Watch them being started, `kubectl get pods -w`

This command deployed your app by creating a pod to put your container in, and then a deployment to manage it in production, ensuring the desired number of replicas are available.

1. To illustrate that a Deployment keeps your desired state (i.e. number of instances running), delete a pod and see what happens. First list your pods:

    `kubectl get pods`

1. Now copy one of your pod's names, and delete it with:

    `kubectl delete <pod name>`

1. Now list your pods again, and see how the one you deleted in terminating, however a new one was spun up to get your cluster back to your specified desired state, `kubectl get pods`

## **EXERCISE: Expose your app via Service**

Now that we have an app deployed to Kubernetes via a Deployment, we want to make it accessible.

1. Create a ClusterIP Service for the API port: `kubectl expose deploy/docker-hello-api --port 8000`

1. Look at IP address allocated: `kubectl get svc`

1. Get the IP address: `IP=$(kubectl get svc docker-hello-api -o go-template --template '{{ .spec.clusterIP }}')`

1. Send some requests to it: `curl http://$IP:8000/`

Now the app is accessible from anywhere within the cluster, but we must do the following to expose it outside:

1. Expose the application externally: `kubectl expose deploy/docker-hello-api --name=docker-hello-api-2 --type=LoadBalancer --port=8000 --target-port=8000`

1. Check the External IP: `kubectl get svc docker-hello-api-2`

    On Play with K8, this will say *pending*, but with AWS and GKE, you will get an external IP address.

## **EXERCISE: Scale**

1. Scale by creating more copies of the pod via by declaraton in the manifest.

    `kubectl scale deploy/docker-hello-api --replicas 5`

1. Go to node2 and do `docker ps` to see all the containers running there.

## **EXERCISE: Rolling update**

We're going to update the image our containers are using to a newer one (which is on Docker Hub):

1. `kubectl set image deploy/docker-hello-api docker-hello-api=wyntuition/docker-hello-api:2`

1. Check the status of the updates with `kubectl describe deploy docker-hello-api`

1. Browse to the app again and see that it updated.

We should now remove our app, so we can deploy it again using the production-preferred method - declarative state.

1. Clean up Deployment, `kubectl delete deploy/docker-hello-api`

1. Clean up Service, `kubectl delete service docker-hello-api`

## Kubernetes Deployments

Deployments manage the desired state, such as the number of pods by wrapping *RelicaSets*. Additionally they provide:

- Rolling updates
- Seamless rollbacks

You update the existing deployment and apply it, and Kubernetes creates a new ReplicaSet and spins up new pods and in conjunction removing the old. It keeps the old replica set revision, so if a rollback is needed it can easily spin up pods within there and remove the new ones.

## Manifest files

## **EXERCISE: Deploy an app via a manifest file**

1. Create a  deployment & service via `Kubernetes manifest`:

    1. Take a look at the following manifest file. This is a manifest file describing a deployment of a hello world API and can be run via `apply`.

        ```
        apiVersion: v1
        kind: Service
        metadata:
        name: docker-hello-api
        labels:
            app: docker-hello-api
            zone: prod
            version: v1
        spec:
        type: LoadBalancer
        ports:
        - port: 8000
        selector:
            app: docker-hello-api
        ---
        apiVersion: apps/v1beta1
        kind: Deployment
        metadata:
        name: docker-hello-api
        spec:
        replicas: 3
        template:
            metadata:
            labels:
                app: docker-hello-api
            spec:
            containers:
            - name: docker-hello-api
                image: wyntuition/docker-hello-api:1
                ports:
                - containerPort: 8000
        ```



    1. Run it directly from this file:

        ```bash
         kubectl apply -f https://raw.githubusercontent.com/excellalabs/docker-workshop-2/master/examples/deployment-service-pod-hello-api.yaml
        ```

    The app is now deployed just like it was after you went through the last steps. You can now navigate to the page.

    Now you can examine your deployment in more depth:

    1. Display info about deployment: `kubectl describe deploy docker-hello-api`

    1. List the pods created by the deployment via a label: `kubectl get pods -l app=docker-hello-api`

    1. Display information about a pod: `kubectl describe pod <pod-name>`

    1. Watch the logs with `kubectl logs deploy/docker-hello-api --tail 1 --follow`

    1. Clean up your app by running a *delete* command against the manifest you used to deploy,

        `kubectl delete -f https://raw.githubusercontent.com/excellalabs/docker-workshop-2/master/examples/deployment-service-pod-hello-api.yaml`

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
* [Kubernetes API v1.11](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.11/)
* [Kubernetes Developer Guide](https://kubernetes.io/docs/community/devel/)