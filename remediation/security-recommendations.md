# Security recommendations

[Back to README](../README.md)

These are proposed controls derived from the documented assessment. They have not been implemented or retested as part of this portfolio write-up. Priority indicates suggested order, not a measured severity score.

## Prioritized plan

| Priority | Area | Recommended action | Suggested acceptance criteria |
| --- | --- | --- | --- |
| 1 | Exposed identities | Revoke or rotate disclosed SCM and AWS credentials; review relevant identity use and sessions | Old credentials fail; suspicious use is assessed; legitimate jobs work with restricted replacements |
| 1 | Published artifacts | Remove unintended Git data from published content and correct access or deployment configuration | Approved tests retrieve intended content only; Git metadata is inaccessible |
| 1 | Pipeline authority | Restrict pipeline changes and privileged build triggers; require review | An unauthorized harmless pipeline change cannot run with privileged access |
| 2 | Container capabilities | Drop unnecessary Linux capabilities and review agent runtime privileges | Required builds succeed with a documented minimal capability set; no escape test is implied |
| 2 | Agent isolation | Separate untrusted work from trusted builds and use disposable agents | Untrusted jobs cannot access protected secrets or previous-job state |
| 2 | Cloud identity | Use short-lived workload credentials and least-privilege policies | Required actions succeed; representative unnecessary actions are denied |
| 2 | Secret prevention | Scan repository history, new commits, and deployable artifacts | Synthetic test secrets are detected before publication |
| 3 | Monitoring | Correlate repository changes, webhook deliveries, builds, and cloud audit activity | A controlled test sequence can be reconstructed without logging secret values |
| 3 | Maintenance | Review access, pipeline ownership, and artifact contents regularly | Owners and review dates are recorded; exceptions have expiry dates |

## Implementation sequence

1. Preserve relevant audit evidence and identify affected credentials, jobs, artifacts, and identities.
2. Contain exposure and revoke compromised access in a coordinated sequence that prevents immediate re-exposure.
3. Correct the publication and pipeline controls that enabled the path.
4. Reduce credential lifetime, cloud permissions, and build-agent reach.
5. Run both negative access tests and positive tests of legitimate workflows.
6. Record results and remaining gaps before calling a finding remediated.

## Retest record template

Use one record per finding. Keep sensitive evidence in an appropriately restricted location rather than in the public repository.

| Field | Value to record |
| --- | --- |
| Finding | Affected boundary and original observation |
| Change | Control implemented and responsible owner |
| Validation date | Date of authorized retest |
| Expected behavior | Specific allowed and denied outcomes |
| Actual result | Observed result and sanitized evidence reference |
| Workflow health | Whether legitimate publishing/build/deployment still works |
| Outcome | Not retested, verified remediated, partially remediated, still open, or not reproduced |
| Remaining work | Gaps, dependencies, and next review |

No finding in this repository currently has a verified-remediated status.
