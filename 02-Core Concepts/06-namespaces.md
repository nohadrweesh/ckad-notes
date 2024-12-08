- Namespace --> Isolation
- default , kube-system ,kube-public
- Within the same namespace, each app could refer to other app by its name
  - db.connect("db-service") # if app & db_service are in same ns
  - db.connect("db-service.ns_name.svc.cluster.local") # if app & db_service are not in same ns
- When the resources create this DNS is created and attached
  - cluster.local --> default domain name
  - svc --> subdoamin for the service
  - namespace
  - service name
```
apiVersion: v1
kind: Namespace
metadata:
    name: dev
```
- k create ns namespace_name
- Switch to a different ns than the default
  - k config set-context $(k config current-context) --namespace=dev
- k get pods --ns dev
- k get pods --all-namespaces or k get pods -A

- Resource Quota: Limit Resources in a namespace
```
apiVersion: v1
kind: ResourceQuota
metadata:
    name: compute-resourcequota
    namespace: ns_name
spec:
    hard:
        pods: "10"
        requests.cpu: "4"
        requests.memory: 4Gi
        limits.cpu: "14"
        limits.memory: 14Gi
```
