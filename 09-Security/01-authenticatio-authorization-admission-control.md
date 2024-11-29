# Secure hosts
- physical security
- Disable password auth 
- allow only SSH key exchange auth

# Secure k8s
- kube-apiserver --> controls everthing , first line of defense
- Authentication(Who can access):
  - Files-> username and passwords
  - Files-> username and tokens
  - Certificates
  - Extenal Authentication Provider -> LDAP
  - Service Accounts
- Authorization(What they can do)
  - RBAC (Role based access control)
  - ABAC (Attribute based access control)
  - Node Authorization
  - Webhook Mode
- TLS Certificates: All communication between kube-apiserver and other components like(etcd, scheduler, Controller or node kublet and kube proxy) are encrypted
- Network Policy: By default all pods can access each other but this can be resticted by NetPol
