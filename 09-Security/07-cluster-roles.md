- Some resources are namesapced like pods, deploy,rc, roles and rolebinding
- Others are cluster-scoped like nodes, pv,pvc,namespaces,clusterrole and clusterrolebindings
- Clusterroles are used to authorize usrs to cluster-based resources
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
    name: cluster-admin
rules:
- apiGroups: [""] ## for core groups can leave it empty
  resources: ["nodes"]
  verbs: ["create","list","get"]

```
- Aoosciate User to ClusterRole
```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
    name: cluster-admin-developer-binding
subjects:
- kind: User
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
- kind: Role
  name: cluster-adminstartor
  apiGroup: rbac.authorization.k8s.io
```

- ClusterRole can be used for namspaced resources, when we do so we authorize across all the namespaces
- kubectl create clusterrole node-role  --verb=get,list,watch,create,update,delete --resource=nodes
- kubectl create clusterrolebinding node-role  --clusterrole=node-role --user=michelle 
- k auth can-i list nodes --as michelle


- k api-resources ==? gets the resources names,api versions wether namespaced or not 
- k create clusterrole storage-admin --resource=persistentvolumes,storageclasses --verb=*
- k create clusterrolebinding michelle-storage-admin  --user=michelle --clusterrole=storage-admin