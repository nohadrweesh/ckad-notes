- K8s assume your app is developed and containerized
- Mostly 1 pod ~ 1 container unless you need some helper containers
- K run pod_name --image image_name
- k get pods
- Yaml in K8s
  - apiVersion
  - kind
  - metadata
  - spec
- Pods with Yaml:
  - apiVersion: v1
  - kind: Pod
  - metadata:
        name: pod_name
        namespace: default    
        labels:
            app: run-server
  - spec 
        containers:
            - name: container_name
              image
        volumes:
            - name: volume_name
              hostPath:
                path:
                type: Directory # optional

- k run nginx --image nginx --dry-run=client -o yaml
- k edit pod pod_name
  - only the properties listed below are editable:
        - spec.containers[*].image
        - spec.initContainers[*].image
        - spec.activeDeadlineSeconds ##Specifies the maximum duration (in seconds) that a Pod can run before it is forcibly terminated by the Kubernetes system
        - spec.tolerations
        - spec.terminationGracePeriodSeconds  ##Specifies the amount of time (in seconds) Kubernetes waits after sending a termination signal (SIGTERM) to the container before forcefully killing it


- k run nginx --image nginx --labels="app=front,env=prod" --port 8080 --expose=true # creates clusterip svc and relate it to the pod

- To describe all pods : k describe pod