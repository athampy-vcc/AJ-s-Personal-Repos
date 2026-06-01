Yes — there are several strong feature candidates you can add to this epic for `volvo-cars/mb-aws-infrastructure_as_code`.

The epic is about **getting Greenfield fully mapped to CIS Benchmark** and establishing a **repeatable cloud security baseline plus periodic review process**. From the repo, a good part of the baseline already exists, but there are still clear gaps and opportunities.

## Recommended features to add to the epic

### 1. Organization-wide IAM Access Analyzer enablement
This looks like one of the clearest gaps.

The repo’s Greenfield standard explicitly says IAM Access Analyzer is desirable, but **not enabled organization-wide yet**:

```markdown name=docs/BF_to_GF_transition_docs/greenfield_standard.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/4c790f55c663cee4c19848595bed497fe886b42e/docs/BF_to_GF_transition_docs/greenfield_standard.md#L34-L35
- Identify public access to resources using the IAM Access Analyzer: Comprehensive analysis and visibility into IAM policies about all resources in each active AWS region can be obtained using IAM Access Analyzer.
It flags resources that allow public access to help you quickly identify and remediate unintended public access. However, it is not enabled for organizations at this time, CNAPP may cover this control in the future.
```

**Feature idea:**
- Enable IAM Access Analyzer at org/account level for Greenfield OUs
- Centralize analyzer findings
- Define remediation workflow for public and cross-account exposure findings
- Add Terraform modules/policies for consistent rollout

**Why it fits the epic:**
- Directly supports CIS/public exposure review
- Fills an explicitly documented gap

---

### 2. CIS control coverage dashboard / traceability matrix in-repo
The epic asks for aligned controls and regular review. The repo already has docs about Greenfield standards and CIS mapping, but not a clear **living implementation matrix** inside Terraform/repo outputs.

Relevant docs:

```markdown name=docs/greenfield-requirements/README.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/4c790f55c663cee4c19848595bed497fe886b42e/docs/greenfield-requirements/README.md#L5-L8
# Greenfield Requirements documentation AWS

## The following AWS documents outline the standars for a greenfield environment
```

```markdown name=docs/BF_to_GF_transition_docs/greenfield_standard.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/4c790f55c663cee4c19848595bed497fe886b42e/docs/BF_to_GF_transition_docs/greenfield_standard.md#L15-L23
## Introduction

As organizations increasingly adopt AWS for their cloud computing needs, and we need to secure our environments as we are following CIS controls to secure our environements.

This document outlines all the AWS controls that are implemented in greenfield and brownfield accounts and those are planned to implement in future as part of our organization's commitment to maintaining a secure and compliant cloud environment.
```

**Feature idea:**
- Add a machine-readable CIS coverage matrix in the repo
- Map each CIS requirement to:
  - Terraform resource/module
  - control owner
  - enforced/detective/manual status
  - Greenfield/Brownfield applicability
  - exceptions
- Generate Markdown or Backstage docs from code

**Why it fits:**
- Turns the epic from “implement controls” into “prove controls”
- Helps audit readiness and review cadence

---

### 3. Periodic security configuration review automation
The epic explicitly calls out the need for **regular review of security configurations**. That sounds only partially addressed today.

**Feature idea:**
- Scheduled review job/pipeline that:
  - validates enabled CIS-aligned services
  - checks drift in Security Hub policies, SCPs, Config aggregators, GuardDuty, logging, encryption defaults
  - publishes review output to GitHub issues / report artifact / Backstage
- Add recurring evidence generation for audit reviews

**Why it fits:**
- This is explicitly mentioned in the epic body as a missing process
- It creates the “review periodically” mechanism, not just static infrastructure

Possible areas already present that could be validated:
- Security Hub config
- SCPs
- Config aggregator
- logging account integrations
- account-level security defaults

---

### 4. Expand Security Hub standardization and remove/justify disabled controls
Security Hub is already configured centrally, but there are disabled controls that deserve explicit handling.

```hcl name=bootstrap/shared-services/security-hub/security_hub.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/4c790f55c663cee4c19848595bed497fe886b42e/bootstrap/shared-services/security-hub/security_hub.tf#L37-L56
      "arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.2.0",
      "arn:aws:securityhub:eu-west-1::standards/cis-aws-foundations-benchmark/v/1.4.0"
    ]
    security_controls_configuration {
      disabled_control_identifiers = [
        "IAM.6", # Hardware MFA should be enabled for the root user
        "IAM.9", # MFA should be enabled for the root user
        "EC2.6", # VPC flow logging should be enabled in all VPCs
        "S3.1"   # S3 general purpose buckets should have block public access settings enabled
      ]
    }
```

