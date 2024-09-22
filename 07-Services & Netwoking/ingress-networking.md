# Ingress
- A k8s resource that manages external access within a cluster(http or https traffic)
- provides a way to expose svcs to external world allowing to control based on request hostname or path

## Ingress Controller
- like google load balancer (GCE), NGINX (which maintained by k8s , traefik,istio) --> these are not just LB , the LB is part of it
- TO use Nginx as an IngressController, we need to create the following
  - Deployment: 
    - with image: registry.k8s.io/ingress-nginx/controller
    - pass some args, one of them could be default-backend-service
  - Service: with type NodePort to expose the deploy
  - Configmap: to feed nginx params
  - ServiceAccount: to Provide permision

## Ingress Resource
- A set of rules and configs applied on ingress controller
- created using defintion file
  ```
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: ingress-wear
  spec:
    backend:
        serviceName: wear-service
        servicePort: 8080

  ```
- backend --> defines where traffic to be routed to
- creates --> k apply 0f ingress-wear.yaml
- get --> k get ingress
- Use rules when we have multiple backends
- Route traffic in same host (*) but based on different paths, host in each rule is not required
  ```
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: ingress-wear-watch
  spec:
    rules:
    - http:
        paths:
            - path: /wear
              pathType: Prefix
              backend:
                service:
                    name: wear-service
                    port: 
                        number: 8080
            - path: /watch
              pathType: Prefix
              backend:
                service
                    name: watch-service
                    port: 
                        number: 8080

  ```
- Route traffic based on hostanmes
```
  apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: ingress-wear-watch-hosts
  spec:
    rules:
    - host: wear.online-store.com
      http:
        paths:
            - backend:
                serviceName: wear-service
                servicePort: 8080
    - host: watch.online-store.com
      http:
        paths:
            -  backend:
                serviceName: watch-service
                servicePort: 8080
           

  ```

- Imperative way (k8s 1.2+) --> k create ingress ingress_name --rule  "host/path=service:port"
- k create ingress ingress-wear-watch-hosts --rule "wear.online-store.com/*=wear-service:8080"
- - if host is * so don't include as it will cause an issue
- k create ingress ingress-wear-watch-hosts --rule "/wear=wear-service:8080"
  

### Rewrite target
- if we want the service to be availble at specific url (/pay) for example , the /pay actually doesn't exist in the app what exist is the / path so we need to rewite the target path using annotations 
- annotaions should be added at metadata
```
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: ingress-wear-watch
    annotations:
        nginx.ingress.kubernetes.io/rewrite-target: / ## / is the actuall app path
  spec:
    rules:
    - http:
        paths:
            - path: /pay ## /pay is the path exposed to users
              pathType: Prefix
              backend:
                service:
                    name: pay-service
                    port: 
                        number: 8080
```                        
- so it just replaces the path with the rewrite-target specified
- Could use some regex for it
- this is related to NGINX-ingress-controller

## create Ingress Controller
- k create namespace ingress-nginx
- k create configmap ingress-nginx-controller -n ingress-nginx --dry-run=client -o yaml > ingress-nginx-controller-cm.yaml
  ```
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: ingress-nginx-controller
    namespace: ingress-nginx

  ```
- k create serviceaccount ingress-nginx -n ingress-nginx --dry-run=client -o yaml
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: ingress-nginx
  namespace: ingress-nginx
