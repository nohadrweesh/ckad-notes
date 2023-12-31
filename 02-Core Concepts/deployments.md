- Deployment includes replicaset thst includes pods
- So Beside maintaining the num of pods that Replicaset provices , Deplpoyment provides the ability to upgrade/downgrade versions using many rolling techniques
- Same defintion file as replicaset

```
apiVersion: apps/v1
kind: Deployment
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

- k create deployment deployment_name --image image_name --replicas 3