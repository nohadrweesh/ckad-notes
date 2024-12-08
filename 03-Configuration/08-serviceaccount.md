- User Account (humans) VS Service Account (apps)
- SA by default creates token which used in authentication, this token is stored into a secret object
- If the app is not deployed to K8S , we need to copy the token to authenicate
- If the app is deployed to K8S then we need to mount the token only

```
        apiVersion: v1
        kind: Pod
        metadata:
            name: pod
            labels:
                app: frontend
        spec:
            autoMountServiceAccountToken: false
            serviceAccountName: sa_name
            containers:
                - name: nginx
                  image: nginx
                  

```                
- By default, Kubernetes automatically mounts a service account token into the filesystem of each Pod. This token allows the Pod to authenticate with the Kubernetes API, which is necessary if the Pod needs to interact with the cluster (e.g., querying API resources or updating configurations).
Setting `autoMountServiceAccountToken` to false disables this behavior, which can be a security enhancement for workloads that do not require access to the Kubernetes API
- autoMountServiceAccountToken can be set at both pod and in SA itself


- Some issues related to this way:
    - Scalability Issue as each sa will have bound secret created
    - Security Issue as the generated token doesn't have expiry date so it's valid for forever
- To solve these issues in versions 1.22 and 1.24
    - no secret and token are created by default , you need to run `k create token` command
    -   