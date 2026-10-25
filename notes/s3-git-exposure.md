# S3 and Git exposure

**Scope: Lab 1 — exposed Git history and Jenkins pipeline execution.** [Case study](../labs/01-git-jenkins-aws.md)

[Back to README](../README.md) · [Next: Jenkins pipeline risk](jenkins-pipeline-risk.md)

## Recorded observation

The authorized lab exposed S3-hosted Git repository data. Reviewing Git history revealed source-control credentials that enabled access to Gitea.

## Why this matters

Git stores historical versions of files. A secret removed from the latest revision can remain in earlier commits or other Git objects. Publishing development metadata alongside a website or application artifact can therefore reveal information absent from the visible application.

In this lab, the historical credential linked repository disclosure to authenticated source-control access. That access became relevant to the later Jenkins pipeline stage.

## Evidence boundaries

The recorded summary supports exposed Git data, historical credential discovery, and subsequent Gitea access. It does not establish whether the underlying cause was a bucket policy, an object ACL, a deployment mistake, or another delivery configuration. This repository does not include the original object paths, usernames, credential values, or history extracts.

## Proposed controls

- Build deployable content in a clean output directory and explicitly allowlist the files published.
- Exclude Git metadata, local configuration, backups, and secret-bearing files from artifacts.
- Restrict S3 access to the intended audience and delivery mechanism; apply public-access controls appropriate to that design.
- Revoke or rotate disclosed credentials and review their use. Removing a file or rewriting history does not invalidate a leaked credential.
- Scan commits, repository history, and release artifacts for secrets.

## Suggested validation

Using approved test identities, verify that only intended artifacts are reachable and Git metadata cannot be retrieved. Test secret scanning with synthetic markers. Confirm that revoked credentials fail authentication and that the replacement identity has only required permissions.

**Status:** recommendations only; no remediation retest documented.

## Supporting excerpts

See the [sanitized command/output record](../evidence/command-excerpts.md) for recorded observations and their interpretation limits.

The supplied S3 commands used a configured AWS identity. They establish access to the known bucket under that identity; they do not demonstrate unauthenticated public bucket access.
