# AWS CIS 5.0 Compliance and SCP Gap Analysis
## Volvo Greenfield Environment

Document Version: 1.0  
Date: 2026-06-24  
Scope: CIS AWS Foundations Benchmark v5.0.0

---
## Overview
This document assesses how far the current AWS organizational guardrails and remediation automations align with the CIS AWS Foundations Benchmark v5.0.0 for the Volvo greenfield environment.

It focuses on the practical coverage provided by existing Service Control Policies (SCPs), supporting remediation workflows, and baseline security automations across organization root and selected greenfield organizational units. The analysis distinguishes between controls that are fully enforced, partially covered, detective/corrective only, or still unaddressed.
## Executive Summary


### Current State
- Policies deployed: 4 root SCPs plus 2 greenfield OU SCPs (common and corp variants)
- Deployment level: Organization root and selected OUs (corp, online, core, sandbox, shared)
- Remediation automation: Security group ingress auto-remediation, S3 lifecycle auto-remediation, tagging remediation Lambda, IAM instance profile remediation Lambda
- Coverage: Around 25+ percent of CIS AWS 5.0 controls have direct preventive or automated remediation evidence
- Gap: Around 70+ percent of CIS AWS 5.0 controls remain partial, detective only, or unsupported by current automation evidence

### Key Findings
1. Strong areas: Preventive guardrails for IAM user lifecycle restrictions, root access key prevention, IMDSv2 enforcement, encryption at rest for EBS/RDS/EFS, and SG ingress remediation.
2. Medium areas: Security service protection (Security Hub, GuardDuty, Config, CloudTrail) is protected from disablement but not always proven as enforce-enabled in all accounts/regions.
3. Weak areas: CloudWatch metric/alert controls in section 4, several IAM governance controls in section 1, and multiple S3/RDS control requirements in sections 2.1 and 2.2.

---

## 1: Deployed AWS Controls by Volvo Repos

### Organization and OU Deployments

Scope:
- Root: Organization-wide attachments (root_policy1, root_policy2, root_policy3, root_policy4)
- Greenfield OUs: greenfield_policy_common and greenfield_policy_corp

| Policy / Automation Family | Category | Enforcement Type | CIS 5.0 Alignment | Status |
|---|---|---|---|---|
| root_policy1 | Account governance | Deny | 1.3, root-user hardening (supporting) | Deployed |
| root_policy2 | Security services protection | Deny | 1.3, 1.19, 4.16 (partial) | Deployed |
| root_policy3 | Platform and encryption guardrails | Deny | 2.2.1, 2.3.1, 5.1.1 (partial) | Deployed |
| root_policy4 | IAM lifecycle and region governance | Deny | 1.3, 1.17, supporting IAM controls | Deployed |
| greenfield_policy_common | Compute hardening | Deny | 5.7 | Deployed |
| greenfield_policy_corp | Network perimeter guardrails | Deny | 5.6 (supporting), 5.2-5.4 (indirect) | Deployed |
| ASR custom remediation module | Detective and corrective | Config + EventBridge + SSM | 5.3, 5.4 | Deployed |
| S3 lifecycle event remediation | Event-driven corrective | EventBridge + queue + runbook | 2.1.3 (supporting), governance | Deployed |
| S3 SSL transport runbook (aws_sss_002) | Corrective | Lambda runbook | 2.1.1 (supporting/partial) | Deployed |
| Resource tagging Lambda module | Governance remediation | Scheduled EventBridge + Lambda | Governance (supporting) | Deployed |
| IAM instance profile Lambda module | IAM remediation | Scheduled EventBridge + Lambda | 1.17 (partial), supporting | Deployed |

---

## OU Level Policy review

Legend:
- Control mapping values are CIS 5.0 references when direct, otherwise Supporting or Indirect.
- Status values: Enforced, Protected, Automated remediation, Partial, Governance-only.

