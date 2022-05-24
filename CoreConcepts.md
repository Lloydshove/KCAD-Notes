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
kubectl get pods
kubectl get pod [podname] -o yaml. //or json
kubectl get services
kubectl run [container name] --image=[image-name]
kubectl describe pod [podname].  // also shows events that happened to the pod
kubectl port-forward [pod] [ports]  // exposing cluster ips and ports outside of the pod
kubectl expose ...  // exposing service features outside
kubectl create [resource] // creating resources
kubectl apply [resource] // update the status to the specified, doing changes as needed
kubectl delete pod [podname] 
kubectl delete -f [yaml fil]
kubectl create -f [yaml file] --dry-run --validate=true
kubectl apply -f [yaml file] --save-config  //adds annotations about yaml that was last run to understand state
kubectl edit -f [yaml file]  //lets you edit it in console
kubectl patch.  // patch a specific property
kubectl exec [podname] [command e.g. sh] // run a command inside the image of the pod

## YAML
```
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx
  labels: 
   app: nginx
   rel: stable
spec:
  containers:
  - name: my-nginx
    image: nginx:alpine
    ports:
    - containerPort : 80
```

--save-config adds annotations to the top of the yaml applied adding detail of the yaml applied.

Labels

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


