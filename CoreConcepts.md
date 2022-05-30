# KCAD-Notes

Main Benefits
* Orchestrate containers (better than docker compose)
* Zero Downtime Deployments
* Self healing powers
* Scaling

Notes based on : https://github.com/DanWahlin/DockerAndKubernetesCourseCode

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

### Volumes 
Store data and state for pods and containers
Multiple volumes per pod
Containers have mountPath to access volumes


#### Types

* emptyDir - shares a pods lifetime.  Share between containers in same Pod
* hostPath - pod mounts onto the nodes filesystem
* nfs - an NFS share mounted into the pod
* configMap/secret - special volumes for pod to accesss Kubernetes resources
* persistenVolumeClaim - more persisten storage abstracted from the details
* Cloud - cluster wide storage


*emptyDir example*

share data between containers inside of same pod only for lifetime of pod
```
apiVersion: v1
kind: pod
spec:
  volumes:
    - name: html
      emptyDir: {}
  containers: 
  - name: nginx
    image: nginx:alpine
    volumeMounts:
      - name: html
        mountPath: /usr/share/nginx/html
        readonly: true
  - name: html-updated
    image: alpine
    command: ["/bin/sh", "-c"]
    args:
      - while true; do date >> /html/index.html;
          sleep 10; done
    volueMounts:
      - name: html
        mountPath: /html      
        
```

*hostPath of Socket hostPath Type volume example file example*

share data on a directory on a host where a pod happens to be running.  Danger pod might be rescheduled to a different node on restart.
```
apiVersion: v1
kind: pod
spec:
  volumes:
    - name: docker-socket
      hostPath:
        path: /var/run/docker.sock
        type: Socket
    containers:
    - name: docker
      image: docker
      command: ["sleep"]
      args: [100000"]
      volumeMounts: 
        - name: docker-socket
          mountPath: /var/run/docker.sock

```

*azureFile example*
```
apiVersion: v1
kind: pod
metadata:
  name: my-pod
spec:
  volumes:
    - name: data
      azureFile:
        secretName: <azure-secret>
        shareName: <share-name>
        readOnly: false
  containers:
  - image: someimage
    name: my-app
    volumeMounts:
    - name: data
      mountPath: /data/storage
```

### PersistentVolumes
* cluster wide storage provisioned by admin with lifecycle independent from the pod
* need some sort of cloud or network attached storage for sharing
* available to pod after restarts/rescheduling
* relies on a storage provider
* is registered with the Kubernetes API


### PersistenVolumeClaims
* request for storage unit of the persistent volume.  claim used in pod to talk to persistent storage.
* kubernetes binds claim to persistent volume


*Example defining azure PVC*
http://github.com/kubernetes/examples

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity: 10Gi
  accessModes:
    - ReadWriteOnce
    - ReadeOnlyMany
  persistentVolumeReclaimPolicy: Retain #dont delete if claim deleted
  azureFile:
    secretName: <azure-secret>
    shareName: <name_from_azure>
    readOnly: false
```

*Example PVClaim*
```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pv-dd-account-hdd-5g
  annotations:
    volume.beta.kubernetes.io/storage-class: accounthdd
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  
```

*Example claim usage in Pod*
```
kind: Pod
apiVersion: v1
...
spec:
  containers:
  - image: nginx
    name: az-c-01
    command:
    - #do something
    volumeMounts:
    - name: blobDisk01. # match name of volume pointing to volume claim
      mountPath: /mnt/blobdisk
  volumes:
  - name: blobdisk01
    persistentVolumeClaim
      claimName: pv-dd-account-hdd-5g. # matching PVC name

```



### StorageClasses
* SC - type of storage template used to dynamically provision storage
* This is dynamic.  Instead of the PV/PVC exmples above that are static
* DynamicProvisioning - Admin sets up SC.  Pod claims using PVC.  PV is dynamically provisioned by SC

* Example Storage Class*
```
apiVersion: sorage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
reclaimPolicy: Retain
provisioner: #different storage type provisioners.  Can have statis provisioning if chossing a static provisioner
volumeBindingMode: WaitForFirstConsumer #Default is immediate.  
```

*Example PVC of storage class*
```
kind: persistenVolumeClaim
metadata: 
  name: my-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: local-storage
  resources:
    requests:
      storage: 1Gi
