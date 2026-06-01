# AWS Native Security Controls

## Executive Snapshot
This environment is built on a centralized AWS-native security baseline. Core controls are powered by **Security Hub, GuardDuty, AWS Config, CloudTrail, KMS, Organizations/SCPs, IAM/SSO, EventBridge, Systems Manager, Step Functions, CloudWatch, and SNS**.

Third-party platforms such as **Qualys, Prisma Cloud, Microsoft Defender for Cloud, and Sentinel** are integrated as enhancements, while AWS-native services remain the primary control backbone.

---

## Security Architecture at a Glance

| Control Domain | Primary AWS Services | Purpose |
|---|---|---|
| Preventive | Organizations, SCPs, IAM | Enforce baseline guardrails before risk is introduced |
| Detective | Security Hub, GuardDuty, Config, CloudTrail | Continuously identify drift, threats, and non-compliance |
| Protection | KMS, S3 encryption, access blocks | Protect security telemetry and logs at rest |
| Response | EventBridge, Systems Manager, Step Functions, SNS, CloudWatch | Automate triage, remediation, and operational visibility |
| Operations | Cross-account roles, SSO, centralized security and logging accounts | Provide scalable governance across all accounts |

---

## Native AWS Security Services in Use

### 1. AWS Security Hub
Security Hub is configured as a centralized organization-level security control plane.

**Evidence**
- `bootstrap/shared-services/security-hub/security_hub.tf` defines:
  - organization configuration
  - multi-region finding aggregation
  - central configuration policy
  - standards enablement and control tuning
- `bootstrap/README.md` lists Security Hub under shared services.
- `docs/workshop-sessions/4-security-governance.md` confirms centralized Security Hub and GuardDuty.

```hcl name=bootstrap/shared-services/security-hub/security_hub.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/security-hub/security_hub.tf#L12-L56
resource "aws_securityhub_finding_aggregator" "aggregator" {
  linking_mode      = local.has_security_hub_aggregated_regions ? "SPECIFIED_REGIONS" : "NO_REGIONS"
  specified_regions = local.has_security_hub_aggregated_regions ? local.security_hub_aggregated_regions : null
}

resource "aws_securityhub_organization_configuration" "configuration" {
  auto_enable           = false
  auto_enable_standards = "NONE"
  organization_configuration {
    configuration_type = "CENTRAL"
  }
}

resource "aws_securityhub_configuration_policy" "configuration_policy" {
  name        = "Disable-Specific-controls"
  description = "A policy to disable specific controls"

  configuration_policy {
    service_enabled = true
    enabled_standard_arns = [
      "arn:aws:securityhub:eu-west-1::standards/aws-foundational-security-best-practices/v/1.0.0",
      "arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.2.0",
      "arn:aws:securityhub:eu-west-1::standards/cis-aws-foundations-benchmark/v/1.4.0"
    ]
```

**Why this matters**
- Centralizes findings and governance across accounts and regions.
- Enables standards-based compliance monitoring (AWS FSBP, CIS 1.2.0, CIS 1.4.0).

---

### 2. Amazon GuardDuty
GuardDuty is used as a centralized threat detection service with findings exported to centralized logging.

**Evidence**
- `bootstrap/shared-services/security/guardduty.tf`
- `docs/BF_to_GF_transition_docs/Logging.md`
- `bootstrap/shared-services/security/README_TEST.md`

```hcl name=bootstrap/shared-services/security/guardduty.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/security/guardduty.tf#L1-L22
data "aws_guardduty_detector" "guardduty" {
  provider = aws.security
}

resource "aws_guardduty_publishing_destination" "logging_configuration" {
  provider        = aws.security
  detector_id     = data.aws_guardduty_detector.guardduty.id
  destination_arn = jsondecode(nonsensitive(data.aws_secretsmanager_secret_version.resource_reference_secret_id.secret_string))["guradduty_logs_bucket_arn"]
  kms_key_arn     = jsondecode(nonsensitive(data.aws_secretsmanager_secret_version.resource_reference_secret_id.secret_string))["guradduty_logs_kms_arn"]
}
```

```markdown name=docs/BF_to_GF_transition_docs/Logging.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/docs/BF_to_GF_transition_docs/Logging.md#L39-L49
## AWS Guardduty logs

Amazon GuardDuty is a security monitoring service that analyzes and processes Foundational data sources,
such as AWS CloudTrail management events, AWS CloudTrail event logs, VPC flow logs, and DNS logs.
Guardduty service is configured in Audit account to gather findings from all AWS account of Volvo Cars landing zone.
The findings from Audit account will be shipped to a S3 bucket in volvocars-logging account.
```

