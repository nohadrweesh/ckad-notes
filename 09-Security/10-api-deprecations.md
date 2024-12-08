- An api group can have multiple versions but why? and when can I remove an older version that's no longer required? --> API deprecation policy
- Example: we're contributing to k8s api by adding this code
  - apis --> kodecloud.com/v1alpha1/course
                                   /webinar
webinar object be like
```
apiVersion: kodecloud.com/v1alpha1
kind: webinar
metadata:
    name: ckad
spec
```
- webinar object didn't go well ,can we remove it directly form our code? NO (check rule #1) --> so we release a new version with the course only
apis --> kodecloud.com/v1alpha1/course
                               /webinar
                      /v1alpha2/course
```                      
apiVersion: kodecloud.com/v1alpha1
kind: Course
metadata:
    name: ckad
spec
```

```                      
apiVersion: kodecloud.com/v1alpha2
kind: Course
metadata:
    name: ckad
spec
```
- so old objects will remain in DB 
- SO we still can be using v1apha1 but objects will be converted internally to v1apha2 --> preferred and stored versions are v1apha2
- As in rule 2 course version v1alpha1 could be converted to v1alpha2 and back to v1alpha1 without any info loss

```                      
apiVersion: kodecloud.com/v1alpha1
kind: Course
metadata:
    name: ckad
spec:
    type: video
```
|
|
|
```                      
apiVersion: kodecloud.com/v1alpha2
kind: Course
metadata:
    name: ckad
spec:
    type: video
    duration: ##not in v1
```
|
|
|
```                      
apiVersion: kodecloud.com/v1alpha1
kind: Course
metadata:
    name: ckad
spec:
    type: video
    duration:
```

- Must mention about deprecation in release notes 
- Timline of an example api
Timeline   Preferred/Storage version                            API group Version
x          v1alpha1
x+1        v1lpha2
x+2        v1beta1
x+3        v1beta1(deprecated but still preferred check #4.b)   v1beta2
x+4        v1beta2(is preferred now!!)                          v1beta1(deprecated)
x+5        v1beta2(deprecated but still preferred check #4.b)   v1beta1(deprecated) v1
x+6        v1                                                   v1beta2(deprecated)
x+7        v1                                                   v1beta2(deprecated)
x+8        v1                                                 
x+9        v1                                                   v2alpha1



- In the previous timeline:
  - Each time we have a new version, we deprecate the older --> GA can deprecate alpha or beta but not the other way around
  - But for alpha this is not allowed as in rule#3 (alpha can't depreacte GA) as what happened in x+9
  - Versions stay for 3 release then gets removed ans in rule 4.a
- Kubectl convert --> utility to convert to specific API version --> outputs new yaml format on screen
 kkubectl-convert -f old_file.yaml --output-version apps/v1
- Kubectl convert --> special plugin that you may have to install to your system
- Install Kubectl-convert:
  - curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl-convert
  - chmod +x kubectl-convert 
  - mv kubectl-convert /usr/local/bin/kubectl-convert
## API Deprecation Policy Rules:
1. API elements may only be removed by incrementing the version of api group
2. API objects must be able to round-trip between API versions in a given release without information loss with the exception of whole REST resources that don't exist in some versions
3. An API version in a given track may not be deprecated until a new API version at least as stable is released
4. a. Other than the most recent API versions in each track ,older API versions must be supported after their announced deprecations for a duration of  no less than:
      - GA: 12 months or 3 releases (whichever longer)
      - Beta: 9 months or 3 releases (whichever longer)
      - Alpha: 0 releases
   b. The `prefered` API version and `storage version`  for a given group may not advance until after a release has been made that supports both new and previous versions
