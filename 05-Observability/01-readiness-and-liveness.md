## Readiness Probe
- Pod Status (Pending -> Creating -> Running -> Terminated)
- Pod Conditions --> complemetness Status , array of true and false
  - PodScheduled
  - Initialized
  - ContainerReady
  - Ready
- Once application is marked ready --> it will receive traffic
- App may need some more time to be ready to receive traffic not just it started
- So we need to tie the Ready condition with the actual state of the app
- Types:
  - HTTP test
  - TCP test
  - EXEC command
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
              readinessProbe:
                httpGet:
                    path: /ready
                    port: 8080
                initialDelaySeconds: 10 # if the app needs time to get up
                periodSeconds: 5 # how often to probe
                failureThreshold: 5 # attempts to probe before stopping
```

```
readinessProbe:
    tcpSocket:
        port: 8080
```

```
readinessProbe:
    exec:
        command:
            - cat
            - /app/ready
```
- By default if the app is not ready after 3 attempts ,the probe will stop --> use failureThreshold to increase attempts

## Liveness Probe
- test if application is healthy
- Problem : if container crashes --> k8s will restart it BUT what if the app is working but it has bug that makes it not able to serve requests ?
- so add livenessProbe to test specific endpoint periodically and if it fails --> app is Unhelathy --> k8s destroys and recreates the app