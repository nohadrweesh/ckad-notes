- env --> array, could be string, read from configmap or secret

```
env:
    - name: APP_COLOR
      value: PINK
    - name: APP_TYPE
      valueFrom:
        configMapKeyRef:
    - name: APP_ENV
      valueFrom:
        secretKeyRef:
```