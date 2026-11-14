# Publish with control: GitHub portfolio checklist

The existing public source repository is [`HafizMohammadSunasara/eks-cicd-pipeline`](https://github.com/HafizMohammadSunasara/eks-cicd-pipeline).

**Suggested repository tagline:** `Real AWS CI/CD to Amazon EKS — a publicly verified Flask deployment, deep architecture, and evidence-backed IAM/VPC/NLB troubleshooting.`

**Suggested GitHub topics:** `aws`, `devops`, `cicd`, `codepipeline`, `codebuild`, `amazon-ecr`, `amazon-eks`, `kubernetes`, `docker`, `flask`, `vpc`, `network-load-balancer`, `troubleshooting`, `cloud-engineering`.

## Avoid accidental live deployment changes

If CodePipeline automatically deploys on commits to `main`, **pushing this ZIP to `main` will likely trigger a pipeline run**. The packaged `buildspec.yml` was revised and not tested in the live pipeline; `k8s-deployment.yml` still includes the old internal Service. Strong recommendation: create a **portfolio-review branch**, inspect the changes, rotate any exposed credentials, and merge only after reviewing the effects on CodePipeline.

## Visual quality checklist

- [ ] Hero banner renders in GitHub README.
- [ ] Architecture, network, and incident SVG files render.
- [ ] Click all 21 screenshot links; verify they prove the corresponding claims.
- [ ] README first screen answers: what was built, why it matters, what worked, what broke.
- [ ] README does not claim production-ready, scanner enforcement, or automatic IaC.
- [ ] Only publish screenshots after reviewing account identity and security-sensitive metadata.
- [ ] Add a real commit/release tag to trace this portfolio snapshot.
- [ ] Pin the repository in GitHub profile; add it to LinkedIn Projects with 1–2 evidence images.

## Recruiter walkthrough (90 seconds)

1. **README hero:** the gap between a green pipeline and successful user delivery.
2. **Architecture:** show code flow and private/public VPC routing.
3. **Proof:** green pipeline → ECR image → EKS nodes → internet-facing NLB → browser response.
4. **Deep dive:** explain `sts:TagSession` failure with actual event wording and why the internal NLB was wrong.
5. **Reflection:** identify token exposure, shared IAM role and cost duplication instead of pretending everything is production perfect.
