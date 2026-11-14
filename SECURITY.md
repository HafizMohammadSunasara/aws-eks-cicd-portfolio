# Security, disclosure, and responsible reuse

## Urgent: exposed credential in original history

During documentation review, the public upstream `buildspec.yml` was found to contain a literal Docker Hub personal access token passed into a `docker login` command. The credential is deliberately **omitted** from this ZIP. **Its validity has not been tested.** Assume exposure: revoke the token from Docker Hub, issue a new token only if needed, and use a managed secret store/environment secret—not a repository literal. Removing it from the current file is **not enough** because previous commits may retain it. Follow your organization's approved secret-removal process to audit Git history, CI logs, and caches. Never post a screenshot containing a token.

## Other risks observed

- The EKS cluster role appeared to reuse a CodeBuild service role with many unrelated managed policies. A least-privilege, service-specific IAM design is a future priority.
- The verified public endpoint is HTTP-only. Do not send passwords or sensitive data over the demo link.
- This ZIP retains resource identifiers and limited AWS console screenshot metadata for provenance. Review evidence before publishing it.
- The original Dockerfile uses an older Python/Flask stack and runs as root. Harden and update dependencies after regression testing.
- An old **internal** NLB and a public NLB were both active at verification; recurring AWS charges may continue until safely cleaned up.

## Do not commit

Passwords, access keys, Docker PATs, `.env` files, SSH keys, Kubernetes credentials, private certificates, production data, or command outputs containing bearer tokens.

## Reporting

For educational feedback or security review, open a GitHub issue containing a non-sensitive reproduction. Never disclose credentials in public issues.
