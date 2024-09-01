- Specify which node(or nodes) this pod will placed on
- First label the node
- k label node node_name key_name=key_value
- k label node node_name size=Large

```
    apiVersion: v1
    kind: Pod
    metadata:
        name: pod
        labels:
            app: frontend
    spec:
        nodeSelector:
            size: Large
        containers:
            - name: nginx
              image: nginx
```
- we may need more complex conditions like Large or Medium or Any node that is not Small --> use node affinity and anti-affinity feature