# Original source snapshot and changes made for portfolio safety

**Upstream:** [`HafizMohammadSunasara/eks-cicd-pipeline`](https://github.com/HafizMohammadSunasara/eks-cicd-pipeline), `main` branch, public at review time. The source contained six files: `Dockerfile`, `README.md`, `app.py`, `buildspec.yml`, `k8s-deployment.yml`, `requirements.txt`.

## File-by-file provenance

| File | ZIP treatment |
|---|---|
| `app.py` | Source content preserved (Flask returns `Welcome to the AL=Nafi!`) |
| `requirements.txt` | Original Flask/Werkzeug pins preserved |
| `Dockerfile` | Original commands preserved, with a comment; **not** security-hardened |
| `k8s-deployment.yml` | Original Deployment + internal Service preserved, comments added for clarity |
| `README.md` | Completely replaced with custom recruiter-facing case-study landing page |
| `buildspec.yml` | **Not preserved verbatim:** original had a literal Docker Hub PAT and inconsistent image names; replaced by a safe, revised **example** that is NOT live-tested |
| `k8s/my-app-public-service.yml` | Added from the service configuration demonstrated in CloudShell |
| `assets/`, `docs/`, `evidence/` | New portfolio diagrams and technical documentation |

## Current run vs GitHub file discrepancy

The screenshot of the final CodePipeline run and its source commit proves an actual successful release, but does **not** prove that the public `buildspec.yml` file in `main` executed exactly as written: the source file contains broken tag/push alignment and a legacy cluster deployment call. CodeBuild buildspec overrides, alternate build configuration, or post-commit changes may account for this mismatch. We therefore separate **observed AWS outcome** from **source inspection**.

## Before replacing your existing GitHub repository

1. Revoke the exposed Docker Hub token and consider approved history scrubbing.
2. Inspect the real AWS CodeBuild project's **Buildspec selection**, privileged mode and environment variables. Validate the revised buildspec on a disposable branch/run before merging it.
3. Decide which Service YAML the pipeline should own; the original `my-app-service` can recreate the Internal NLB. The ZIP does **not** silently delete or mutate live infrastructure.
4. Verify screenshots contain no personal data you would prefer private, then upload the curated portfolio.

This package was created as an **upload-ready snapshot**; it has not been automatically pushed to GitHub or used to change the running AWS environment.
