# Jobs
- Different type of workload than normal Pod(web app, DB) like analytics
- `docker run ubuntu expr 3+4` --> you will see status as existed because it finishes the operation
- in k8s --> status will be completed
  ```
  apiVersion: v1
  kind: Pod
  metadata:
    name: plus-pod
  spec: 
    restartPolicy: Always
    containers:
        - name: plus
          image: ubuntu
          command: ['expr','3','+','4']

  ```
- in k8s , it will containue to bring the previous pod up again and again and you can see this in the restarts count
- K8s wants apps to live forever by default --> do this by the param restartPolicy which is set to `Always` by default
- restartPolicy: Always|Never|OnFailure
```
apiVersion: batch/v1
kind: Job
metadata:
    name: plus-job
spec:
    completions: 3
    parallelism: 3
    backoffLimit: 10
    template:
        spec:
            restartPolicy: Never
            containers:
                - name: plus
                  image: ubuntu
                  command: ['expr','3','+','4']

```
- we can use k logs pod_name --> to see the output of the plus operation
- we have completions param that should be used to create num of pods which created sequentailly (one then when it completes it creates the other)
- If we need to run these pods in paralel we have parallelism param which create for example 3 at a time and if 2 succseed and one fail , k8s will create just another one
- backoffLimit --> defines how many times the controller should try before consider the job as failed