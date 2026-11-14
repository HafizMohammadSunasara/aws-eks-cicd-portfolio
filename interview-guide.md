# Explaining the project in a technical interview

## 30-second pitch

> I built a hands-on AWS CI/CD flow that takes a GitHub commit through CodePipeline and CodeBuild, creates a Docker image in ECR, and deploys a Flask application on Amazon EKS. The pipeline eventually went green, but the app still wasn't publicly reachable. I investigated an EKS Auto Mode STS `TagSession` denial, then discovered the load balancer was internal. I configured two public subnets with IGW routing and correct tags, created an internet-facing Kubernetes Service, and verified the app response in Chrome. I also documented the security and cost debts I would prioritize before production.

## Deep-dive questions and strong, honest answers

**Q: What does CodePipeline actually do?**  
A: Orchestrates the source, build and deploy stages and tracks execution outcomes. CodeBuild performs configured commands; ECR stores built images; EKS runs the workload.

**Q: Why wasn't a green pipeline enough?**  
A: Pipeline stages only confirm their configured actions completed. An internal NLB was healthy in AWS but intentionally inaccessible from a public browser. End-user validation is an additional requirement.

**Q: What was the error behind the pending load balancer?**  
A: `kubectl describe service` showed `FailedBuildModel`; the EC2 route-table discovery call failed while acquiring credentials because `sts:TagSession` returned 403. The failing layer was AWS IAM/STS, not the Flask code.

**Q: Why not make every subnet public?**  
A: The internet-facing NLB requires public placement and an IGW route. Worker nodes can remain private, using NAT for egress. Publicizing all nodes increases attack surface with no benefit to app ingress.

**Q: Does four Ready nodes mean the app is highly available?**  
A: No. The documented Deployment had one replica; node readiness does not guarantee app failover. Multiple replicas, topology spread and disruption tests are necessary.

**Q: Why create a second Service rather than change the old NLB?**  
A: The NLB scheme is effectively a creation-time characteristic. A second Service provided a safer validation path without destroying the original. It temporarily introduced a second billable NLB, which needs planned cleanup.

**Q: What's the highest-priority problem you discovered after it worked?**  
A: A hardcoded Docker Hub PAT in the original public buildspec. It must be revoked, removed from repository contents/history, and replaced with a secure credential mechanism—or omitted entirely if ECR is sufficient.

**Q: Could I reproduce the whole AWS setup from the repo today?**  
A: Not automatically. The live VPC/EKS configuration was created partly in the console. The repository documents it, includes source/manifests and identifies IaC as the next iteration.

**Q: What would you change first for a team?**  
A: Token rotation, least-privilege role separation, CI/CD artifact consistency, HTTPS, structured rollout/rollback and controlled IaC.

## Recruiter-readable bullet

> Built and debugged a GitHub-to-Amazon EKS CI/CD workflow using AWS CodePipeline, CodeBuild, Docker, ECR and Kubernetes; resolved IAM/STS and public NLB networking issues, and verified an internet-accessible Flask application with evidence-backed incident documentation.
