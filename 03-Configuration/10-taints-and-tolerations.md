- Taints set on node & tolerations for pods
- k taint node node_name key=value:taint_effect
- taint_effect --> what happens to Pods that doesn't tolerate this taint:
  - NoSchedule    --> Pod won't be scheduled on the node
  - PreferNoSchedule --> Scheduler will try not to schedule it on the node but it's not guarnteed
  - NoExecute --> Pod won't be scheduled on the node and exiting pods will be evicted if not tolerate the taint
- So taints & tolerations are meant for the node to not accept any pod it doesn't want but the tolerated pod can be placed to different node

```
    apiVersion: v1
    kind: Pod
    metadata:
        name: pod
        labels:
            app: frontend
    spec:
        tolerations:
        - key: "app"
          operator: "equal"
          value: "blue"
          effect: NoSchedule
        containers:
            - name: nginx
              image: nginx
```
- Pod may be placed on nodes that matches its tolerations(key,value,effect)