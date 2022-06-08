```
docker image prune -a --filter “until=24h" 
```
Prunes all UNUSED docker images created in last 24 hours

---

```
kubectl create secret generic app-token --from-file=token=./secret-token.yaml
```
creates a Secret called app-token with the contents of the file ./secret-token.yaml stored in the token key of the object

---

KUBE_LOGGING_DESTINATION cntrols the log destination in kubernetes 

---

```
env:
  - name: POD_NAME
    valueFrom:
      fieldRef:
        fieldPath: metadata.name
```
Defines a POD_NAME env variable using the name of the pod

---

YAML is a superset of JSON, so a valid JSON file is a valid YAML file.

---

Stateful Sets are a resource that provides an ordered automated rolling update. 

---

Blue Green deployments give zero downtime, instant switchover and fast rollback.

---

kompose topic and applying output files

---

Service account creation topic

---

Ingress controller

---

Env Var files

---

Modifying secrets

---

StatefulSet

---

What property of a Pod do you set to provide configuration to the container it is running? 


---

When would you use ImagePullSecrets?

---

When connecting to the external IP (123.34.56.78) on Transmission Control Protocol (TCP) Port 80 you get a timeout. What would you do to solve the timeout?

Confirm the Pods are running for capture-order-api;
Check that the Dockerfile expose port matches the TargetPort in the service definition.

---

What property of a Pod do you set to provide configuration to the container it is running? 
Environment Variables

---

What is the general purpose of labels and selectors in Kubernetes?
To meaningfully identify objects for users but not by directly implying semantics to the core system


---

Given a deployment called mydeployment, which kubectl command sets up a port forward from your localhost on port 8888 to a Pod selected by mydeployment on port 5000?

kubectl port-forward **deployment**/mydeployment 8888:5000

---

Kubernetes design patterns!!

---


---


---



---

