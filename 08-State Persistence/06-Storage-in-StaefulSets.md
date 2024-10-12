# Static Provisioning
- PV --> PVC --> use inPod

# Dynamic Provsioning
- SC(that provsion Cloud Provider storage and this creates PV automatically) --> PVC --> use in Pod

# Storage in statefulSet
- we can have one PVC attached to all Pods (if provsioner allows that)
- If we need a separate PVC for each POD(like in MYSQL example) se we need PVC template which is added within statefulSet template
- If pod is deleted, recreated or rescheduled, k8s doesnm't delete the assocaited PVC and it reattaches to the replacement POD