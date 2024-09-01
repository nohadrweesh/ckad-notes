- k create secret generic db_secret --from-literal=host=mysql
- k create secret generic db_secret  --from-file=secret_prop.conf
secret_prop.conf
```
DB_HOST: mysql
DB_USER: root
DB_PASSWORD: passwrd
```

- Decalarative data instead of spec 
- We need to add data in encoded format
  - Encode: echo -n "mysql" | base64
  - Decode: echo -n "bXlzcWw=" | base64 -d

```
apiVersion: v1
kind: Secret
metadata:
    name: secret
data:
    DB_HOST: bxkhf
    DB_USER: bxkhffff
    DB_PASSWORD: bxkhfesrfg

```

- k describe secret --> doesn't show secret data values
- k get secret secret_name -o yaml -->  shows secret data values encoded

- Injecting ConfigMap into a pod
    - ENV: Using envFrom

    ```
        apiVersion: v1
        kind: Pod
        metadata:
            name: pod
            labels:
                app: frontend
        spec:
            containers:
                - name: nginx
                  image: nginx
                  envFrom:
                    - secretRef:
                        name: secret_name
    ```
        
    - SINGLE ENV: Using env
    ```
        apiVersion: v1
        kind: Pod
        metadata:
            name: pod
            labels:
                app: frontend
        spec:
            containers:
                - name: nginx
                  image: nginx
                  env:
                    - name:APP_COLOR
                      valueFrom:
                        secretKeyRef:
                            name: secret_name
                            key: APP_COLOR
    ```

    - Volume: Using volumes
    ```
        apiVersion: v1
        kind: Pod
        metadata:
            name: pod
            labels:
                app: frontend
        spec:
            containers:
                - name: nginx
                  image: nginx
            volumes:
                - name: secret-volume
                  secret:
                    name: secret_name
                  
    ```
- Secrets are encoded NOT encrypted --> don't push to SCM
- EVEN in etcd secrets are not encrypted --> Enable encryption at rest
- In same namespace any pod can use the secrets --> Configure RBAC for least privilage access to secrets
- Configure third-party secrets store providers like AWS,Vault
- Secrets are not safe but are a safer option
- It's not secrets that are safe it's the practices around it:
  - Secrets are sent to nodes with pods use this secret
  - Kubelet writes secrets to temp files instead of disk storage
  - Once pod is deleted related secret is deleted
  
## Encryption at rest
- etcd-client --> etcd command this is a client to get info from the etcd server (pod running)
- by viewing the secrets by etcd command , you'll see it's not encrypted
- By creating EcryptionConfiguration we're encrypting secrets at rest
- in EcryptionConfiguration Object, we need to use different provider other than identity, identity means no Encryption       
    