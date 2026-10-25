# Lab 1 — Git exposure, Jenkins execution, and AWS access

[Portfolio overview](../README.md) · [Evidence](../evidence/command-excerpts.md) · [Attack paths](../diagrams/attack-path.md)

## Objective and outcome

I assessed how exposed development artifacts could connect to source-control access, build execution, and cloud credentials in an authorized training lab. I traced a path from S3-hosted Git data to Jenkins build-agent execution and successful EC2 read access.

## Investigation

1. **Located the artifact exposure.** Application reconnaissance revealed an S3 bucket reference. Account-wide bucket listing was denied, but the configured AWS identity could list and download the known bucket. Its contents included Git metadata and pipeline configuration.
2. **Recovered a historical credential.** Gitleaks returned no findings in the recorded run. Manual Git-history review revealed a removed authentication value that remained in a previous revision. The lab account records subsequent authenticated Gitea access.
3. **Tested pipeline execution.** I modified a lab Jenkinsfile and triggered the Gitea/Jenkins build relationship. The supplied record includes a successful webhook response and a shell running as the Jenkins user.
4. **Investigated the build context.** Workspace, operating-system, mount, capability, and environment inspection exposed the build context and AWS credentials available to the job.
5. **Checked cloud access.** STS identified the active AWS principal, and an EC2 describe operation returned instance tags. Failed enumeration attempts were retained to document access limits.

## Engineering judgment

The manual history review added information the automated scan did not report. The pipeline test showed why repository write access can become code-execution authority. Credential injection into a trusted job can be legitimate; the security problem was reaching that credential-bearing execution context through the demonstrated attack path.

## Evidence and limits

The [34-section record](../evidence/command-excerpts.md) includes successful and denied operations. It contains sanitized, supplied excerpts rather than independently authenticated raw logs. The webhook response and subsequent shell are recorded, but the complete modification payload and uninterrupted session transcript are not included.

- Known-bucket access used credentials; anonymous public access is not established.
- An administrator-like username does not establish administrator permissions.
- Successful EC2 reads do not establish host compromise, unrestricted AWS access, or role escalation.
- Container enumeration did not demonstrate container escape.

## Defensive recommendations

Publish clean artifacts without Git metadata, rotate exposed credentials, protect pipeline definitions, isolate credential-bearing builds, and scope cloud identities to required operations. [Control plan and validation criteria](../remediation/security-recommendations.md).
