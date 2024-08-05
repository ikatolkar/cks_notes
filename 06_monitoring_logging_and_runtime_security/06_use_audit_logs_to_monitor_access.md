# Monitoring, Logging and Runtime Security - 20%

## Use Audit Logs to monitor access

### Kube-apiserver auditing
Reference:
https://kubernetes.io/docs/tasks/debug/debug-cluster/audit/

#### Audit Policy Levels
1. None - Don't log any events that match this rule
2. Metadata - log request metadata (requesting user, timestamp, resource, verb, etc.) but not request or response body.
3. Request - log event metadata and request body but not response body. This does not apply for non-resource requests. 
4. RequestResponse - log event metadata, request and response bodies. This does not apply for non-resource requests. 

#### Enable Kube API Server Auditing
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
apiVersion: audit.k8s.io/v1 # This is required.
kind: Policy
# Don't generate audit events for all requests in RequestReceived stage.
omitStages:
  - "RequestReceived"
rules:
  # Log pod changes at RequestResponse level
  - level: RequestResponse
    resources:
    - group: ""
      # Resource "pods" doesn't match requests to any subresource of pods,
      # which is consistent with the RBAC policy.
      resources: ["pods"]
  # Log "pods/log", "pods/status" at Metadata level
  - level: Metadata
    resources:
    - group: ""
      resources: ["pods/log", "pods/status"]

  # Don't log requests to a configmap called "controller-leader"
  - level: None
    resources:
    - group: ""
      resources: ["configmaps"]
      resourceNames: ["controller-leader"]

  # Don't log watch requests by the "system:kube-proxy" on endpoints or services
  - level: None
    users: ["system:kube-proxy"]
    verbs: ["watch"]
    resources:
    - group: "" # core API group
      resources: ["endpoints", "services"]

  # Don't log authenticated requests to certain non-resource URL paths.
  - level: None
    userGroups: ["system:authenticated"]
    nonResourceURLs:
    - "/api*" # Wildcard matching.
    - "/version"

  # Log the request body of configmap changes in kube-system.
  - level: Request
    resources:
    - group: "" # core API group
      resources: ["configmaps"]
    # This rule only applies to resources in the "kube-system" namespace.
    # The empty string "" can be used to select non-namespaced resources.
    namespaces: ["kube-system"]

  # Log configmap and secret changes in all other namespaces at the Metadata level.
  - level: Metadata
    resources:
    - group: "" # core API group
      resources: ["secrets", "configmaps"]

  # Log all other resources in core and extensions at the Request level.
  - level: Request
    resources:
    - group: "" # core API group
    - group: "extensions" # Version of group should NOT be included.

  # A catch-all rule to log all other requests at the Metadata level.
  - level: Metadata
    # Long-running requests like watches that fall under this rule will not
    # generate an audit event in RequestReceived.
    omitStages:
      - "RequestReceived"
```

#### Other api server audit flags
1. --audit-log-path : file path that the backend uses to write logs, not specifying this means writing to stdout
2. --audit-log-maxage : maximum number of days to retain a log file
3. --audit-log-maxbackup : max number of audit log files to retain
4. --audit-log-maxsize : max size of each audit log file before rotation
5. --audit-webhook-config-file : file containing url, auth etc for webhok backend
6. --audit-webhook-initial-backoff : amount of time to wait before retrying, this is increased exponentially for subsequent retries
7. --audit-webhook-mode : batch, blocking, blocking-strict

