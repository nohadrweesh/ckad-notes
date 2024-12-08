## Deployments VS StatefullSets
- As an example : we need to deploy MYSQL DB (1 master server, 2 slave server)
1- First Setup master first then 2 slaves
2- Clone data from master to slave-1
3- Enable Continous Replication from master to slave-1
4- Wait for slave-2 to be ready
5- Clone data from slave-1 to slave-2
6- Enable Continous Replication from master to slave-2
7- Configure master address on slaves

- We need to deploy this Using K8s deployments
- We face some issues:
  - for step 1: we can't gurantee the order pods creation
  - for step 4: we can't wait for specific deployment pods
  - for rest of steps:we need to identify one pod to be master and others to be slaves (need static names)
- Solution StatefulSets:
  - It creates pod in sequentailly order (first pods needs to be created and running then the other is created) --> Fixes step 1,4
  - It assigns unique original index to each pod (starting from 0,1,...) so no random names(which exist in deployments), it keeps sticky identity for pods even if pod is destroyed and recreated --> Fixes other steps

- SO, we need statefulSets if app needs:
  - to come up in particaular order
  - to have sticky names

```
apiVersion: apps/v1
kind: StatefullSet
metadata:
    name: mysql
spec
    template:
        metadata:
            labels:
                app: mysql
        spec:
            container:
            - name: mysql
              image: mysql
    replicas: 3
    selector:
        matchLabels:
            app:mysql
    serviceName: mysql-h ##headless service
    podManagmentPolicy: parallel ##Optional to override the pod creation ordering
```
- Creating and deleting happens in order (creating: 0 - 1 - 2), (deleting 2  - 1 - 0) unless speciying podManagmentPolicy to be parallel