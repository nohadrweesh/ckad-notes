## Authorization
- what can users and apps do
- We don't want all of them to have same level of access

## Authorization Mechanisms
- Node
  - Both users and kublet access kube-apiserver to handle multiple operations (read,updata,.. resources) --> these requests are handled by special authorizer known as node authorizer
  - kublet are part of systrem nodes groups and have named prefixed with system:node
  - So any request coming from a system:node --> authorized using Node authorizer
- ABAC (attribute based authorization)
  - For each user create a policy file with the allowed operation 
   {
    kind: policy,
    spec: {
        user: dev-user
        namespace: *,
        resource: pods,
        apiGroup: *
    }
   }
   - difficult to manage as any time need to add change to securoity need to edit this policy file manually
- RBAC (role based authorization)
  - more standard approach to amnage access
  - create role for each type(dev,admin)
  - assign users to this role
- Webhook
  - Outsource authorization mechanism
  - Kubeapiserver send user info to third pary tool (like open policy agent ) and make it decide whether authorized or not
- AllwaysAllow --> default
- AllwaysDeny

- These modes are specified in kubeapiserver by setting `--Authoriztion-mode `flag
- Can specify multiple modes `--Authoriztion-mode=Node,RBAC,Webhook`
- When a user make a request:
  - Node denies it (because it's not a node)
  - Request forwarded to RBAC --> assume it approve the request , so request is granted permision
- So denies --> forward the request to next authorizer, allowing ends the authorization chain
- To get authorization-mode 
  -  kubectl describe pod kube-apiserver-controlplane -n kube-system | grep authorization-mode
  -  ps -aux | grep authorization-mode --> as kube-apiserver is a process on Controlplane
  -  cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep authorization-mode