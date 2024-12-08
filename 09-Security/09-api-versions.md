- API groups , resources and verbs like
├── /apps
│   ├── /v1
│   │   ├── /deployments
│   │   ├── /replicasets
│   │   ├── /statefulsets
├── /extentions
│   ├── /v1
│   │   ├── /networkpolicies
├── networking.k8s.io
├── authentication.k8s.io
└── certificates.k8s.io
│   ├── /v1
│   │   ├── /certificatessigningrequests
- apps,extentions,networking.k8s.io,authentication.k8s.io -->API groups
- v1 --> API versions:
  - /v1 (GA Stable --> generally available stable version)
  - /v1alpha1 --> first developed and merged to k8s code,may have bugs,may lack e2e testing ,not enabled by default(like /internal.apiserver.k8s.io/v1alpha1/storageversion)
  - /v1beta1 -->(After some time alpha code is tested and have some tests it moves to beta), enabled by default, have e2e testing ,may have minor bugs, flowcontrol group is in beta phase /flowcontrol.apiserver.k8s.io
  - /vx(like /v1) --> (after beta code is higly tested),has conformance testes,highly reliable
- The same api group (like apps) can have multiple versions like v1 and v1beta1 and we can create objects using different versions but only one can be `preferred` or `storage` version
- the preferred version --> when I run `k get deployment` or `k explain deployment` --> which will be queried ? this is the preferred version
- the stoarge version --> if we have multiple versions, all be converted to the storage version before storing in etcd
- so prefered version is the default version in commands and storage version is the version in which obj is stored in etcd
- prefered and stored versions usually the same but can be different 
- can get preferred version for specific api group --> when list that spcific api like
  - k proxy 8001& --> this connects to the k8s service in background 
  - now you can run commands like: curl localhost:8001/apis/authorization.k8s.io
- to get the storage version this can only get through etcd utility like `etcdctl` utility
- to enable specific version --> need to enable it in apiserver in the runtime-config param `--runtime-config=batch/v1alpha1,batch/v1alpha2`