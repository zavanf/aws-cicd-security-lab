# Jenkins pipeline risk

**Scope: Lab 1 — exposed Git history and Jenkins pipeline execution.** [Case study](../labs/01-git-jenkins-aws.md)

[Back to README](../README.md) · [Previous: S3/Git exposure](s3-git-exposure.md) · [Next: AWS IAM findings](aws-iam-findings.md)

## Recorded observation

Within the authorized lab, a Jenkinsfile was modified through repository access and a Gitea webhook triggered a Jenkins build. The build demonstrated command execution on the build agent. Enumeration of the Jenkins container then identified AWS credentials in pipeline environment variables.

## Why this matters

A pipeline definition is executable configuration. When changes to it are accepted and built, repository write permissions can carry execution authority over the build agent. Any secrets accessible to that job may also be accessible to code running in the job.

Webhook delivery, repository permissions, pipeline review, job isolation, and credential scope all affect this boundary. A working webhook is not by itself a vulnerability; the risk arises from which changes it can cause Jenkins to execute and with what authority.

## Evidence boundaries

The recorded summary supports build-agent execution and credential discovery in the pipeline environment. It does not establish Jenkins controller compromise, a container escape, host compromise, or the exact branch-protection and credential-binding configuration. No executable payload or original Jenkinsfile is published here.

## Proposed controls

- Require review for pipeline-definition changes and restrict who can approve them.
- Ensure required checks apply to the exact revision being built and deployed.
- Restrict webhook and job-trigger authority; validate webhook authenticity where supported.
- Keep untrusted changes and pull-request builds separate from credential-bearing jobs.
- Use isolated, disposable agents with restricted filesystem, network, and credential access.
- Deliver credentials only to the jobs and stages that require them, using temporary identities where feasible.
- Treat log masking as protection against accidental display, not as a boundary against code executing inside a job.

## Suggested validation

With a harmless test change, confirm that an unauthorized pipeline modification cannot enter a privileged build. Verify untrusted jobs cannot access protected credentials. Confirm agents are discarded or cleaned between jobs and that a legitimate approved build still succeeds.

**Status:** recommendations only; no remediation retest documented.

## Supporting excerpts

See the [sanitized command/output record](../evidence/command-excerpts.md) for recorded observations and their interpretation limits.

The supplied container-capability excerpt additionally records broad PID 1 capabilities, including powerful administration capabilities in the abbreviated decode. This warrants a least-privilege review; no container escape is demonstrated.
