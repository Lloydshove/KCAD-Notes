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

