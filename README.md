# docker-workshop-ii
Getting started with deploying with Docker

1. [Minikube Hello World](https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/)

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
