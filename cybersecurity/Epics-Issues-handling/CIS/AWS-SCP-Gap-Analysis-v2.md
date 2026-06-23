
## CIS 3.0 Remaining Findings After Excluding Already-Implemented Auto Remediations

### Excluded (already implemented)
| Control | CIS 3.0 mapping | Why excluded | Evidence |
|---|---|---|---|
| AWS S3 bucket not configured with secure data transport policy (S3.5) | 2.1.1 | Already auto-remediated by S3 SSL enforcement runbook | Mapping: CIS-AWS-Prisma-Policy-Control-Mapping.md, Remediation: AWS-Automatic-Remediations.md |
| AWS S3 CloudTrail bucket for which access logging is disabled (CloudTrail.7) | 3.4 | Already auto-remediated by S3 logging enablement runbook | Mapping: CIS-AWS-Prisma-Policy-Control-Mapping.md, Remediation: AWS-Automatic-Remediations.md |
| S3 lifecycle baseline | Not a direct CIS 3.0 scored control in this mapping | Implemented auto-remediation exists, so not a remaining gap | Evidence: AWS-Automatic-Remediations.md |

### Remaining findings (clean list)

| Priority | Gap | CIS 3.0 control | Suggested policy/remediation | Why this helps quickly |
|---|---|---|---|---|
| High | S3 public exposure can still be introduced | 2.1.4 | Add SCP to deny disabling account/bucket public access block and deny public ACL/policy patterns except approved roles | Prevents new public bucket exposures org-wide |
| High | RDS instances can be created/modified as public | 2.3.3 | Add SCP denying create/modify when PubliclyAccessible=true | Removes a frequent high-impact finding |
| High | RDS auto minor upgrades can be disabled | 2.3.2 | Add SCP denying modify when AutoMinorVersionUpgrade=false | Reduces patching drift with low effort |
| Medium | IAM CloudShellFullAccess usage not centrally blocked | 1.22 | Add SCP denying attachment/use of AWSCloudShellFullAccess except security-approved roles | Reduces privilege misuse paths |
| Medium | IAM Access Analyzer can be disabled/deleted | 1.20 | Add SCP protecting analyzer resources from disable/delete except security admins | Preserves continuous external-access detection |
| Medium | Default security group and admin-port exposure gaps | 5.4, 5.1 | Add SCP denying inbound rules on default SG and deny 22/3389 open to world except break-glass/network roles | Cuts noisy recurring network findings |
| Medium | Many logging-alarm controls are still manual checks | 3.x manual-check set | Deploy org-wide CloudWatch metric filters/alarms baseline via Terraform + StackSets | Converts manual drift into standardized automation |

### Evidence used for remaining list
- CIS mapping source: CIS-AWS-Prisma-Policy-Control-Mapping.md
- IAM CloudShellFullAccess mapping: CIS-AWS-Prisma-Policy-Control-Mapping.md
- IAM Access Analyzer mapping: CIS-AWS-Prisma-Policy-Control-Mapping.md
- RDS public and minor-upgrade mappings: CIS-AWS-Prisma-Policy-Control-Mapping.md, CIS-AWS-Prisma-Policy-Control-Mapping.md
- S3 public access mapping: CIS-AWS-Prisma-Policy-Control-Mapping.md
- Existing remediation inventory: AWS-Automatic-Remediations.md
