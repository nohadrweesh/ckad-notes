## Problem with Volumes
- We comfigure volume and its configuration pod definitipon file
- In large env we aill have multiple pods and multiple volumes --> we need to control volumes centrally (not related to pods)
- We want Adminstrator to create a large pool of storages and users then can use it --> PVC

## PersistentVolume
```
apiVersion: v1
kind: PprsistentVolume
metadata:
    name: pv
spec:
    accessModes:
        - ReadOnlyMany
        - ReadWriteOnce
        - ReadWriteMany
    capacity:
        storage: 1Gi
    hostPath:
        path: /tmp


```
- accessMode --> how the volume is mounted on host
- capacity.storage --> reserved for this volume
- volume type --> like hostPath(this type not recommend in prod) or awsElasticBlockStore

## Persitent Volume Claim
- Create the objects (PV & PVC)
- PVC should be bound to suitable PV in terms of capacity, accessmode,storage class --> if no suitable PV found ,PVC remain in Pending State
- Even if PV storage capacity is larger than PVC , the ramining capacity won't be assigned to another PVC, it's 1-1 relation

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
    name: pvc
spec:
    accessModes:
        - ReadWriteOnce
    resources:
        requests:
            storage: 500Mi
```

- pvc state: Pending --> Bound
- When PVC is deleted , what happens to the attached PV? tis depends on the policy persistentVolumeReclaimPolicy in the PV defintion
persistentVolumeReclaimPolicy: Retain | Delete | Recycle
    - Retain : it exist till admin del it
    - Recycle: data is scrubbed(deleted) but the PV is availble to be bound again

## Using PVC in pod
```
apiVersion: v1
kind: Pod
metadata:
    name: backend-pod
spec:
    containers:
        - name: nginx
          image: nginx
          volumeMounts:
            - name: data-volume
              mountPath: /var/www/html
    volumes:
        - name: data-volume
          persistentVolumeClaim:
            claimName: pvc

```
- can't change the pvc accessmodes after pvc is created (need to del and recreate)
- PV state: Available --> Bound --> Released (if pvc is deleted)
- If pvc(50) and pv(100) then whole 100 is available for the PVC
- If pvc is used in pod and tried deleting the pvc --> it will stuck in terminating state