| Assignment / Control Name | Scope | Implementation status | CIS 5.0 mapping | Control intent |
|---|---|---|---|---|
| RestrictRootUser and RestrictAssumedRootSession | Organization root | Enforced | 1.6 (supporting), 1.3 (supporting) | Restrict root account operational use |
| PreventRootAccessKeys | Organization root | Enforced | 1.3 | Deny creation of root access keys |
| PreventSecurityandIAMActions | Organization root | Protected (deny disable/delete actions) | 1.19, 3.1, 3.3, 4.16 (partial) | Prevent disablement of Security Hub, GuardDuty, Config, CloudTrail |
| PreventLeaveOrganization | Organization root | Enforced | Supporting governance | Protect org guardrail integrity |
| DenyPrismaCloudTagsModification | Organization root | Enforced | Supporting governance | Protect governance/tracing tags from tampering |
| ProtectPlatformResources | Organization root | Enforced | Supporting governance | Prevent drift on terraform-managed resources |
| ProtectRoles | Organization root | Enforced | Supporting IAM governance | Protect critical infra roles |
| ProtectVPCFlowLogs | Organization root | Partial (protective, not blanket enablement) | 3.7 (partial) | Prevent deletion/modification of platform flow log assets |
| DenyCreationAndAttachmentOfGP2 | Organization root | Enforced | 5.1.1 (supporting modernization) | Block gp2 usage and enforce storage baseline |
| DenyUnencryptedRDS | Organization root | Enforced | 2.2.1 | Deny unencrypted RDS DB instances |
| DenyUnencryptedAurora | Organization root | Enforced | 2.2.1 (Aurora variant) | Deny unencrypted Aurora clusters |
| DenyUnencryptedEFS | Organization root | Enforced | 2.3.1 | Deny unencrypted EFS creation |
| PreventIAMUserCreation | Organization root | Enforced with scoped exceptions | 1.14 (supporting), 1.20 (supporting) | Block manual IAM user lifecycle paths |
| DenyOutsideRequestedRegions | Organization root | Enforced | Supporting governance | Restrict blocked regions |
| RequireImdsV2 (greenfield_policy_common) | Greenfield OUs | Enforced | 5.7 | Deny EC2 launch without IMDSv2 |
| PreventInternetConfig (greenfield_policy_corp) | Corp OU | Enforced | 5.6 (supporting), 5.2-5.4 (indirect) | Centralize internet-facing network control |
| ASR SG remediation (VPC_SG_OPEN_ONLY_TO_AUTHORIZED_PORTS) | Member account level | Automated remediation | 5.3, 5.4 | Auto-revoke unauthorized open SG ingress |
| S3 lifecycle CreateBucket event remediation | Multi-account account/region deployment | Automated remediation | 2.1.3 (supporting) | Apply lifecycle baseline to new buckets |
| S3 secure transport runbook (aws_sss_002) | Shared remediation framework | Automated remediation | 2.1.1 (partial) | Enforce deny non-SSL transport in bucket policy |
| IAM instance profile remediation Lambda | Scheduled in accounts/regions | Automated remediation | 1.17 (partial), supporting | Auto-attach required profile/policies for EC2 |

---

## 2: CIS 5.0 Controls Coverage Matrix

### Overview: Deployment by CIS 5.0 Section

| CIS 5.0 Section | Total Controls | Evidence-backed coverage | Enforcement status |
|---|---|---|---|
| Identity and Access (1.x) | 21 | 3-5 direct or partial | Mostly partial |
| Storage and Databases (2.1-2.3) | 9 | 3-4 direct or partial | Mixed |
| Logging and Monitoring (3.x and 4.x) | 25 | 3-5 direct or partial | Mostly protective/detective |
| Networking and Compute (5.x) | 8 | 4-5 direct or partial | Strongest section |

Note: Percentages are design-evidence estimates from code and documented policy definitions, not live account compliance scores.

---

### Section 1: Identity and Access (1.x - Critical Gap)

| CIS 5.0 Req ID | Control | Policy status | Assignment / Mechanism | Implementation mode | Remediation |
|---|---|---|---|---|---|
| 1.3 | No root access key exists | Deployed | PreventRootAccessKeys | Enforced | Maintain and continuously validate |
| 1.7 / 1.8 | Password length and reuse | None evidenced | No password policy baseline evidence in analyzed controls | N/A | Add IAM account password policy baseline |
| 1.9 | MFA for IAM users with console access | None evidenced | No direct enforcement evidence | N/A | Add conditional deny and identity governance flow |
| 1.14 | IAM users only through groups | Partial | PreventIAMUserCreation indirectly reduces user sprawl | Preventive but indirect | Add direct compliance checks and exceptions governance |
| 1.17 | IAM instance roles for resource access | Partial | IAM instance profile remediation Lambda | Corrective automation | Strengthen to proactive preventive controls where possible |
| 1.19 | Access Analyzer enabled for all regions | Partial | Security service protection prevents disable/delete actions | Protective only | Add explicit deployment and region conformance validation |
| 1.21 | Restrict AWSCloudShellFullAccess | None evidenced | No direct policy evidence | N/A | Add explicit deny guardrail for CloudShellFullAccess attachment |

---

### Section 2: Storage and Databases (2.1-2.3 - Medium to High Gap)

