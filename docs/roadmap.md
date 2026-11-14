# Roadmap — turning a successful lab into long-term engineering value

This project's highest value is the reproducible *thinking*, not the temporary NLB URL.

| Stage | Focus | Evidence to add | Completion criterion |
|---|---|---|---|
| 0 — achieved | Delivery and public access | Green pipeline, ECR, EKS, public NLB, browser | HTTP response verified from outside AWS |
| 1 — priority | Safety and hygiene | Token rotation, IAM role split plan, cost cleanup | No exposed credentials; one intended LB; budget guardrails |
| 2 | Repeatable delivery | SHA image tag, automated tests, deployment status | Trace commit → digest → running Pod |
| 3 | Production-style Kubernetes | Probes, requests, multi replicas, PDB, rolling release | Chaos test preserves user response |
| 4 | Identity and network | Narrow IAM, SG review, HTTPS | Access paths documented, TLS verified |
| 5 | Observability | Metrics, logs, alerts, dashboards | Trigger an intentional fault and see alert + resolution |
| 6 | Infrastructure as Code | Terraform modules for VPC/EKS/ECR | Destroy/recreate test cluster from code |
| 7 | Release engineering | GitOps or Helm, staged deployment | Approval/rollback simulation and audit trail |

## Investment thesis: why build this project deeper?

An employer hires an engineer to **reduce deployment risk and recovery time**, not merely to type AWS commands. This case demonstrates the seed of that capability:

1. **Release confidence:** source and artifact tracking helps teams know what was shipped.
2. **Failure isolation:** distinguishing app health, Kubernetes health, NLB scheme and IAM trust cuts guesswork.
3. **Operational safety:** keeping private nodes private and using public NLB placement models a common internet-facing backend pattern.
4. **Cost awareness:** removing redundant NLBs and setting budgets turns a working lab into a sustainable platform.
5. **Security awareness:** finding and rotating a source-embedded token is as valuable as making a deploy work.

## Evidence-first portfolio evolution

For each future phase: define success criteria, capture *before* and *after*, log commands/configuration, record trade-offs and add a screenshot with its date. Do not claim capabilities merely because an AWS service exists in the account.
