# KCAD-Notes

Main Benefits
* Orchestrate containers (better than docker compose)
* Zero Downtime Deployments
* Self healing powers
* Scaling

## Commands

kubectl version
kubectl cluster-info
kubectl get all  //Info on pods, deployment, services ++ 
kubectl run [container name] --image=[image-name]. 
kubectl port-forward [pod] [ports]  // exposing cluster ips and ports outside of the pod
kubectl expose ...  // exposing service features outside
kubectl create [resource] // creating resources
kubectl apply [resource] // update the status to the specified, doing changes as needed

## Pods

* Basic execution unit, smallest and simplest unit to create or deploy.
* Have IP, memory, volumes etc.  shared across containers
* Pods run containers.
* Pods are how to organize parts of an application
* Master Node runs pods on other nodes
  * Nodes can run multiple replicas within it
  * Each pod has own cluster IP
  * Pod containers share same network namespace and loopback so can easily communicate with each other
  * Multiple containers within same pod need to have unique IPs
    * Called *sidecar container* when they are closely linked and need to both start up together
    * 1 container per pod more common
  * Pods never span nodes, always in 1 node.

### Running

kubectl run [podname] --image=nginx:alpine
kubectl port-forward [podname] [ext port]:[int port] 
kubectl delete pod [podname]
kubectl delete deployment [name of deployment] //to stop pod coming back when using deployments


###

## Deployments

###

###

## Services

###

###

## Storage

###

###

## Configmaps and Secrets

###

###

## All together

###

###