| CIS 5.0 Req ID | Control | Policy status | Assignment / Mechanism | Implementation mode | Remediation |
|---|---|---|---|---|---|
| 2.1.1 | S3 bucket policy denies HTTP | Partial | aws_sss_002 runbook | Corrective remediation | Add preventive SCP or policy-as-code baseline |
| 2.1.2 | MFA Delete on S3 buckets | None evidenced | No direct baseline evidence | N/A | Add control-specific detection/remediation |
| 2.1.4 | S3 Block Public Access enabled | Partial | Corp guardrails and S3 governance patterns | Partial preventive | Extend to all in-scope OUs and validate account-level setting |
| 2.2.1 | RDS encryption at rest | Deployed | DenyUnencryptedRDS and DenyUnencryptedAurora | Enforced | Maintain and add drift checks |
| 2.2.2 | RDS auto minor version upgrade enabled | None evidenced | No direct deny/required control evidence | N/A | Add SCP or config-rule based enforcement |
| 2.2.3 | RDS public access disabled | None evidenced | No direct deny for PubliclyAccessible | N/A | Add explicit deny conditions for public RDS |
| 2.3.1 | EFS encryption enabled | Deployed | DenyUnencryptedEFS | Enforced | Maintain and validate exception handling |

Analysis: Encryption controls for RDS/EFS are a clear strength. Main storage gap remains S3-specific CIS requirements and RDS configuration hardening controls (public access and minor upgrades).

---

### Section 3: Logging and Monitoring Baseline (3.x - High Gap)

| CIS 5.0 Req ID | Control | Policy status | Assignment / Mechanism | Implementation mode | Remediation |
|---|---|---|---|---|---|
| 3.1 | CloudTrail enabled in all regions | Partial | PreventSecurityandIAMActions protects from disablement | Protective only | Add explicit organization trail deployment validation |
| 3.2 | CloudTrail log validation enabled | None evidenced | No direct evidence | N/A | Add mandatory trail config baseline |
| 3.3 | AWS Config enabled in all regions | Partial | PreventSecurityandIAMActions protects Config from disable/delete actions | Protective only | Add explicit all-region Config recorder enforcement |
| 3.5 | CloudTrail logs encrypted with CMK | None evidenced | No direct enforcement evidence in analyzed controls | N/A | Add explicit trail KMS key requirement |
| 3.7 | VPC flow logging enabled in all VPCs | Partial | ProtectVPCFlowLogs protects platform flow logs from deletion | Protective only | Add global enablement policy and drift remediation |
| 3.8 / 3.9 | S3 object-level write/read logging | None evidenced | No direct baseline evidence | N/A | Add CloudTrail data-event baseline for S3 buckets |

Analysis: Current controls are mostly anti-tamper protections rather than explicit must-enable guardrails. CIS 3.x needs proactive deployment baselines at org scale.

---

### Section 4: Monitoring and Alerting (4.x - Critical Gap)

| CIS 5.0 Req ID | Control | Policy status | Assignment / Mechanism | Implementation mode | Remediation |
|---|---|---|---|---|---|
| 4.1 to 4.15 | CloudWatch metric filters and alarms for key events | None evidenced | No direct metric-filter/alert deployment evidence in analyzed controls | N/A | Build centralized baseline module and roll via StackSets |
| 4.16 | Security Hub enabled | Partial | PreventSecurityandIAMActions blocks disable/delete patterns | Protective only | Add direct enablement conformance and region checks |

Analysis: This is the largest practical gap against CIS 5.0. Existing root guardrails protect security tools but do not prove required alerting/monitoring controls are deployed everywhere.

---

### Section 5: Networking and Compute (5.x - Strongest Area, Still Partial)

| CIS 5.0 Req ID | Control | Policy status | Assignment / Mechanism | Implementation mode | Remediation |
|---|---|---|---|---|---|
| 5.1.1 | EBS encryption by default in all regions | Deployed | aws_ebs_encryption_by_default in account security baselines | Enforced | Maintain region rollout checks |
| 5.2 | NACL ingress from 0.0.0.0/0 to admin ports restricted | Partial | Network guardrails plus internet config restrictions (indirect) | Mixed | Add explicit NACL-focused preventive controls |
| 5.3 | SG ingress from 0.0.0.0/0 to admin ports restricted | Deployed | ASR SG remediation with Config rule and SSM automation | Automated remediation | Consider preventive deny controls for recurring findings |
| 5.4 | SG ingress from ::/0 to admin ports restricted | Deployed (same remediation family) | ASR SG remediation pattern | Automated remediation | Validate IPv6 coverage consistently in rule logic |
| 5.5 | Default SG restricts all traffic | Partial | SG remediation and baseline governance patterns | Corrective/partial | Add explicit default SG lock-down control validation |
| 5.6 | VPC peering routing least access | Partial | greenfield_policy_corp prevents selected peering/network edge actions | Preventive but scoped | Extend/standardize across non-corp scopes as needed |
| 5.7 | EC2 IMDSv2 only | Deployed | RequireImdsV2 plus account metadata defaults | Enforced | Maintain and validate exception paths |

Analysis: Networking/compute is the most mature area due to combined preventive SCPs and remediation automation. Remaining gap is moving indirect network constraints into explicit CIS control evidence for NACL/default SG/peering least-access.

---
