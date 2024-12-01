# Securing K8s
- We use kubectl utlity to run various operations on K8s cluster
- FLow
  `Kubectl ---> ApiServer ---> Create Pod --> Information stored in etcd`
- When request hits apiServer, first it goes through authentication process(usually through certificates)
- As this is done through kubectl --> so we have kubeconfig and authentication process is responsible for idnetifying the user who is sending the request
  `Kubectl ---> Authentication ---> Create Pod`
- Then it goes through authorization process,mostly useing role-based access control(rbac)
   `Kubectl ---> Authentication ---> Authorization ---> Create Pod`

- So Authorization using RBAC controls wether to create/update/list/delete resouces or even contorl specific resource names BUT all at K8s api level
- But if we want to create more like some image names control(specific hub) or specific capabilities, or enforce some metadata ---> Need for AdmissionControls
  `Kubectl ---> Authentication ---> Authorization ---> Admission Control ---> Create Pod`
- Admission Control --> help in creating better security measures and can evenc change the request itself like;
  - AlwayPullImage
  - EventRateLimit
  - NamespaceExists (deny requests if ns is not created) VS NamespaceAutoProvsion (creates ns if not exist)

- To get apiserver enabled admissioncontroller
  - kube-apiserver -h | grep enable-admission-plugin
- To run this in kubeadm setup env need to run the previous command in the api-server control plane pod
  - kubectl exec kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep enable-admission-plugin
- To update admission controller
  - in kube-apiserver.service modilfy ExecStart `--enable-admission-plugins=NodeRestriction,NamespaceAutoProvision`
  - OR(if in kubeadm based setup) in /etc/kuberntes/mainfests/kube-apiserver.yaml update the command by adding `--enable-admission-plugins=NodeRestriction,NamespaceAutoProvision`
- To Disable admission controller use this flag `--disable-admission-plugins=DefaultStorageClass`



-  NamespaceExists and NamespaceAutoProvision admission controllers are deprecated and now replaced by NamespaceLifecycle admission controller.
The NamespaceLifecycle admission controller will make sure that requests
to a non-existent namespace is rejected and that the default namespaces such as
default, kube-system and kube-public cannot be deleted.

- DefaultStorageClass admission controller: This admission controller observes creation of PersistentVolumeClaim objects that do not request any specific storage class and automatically adds a default storage class to them. This way, users that do not request any special storage class do not need to care about them at all and they will get the default one.


## Types Of AdmissionControllers plugins
- Validating (like NamespaceExists)
- Mutating (like NmaespaceAutoProvision and DefaultStorageClass)


- Order of AdmissionController plugins: run the mutating then validitng as mutating may change the state and thus validation result could change
- All these are part of k8s admission controllers(compiled and shipped with it) but we can create ours
  

### External AdmissionControllers:
- to support it 2 AdmissionController available
  - MutatingAdmissionWebhook
  - ValidatingAdmissionWebhook
- We configure these webhooks to point to a server hosted (either in the k8s cluster or external)
- the server will have our own admission service running with our code and logic
- After the request goes through all the built-in admission controllers it hits the webhook configured
- Once it hits the webhook --> it makes a call to the admissioncontroller server by passing admission review object in json format similar to this
{
    apiversion:
    kind:
    request:{
        uid:
        kind:
        resource:
        subResource:
        requestKind:
        requestResource
    }
}
- the server responds with admission review object with a result wether reques isAllowed or not
{
    apiVersion:
    kind
    response:{
        uid:
        allowed:"true
    }
}

## How to set this up:
- Deploy AmdissionControl server with our own code and logic
  - could be in any lang but must have (POST validate and mutate apis)
- Configure the webhook on k8s by creating a webhook configuration object
  - could run it somewhere or containerize and run it in k8s as a deployment withsvc
  - configure the cluster to reach out to the server to validate or mutate the requests
    """
    apiVersion: admissionregistration.k8s.io/v1
    kind: ValidatingWebhookConfiguration ## or could be MutatingWebhookConfiguration
    metadata:
        name: "pod-policy.example.com
    webhooks:
    - name:
      clientConfig:
        url: "https://external-server.example.com" if external
        service:
            namespace: webhook-ns
            name: webhook-svc
        caBundle: "" ##as Communication between apiserver and webhook should be over TLS
      rules: #specify when to call this webhook for validation
      - apiGroups: ["*"]
        apiVersions: ["v1"]
        operations: ["CREATE"]
        resources: ["pods"]
        scope: "Namespaced"

    """