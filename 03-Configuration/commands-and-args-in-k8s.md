- ENTRYPOINT (main command) --> command
- CMD (args)                --> args
- docker run ubuntu-sleeper 10
```
apiVersion: v1
kind: Pod
metadata;
    name:ubuntu-sleeper-pod
spec:
    container:
        - name: ubunru
          image: ubuntu
          args: ["10"]
```
- docker run ubuntu-sleeper 10 --entrypoint sleep-0.2
```
apiVersion: v1
kind: Pod
metadata;
    name:ubuntu-sleeper-pod
spec:
    container:
        - name: ubunru
          image: ubuntu
          command: ["sleep-0.2"]
          args: ["10"]
```
# Passing args in imperative commands

- k run webapp --image=kodeimage -- --color green
- anything after -- is an arg
- k run webapp --image=kodeimage --command=