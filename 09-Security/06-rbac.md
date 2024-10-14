## RBAC
- create Role
```
apiVersion: rbac.authorization.k8s.io
kind: Role
metadata:
    name: developer
    namespace: ## can specify it to limit access within spoecic namespace
rules:
- apiGroups: [""] ## for core groups can leave it empty
  resources: ["pods"]
  verbs: ["create","list","get","delete","update"]
- apiGroups: [""] ## for core groups can leave it empty
  resources: ["configmaps"]
  verbs: ["create"]
```
- for core groups can leave apiGroups blank
- Aoosciate User to Role
```
apiVersion: rbac.authorization.k8s.io
kind: RoleBinding
metadata:
    name: devuser-developer-binding
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
- kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```
- k get roles
- k get rolebindings
- If user can check if he is authorized to do something can use `can-i` command
  - k auth can-i create deployment --> returns yes | no
- IF admin wants to check if another user can do something(optioanlly can specify namespace)
  -  k auth can-i create deployment --as dev-user --namesapce test--> returns yes | no
- Can also specify resourceNames in Role definition
```
apiVersion: rbac.authorization.k8s.io
kind: Role
metadata:
    name: developer
    namespace: ## can specify it to limit access within spoecic namespace
rules:
- apiGroups: [""] ## for core groups can leave it empty
  resources: ["pods"]
  verbs: ["create","list","get","delete","update"]
  resourceNames: ["blue","orange"]

```
- kubectl create role developer --verb=create,list,delete --resource=pods 
- kubectl create rolebinding dev-user-binding --role=developer --user=dev-user
- k auth can-i get  pod/dask-blue-app -n blue
- Can also create pod as another user k create deployment nginx --image ngnix --as-user dev-user