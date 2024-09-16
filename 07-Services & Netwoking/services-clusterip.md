## ClusterIP
- the default svc type
- The svc create virtual IP inside the cluster to enable communication between differetn svc (like frontend and backend and db)

```
apiVersion: v1
kind: Service
metadata:
    name: backend-svc
spec:
    type: ClusterIP
    ports:
       - targetPort: 80
         port: 80
    selector:
        app: backend
```
- each group of pods (backend VS frontend VS DB) can refer to other group svc by its clusterip or its name