# Mapping of CNAPP (Prisma) AWS Policies to CIS AWS Foundations Benchmark Requirements (per version)

Understand which CIS AWS Benchmark version supports each Prisma CNAPP policy, and whether a control was deprecated, removed, replaced, or carried forward as versions progress.

**How to read the cells:**

- The **Security Hub control** column shows the AWS Security Hub control ID the policy maps to (the bridge between Prisma's naming and CIS).
- For **CIS v1.2.0, v1.4.0, v3.0.0, v5.0.0**, each cell shows the *actual CIS requirement ID* (e.g. `3.1`) or an authoritative status — `Not supported – CIS removed`, `Not supported – replaced by 5.3/5.4`, `Manual check`, `Not supported – added later` — verified against AWS's official Security Hub CIS documentation.
- For **v1.3.0, v1.5.0, v2.0.0, v4.0.0, v6.0.0**, the cell shows `Supported` / `Not supported` from the Prisma export (AWS does not publish a verified ID crosswalk for these versions).
- The **Lifecycle note** flags controls dropped in the newest version (v6.0.0), introduced later, intermittently mapped, or with no CIS equivalent.

> Verified CIS IDs are shown for the four versions AWS officially documents (v1.2.0, v1.4.0, v3.0.0, v5.0.0). Status phrases like 'CIS removed' or 'replaced by' come directly from AWS's published version-comparison table. CIS renumbered sections across versions (e.g. logging was section 3 in v1.2.0, section 4 in v1.4.0), so IDs differ for the same control between versions.

| Control / Policy (Prisma CNAPP) | Security Hub control | CIS v1.2.0 requirement | CIS v1.3.0 requirement | CIS v1.4.0 requirement | CIS v1.5.0 requirement | CIS v2.0.0 requirement | CIS v3.0.0 requirement | CIS v4.0.0 requirement | CIS v5.0.0 requirement | CIS v6.0.0 requirement | Lifecycle note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| AWS Access key enabled on root account | [IAM.4] | 1.12 | Supported | 1.4 | Supported | Supported | 1.4 | Supported | 1.3 | Supported | — |
| AWS CloudTrail bucket is publicly accessible | [CloudTrail.6] | 2.3 | Supported | 3.3 | Supported | Supported | Not supported – CIS removed | Not supported | Not supported – CIS removed | Not supported | Not in v6.0.0 (last: v2.0.0) |
| AWS CloudTrail is not enabled with multi trail and not capturing all management events | [CloudTrail.1] | 2.1 | Supported | 3.1 | Supported | Supported | 3.1 | Supported | 3.1 | Supported | — |
| AWS CloudTrail log validation is not enabled in all regions | [CloudTrail.4] | 2.2 | Supported | 3.2 | Not supported | Not supported | 3.2 | Not supported | 3.2 | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS CloudTrail logs are not encrypted using Customer Master Keys (CMKs) | [CloudTrail.2] | 2.7 | Supported | 3.7 | Not supported | Not supported | 3.5 | Not supported | 3.5 | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS CloudTrail trail logs is not integrated with CloudWatch Log | [CloudTrail.5] | 2.4 | Supported | 3.4 | Supported | Supported | Not supported – CIS removed | Not supported | Not supported – CIS removed | Not supported | Not in v6.0.0 (last: v2.0.0) |
| AWS Config Recording is disabled | [Config.1] | 2.5 | Supported | 3.5 | Not supported | Not supported | 3.3 | Not supported | 3.3 | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS Customer Master Key (CMK) rotation is not enabled | [KMS.4] | 2.8 | Supported | 3.8 | Not supported | Not supported | 3.6 | Not supported | 3.6 | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS Default Security Group does not restrict all traffic | [EC2.2] | 4.3 | Supported | 5.3 | Not supported | Not supported | 5.4 | Not supported | 5.5 | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS EBS volume region with encryption is disabled | [EC2.7] | Not supported | Supported | 2.2.1 | Supported | Supported | 2.2.1 | Supported | 5.1.1 | Supported | Introduced v1.3.0 |
| AWS EC2 Instance IAM Role not enabled | — | Supported | Supported | Supported | Not supported | Not supported | Not supported | Not supported | Not supported | Not supported | Not in v6.0.0 (last: v1.4.0); No CIS Security Hub equivalent |
| AWS EC2 instance not configured with Instance Metadata Service v2 (IMDSv2) | [EC2.8] | Not supported | Not supported | Not supported | Not supported | Supported | 5.6 | Supported | 5.7 | Not supported | Not in v6.0.0 (last: v5.0.0); Introduced v2.0.0 |
| AWS Elastic File System (EFS) with encryption for data at rest is disabled | [EFS.1] | Not supported | Not supported | Not supported | Supported | Supported | 2.4.1 | Supported | 2.3.1 | Supported | Introduced v1.5.0 |
| AWS IAM AWSCloudShellFullAccess policy is attached to IAM roles, users, or IAM groups | [IAM.27] | Not supported – added later | Not supported | Not supported – added later | Not supported | Supported | 1.22 | Supported | 1.21 | Supported | Introduced v2.0.0 |
| AWS IAM Access analyzer is not configured | [IAM.28] | Not supported – added later | Supported | Not supported – added later | Supported | Supported | 1.20 | Supported | 1.19 | Supported | Introduced v1.3.0 |
| AWS IAM has expired SSL/TLS certificates | [IAM.26] | Not supported – added later | Supported | Not supported – added later | Supported | Supported | 1.19 | Supported | 1.18 | Supported | Introduced v1.3.0 |
| AWS IAM password policy allows password reuse | [IAM.16] | 1.10 | Supported | 1.9 | Supported | Supported | 1.9 | Supported | 1.8 | Supported | — |
| AWS IAM password policy does not expire in 90 days | [IAM.17] | 1.11 | Not supported | Not supported – CIS removed | Not supported | Not supported | Not supported – CIS removed | Not supported | Not supported – CIS removed | Not supported | Not in v6.0.0 (last: v1.2.0) |
| AWS IAM password policy does not have a lowercase character | [IAM.12] | 1.6 | Not supported | Not supported – CIS removed | Not supported | Not supported | Not supported – CIS removed | Not supported | Not supported – CIS removed | Not supported | Not in v6.0.0 (last: v1.2.0) |
| AWS IAM password policy does not have a minimum of 14 characters | [IAM.15] | 1.9 | Supported | 1.8 | Supported | Supported | 1.8 | Supported | 1.7 | Supported | — |
| AWS IAM password policy does not have a number | [IAM.14] | 1.8 | Not supported | Not supported – CIS removed | Not supported | Not supported | Not supported – CIS removed | Not supported | Not supported – CIS removed | Not supported | Not in v6.0.0 (last: v1.2.0) |
| AWS IAM password policy does not have a symbol | [IAM.13] | 1.7 | Not supported | Not supported – CIS removed | Not supported | Not supported | Not supported – CIS removed | Not supported | Not supported – CIS removed | Not supported | Not in v6.0.0 (last: v1.2.0) |
| AWS IAM password policy does not have an uppercase character | [IAM.11] | 1.5 | Not supported | Not supported – CIS removed | Not supported | Not supported | Not supported – CIS removed | Not supported | Not supported – CIS removed | Not supported | Not in v6.0.0 (last: v1.2.0) |
| AWS IAM policy allows full administrative privileges | [IAM.1] | 1.22 | Supported | 1.16 | Supported | Supported | Not supported | Supported | Not supported | Supported | — |
| AWS IAM policy attached to users | [IAM.2] | 1.16 | Supported | Not supported | Supported | Supported | 1.15 | Supported | 1.14 | Supported | — |
| AWS IAM support access policy is not associated to any role | [IAM.18] | 1.2 | Supported | 1.17 | Supported | Supported | 1.17 | Supported | 1.16 | Supported | — |
| AWS IAM user has both Console access and Access Keys | — | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | No CIS Security Hub equivalent |
| AWS IAM user has two active Access Keys | — | Not supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Supported | Introduced v1.3.0; No CIS Security Hub equivalent |
| AWS Inactive users for more than 30 days | [IAM.8] | 1.3 | Not supported | Not supported – see IAM.22 | Not supported | Not supported | Not supported – see IAM.22 | Not supported | Not supported – see IAM.22 | Not supported | Not in v6.0.0 (last: v1.2.0) |
| AWS Log metric filter and alarm does not exist for AWS Config configuration changes | [CloudWatch.9] | 3.9 | Supported | 4.9 | Not supported | Not supported | Manual check | Not supported | Manual check | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS Log metric filter and alarm does not exist for AWS Organization changes | — | Not supported | Not supported | Not supported | Not supported | Supported | Supported | Supported | Supported | Supported | Introduced v2.0.0; No CIS Security Hub equivalent |
| AWS Log metric filter and alarm does not exist for AWS management console authentication failures | [CloudWatch.6] | 3.6 | Supported | 4.6 | Not supported | Supported | Manual check | Supported | Manual check | Supported | Intermittent |
| AWS Log metric filter and alarm does not exist for CloudTrail configuration changes | [CloudWatch.5] | 3.5 | Supported | 4.5 | Supported | Supported | Manual check | Supported | Manual check | Supported | — |
| AWS Log metric filter and alarm does not exist for IAM policy changes | [CloudWatch.4] | 3.4 | Supported | 4.4 | Supported | Supported | Manual check | Supported | Manual check | Supported | — |
| AWS Log metric filter and alarm does not exist for Network Access Control Lists (NACL) changes | [CloudWatch.11] | 3.11 | Supported | 4.11 | Not supported | Not supported | Manual check | Not supported | Manual check | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS Log metric filter and alarm does not exist for Network gateways changes | [CloudWatch.12] | 3.12 | Supported | 4.12 | Supported | Supported | Manual check | Supported | Manual check | Supported | — |
| AWS Log metric filter and alarm does not exist for Route table changes | [CloudWatch.13] | 3.13 | Supported | 4.13 | Supported | Supported | Manual check | Supported | Manual check | Supported | — |
| AWS Log metric filter and alarm does not exist for S3 bucket policy changes | [CloudWatch.8] | 3.8 | Supported | 4.8 | Supported | Supported | Manual check | Supported | Manual check | Supported | — |
| AWS Log metric filter and alarm does not exist for VPC changes | [CloudWatch.14] | 3.14 | Supported | 4.14 | Supported | Supported | Manual check | Supported | Manual check | Supported | — |
| AWS Log metric filter and alarm does not exist for disabling or scheduled deletion of customer created CMKs | [CloudWatch.7] | 3.7 | Supported | 4.7 | Not supported | Not supported | Manual check | Not supported | Manual check | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS Log metric filter and alarm does not exist for unauthorized API calls | [CloudWatch.2] | 3.1 | Supported | Manual check | Supported | Not supported | Manual check | Not supported | Manual check | Not supported | Not in v6.0.0 (last: v1.5.0) |
| AWS Log metric filter and alarm does not exist for usage of root account | [CloudWatch.1] | 3.3 | Not supported | 4.3 | Not supported | Supported | Manual check | Supported | Manual check | Supported | Introduced v2.0.0 |
| AWS MFA is not enabled on Root account | [IAM.9] | 1.13 | Supported | 1.5 | Supported | Supported | 1.5 | Supported | 1.4 | Supported | — |
| AWS MFA not enabled for IAM users | [IAM.5] | 1.2 | Supported | 1.10 | Supported | Supported | 1.10 | Supported | 1.9 | Supported | — |
| AWS Network ACLs allow ingress traffic on Admin ports 22/3389 | [EC2.21] | Not supported | Not supported | 5.1 | Not supported | Supported | 5.1 | Supported | 5.2 | Supported | Introduced v2.0.0 |
| AWS RDS database instance is publicly accessible | [RDS.2] | Not supported – added later | Not supported | Not supported – added later | Supported | Supported | 2.3.3 | Supported | 2.2.3 | Supported | Introduced v1.5.0 |
| AWS RDS database not encrypted using Customer Managed Key | [RDS.3] | Not supported – added later | Not supported | 2.3.1 | Supported | Not supported | 2.3.1 | Supported | 2.2.1 | Supported | Introduced v1.4.0; Intermittent |
| AWS RDS instance is not encrypted | [RDS.3] | Not supported – added later | Not supported | 2.3.1 | Not supported | Supported | 2.3.1 | Not supported | 2.2.1 | Not supported | Not in v6.0.0 (last: v3.0.0); Introduced v2.0.0 |
| AWS RDS instance with Multi-Availability Zone disabled | [RDS.5] | Not supported – added later | Not supported | Not supported – added later | Not supported | Not supported | Not supported – added later | Supported | 2.2.4 | Supported | Introduced v4.0.0 |
| AWS RDS minor upgrades not enabled | [RDS.13] | Not supported – added later | Not supported | Not supported – added later | Supported | Supported | 2.3.2 | Supported | 2.2.2 | Supported | Introduced v1.5.0 |
| AWS S3 Buckets Block public access setting disabled | [S3.1] | Not supported – added later | Supported | 2.1.5 | Supported | Supported | 2.1.4 | Supported | 2.1.4 | Supported | Introduced v1.3.0 |
| AWS S3 CloudTrail bucket for which access logging is disabled | [CloudTrail.7] | 2.6 | Supported | 3.6 | Supported | Supported | 3.4 | Supported | 3.4 | Supported | — |
| AWS S3 bucket is not configured with MFA Delete | [S3.20] | Not supported – added later | Not supported | 2.1.3 | Supported | Not supported | 2.1.2 | Not supported | 2.1.2 | Not supported | Not in v6.0.0 (last: v1.5.0); Introduced v1.4.0 |
| AWS S3 bucket not configured with secure data transport policy | [S3.5] | Not supported – added later | Supported | 2.1.2 | Not supported | Not supported | 2.1.1 | Not supported | 2.1.1 | Not supported | Not in v6.0.0 (last: v1.4.0); Introduced v1.3.0 |
| AWS S3 buckets do not have server side encryption | — | Not supported | Supported | Supported | Not supported | Not supported | Not supported | Not supported | Not supported | Not supported | Not in v6.0.0 (last: v1.4.0); Introduced v1.3.0; No CIS Security Hub equivalent |
| AWS Security Group allows all traffic on RDP port (3389) | [EC2.14] | 4.2 | Supported | Not supported – replaced by 5.2/5.3 | Supported | Supported | Not supported – replaced by 5.2/5.3 | Supported | Not supported – replaced by 5.3/5.4 | Supported | — |
| AWS Security Group allows all traffic on SSH port (22) | [EC2.13] | 4.1 | Supported | Not supported – replaced by 5.2/5.3 | Supported | Supported | Not supported – replaced by 5.2/5.3 | Supported | Not supported – replaced by 5.3/5.4 | Supported | — |
| AWS VPC Flow Logs not enabled | [EC2.6] | 2.9 | Supported | 3.9 | Not supported | Not supported | 3.7 | Not supported | 3.7 | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS access keys are not rotated for 90 days | [IAM.3] | 1.4 | Supported | 1.14 | Supported | Supported | 1.14 | Supported | 1.13 | Supported | — |
| AWS access keys not used for more than 45 days | [IAM.22] | Not supported – added later | Supported | 1.12 | Supported | Supported | 1.12 | Supported | 1.11 | Supported | Introduced v1.3.0 |
| AWS account security contact information is not set | [Account.1] | 1.18 | Not supported | 1.2 | Not supported | Supported | 1.2 | Supported | 1.2 | Supported | Introduced v2.0.0 |
| AWS root account activity detected in last 14 days | [IAM.20] | 1.1 | Not supported | Not supported – CIS removed | Not supported | Supported | Not supported – CIS removed | Supported | Not supported – CIS removed | Supported | Introduced v2.0.0 |
| AWS root account configured with Virtual MFA | [IAM.6] | 1.14 | Supported | 1.6 | Not supported | Not supported | 1.6 | Not supported | 1.5 | Not supported | Not in v6.0.0 (last: v1.4.0) |
| AWS route table with VPC peering overly permissive to all traffic | — | Supported | Supported | Supported | Not supported | Not supported | Not supported | Not supported | Not supported | Not supported | Not in v6.0.0 (last: v1.4.0); No CIS Security Hub equivalent |
