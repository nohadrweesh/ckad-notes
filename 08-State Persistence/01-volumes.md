# Volumes
- Pods(and containers) are transient by nature, any data created by it will be destroyed once the pod(or container)destroyed
- To persist data --> use volumes
```
apiVersion: v1
kind: Pod
metadata:
    name: alpine-pod
spec:
    containers:
        - name: alpine
          image: alpine
          command: ["shuf -i 0-100 -n 1 >> /opt/number.out"]
          volumeMounts:
            - name: data-volume
              mountPath: /opt
    volumes:
        - name: data-volume
          hostPath:
            path: /data
            type: Directory
```
## Volume types:
1- hostPath --> creates the directory directly on host(node) but for multiple node cluster this is not recommended (unless you configure external replicated cluster storage solution) as the volume directory won't be the same on all nodes

2- emptyDir --> files will exist on host as long as the pod is running in the node, once pod deleted, files will get deleted too

3- Other types like (NFS ,GlusterFS,AWS ebs,Azure disk, GCP Disc solutions)
```
volumes:
    - name: aws-volume
      awsElasticBlockStore:
            volumeID: <volume-id>
            fsType: ext4
```