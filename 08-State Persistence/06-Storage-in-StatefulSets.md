# Static Provisioning
- PV --> PVC --> use in Pod

# Dynamic Provsioning
- StorageCLass SC(that provsion Cloud Provider storage and this creates PV automatically) --> PVC --> use in Pod

# Storage in statefulSet
- we can have one PVC attached to all Pods (if provsioner allows that)
- If we need a separate PVC for each POD(like in MYSQL example) se we need PVC template which is added within statefulSet template `volumeClaimTemplates`
- If pod is deleted, recreated or rescheduled, k8s doesn't delete the associated PVC and it reattaches to the replacement POD
```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: redis-cluster
spec:
  serviceName: redis-service
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
        - name: redis
          image: redis:6.0
          volumeMounts:
            - name: data
              mountPath: /data
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi

```