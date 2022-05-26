# KCAD-Notes

Main Benefits
* Orchestrate containers (better than docker compose)
* Zero Downtime Deployments
* Self healing powers
* Scaling

## Commands

* kubectl version 
* kubectl cluster-info 
* kubectl get all  //Info on pods, deployment, services ++ 
* kubectl get pods 
* kubectl get pod [podname] -o yaml. //or json
* kubectl get services
* kubectl run [container name] --image=[image-name]
* kubectl describe pod [podname].  // also shows events that happened to the pod
* kubectl port-forward [pod] [ports]  // exposing cluster ips and ports outside of the pod
* kubectl expose ...  // exposing service features outside
* kubectl create [resource] // creating resources
* kubectl apply [resource] // update the status to the specified, doing changes as needed
* kubectl delete pod [podname] 
* kubectl delete -f [yaml fil]
* kubectl create -f [yaml file] --dry-run --validate=true
* kubectl apply -f [yaml file] --save-config  //adds annotations about yaml that was last run to understand state
* kubectl edit -f [yaml file]  //lets you edit it in console
* kubectl patch  // patch a specific property
* kubectl exec [podname] [command e.g. sh] // run a command inside the image of the pod

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
    livenessProbe:
      httpGet:
        path: /index.html
        port: 80
      initialDelaySeconds: 15
      timeoutSeconds: 2
      periodSeconds:  5
      failureThreshold: 1
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


### Probes
Diagnostic that runes periodically

*Liveness* is it sick or health
*Readiness* is it ready to receive requests

Failed pod containers are restarted by default (can override)

*Types*
ExecAction - an action inside container
TCPSocketAction - check against container IP on a specified port
HTTPGetAction - against container

*Success/Fail/Unknown* 



## Deployments
Deployments and ReplicaSets ensure pods stay running and can be used to scale

kubectl get deployments
kubectl get deployment --show-labels
kubectl ger deployment -l [label name=value]
kubectl scale deployment [deployment-name] --replicas=[number of replicas]
kubectl scale -f [deployment filename] --replicas=[number of replicas]
e.g. kubectl scale deployment [deployment-name] --replicas=[number of replicas]

### ReplicaSets
- container is in a pod is in a ReplicaSet
- self healing mechanism that brings deleted pods back to life
- provides fault tollerance and scaling horizontalls
- relies on pod templates

### Deployments
- Manages ReplicaSets
- Allows Zero downtime updating by creating and deploying replicasets
- Supports rollback functionality
- Creates unique labels for replica sets and pods
- Similar Yaml to ReplicaSet

Selector using labels to find any templates that match those labels and use them
*Example*
```
apiVersion: apps/v1
kind: Deployment
metadata:
    name: frontend
    labels:
        app: my-nginx
        tier: frontend
spec:
  replicas:4 
  minReadySeconds: 10   # wait this long to confirm process really working before replacing existing version
  selector:
    matchLabels:
        tier: frontend
  template:
    metadata:
        labels:
          tier: frontend
    spec:
      containers:
        - name: my-nginx
          image: nginx:alpine
          ports:
           - containerPort: 80
```

### Rolling updates
Create new pod, replace one of old, repeat until all pods replaced.
This is default and automatic.


## Services
Cannot rely on IP addresses of pods, so need services.
Uses labels to associate Services and Pods
Layer 4 TCP/IP
Load balances between pods
Services are NOT ephemeral

### Types
#### ClusterIP 
expose service on a cluster-internal IP. (default)
only pods within the cluster can communicate to each other

#### NodePort 
Externally expose the service on each Node's IP at a static port (30000-32767 by default)
Proxies the allocated port to internal pod allocated port

#### LoadBalancer 
Provision an external IP to act as a load balancer for the service
Exposes externally
Useful with cloud providers load balancer
Nodeport and ClusterIP services created - Loadbalancer proxies that down to the nodes


#### ExternalName 
Maps a service to a DNS name or IP so pods are not exposed to the actual location of the external service

### Creating

Expose externally pods deployments or services : 
kubectl port-forward pod/[pod name] 8080:80
kubectl port-forward deployment/[deployment name] 8080:80
kubectl port-forward service/[service name] 8080:80

```
apiVersion:  v1
kind: Service
metadata:
  name: nginx
  labels: 
    app : nginx
  
spec: 
  type: ClusterIp # default?
  selector:
    app: nginx
  ports:
  - name: http
    port: 80
    targetPort: 80
```

```
....
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 31000 #optional as you get one dynamically .  same as port-forward command
```



```
....
metadata:
  name: external-service
spec:
  type: ExternalName
  externalName: api.acmecorp.com
  ports:
  - port: 80
```



....
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
```

```


Name of service in metadata section gives the service an internal DNS entry.  e.g. frontend could call a service named backend with "backend:port"


## Storage

###

###

## Configmaps and Secrets

###

###

## All together

###

###