```
- k create serviceaccount ingress-nginx-admission -n ingress-nginx --dry-run=client -o yaml
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: ingress-nginx-admission
  namespace: ingress-nginx
```
- Create 2 roles, 2 rolebindings, 1 clusterrole
- Create the deployment and service
```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    labels:
        app.kubernetes.io/component: controller
        app.kubernetes.io/instance: ingress-nginx
        app.kubernetes.io/managed-by: Helm
        app.kubernetes.io/name: ingress-nginx
        app.kubernetes.io/part-of: ingress-nginx
        app.kubernetes.io/version: 1.1.2
        helm.sh/chart: ingress-nginx-4.0.18
    name: ingress-nginx-controller
    namespace: ingress-nginx
    spec:
    minReadySeconds: 0
    revisionHistoryLimit: 10
    selector:
        matchLabels:
        app.kubernetes.io/component: controller
        app.kubernetes.io/instance: ingress-nginx
        app.kubernetes.io/name: ingress-nginx
    template:
        metadata:
        labels:
            app.kubernetes.io/component: controller
            app.kubernetes.io/instance: ingress-nginx
            app.kubernetes.io/name: ingress-nginx
        spec:
        containers:
        - args:
            - /nginx-ingress-controller
            - --publish-service=$(POD_NAMESPACE)/ingress-nginx-controller
            - --election-id=ingress-controller-leader
            - --watch-ingress-without-class=true
            - --default-backend-service=app-space/default-http-backend
            - --controller-class=k8s.io/ingress-nginx
            - --ingress-class=nginx
            - --configmap=$(POD_NAMESPACE)/ingress-nginx-controller
            - --validating-webhook=:8443
            - --validating-webhook-certificate=/usr/local/certificates/cert
            - --validating-webhook-key=/usr/local/certificates/key
            env:
            - name: POD_NAME
            valueFrom:
                fieldRef:
                fieldPath: metadata.name
            - name: POD_NAMESPACE
            valueFrom:
                fieldRef:
                fieldPath: metadata.namespace
            - name: LD_PRELOAD
            value: /usr/local/lib/libmimalloc.so
            image: registry.k8s.io/ingress-nginx/controller:v1.1.2@sha256:28b11ce69e57843de44e3db6413e98d09de0f6688e33d4bd384002a44f78405c
            imagePullPolicy: IfNotPresent
            lifecycle:
            preStop:
                exec:
                command:
                - /wait-shutdown
            livenessProbe:
            failureThreshold: 5
            httpGet:
                path: /healthz
                port: 10254
                scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            name: controller
            ports:
            - name: http
            containerPort: 80
            protocol: TCP
            - containerPort: 443
            name: https
            protocol: TCP
            - containerPort: 8443
            name: webhook
            protocol: TCP
            readinessProbe:
            failureThreshold: 3
            httpGet:
                path: /healthz
                port: 10254
                scheme: HTTP
            initialDelaySeconds: 10
            periodSeconds: 10
            successThreshold: 1
            timeoutSeconds: 1
            resources:
            requests:
                cpu: 100m
                memory: 90Mi
            securityContext:
            allowPrivilegeEscalation: true
            capabilities:
                add:
                - NET_BIND_SERVICE
                drop:
                - ALL
            runAsUser: 101
            volumeMounts:
            - mountPath: /usr/local/certificates/
            name: webhook-cert
            readOnly: true
        dnsPolicy: ClusterFirst
        nodeSelector:
            kubernetes.io/os: linux
        serviceAccountName: ingress-nginx
        terminationGracePeriodSeconds: 300
        volumes:
        - name: webhook-cert
            secret:
            secretName: ingress-nginx-admission

    ---
    apiVersion: v1
    kind: Service
    metadata:
    creationTimestamp: null
    labels:
        app.kubernetes.io/component: controller
        app.kubernetes.io/instance: ingress-nginx
        app.kubernetes.io/managed-by: Helm
        app.kubernetes.io/name: ingress-nginx
        app.kubernetes.io/part-of: ingress-nginx
        app.kubernetes.io/version: 1.1.2
        helm.sh/chart: ingress-nginx-4.0.18
    name: ingress-nginx-controller
    namespace: ingress-nginx
    spec:
    ports:
    - port: 80
        protocol: TCP
        targetPort: 80
        nodePort: 30080
    selector:
        app.kubernetes.io/component: controller
        app.kubernetes.io/instance: ingress-nginx
        app.kubernetes.io/name: ingress-nginx
    type: NodePort
```
- Use the ingress
-  k create ingress wear-watch -n app-space --rule "/wear=wear-service:8080" --rule "/watch=video-service:8080" --annotation nginx.ingress.kubernetes.io/rewrite-target=/  --dry-ru
n=client -o yaml > wear-watch.yaml
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
  creationTimestamp: null
  name: wear-watch
  namespace: app-space
spec:
  rules:
  - http:
      paths:
      - backend:
          service:
            name: wear-service
            port:
              number: 8080
        path: /wear
        pathType: Exact
      - backend:
          service:
            name: video-service
            port:
              number: 8080
        path: /watch
        pathType: Exact ## check this
status:
  loadBalancer: {}
```