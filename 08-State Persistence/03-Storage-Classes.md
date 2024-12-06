## PersistentVolume
- If I have PV for Google Disc , so anytime I deploy my app ,I need to create this disc first and this happens each time (Static provisioning of volumes)
- With storage classes I can define a provisoner such as google storage that can automatically provision storage on google and attach that to Pods when claim is made (Dynamic provisioning of volumes)
- This dynamically creates a PV BUT you don't have to create it
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
    name: google-sc
provisioner: kubernetes.io/gcp-pd
parameters:
    type: pd-standard |pd-ssd
    replication-type: none | regional-pd
```
- Can add parameters section(optional can be omitted) (its attributes are unique for each provsioner)
- And in the PVC specify storageClassName
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
    name: pvc
spec:
    accessModes:
        - ReadWriteOnce
    storageClassName: google-sc
    resources:
        requests:
            storage: 500Mi
```

- k get sc
- What is the name of the Storage Class that does not support dynamic volume provisioning? The storage class that does not support dynamic volume provisioning is typically associated with "static provisioning."
- An example of a storage class that does not support dynamic provisioning would be one that uses the "Retain" reclaim policy and does not have a provisioner specified. Here’s an example:
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: static-storage
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
volumeBindingMode: Immediate | waitForFirstConsumer

```

- volumeBindingMode field in a Kubernetes StorageClass specifies when the volume should be bound to the PersistentVolumeClaim (PVC). There are two possible values for volumeBindingMode:
1- Immediate:
- The volume binding occurs immediately after the PVC is created.
- Suitable for storage backends that can provision volumes quickly or where topology constraints (like node affinity) are not critical.
- The scheduler binds the PVC to a Persistent Volume (PV) right away, without waiting for the pod scheduling to occur. This can be useful for storage systems that support dynamic provisioning and have sufficient capacity available at all times.
2- WaitForFirstConsumer
- The volume binding is delayed until a pod using the PVC is scheduled.
- This mode helps with better placement of volumes in terms of topology constraints, such as node affinity or zone affinity.
- This helps improve scheduling efficiency, particularly in large clusters with multiple zones. By waiting until a pod is scheduled, the Kubernetes scheduler can ensure that the PV is created in the same zone as the pod, which can help optimize resource usage and reduce latency.


- The StorageClass used by the PVC uses WaitForFirstConsumer volume binding mode. This means that the persistent volume will not bind to the claim until a pod makes use of the PVC to request storage.
