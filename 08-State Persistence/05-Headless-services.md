# Normal SVC VS Headless SVC
- Normal SVC : gets ClusterIP(10.96.5.1) assigned and a DNS(mysql.default.svc.cluster.local)
- In the previous MySQL example , it's okay to serve read from the svc endpoint (that could be frowarded to any slave or to master) but writes can only be served from master (to replicate to other slaves)
- So we need to reach master pod directly (not via IP as it changes) so we need a svc that deosn't load balance requests but gives us a DNS record
- SO Headless SVC doesn't load balance like nomral SVCs but it just creates DNS records

# Headless Service
- doesn't use default Cluster Ip address, Unlike a regular Kubernetes service, a headless service does not get assigned a cluster IP
  - in svc yaml set `clusterIP: None`
- used for direct communication between pods 
  - Direct pod access instead of accessing a single IP that load balances between them
  - Usedul for Staeful app in which I need tho know the excact pod I'm interacting with (lile master, slave-1 in mysql example)
- can also be used for providing DNS records for individual StatefulSet pods
  - when healess svc is created K8s creates A record or AAAA record for each pod that matches svc selector

- record would be as follows: pod_name-{indes}.healess_service_name.namespace.svc.cluster.local , like
```
my-app-0.my-headless-service.default.svc.cluster.local
my-app-1.my-headless-service.default.svc.cluster.local
my-app-2.my-headless-service.default.svc.cluster.local
```
## Use Cases:
- Stateful apps (like mysql example so dlave can call master)
- Service Discovery: to facilitate service discovery within a cluster, providing direct access to individual pods. This is especially useful in scenarios where applications need to perform client-side load balancing or maintain persistent connections to specific pods
- Custom Load Balancing: If you have a specific load balancing mechanism in place or need to handle request routing within your application, headless services provide the flexibility to manage pod communication without the default Kubernetes load balancing

- a headless service in Kubernetes allows for more granular control over pod communication by bypassing the default load-balancing behavior and providing direct DNS entries for each pod, which is particularly useful for stateful and distributed applications.

## Creation
```
apiVersion: v1
kind: Service
metadata:
    name: headless-svc
spec:
    ClusterIP: None ## headless
    ports:
        - port: 80
          targetPort: 80
    selector:
        matchLabels:
            app: my-app
```
- can attach the headless svc to a pod
```
apiVersion: v1
kind: Pod
metadata:
    name: myapp-pod
spec:
    containers:
    - name: mysql
      image: mysql
    subdomain: headless-svc
    hostname: mysql-pod
```
- this creates an A record for the pod named:  mysql-pod.headless-svc.default.svc.cluster.local
- I fI added these 2 properties (subdomain and hostname) in a Deployment pod template this ends up creating all the pods with same A records
- In StatedulSet we don't need to specify Pod subdomain and hostname , we will just add the serviceName and this takes care of creating A records with sutaible name for each pod