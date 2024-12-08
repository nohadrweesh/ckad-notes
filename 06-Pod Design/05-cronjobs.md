# CronJob
- runs on a speciifc schedule
- *    *        *       *       *
- min hour dayOfMonth month dayOfWeek
```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
    name: reporting-cronjob
spec:
    schedule: 1/* * * * *
    jobTemplate:
        spec:
            completions: 3
            parallelism: 3
            template:
                spec:
                    restartPolicy: Never
                    containers:
                        - name: reporting-pod
                          image: reporting-image
```