**Why this matters**
- Delivers centralized detection coverage.
- Feeds high-value security telemetry into long-term, encrypted storage.

---

### 3. AWS Config
AWS Config is implemented for organization-wide detective controls and compliance visibility.

**Evidence**
- `bootstrap/accounts/prod/awsmain/eu-west-1/iam/ou.tf`
- `bootstrap/shared-services/security/README_TEST.md`
- `docs/BF_to_GF_transition_docs/Logging.md`

```hcl name=bootstrap/accounts/prod/awsmain/eu-west-1/iam/ou.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/accounts/prod/awsmain/eu-west-1/iam/ou.tf#L10-L18
resource "aws_iam_service_linked_role" "config" {
  aws_service_name = "config.amazonaws.com"

  tags = {
    envtype     = "prod"
    owner-appid = "app-4857"
  }
}
```

```markdown name=bootstrap/shared-services/security/README_TEST.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/security/README_TEST.md#L9-L18
### organisational_config_rule.tf

This file contains terraform script to configure Security account as trusted access for **config-multiaccountsetup.amazonaws.com** and for deploying
AWS config rules at organisation level.
```

**Why this matters**
- Establishes continuous configuration governance.
- Supports multi-account compliance assurance.

---

### 4. AWS CloudTrail
CloudTrail is a foundational audit source, with logs centralized for investigations and compliance.

**Evidence**
- `docs/BF_to_GF_transition_docs/Logging.md`
- `docs/workshop-sessions/4-security-governance.md`

```markdown name=docs/BF_to_GF_transition_docs/Logging.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/docs/BF_to_GF_transition_docs/Logging.md#L1-L27
All log data for Volvo Cars would be stored in volvocars-logging account as the Figure 3 below shows the flow of logs (CloudTrail, Config, Guardduty and VPC Flow) from all AWS accounts into volvocars-logging account.
...
## AWS CloudTrail logs

AWS CloudTrail service provides a full audit history of all actions taken against AWS services, including users logging into accounts. AWS Control Tower is integrated with AWS CloudTrail.
```

**Why this matters**
- Provides immutable audit evidence across accounts.
- Strengthens incident response and control attestation.

---

### 5. AWS KMS
KMS is deeply embedded in log protection and encryption controls.

**Evidence**
- `bootstrap/shared-services/logging/guardduty_logs_kms.tf`
- `bootstrap/shared-services/logging/guardduty_logs_s3.tf`
- `docs/workshop-sessions/4-security-governance.md`

```hcl name=bootstrap/shared-services/logging/guardduty_logs_kms.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/logging/guardduty_logs_kms.tf#L61-L83
resource "aws_kms_key" "guardduty_logs_key" {
  description         = "KMS Key to encrypt central S3 bucket log archive account for VPC Flow Logs"
  key_usage           = "ENCRYPT_DECRYPT"
  enable_key_rotation = true
  multi_region        = true
}

resource "aws_kms_alias" "guardduty_logs_key_alias" {
  name          = "alias/${var.guardduty_logs_key_name}"
  target_key_id = aws_kms_key.guardduty_logs_key.key_id
}
```

```hcl name=bootstrap/shared-services/logging/guardduty_logs_s3.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/logging/guardduty_logs_s3.tf#L26-L35
resource "aws_s3_bucket_server_side_encryption_configuration" "guardduty_logs_bucket_encryption" {
  bucket = aws_s3_bucket.guardduty_logs.id
  rule {
    apply_server_side_encryption_by_default {
      kms_master_key_id = aws_kms_key.guardduty_logs_key.key_id
      sse_algorithm     = "aws:kms"
    }
  }
}
```

**Why this matters**
- Enforces encryption at rest for high-value telemetry.
- Uses key rotation and multi-region key strategy for resilience.

---

### 6. AWS Organizations + Service Control Policies (SCPs)
Organizations and SCPs provide the preventive governance layer across organizational units.

**Evidence**
- `bootstrap/shared-services/security/README_TEST.md`
- root `README.md` and `bootstrap/README.md`
- workshop documentation

```markdown name=bootstrap/shared-services/security/README_TEST.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/security/README_TEST.md#L45-L52
### scp.tf

This file creates SCP and attached it to all Organization units

## Service Control Policies attached to the OUs

### All the SCP policies listed below are applicable to every organizational unit under the root
```

