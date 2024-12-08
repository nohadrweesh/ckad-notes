## Resources VS Custom Resources:
- Resources like deployment : we can create/list/Update/Delete deployments in etcd 
- But we know that creating a deployment object actually creates a replica set which in turns creates pods (in specific number)-> who creates the pods?
- This is the fucntion of controller (deployment controller) which is a background process which continuously monitor the status of resources it manages and changes the necessary parts (create or rerun pod)
- Each resource has equivalent Controller


## Custom Resource
- want to create Object with type FlightTicket like
```
apiVersion: flights.com/v1
kind: FlightTicket
metadata:
    name: my-flight-ticket
spec:
    from: Cairo
    to: London
    number: 2
```
- what we need to is:
  - to create the FlightTicket resources in etcd and can use commnads like k get flightticket
  - make some logic like make api calls to book-tickets.com 
- The scond thing needs a Controller to `monitor` etcd and based on it `change` the actual api calls
- The first thing can be achieved by creating custom resource definition(CRD)

```
apiVersions: apiextentions.k8s.io
kind: CustomResourceDefintion
metadata:
    name: flighttickets.flights.com
spec:
    scope: Namespaced
    group: flights.com
    names:
        singular: flightticket
        plural: flighttickets
        shortNames:
        - ft
    versions:
    - name: v1
      served: true #which one served to the api server
      storage: true #which is the storage version
      scheme:
        openAPIV3Scheme:
            type: Object
            properties:
                from:
                    type: string
                to: 
                    type: string
                number:
                    type: number
                    minimum: 1
                    maximum: 10
```
- create this crd --> k apply -f crd-flight.yaml


## Developing custom Controller:
- Controller is running in  a loop and continuously watching etcd and make changes by (for example)making api calls
- So I could write it in any programming language and in code query objects from the k8s api and watch for changes and make further calls to the api to make changes to the system
- Although it could be chalenging coding the custom controller in any language other than golang as it has k8s go client which provide support for other libraries like shared informers that provide caching and quering mechanisms
- there's a sample controller in k8s github organization, can be cloned and modified then can be run `go run -o sample-controller .`
- run it via `./sample-controller -kubeconfig=$HOME/.kube/config`
- SO controller is ready and can decide on how to distribute it, maybe dockerized it and deploy to a k8s deployment


## Operator Framework
- Used to deploy both CRD , its resources and it's related custom controller as a deployment together instead of deploying each one separately
- It's  more than just deploying them 
- Real Example: ETCD it has the following:
  - EtcdCluster --> ETCD Controller
  - EtcdBackup  --> Backup Operator
  - EtcdRestore --> Restore Operator
- So it can take a backup,restore it
- SO k8s operator do what a human operator can do by managing a specific app, install it, maintain(take and restore backups)
- have operator hub, can install specific operator by:
  - Install the operator lifecycle manager(OLM) using curl
  - Install the operator `k create -f operaot_file.yaml`
  - watch the operator comming up `k get csv name -n ns`