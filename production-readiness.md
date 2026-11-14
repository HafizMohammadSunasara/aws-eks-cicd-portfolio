# Production-readiness assessment — deliberately honest

## Current status

| Area | Evidence | Next work |
|---|---|---|
| GitHub → AWS pipeline | **Verified:** all three stages green | Confirm immutable image tags, artifact promotion and release provenance |
| Container image | **Verified:** ECR image present | Add build attestations and digest pinning |
| Kubernetes | **Verified:** app and nodes running | Multiple replicas, PDBs, requests/limits, liveness/readiness probes |
| Public reachability | **Verified:** HTTP browser response | HTTPS, a proper domain and restricted ingress |
| Identity & permissions | **Debt:** shared overprivileged cluster/CodeBuild role | Dedicated IAM service roles, minimal policies, access review |
| CI source security | **Critical debt:** token in historic public buildspec | **Revoke immediately**, audit history, secret scanning |
| Application security | Flask demo | Auth only if needed, rate limiting, runtime updates, threat model |
| Observability | Not demonstrated | CloudWatch + metrics + SLO/error budgets + alerts |
| Availability | 4 Ready nodes, 1 documented app replica | Multi-AZ replica spreading, failover and chaos test |
| Costs | Two NLBs coexisted, NAT present | Remove unused internal LB safely, cost budgets, cleanup runbook |
| Reproducibility | AWS console-driven setup | Terraform/OpenTofu + version-controlled infrastructure |

## P0: security and cost before broad publicity

1. **Revoke/rotate the exposed Docker Hub PAT**. The ZIP removes it, but only the account owner can invalidate it. Search Git history/CI logs for copies and enable secret scanning.
2. **Keep only necessary load balancers.** The original `my-app-service` generates an Internal NLB and `my-app-public-service` generates the new Internet-facing NLB. Don't delete a controller-managed LB directly through EC2; adjust Kubernetes manifests and Service objects through a controlled release. Confirm pipeline isn't recreating the old Service after the next push.
3. **Validate IAM responsibilities.** The cluster role being shared with a broadly permissioned CodeBuild role is not appropriate for production. Plan role separation and a migration rather than blindly detaching active policies.
4. **Budget and resource cleanup:** four on-demand EC2 nodes, managed EKS, NLB(s), NAT Gateway(s), public IPv4 and data transfer can generate recurring charges. Set budgets and teardown when the lab is idle; preserve evidence first.

## P1: make it resilient and observable

- Replace `latest`-only deployments with a commit SHA or immutable ECR digest.
- Use `replicas: 2+`, `topologySpreadConstraints`, PDBs and readiness probes; verify failure recovery.
- Remove `kubectl replace --force`, which can destroy/recreate workloads and defeat rolling updates.
- Set `resources.requests/limits` and `securityContext` to run as non-root with a supported runtime.
- Add deploy rollback gates, health verification and automated release checks.
- Route HTTPS through a certificate-capable load-balancer/listener configuration and manage a domain.
- Add CloudWatch/EKS logging, metrics, dashboards, alerting and a failure drill.

## P2: infrastructure engineering

- Express VPC CIDR, public/private subnets, NAT/IGW routes, security groups, EKS, IAM and ECR as IaC.
- Separate staging and production, use PR reviews and protected branches.
- Add unit/API integration tests and security scans (SCA/container scans). **Do not claim scans ran:** Clair commands were commented out in the fetched buildspec.
- Automate cleanup of ephemeral environments and record costs per deployment.

## Honest portfolio phrasing

Use: **"Built and verified an end-to-end AWS CodePipeline → ECR → EKS deployment with an internet-facing Network Load Balancer, and documented real IAM and networking incidents."**

Avoid without evidence: **"zero-downtime production grade," "fully secure," "self-healing," "tested multi-AZ failover," "100% automated infrastructure,"** or **"complete DevSecOps scanning."**
