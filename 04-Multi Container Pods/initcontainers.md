- init Containers run to a completion then the main processes in main containers will run 
- If any of the init containers failed to complete the pod is restarted till all of them complete
- Init Containers run one at a time in sequential order
```
    apiVersion: v1
    kind: Pod
    metadata:
        name: pod
        labels:
            app: frontend
    spec:
        initContainers:
            - name: busybox
              image: busybox

        containers:
            - name: nginx
              image: nginx


```
- when init contianer ends the proccess--> state will be Terminated , Reason : completed