```

*example pod/ /  using PVC and Storage Class*
```
apiVersion: apps/v1
kind: [Pod | StatefulSet | Deployment]
...
  spec:
    volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: my-pvc
```

## StatefulSets
Stateful type of pod with
- predictable name
- guarantees ordering and uniqueness of pods

some state features...?

## Configmaps and Secrets
* Part of storage 
* Pods use to get data into containers

### Configmaps
* Way to store config info and provide it to containers
* Accessible throughout the cluster
* Inject config data into container
* Can store entire files
  * Filename is key, content is value
  * Can pass on command line
  * ConfigMap manifest file
* Accessed by pod from env variables 
OR 
* ConfigMap Volume (access files)  

#### Creating

*Example ConfigMap*
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-settings
  labels:
    app: app-settings
data:
  enemies: aliens
  lives: "3"
  enemies.cheat: "true"
  enemies.cheat.level=noGoodRotten
```

kubectl create -f file.configmap.yml

*Example key/value pairs file*
```
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
```
kubectl create configmap [cm-name] --from-file=[path to file]

*But* key values created a little different.  file name becomes the key. 


*Example using environment file*
Same as properties but... different command and NO filename as key and under data property
kubectl create configmap [cm-name] --from-env-file=[path to file]

or

kubectl create configmap [cm-name] --from-literal=<key>=<value>. --from-literal=[more examples....]
  
#### Using

kubectl get cm [cm-nam] -o yaml
  
*Example using env-vars for single variables*
```
apiVersion: apps/v1
...
  
spec:
  template:
    ...
  spec:
    containers: ...
    env:
    - name: Enemies     # ENV Variable name
      valueFrom:   # Value instead of ENV
        configMapKeyRef:
          name: app-settings   #ConfigMap name
          key: enemies    #Key in ConfigMap
```

*Example using env vars loading hole ConfigMap contents*
```
apiVersion: apps/v1
...
  
spec:
  template:
    ...
  spec:
    containers: ...
    env:
    - name: Enemies     # ENV Variable 
      envFrom:    #ENV instead of Value
        configMapKeyRef:
          name: app-settings   #ConfigMap name
```


*Example accessing configMap Volume*
each value maps to a file with name of key and content of value.  Load the files ourselves.
```
apiVersion: apps/v1
...
  
spec:
  template:
    ...
  spec:    
    volumes:
    - name: app-config-vol
      configMap:
        name: app-settings #matches the configMap name
    containers:
      volumeMounts:
        - name: app-config-vol
          mountPath: /etc/config       
```
  
kubectl delete cm [configMap name]  
  
  
### Secrets
* An object with small amount of sensitive data.
* Can securely make it available to pods and containers in pods.
* No secrets in container images/files/deployment manifests
* Can mount secrets into pods as files 
  or
* As Env variables
* Only pods that need the secrets get access to them  
* Stored in tmpfs on the node so not on disk
* stored on master node /etcd
* SSL/TLS for etcd peer to peer
* Manifests are not encrypted, just encoded
* Role based access control RBAC to secure who can create pods  

#### Creating

kubectl create secret generic my-secret --from-literal=pwd=my-password    #save literal password
kubectl create secret generic my-secret --from-file=ssh-privatekey=~/.ssh/id_rsa -- from-file=ssh-publickey=~/.ssh/id_rsa.pub
kubectl create secret tls tls-secret --cer=path/to/tls.cert --key=path/to/tls/key
  
*Example secrets yaml but only base64 encoded*
```
apiversion: v1
kind: Secret
metadata:
  name: db-paswords
type: Opaque
data:
  app-password:  <some base 64 encoded>
  admin-password: <another base 64 encoded>
```
  
#### Using
kubectl get secrets
kubectl get secrete [secret name] -o yaml     #come out base64 encoded

  
*Example secret into single env variable*  
```
  apiVersion: apps/v1
  ..
  spec:
    template:
      ...
    spec:
      containers: 
         ...
      env:
      - name: DATABASE_PASSWORD
        valueFrom:
          secretKeyRef:
            name: db-passwords
            key: db-password
```

  
*Example secrets file into volume mount*  
```
  apiVersion: apps/v1
  ..
  spec:
    template:
      ...
    spec:
      Volumes:
      - name: secrets
        secret:
          secretName:db-passwords
      containers: 
       ...
        volumeMounts:
          - name: secrets
            mountPath: /etc/db-passwords
            readOnly: true
```
  
  
## All together

###

###


