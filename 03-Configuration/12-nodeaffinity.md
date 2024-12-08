- more complex conditions than used in nodeselector

```
    apiVersion: v1
    kind: Pod
    metadata:
        name: pod
        labels:
            app: frontend
    spec:
        affinity:
            nodeAffinity:
                requiredDuringSchedulingIgnoredDuringExecution:
                    nodeSelectorTerms:
                    - matchLabels:
                        - key: size
                          operator: in
                          values:
                          - Large
                          - Medium
        containers:
            - name: nginx
              image: nginx
```

we can see something like

```
 matchLabels:
    - key: size
      operator: exists
```

- So for affinity types we have 2 available and 1 planned, 
- Available:
  - requiredDuringSchedulingIgnoredDuringExecution
  - preferredDuringSchedulingIgnoredDuringExecution
- planned:
  - requiredDuringSchedulingRequiredDuringExecution
- During Excution means if pod is created and is running but the node label is removed what will happen to the pod , will it continue or it will be evicted


- Combine taints&tolerations with node affinity to both place pods on required nodes and don't place no-needed pods on these nodes