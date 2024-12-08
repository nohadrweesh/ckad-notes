## Resources that doesn't have the create statement using help flaf
- pv
- pvc

- use k expos pod pod_name --name svc_name --port port_nu,m --dry-run=client -o yaml
- If role in ns --> rolebinding should also specified in ns
- pv is cluster wide BUT pvc -->is namespaced --> and only  apod in the same ns of the pvc can use it
service ports:
- volumeClaimTemplates in statefulsets
  - Pod-Scoped Volumes:

Unlike regular volumes, the volumes created via volumeClaimTemplates are pod-specific and persist even if the pod is deleted and recreated.
Data Persistence:

Because StatefulSets guarantee that pods retain their identity (e.g., pod-0, pod-1), the PVCs ensure that the data associated with a specific pod is preserved across pod restarts or rescheduling.
Volume Scaling:

As you scale the StatefulSet replicas, Kubernetes dynamically creates new PVCs for new pods based on the same template.

Feature	Regular Volumes	volumeClaimTemplates
Scope	Shared among pods	Unique to each pod in the StatefulSet
Creation	Pre-created manually	Dynamically created by the StatefulSet
Use Case	Stateless applications	Stateful applications
Persistence	Does not persist with pod identity	Persists with pod identity
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


netpol
storageclass
cm volume
pod command should start with /bin/sh -c
probes
job & cronjob
scheuling pod on specific nodes
ingress
use k replace --forcs -->
cronjob --> if task not completed withon 20 seconfds, job fail and pod terminated --> set activeDeadSeconds=20

## test reaching svc from a pod
k exec -it webapp-color -- sh
in shell run this -->  nc -v -z -w 2 secure-service 80 -->


- creating apod --expose --> creates clusterip svc
- replicaset vs replicationcontroller(older)
- rs --> if changed the image need to delete the pods or rc to be affected
- in docker CMD vs Entrypoint --> we run cmd + entrypoint, override cmd directly and override entrypoint with --entrypoint flag
- in k8s --> command is for entrypoint (whole commnad override) ,args is for cmd(passing args to the docker command)
- securityContext at both pod level and container level,capabilities only supported at container level, container level overide pod level
- resources (requests and limits) , LimitRange, ResourceQuota
- taints & tolerations are meant for the node to not accept any pod  it doesn't want (based on taint_effect) but the tolerated pod can be placed to different node
- nodeSelector --> specify simple node label
- nodeName --> specify the nodeName directly
- If any of the init containers failed to complete the pod is restarted till all of them complete
- RollingUpdate is the default strategy, use --record flag when update deploy to record event (Change cause is added as annotation -> kubernetes.io/change-cause
- maxUnavailable --> how many pods can be down in updating deployment)
- maxSurge --> how many pods can we go above num of pods specified in replica
- Pod has restartPolicy set to Always by default