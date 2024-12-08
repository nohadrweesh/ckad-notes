# Deployment Strategies
1. Recreate
 ```
   strategy:
    type: Recreate
   ```
2. RollingUpdate
   ```
   strategy:
    type: RollingUpdate
    rollingUpdate:
        maxUnavailble: 25
        maxSurge: 25
   ```
3. Blue Green
    - Not a native strategy type, can be implemented by IStio(service mesh)
    - in K8s we can use labels to implement it:
      - assign label(v1) to blue pods and make the svc use it
      - after testing the green pods , remove label(v1) from blue pods and assign label(v1) to green pods
    - Blue (old) pods still exist and green (new pods) also
    - traffic is 100 routed to blue ones and once green app is tested, we shift traffic
4. Canary
    - Not a native strategy type, can be implemented by IStio(service mesh)
    - in K8s we can use labels to implement it:
      -  assign common label(frontend) to primary and canary deployments and make the svc use it
      -  make the canary deployment replicas smaller(by the num we need) than the primary deployment