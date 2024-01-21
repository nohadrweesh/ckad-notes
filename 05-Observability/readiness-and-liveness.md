## Readiness Probe
- Pod Status (Pending -> Creating -> Running -> Terminated)
- Pod COnditions --> complemetns Status , array of true and false
  - PodScheduled
  - Initialized
  - ContainerReady
  - Ready
- Once application is marked ready --> it will recive traffic
- App may need some more time to be ready to revive traffic not just it started
- So we need to tie the Ready condition with the actual state of the app
- TYpes:
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
- 