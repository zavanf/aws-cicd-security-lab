# AWS IAM findings

**Scope: Lab 1 — exposed Git history and Jenkins pipeline execution.** [Case study](../labs/01-git-jenkins-aws.md)

[Back to README](../README.md) · [Previous: Jenkins pipeline risk](jenkins-pipeline-risk.md) · [Recommendations](../remediation/security-recommendations.md)

## Recorded observation

AWS credentials were identified in Jenkins pipeline environment variables. The AWS CLI was used to enumerate IAM and EC2 access within the authorized training environment.

## Security interpretation

The demonstrated boundary was movement from build-agent execution to access to a cloud identity's credentials. The resulting impact depends on the identity's effective permissions, policy conditions, session restrictions, and accessible resources.

Credential exposure is a finding. Whether an identity is overprivileged requires comparing its effective access with the workload's legitimate needs. The available summary does not provide enough policy evidence to make that comparison conclusively.

## Scope and limits

| Topic | Supported statement | Limit |
| --- | --- | --- |
| Credential handling | AWS credentials were discovered in the pipeline environment | Credential lifetime and issuance method are not documented here |
| IAM and EC2 | Access was enumerated using the AWS CLI | The sanitized record shows successful STS identity checks and EC2 instance-tag reads; comprehensive permissions are not established |
| Lambda and CloudFormation | Included in the broader environment reviewed | No verified exploit or resource modification is claimed |
| Privilege escalation | A cloud attack surface was considered | Administrator access, role escalation, and account takeover are not established |
| Impact | Exposure created an opportunity to exercise granted cloud access | No data theft, persistence, destructive action, or production compromise is claimed |

## Proposed controls

- Revoke exposed long-lived credentials and address any active sessions as appropriate to their credential type.
- Prefer short-lived workload identities with narrowly scoped trust relationships.
- Grant only the actions and resources required by each build or deployment stage.
- Review role-assumption permissions, sensitive IAM actions, and policy conditions against documented job requirements.
- Separate build and deployment identities where their permissions differ.
- Review available cloud audit events and alert on unexpected identity use or sensitive API activity.

## Suggested validation

Document the intended identity and permitted operations. Verify that required deployment actions succeed while representative out-of-scope actions are denied. Confirm expired or revoked credentials cannot be reused, and that controlled test activity appears in the available audit trail.

**Status:** recommendations only; no policy changes or remediation retest documented.

## Supporting excerpts

See the [sanitized command/output record](../evidence/command-excerpts.md) for recorded observations and their interpretation limits.
