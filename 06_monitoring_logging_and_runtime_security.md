# Auditing

## Audit Policy Levels
1. None - Don't log any events that match this rule
2. Metadata - log request metadata (requesting user, timestamp, resource, verb, etc.) but not request or response body.
3. Request - log event metadata and request body but not response body. This does not apply for non-resource requests. 
4. RequestResponse - log event metadata, request and response bodies. This does not apply for non-resource requests. 

## Enable Kube API Server Auditing
1. Create a directory for storing the audit logs and audit policy 
```sh
mkdir -p /etc/kubernetes/audit
```

2. Mount the directory as HostPath Volumes in kube-apiserver

```sh
volumeMounts:
  - mountPath: /etc/kubernetes/audit-policy.yaml
    name: audit
    readOnly: true
  - mountPath: /var/log/kubernetes/audit/
    name: audit-log
    readOnly: false

volumes:
- name: audit
  hostPath:
    path: /etc/kubernetes/audit-policy.yaml
    type: File
- name: audit-log
  hostPath:
    path: /var/log/kubernetes/audit/
    type: DirectoryOrCreate

```

3. Add auditing related configuration in kube-apiserver

```sh
- --audit-policy-file=/etc/kubernetes/audit/audit-policy.yaml
- --audit-log-path=/etc/kubernetes/audit/audit.log
```

4. Audit policy

```sh

apiVersion: audit.k8s.io/v1
kind: Policy
omitStages:
  - "RequestReceived"
rules:
  - level: None
    resources:
    - group: ""
      resources: ["secrets"]
    namespaces: ["kube-system"]

  - level: None
    users: ["system:kube-controller-manager"]
    resources:
    - group: ""
      resources: ["secrets","configmaps"]

  - level: RequestResponse
    resources:
    - group: ""
      resources: ["secrets"]
  - level: Metadata
    omitStages:
      - "RequestReceived"

```

## Other api server audit flags
1. --audit-log-path : file path that the backend uses to write logs, not specifying this means writing to stdout
2. --audit-log-maxage : maximum number of days to retain a log file
3. --audit-log-maxbackup : max number of audit log files to retain
4. --audit-log-maxsize : max size of each audit log file before rotation
5. --audit-webhook-config-file : file containing url, auth etc for webhok backend
6. --audit-webhook-initial-backoff : amount of time to wait before retrying, this is increased exponentially for subsequent retries
7. --audit-webhook-mode : batch, blocking, blocking-strict
# Behavioral Analytics at host and container level

# Immutable Containers

