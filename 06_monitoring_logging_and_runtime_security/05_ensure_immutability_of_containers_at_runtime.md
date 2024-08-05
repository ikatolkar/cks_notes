# Ensure immutability of containers at runtime
## Immutable Containers
Enable readOnlyRootFilesystem in container security context
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: immutable-pod
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["sleep","3600"]
    securityContext:
      readOnlyRootFilesystem: true

```
