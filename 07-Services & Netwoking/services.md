# Services
- it enables communication between differen svcs
- Example:
  - If I have a node in my computer and it has a running pod in it, how can I access the pod from my laptop?
  - I can't use the pod ip because it's an internal network ip (on the node)
  - I can ssh into the node (using node ip) then call the pod
  - So using a service that expose the pod on specific port --> then we can access the pod on node_id:node_port

## NodePort
- This is NodePort Service:makes the internal port accessible on a port in the node
- It enable external access 
- SVC is like a virtual server inside the node and it has its own ip address called the cluster ip of the svc
- It includes 3 ports, named on the viewpoint of the svc:
  - targetPort: Port on the pod where the actual app (web server) is running --> 80 for nginx and its where the svc forward the request to
  - port: port on the svc itself
  - nodePort: port in the node itself , we use it to access the pod(web server) externally

```
apiVersion: v1
kind: Service
metadata:
    name: svc-name
spec:
    type: NodePort
    ports:
       - targetPort: 80
         port: 80
         nodePort: 30008
    selector:
        app: frontend
```

- we can have multiple ports mapping
- the only required field is port, if targetPort is not set --> it's set to targetPort value, if nodePort is not set --> it's set to any valid value from 30000:32767
- After creating the service can use svc_ip:nodeport to access it
- If the svc selects pods that are on different nodes in the cluster, k8s will creates a svc that spans different nodes and make it accessble on the same nodeport , so we can use node_$i_ip:svc_nodeport --> is accessable
## ClusterIP
- The svc create virtual IP inside the cluster to enable communication between differetn svc (like frontend and backend)

## LoadBalancer
- It provsions a LB in supported  cloud service provider