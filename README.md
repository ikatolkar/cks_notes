# CKS Notes

## Best Github repo for CKS
https://github.com/walidshaari/Certified-Kubernetes-Security-Specialist

https://github.com/wuestkamp/Certified-Kubernetes-Security-Specialist


## Killer CKS
https://killercoda.com/killer-shell-cks

## Weightage

TOPIC										%
1. Cluster Setup							10
2. Cluster Hardening						15
3. System Hardening							15
4. Minimize Microservice Vulnerabilities	20
5. Supply Chain Security					20
6. Monitoring, Logging, Runtime Security	20


## Curriculum

### Cluster Setup
- Use Network security policies to restrict cluster level access
- Use CIS benchmark to review the security configuration of Kubernetes components
 (etcd, kubelet, kubedns, kubeapi)
- Properly set up Ingress objects with security control
- Protect node metadata and endpoints
- Minimize use of, and access to, GUI elements
 Verify platform binaries before deploying

### Cluster Hardening
- Restrict access to Kubernetes API
- Use Role Based Access Controls to minimize exposure
- Exercise caution in using service accounts e.g. disable defaults, minimize permissions on newly created ones
- Update Kubernetes frequently

### System Hardening
- Minimize host OS footprint (reduce attack surface)
- Minimize IAM roles
- Minimize external access to the network
- Appropriately use kernel hardening tools such as AppArmor, seccomp

### Minimize Microservice Vulnerabilities
- Setup appropriate OS level security domains
- Manage kubernetes secrets
- Use container runtime sandboxes in multi-tenant environments (e.g. gvisor, kata containers)
- Implement pod to pod encryption by use of mTLS

### Supply Chain Security
- Minimize base image footprint
- Secure your supply chain: whitelist allowed image registries, sign and validate images
- Use static analysis of user workloads (e.g. kubernetes resources, docker files)
- Scan images for known vulnerabilities

### Monitoring, Logging, Runtime Security
- Perform behavioral analytics of syscall process and file activities at the host and container level to detect malicious activities
- Detect threats within physical infrastructure, apps, networks, data, users and workloads
- Detect all phases of attack regardless where it occurs and how it spreads
- Perform deep analytical investigation and identification of bad actors within environment
- Ensure immutability of containers at runtime
- Use Audit Logs to monitor access

## Resources

https://github.com/wuestkamp/Certified-Kubernetes-Security-Specialist#urls-allowed-in-the-extra-single-tab

https://github.com/moabukar/CKS-Exercises-Certified-Kubernetes-Security-Specialist

https://www.youtube.com/watch?v=wqsUfvRyYpw

https://kubernetes.io/docs/tutorials/security/

https://kubernetes.io/blog/2018/03/principles-of-container-app-design/

https://www.microsoft.com/en-us/security/blog/2020/04/02/attack-matrix-kubernetes/

https://kodekloud.com/courses/certified-kubernetes-security-specialist-cks/
