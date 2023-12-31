- Controllers: ReplicaSet Controller
- ReplicaSet :
  - loadbalancing & scaling
  - make sure the required num of pods always runnig even any of them fails
- Replication Controller (Older) VS ReplicaSet
- ReplicaSet can manage pods that were not created as part of the replicaset creation 
- So ReplicaSet has selector field mandatory but ReplicationController has it optional and it it doesn't exist it assumes this as the labels in pod
- ReplicationController Yaml
```
apiVersion: v1
kind: RepliacationController
metadata:
    name: rc
    labels:
        app: frontend
spec:
    replicas: 3
    template:
        metadata:
            name: pod
            labels:
                app: frontend
        spec:
            containers:
            - name: nginx
              image: nginx
```

- ReplicaSet Yaml
```
apiVersion: apps/v1
kind: RepliacaSet
metadata:
    name: rc
    labels:
        app: frontend
spec:
    replicas: 3
    selector:
        matchLabels:
            app: frontend
    template:
        metadata:
            name: pod
            labels:
                app: frontend
        spec:
            containers:
            - name: nginx
              image: nginx
```
- k get rs/replicaset
- Scaling:
  - k scale --replicas 3 rc_name
  - k scale --replicas 3 rc_name_definition.yaml
  - Could also scale based on loads(danger)
- If I cahnged the ReplicaSet contianer image in its definition file and applied it ,(Surprise) it won't automatically delete the existing pods and recreate with the new image you have to either recreate the ReplicaSet or delete the pods