# Users (Admins & Devs) & Bots
- Can't create or list users in k8s
- Can create & list SA
## Users Access
- is controlled via kubeapiserver either via kublet or apis(curl https://kube-server-ip:6443)
- So kube-apiserver authenitcates request then process it
- Users Auth mecahnism:
  - Files-> username and passwords
  - Files-> username and tokens
  - Certificates
  - Extenal Authentication Provider -> LDAP
- Basic Auth Mechanism (passwords)
  - create file with user id, user name, user password and pass it the apiserver --basic-auth-file=users.csv
  - then in a curl command pass this info `curl -v -k https://master-node-ip:6443/api/v1/pods -u "username:password"`
- Basic Auth Mechanism (tokens)
  - create file with user id, user name, user token and pass it the apiserver --token-auth-file=users.csv
  - then in a curl command pass this info `curl -v -k https://master-node-ip:6443/api/v1/pods -header "Authorization:Bearer token"`
- This is NOT the RECOMMENED method:
  - Consider volume mount while providing the auth file in kubeadm setup
  - Setup RBAC for new users