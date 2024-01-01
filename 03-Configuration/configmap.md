- Create Configmap then inject into Pod
- whole confgigMap envFrom
- k create configmap cm --from-literal=key1=value1 --from-literal=key1=value1
- k create configmap cm --from-file=cm_prop.conf
cm_prop.conf
```
APP_COLOR: RED
APP_ENV: PROD
```
- Decalarative data instead of spec
```
apiVersion: v1
kind: ConfigMap
metadata:
    name: cm
data:
    APP_COLOR: RED
    APP_ENV: PROD

```
- Injecting ConfigMap into a pod
    - ENV: Using envFrom

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
                  envFrom:
                    - configMapRef:
                        name: app_config_map
    ```
        
    - SINGLE ENV: Using env
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
                  env:
                    - name:APP_COLOR
                      valueFrom:
                        configMapKeyRef:
                            name: app-config
                            key: APP_COLOR
    ```

    - Volume: Using volumes
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
            volumes:
                - name: app-config-map-volume
                  configMap:
                    name: app-config
                  
            
    ```
        
    