**Feature idea:**
- Review disabled Security Hub controls one by one
- For each:
  - implement the missing prerequisite, or
  - document formal exception/risk acceptance
- Add code comments linked to exception docs
- Add validation to prevent silent drift in disabled controls list

**Why it fits:**
- Directly advances “fully mapped to CIS”
- Prevents a false sense of compliance

---

### 5. Enforce VPC Flow Logs consistently across all Greenfield accounts/VPCs
This looks especially relevant because one disabled Security Hub control is:

- `EC2.6` — VPC flow logging should be enabled in all VPCs

The repo docs say VPC flow logs should be enabled:

```markdown name=docs/BF_to_GF_transition_docs/Logging.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/4c790f55c663cee4c19848595bed497fe886b42e/docs/BF_to_GF_transition_docs/Logging.md#L49-L55
## AWS VPC Flow logs

VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC.
VPC flows will be enabled for each configured deployed in Volvo Cars Control Tower environment.
The VPC flow will be stored in single S3 bucket for all VPCs in volvocars-logging account.
```

**Feature idea:**
- Add enforcement so every Greenfield VPC has flow logs enabled by default
- Add detection for noncompliant VPCs
- Centralize retention, encryption, and destination validation
- Re-enable the corresponding Security Hub control when ready

**Why it fits:**
- Bridges the gap between desired architecture and active enforcement

---

### 6. Baseline account-level security defaults module for all Greenfield accounts
Some account-level controls exist, but examples suggest they are applied per account/region rather than via a clearly unified baseline module.

Examples:

```hcl name=bootstrap/accounts/prod/dspa-cle-qa/us-east-1/security.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/4c790f55c663cee4c19848595bed497fe886b42e/bootstrap/accounts/prod/dspa-cle-qa/us-east-1/security.tf#L1-L16
# Encrypted EBS volumes are enabled by default in greenfield environments
resource "aws_ebs_encryption_by_default" "encrypt_ebs" {
  enabled = true
}

# Set IMDSv2 as default. Using IMDSv1 is blocked by SCPs in greenfield environments
resource "aws_ec2_instance_metadata_defaults" "enforce_imdsv2" {
  http_tokens = "required"
```

**Feature idea:**
- Create a reusable “greenfield_security_baseline” module that standardizes:
  - EBS encryption by default
  - IMDSv2 defaults
  - account/region security settings
  - CloudTrail/Config/SecurityHub/GuardDuty onboarding checks
- Roll it out uniformly across all Greenfield accounts

**Why it fits:**
- Reduces per-account drift
- Makes benchmark alignment consistent and auditable

---

### 7. Automated remediation coverage expansion
There is already an Automated Security Response setup for some CIS checks:

```markdown name=bootstrap/shared-services/automated-remediation-framework/README.md url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/4c790f55c663cee4c19848595bed497fe886b42e/bootstrap/shared-services/automated-remediation-framework/README.md#L11-L13
| 1.12 Ensure credentials unused for 45 days or greater are disabled                | CIS_1.4.0_1.12_AutoTrigger  | Enabled  | cis-aws-foundations-benchmark/v/1.4.0/1.12  |
| Ensure IAM password policy prevents password reuse               | CIS_1.4.0_1.9_AutoTrigger   | Enabled  | arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.4.0/rule/1.9  |
| 2.1.2 Ensure S3 Bucket Policy is set to deny HTTP requests               | CIS_1.4.0_2.1.2_AutoTrigger   | Enabled  | cis-aws-foundations-benchmark/v/1.4.0/2.1.2  |
```

**Feature idea:**
- Add more remediation playbooks for high-value CIS controls
- Prioritize:
  - S3 public exposure
  - missing encryption
  - missing logging
  - risky IAM policy patterns
  - stale credentials
- Add severity/risk tagging and exception handling

**Why it fits:**
- Moves from detective to corrective security
- Good child-issue stream under the epic

---

### 8. Config aggregator and detective controls completeness for Greenfield onboarding
There is existing automation around AWS Config aggregation:

```python name=bootstrap/shared-services/security/lambda/update_config_aggregator.py url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/4c790f55c663cee4c19848595bed497fe886b42e/bootstrap/shared-services/security/lambda/update_config_aggregator.py#L43-L139
def get_existing_account_aggregation_sources(
    config_client: ConfigServiceClient, aggregator: str
) -> List[AccountAggregationSourceTypeDef]:
...
config_aggregator_name: str = "aws-controltower-GuardrailsComplianceAggregator"
```

**Feature idea:**
- Ensure every new Greenfield account is automatically added to all required detective/security services
- Add onboarding validation for:
  - AWS Config recorder
  - aggregator membership
  - Security Hub association
  - GuardDuty org membership
  - log delivery
- Fail onboarding if required controls are absent

**Why it fits:**
- Makes Greenfield compliance automatic instead of manual/best-effort

---

### 9. SCP hardening expansion for Greenfield OUs
The repo already has Greenfield-specific SCP logic:

```hcl name=bootstrap/shared-services/security/scps.tf url=https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/4c790f55c663cee4c19848595bed497fe886b42e/bootstrap/shared-services/security/scps.tf#L798-L833
data "aws_iam_policy_document" "greenfield_policy_common" {

  statement {
    sid    = "RequireImdsV2"
    effect = "Deny"

    actions   = ["ec2:RunInstances"]
    resources = ["arn:aws:ec2:*:*:instance/*"]
```

**Feature idea:**
- Add more preventive SCP guardrails for Greenfield OUs, such as:
  - deny disabling security services
  - deny unencrypted storage creation where feasible
  - deny public S3 settings changes
  - deny risky network exposure patterns
  - deny CloudTrail / Config tampering
- Separate mandatory baseline vs exception-based policies

**Why it fits:**
- Preventive guardrails are the strongest way to keep Greenfield aligned

---

### 10. Formal exceptions / waiver framework for unsupported CIS controls
Some controls may not be implementable immediately or may be intentionally disabled.

**Feature idea:**
- Add exception registry in-repo
- For each non-implemented / disabled CIS control:
  - reason
  - owner
  - compensating control
  - expiration/review date
- Link exceptions to Terraform comments/docs and Security Hub disabled controls

**Why it fits:**
- Makes the epic audit-friendly
- Prevents “TBD forever” gaps

---

## My prioritized shortlist
If you want the most impactful features to add as child items under this epic, I’d prioritize these:

1. **Enable IAM Access Analyzer organization-wide**
2. **Implement recurring security configuration review automation**
3. **Enforce VPC Flow Logs for all Greenfield VPCs and re-evaluate Security Hub EC2.6**
4. **Create a Greenfield security baseline module for all accounts**
5. **Expand automated remediation for CIS findings**
6. **Build CIS traceability matrix / compliance reporting**
7. **Review and resolve all disabled Security Hub CIS controls**
8. **Create formal exception management for non-implemented controls**

---

## Suggested child issues under the epic
You could phrase them like this:

- **Enable IAM Access Analyzer for all Greenfield accounts**
- **Create CIS-to-Terraform control mapping for AWS Greenfield baseline**
- **Implement scheduled review of AWS Greenfield security configuration**
- **Enforce VPC Flow Logs in all Greenfield VPCs**
- **Create reusable Terraform module for Greenfield account security baseline**
- **Expand automated remediation coverage for CIS benchmark findings**
- **Review disabled Security Hub controls and implement/document exceptions**
- **Add onboarding validation for Config, GuardDuty, Security Hub, and centralized logging**
- **Create exception register for unsupported or deferred CIS controls**
- **Expand Greenfield SCPs to protect mandatory security services**

---

## Important note
The code search results are **limited and may be incomplete**. To inspect more matches in GitHub UI, use repository code search directly:

- https://github.com/volvo-cars/mb-aws-infrastructure_as_code/search?q=CIS
- https://github.com/volvo-cars/mb-aws-infrastructure_as_code/search?q=SecurityHub
- https://github.com/volvo-cars/mb-aws-infrastructure_as_code/search?q=GuardDuty
- https://github.com/volvo-cars/mb-aws-infrastructure_as_code/search?q=Config
- https://github.com/volvo-cars/mb-aws-infrastructure_as_code/search?q=Access+Analyzer

If you want, I can next turn this into a **clean epic breakdown with feature titles, descriptions, and acceptance criteria** ready to paste into GitHub issues.