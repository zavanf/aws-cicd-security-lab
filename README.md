# AWS Cloud & CI/CD Security Assessment

**Zavan Fahim | Authorized training environment**

I traced an attack path from S3-hosted Git history to source-control access, CI/CD pipeline poisoning, Jenkins build-agent execution, and AWS EC2 read access.

## Recorded results

- Recovered a historical source-control credential after Gitleaks returned no findings.
- Modified a Jenkinsfile and triggered a Gitea webhook to demonstrate build-agent command execution.
- Identified AWS job credentials, validated the associated identity, and confirmed EC2 read access.
- Documented the investigation in **34 sanitized command/output sections**, including failed attempts and access limitations.

## Explore the assessment

**S3-hosted repository → historical credential → Gitea → Jenkinsfile change → build-agent execution → AWS credentials → EC2 read access**

- [Case study](labs/01-git-jenkins-aws.md)
- [Sanitized command/output record](evidence/command-excerpts.md)
- [Attack-path diagram](diagrams/attack-path.md)
- Technical notes: [S3 and Git](notes/s3-git-exposure.md), [pipeline trust](notes/jenkins-pipeline-risk.md), [AWS access](notes/aws-iam-findings.md)
- [Proposed security controls and retest criteria](remediation/security-recommendations.md)

## Technical contributions

- **Manual investigation:** recovered a historical credential missed by an automated scan.
- **CI/CD security testing:** demonstrated how a source-controlled pipeline change could execute commands in a trusted build environment.
- **Cloud identity analysis:** examined exposed build credentials and validated identity and EC2 access without inferring privileges from usernames.
- **Reporting:** documented the attack path, access boundaries, failed attempts, and proposed controls with validation criteria.

**Technologies:** Git, Gitleaks, Gitea, Jenkins, Linux, AWS CLI, STS, IAM, S3, EC2.

CloudFormation was present in the pipeline. This portfolio does not claim infrastructure deployment or a verified exploit against CloudFormation or Lambda.

## Scope and evidence

This assessment took place in an authorized training environment. It is supported by a recorded summary and 34 sanitized command/output sections; the write-up is not an independent retest or a production engagement.

The recorded outcome establishes EC2 read access, not administrator access. The 34 sections measure documentation scope, not separate vulnerabilities or successful exploits.

All remediation is **proposed**. Public documentation omits credential values, target identifiers, payloads, exact lab answers, and proprietary exercise text.
