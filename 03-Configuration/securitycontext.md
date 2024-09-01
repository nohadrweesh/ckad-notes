- securityContext at both pod level and container level
- container level overrides pod level
- capabilities only supported at container level

```
        apiVersion: v1
        kind: Pod
        metadata:
            name: pod
            labels:
                app: frontend
        spec:
            securityContext:
                runAsUser: 0
            containers:
                - name: nginx
                  image: nginx
                  securityContext:
                    capbilities: 
                        add: ["MAC_ADMIN"]

```                  