## Authenticate using certiifcates
- curl https://my-kube-playground:6443/api/v1/pods \
       --key admin.key
       --cert admin.crt
       --cacert ca.crt
- kubelet get pods
       --server my-kube-playground:6443
       --client-key admin.key
       --client-certificate admin.crt
       --certificate-auth
- by default thr kubectl command looks for kubeconfig file in $HOME/.kube/config and if it exists you don't have to specify it in each command
```
       --server my-kube-playground:6443
       --client-key admin.key
       --client-certificate admin.crt
       --certificate-authority ca.cert
```
- Kubeconfig file structure
- We have 3 different section:
  1. Clusters (different orgs or different cloud providers like dev,prod,google)
  2. Users (like Admin and devs)
  3. Context that relates users with clusers so Admin@prod,dev@google
- `--server my-kube-playground:6443` goes into clusters section --> named MyKubePlayground 
- the rest (admin users key and certs ) goes into users section --> named MyKubeAdmin
- A new context   MyKubeAdmin@MyKubePlayground
- Kubeconfig file
```
apiVersion: v1
kind: Config
current-context: my-kube-admin@my-kube-playground ## specify which context to start with
clusters:
    - name: my-kube-playground
      cluster: 
        certificate-authority: ca.cert
        certificate-authority-data: ##instead of certificate-authority field
        server: my-kube-playground:6443

contexts:
    - name: my-kube-admin@my-kube-playground
      context:
        cluster: my-kube-playground
        user: my-kube-admin
        namespace: finace # optional namespace

users:
    - name: my-kube-admin
      user:
        client-certificate: admin.crt
        client-key: admin.key

```
- clusters, users, contexts all are arrays to specify multiples
- k config view
- k config view --kubeconfig=my-custom-config --> specify which kubeconfgi to use
- k config use-context dev@prod --> change current-context and this changes in kubeconfig file itself
- Each cluster could contain multiple namespaces and can specify it in the context
- For specifying `certificate-authority` it's better to add full path  and instead can add the certificate data but encoded in field

- Set kubeconfig and get the current context we will need also to specify that we need the current context of this config
  - kubectl config --kubeconfig=/root/my-kube-config use-context research
  - kubectl config --kubeconfig=/root/my-kube-config current-context
  - We can set our new file as the default config file cp my-kube-config $HOME/.kube/config