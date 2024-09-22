# Traffic in webapp
- we have FE on port 80, BE on port 5000, DB on port 3306
- Traffic flows from FE to BE and from BE to DB
- Ingress and Egress --> the traffic orignated from where
- We're ingoring the responses here
- these are the ingress and egress rules:
  - FE: ingress 80
  - FE: egress 5000
  - BE: ingress 5000
  - BE: egress 3306
  - DB: ingress 3306
- Network security: need to make sure each app can talk to each other

## NetworkPolicy
- K8s object to allow restrict traffic to pods (netpol)
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
    name: db-policy
spec:
    podSelector:
        matchLabels:
            role: db
    policyTypes:
    - Ingress
    - Egress
    Ingress:
    - from:
      - podSelector:
            matchLabels:
                role: api-pod
        namespaceSelector:
            matchLabels:
                name: prod ## should be set in the ns first
      - ipBlock  ## for external server access
            cidr: 129.3.4.5/32
      ports:
      - protocol: TCP
        port: 3306
    egress:
    - to:
      - ipBlock:
            cidr: cidr: 129.3.4.5/32
      ports:
      - protocol: TCP
        port: 80
```
- Not all the ntworking solutions support NetworkPolicy (flannel doesn't support ) kube-router,Calico support
- In non-supported solution--> we still can create NetworkPolicy but it's not affectng
- By default k8s allows all pods to call each other but whenewver we create networkpolicy and aaoscaite it with specific pos so only the allowed traffic in policy can b routed
- namespaceSelector --> used to control from which ns the pod we select
- if I have multiple records in the from array --> it woeks as OR operation 
- in the from array element(like first one) whcih has pod and ns selctor it works as AND operation , both should be met


## General Note
- allowing egress traffic to TCP and UDP on port 53 in your NetworkPolicy ensures that Pods can resolve DNS queries correctly. This is crucial for maintaining the ability of applications running in the cluster to communicate effectively with each other using service names. Without this allowance, Pods may face difficulties in connecting to other services or resources by name, potentially disrupting their functionality
- TCP(Transmission Control Protocol) is used for tasks like zone transfers.
UDP(User Datagram Protocol) is primarily used for standard DNS queries