**Examples of policy intent**
- Deny sensitive billing, security, and IAM changes.
- Require IMDSv2.
- Restrict root user actions.
- Prevent unsafe internet-facing and security-risk network configurations.

**Why this matters**
- Applies preventative controls before misconfigurations are deployed.
- Creates consistent governance boundaries at OU level.

---

### 7. IAM, Service-Linked Roles, SSO, and Cross-Account Role Assumption
IAM is central to delegated operations and separation of duties.

**Evidence**
- Multiple provider blocks use `assume_role`.
- Service-linked roles are enabled for services such as AWS Config.
- Documentation includes SSO and cross-account access controls.

```hcl name=bootstrap/shared-services/security/main.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/security/main.tf#L1-L34
provider "aws" {
  region = "eu-west-1"

  assume_role {
    role_arn = "arn:aws:iam::${var.organization_account_id}:role/${var.terraform_bootstrap_role_name}"
  }
}

provider "aws" {
  alias  = "security"
  region = "eu-west-1"

  assume_role {
    role_arn = "arn:aws:iam::${var.security_account_id}:role/${var.terraform_bootstrap_role_name}"
  }
}
```

**Why this matters**
- Enables secure centralized administration across accounts.
- Supports auditable access patterns and least-privilege operations.

---

### 8. S3 as Centralized Secure Log Archive
S3 is used as a hardened telemetry archive, not just general storage.

**Evidence**
- Dedicated GuardDuty logs bucket
- centralized logging documentation
- public access blocks, encryption, and lifecycle controls

```hcl name=bootstrap/shared-services/logging/guardduty_logs_s3.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/logging/guardduty_logs_s3.tf#L14-L24
resource "aws_s3_bucket_public_access_block" "guardduty_logs_bucket_public_access" {
  bucket                  = aws_s3_bucket.guardduty_logs.id
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

**Why this matters**
- Keeps security telemetry private and protected by default.
- Supports long-term retention and forensic readiness.

---

### 9. Native AWS Remediation and Orchestration
Automated response workflows are implemented with Security Hub-driven orchestration.

**Evidence**
- `bootstrap/shared-services/automated-remediation-framework/operational-manual.md`

```markdown name=bootstrap/shared-services/automated-remediation-framework/operational-manual.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/automated-remediation-framework/operational-manual.md#L21-L43
- **admin**: account that will perform the administration actions of the solution, namely initiating remediations manually or enabling fully automated remediation with EventBridge rules.
...
This solution collects output from remediation runbooks, which run under AWS Systems Manager, and logs the result to CloudWatch Logs group **SO0111-SHARR**
...
The Orchestrator Step Function logs all step transitions to the **SO0111-SHARR-Orchestrator** CloudWatch Logs Group
```

```markdown name=bootstrap/shared-services/automated-remediation-framework/operational-manual.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/29cfe6299733be41f8347e64334d37ccafe098ea/bootstrap/shared-services/automated-remediation-framework/operational-manual.md#L67-L82
Automated Security Response on AWS creates an Amazon SNS topic, **SO0111-SHARR_Topic**. This topic is used to post updates about remediation progress.
```

**Why this matters**
- Converts findings into orchestrated response workflows.
- Improves consistency, speed, and auditability of remediation.

---

## Architecture Summary

### 1) Preventive Controls
- AWS Organizations
- Service Control Policies (SCPs)
- IAM restrictions and root usage constraints
- Tag policy and IMDSv2 enforcement patterns

### 2) Detective Controls
- Security Hub
- GuardDuty
- AWS Config
- CloudTrail
- VPC Flow Logs

### 3) Data and Log Protection
- KMS-managed encryption
- Encrypted S3 buckets
- Centralized logging account model
- Retention and lifecycle management

### 4) Response and Remediation
- EventBridge
- Systems Manager Automation
- Step Functions
- CloudWatch Logs
- SNS
- Security Hub-driven automation workflows

### 5) Operating Model
- Centralized security account (`volvocars-security`)
- Centralized logging account (`volvocars-logging`)
- Cross-account role assumption
- Organization-wide governance

---

## Important Nuance: Native vs Non-Native
This implementation also integrates:
- Qualys
- Prisma Cloud
- Microsoft Defender for Cloud
- Sentinel

These are **not AWS-native services**. They enhance coverage, while the primary security foundation remains AWS-native.

