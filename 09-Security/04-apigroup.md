# k8s api
- All operation we do either via kubernetes api or kubelet utility
- curl https://kube-master:6443/version
- curl https://kube-master:6443/api/v1/pods
- k8s apis are grouped for multuple purposes(healthz,version,api,apis,logs,metrics)
- [api (core),apis(named)] groups are for cluster functionality
- Core group(api) is for all the core functionality like
  - pods,namespaces,rc,events,nodes,secrets
- Named group(apis) is more organized and all newer features will be available through it:
├── /apps
│   ├── /v1
│   │   ├── /deployments
│   │   ├── /replicasets
│   │   ├── /statefulsets
├── /extentions
│   ├── /v1
│   │   ├── /networkpolicies
├── networking.k8s.io
├── authentication.k8s.io
└── certificates.k8s.io
│   ├── /v1
│   │   ├── /certificatessigningrequests

- so apps, extentions, networking.k8s.io, authentication.k8s.io,certificates.k8s.io --> are api groups
- deployments,replicasets,statefulsets --> are resources in this group
- Each resource has a set of actions (create,list,get,delete,update,watch)--> Verbs


- To access these apis on k8s server , I may face an authorization issue and probably can only view the version , need to pass the certificates in each api so I could be able to view/modify resources
- Instead I can use kubectl proxy client
- k proxy --> laucnhes a proxy service locally on port 8001 and uses creds and certifcates from kubeconfig file to access the cluster so I don't have to sepcify them in curl command
- So after that use curl localhost:8001 instead of curl localhost:6443
- So Process is User --> kubectl proxy --> kube apiserver
- NOTE that kube Proxy !== Kubectl Proxy
- KubeProxy--> enables connectivity between pods and services accorss different nodes in the cluster
- Kubectl Proxy--> HTTP Proxy service created by kubectl utility to access kube-apiserver


## Running proxy:
- k proxy 8001& --> this connects to the k8s service in background 
- now you can run commands like: curl localhost:8001/apis/authorization.k8s.io 
  