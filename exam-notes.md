## Resources that doesn't have the create statement using help flaf
- pv
- pvc

- use k expos pod pod_name --name svc_name --port port_nu,m --dry-run=client -o yaml
- If role in ns --> rolebinding should also specified in ns
- pv is cluster wide BUT pvc -->is namespaced --> and only  apod in the same ns of the pvc can use it
service ports:
volumeClaimTemplates
hostPath vs emptyDir
- cancreate multiple items in same file like
"""
apiVersion: v1
kind: List
items:
  - kind: PersistentVolume
    apiVersion: v1
    metadata:
      name: redis01
    spec:
      accessModes: ["ReadWriteOnce"]
      capacity:
        storage: 1Gi
      hostPath:
        path: /redis01
  - kind: PersistentVolume
    apiVersion: v1
    metadata:
      name: redis02
    spec:
      accessModes: ["ReadWriteOnce"]
      capacity:
        storage: 1Gi
      hostPath:
        path: /redis02
"""
- Service : mainly selector(dict not array) and ports
