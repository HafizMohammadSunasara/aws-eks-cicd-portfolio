# Reproduction guide — safe, scoped, and honest

## What the ZIP can reproduce

- The **Flask app** source, original Dockerfile and requirements.
- A minimal container build and local HTTP demonstration.
- The Kubernetes Deployment and a public NLB Service manifest as configuration examples.
- A CodeBuild buildspec **sanitized revised example** (not guaranteed to match the successful original action's override settings).

## What it cannot reproduce automatically

The project currently lacks Terraform/CloudFormation definitions for VPC, route tables, IGW/NAT, IAM, EKS, CodePipeline and CodeBuild. These were provisioned/configured through AWS console and previous lab activity. Reproducing the full cloud deployment therefore requires manual resource creation or a future IaC version.

## Local application test

Use a compatible Python environment (the original source pins Flask 2.0.3/Werkzeug 2.0.2 on Python 3.9). In a disposable environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python app.py
# In another shell:
curl http://127.0.0.1:80/
```

Or, if Docker is configured:

```bash
docker build -t eks-cicd-portfolio:local .
docker run --rm -p 8080:80 eks-cicd-portfolio:local
curl http://127.0.0.1:8080/
```

Expected response: `Welcome to the AL=Nafi!`. Do not run the Docker build with secrets in the working directory.

## AWS requirements before doing anything billable

1. AWS account and appropriate IAM identities/role trust, a budget and a teardown plan.
2. VPC address space large enough for EKS CNI; two AZs; public IGW routes and private NAT egress where required.
3. EKS cluster and nodes; ECR repository named `eks-ecr-app`; CodeConnections GitHub connection; CodePipeline/CodeBuild definitions.
4. CodeBuild variables `IMAGE_REPO_NAME=eks-ecr-app` and `AWS_DEFAULT_REGION=us-east-1`, plus ECR push permissions. **Privileged mode** is normally required when building Docker images inside CodeBuild.
5. Build stage artifact/deploy stage configuration reviewed against `k8s-deployment.yml` and actual EKS permissions.

## Public Service (after EKS is already functional)

The example in [`k8s/my-app-public-service.yml`](../k8s/my-app-public-service.yml) contains **historical subnet IDs for this one VPC**. Substitute public subnet IDs belonging to your own VPC; associate those subnets with an IGW route and EKS public ELB tags. Then:

```bash
aws eks update-kubeconfig --region us-east-1 --name eks
kubectl apply -f k8s/my-app-public-service.yml
kubectl get svc my-app-public-service -n default
kubectl describe svc my-app-public-service -n default
```

Don't apply the original `k8s-deployment.yml` blindly into the verified account: its `my-app-service` is an additional **internal** NLB. Control the Service lifecycle and consolidate manifests before another pipeline release.

## Validation and cleanup

Check that Pods are Ready, Service endpoints exist, NLB is **Internet-facing**, target group targets are Healthy, and a browser/curl request returns HTTP 200. Finally, check account charges and remove unused Kubernetes-controlled Services, NLBs, NAT gateways and extra lab nodes through an approved teardown sequence. **Avoid deleting an AWS LB directly if EKS still owns it.**
