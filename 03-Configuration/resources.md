- Scheduler checks the available nodes and chosses one with sufficient cpu and mem, if no node has sufficient cpu and mem, pod will continue in Pending state

```
        apiVersion: v1
        kind: Pod
        metadata:
            name: pod
            labels:
                app: frontend
        spec:
  
            containers:
                - name: nginx
                  image: nginx
                  resources:
                    requests:
                        memory: "1Gi"
                        cpu: 1
                    limits:
                        memory: "2Gi"
                        cpu: 2

```                  
- if container tries to use more cpu than its limit --> it will be throtteled (continue with its limit)
- if container tries to use more mem than its limit --> it will be killed with OOM (out Of mem)
- If pod has mem and needs more than its limit , we need to kill it to retrieve the mem assigned
- Setting Requests, No LIMITS is best for real case scanarios

## LimitRange
- at namespace level
```
apiVersion: v1
kind: LimitRange
metadata:
    name: cpu-res-limits
spec:
    limits:
        - default:        #limit
            cpu: 500m 
          defaultRequest: #request
            cpu: 500m
          max:            #limit
            cpu: "1"
          min:            #request
            cpu: 100m
          type: container

```

```
apiVersion: v1
kind: LimitRange
metadata:
    name: mem-res-limits
spec:
    limits:
        - default:        #limit
            memory: 1Gi 
          defaultRequest: #request
            memory: 1Gi
          max:            #limit
            memory: 1Gi
          min:            #request
            memory: 500Mi
          type: container

```

- LimitRange only affect newly created pods
- If I have limitRange for defaultLimit 100m and I try to create pod with request set to 700m and no limit is set --> will cause issue ,why?
- Because by default limit is set to 100m is per the limitRange BUT this is less than the request so it will fail with error `is invalid: spec.containers[0].resources.requests: Invalid value: "700m": must be less than or equal to cpu limit`

## ResourceQuota
- Affects total amount of res consumed by all pods in specific namespace
  
```
apiVersion: v1
kind: ResourceQuota
metadata:
    name: res-quota
spec:
    hard:
        requests.cpu: 5
        requests.memory: 1Gi
        limits.cpu: 10
        limits.memory: 5Gi
```
