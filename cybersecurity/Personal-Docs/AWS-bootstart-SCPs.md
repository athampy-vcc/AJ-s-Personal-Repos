Link to page - https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/develop/bootstrap/shared-services/security/scps.tf

This defines two things: a **generated test-environment README** for a remediation integration, and a **Terraform file that creates AWS Organizations Service Control Policies (SCPs)** to restrict what AWS accounts in the organization can do.

## 1) `README_TEST.md`

This file is documentation, not executable code.

### What it describes
It explains a flow where:

1. **Microsoft Defender for Cloud** detects an unhealthy AWS assessment.
2. A **Defender for Cloud automation** filters matching events.
3. It triggers an **Azure Logic App**.
4. The Logic App extracts fields like:
   - AWS account ID
   - resource ID
   - owner email
   - policy/assessment UUID
5. The Logic App sends a message to an **AWS SQS queue**.
6. An AWS **Lambda** consumes the SQS message and runs the mapped remediation.

### Important detail
The README says the automation currently uses a **placeholder resource ID filter**, so the automation is effectively **inactive for real events** until that placeholder is replaced or parameterized.

### Other sections
It also documents:
- Azure resources involved
- AWS resources involved
- fallback email
- the list of enabled policy UUIDs
- a large superset of all Defender for Cloud AWS policies

So this file is mainly operational documentation for the test deployment.

---

## 2) `bootstrap/shared-services/security/scps.tf`

This is the actual Terraform code. It defines **organization-wide guardrails** using AWS SCPs.

### High-level purpose
The file:

- builds reusable lists of allowed/exempt IAM roles in `locals`
- creates **4 root SCPs**
- attaches them to the **organization root**
- creates **2 Greenfield OU SCPs**
- attaches them to specific OUs like `corp`, `online`, `core`, `sandbox`, and `shared`

SCPs don’t grant permissions. They define the **maximum allowed permissions** in the affected accounts.

---

# Breakdown

## `locals { ... }`

This section builds reusable ARN lists.

Examples:

- `iam_smartfacts_lambda_roles`  
  ARNs for SmartFactory automation Lambda roles, based on `var.iam_user_scp_exception_accounts`

- `iam_smartfacts_selfservice_user_paths`  
  Allowed IAM user paths for those automation accounts:
  `arn:aws:iam::<account>:user/selfservice/*`

- `iam_smartfacts_general_boundaries`  
  Permission boundary policies those users must use

- `platform_administrator_roles`  
  Admin exemptions like:
  - `terraform-bootstrap-cicd-role`
  - AWS SSO AdministratorAccess roles

- `network_administrator_roles`  
  Network admin exemptions, plus dynamically discovered shared-services SSO admin roles

- `terraform_cicd_roles`  
  Terraform deployment roles across bootstrap/foundation/services

These locals are used repeatedly in SCP conditions so the same exemption logic is reused.

---

## `data "aws_iam_roles" "shared_services_awsreservedsso_administratoraccess_roles"`

This queries AWS IAM roles in the shared services account matching:

- regex: `AWSReservedSSO_.*AdministratorAccess_.*`
- path prefix: `/aws-reserved/sso.amazonaws.com/`

Purpose: dynamically include shared-services SSO admin roles in exemptions.

---

# Root SCPs

These are created as:

- `root_policy1`
- `root_policy2`
- `root_policy3`
- `root_policy4`

and attached to the organization root.

---

## `scp_root1`

This policy contains several deny rules.

### `PreventAWSBilling`
Denies billing/account/payment/tax changes unless caller ARN matches:
- platform admins
- marketplace admins

### `PreventAIAndMLResources`
Blocks creation/start of AI/ML services like:
- Bedrock
- SageMaker model creation
- Rekognition
- Comprehend
- Forecast
- Textract
- Translate
- etc.

This is a blanket guardrail preventing those services from being created.

### `RestrictRootUser`
Denies everything for the root user except `sts:AssumeRoot`, when not in an assumed-root session.

### `RestrictAssumedRootSession`
If operating as an assumed root session, only allows a tiny S3 bucket-policy-related set of actions:
- `s3:ListAllMyBuckets`
- `s3:GetBucketPolicy`
- `s3:PutBucketPolicy`
- `s3:DeleteBucketPolicy`

Everything else is denied.

### `DenyAssumeRootOutsideManagementAccount`
Only the organization management account root can call `sts:AssumeRoot`.

### `PreventGlacierVaults`
Denies deleting Glacier vaults/archives except for platform admins.

### `DenyOnPremConnectivity`
Denies creating peering/VPN connectivity unless caller is one of:
- platform admin
- AWS managed admin role
- network admin

### `PreventMarketPlace`
Denies AWS Marketplace subscriptions/agreements except marketplace admins.

---

## `scp_root2`

### `PreventSecurityandIAMActions`
Prevents disabling or deleting core security services/settings such as:
- Security Hub
- GuardDuty
- Config
- CloudTrail
- some IAM/SAML operations
- disabling EBS encryption by default

Exemptions:
- platform admins
- AWS managed admin roles

### `PreventRootAccessKeys`
Explicitly denies creating access keys for the root user.

### `PreventLeaveOrganization`
Prevents org-structure-changing actions like:
- leaving org
- moving accounts
- attaching/detaching policies
- creating/deleting OUs/policies/accounts
- disabling org features

Exemptions:
- platform admins
- AWS managed admin roles

### `SavingPlansnRI`
Prevents buying or modifying:
- Savings Plans
- Reserved Instances

Except for:
- platform admins
- AWS managed admins
- FinOps admins

### `DenyPrismaCloudTagsModification`
Denies all actions if the request tries to modify tag keys:
- `yor_trace`
- `git_org`
- `git_repo`

Except for specific trusted automation/admin roles:
- platform admins
- AWS managed admins
- Terraform CICD roles
- stackset creation roles
- AWS AR roles

This protects governance/tracing tags from manual tampering.

---

## `scp_root3`

### `ProtectPlatformResources`
Blocks modification/deletion of Terraform-managed platform resources, such as:
- CloudFormation stacks/stacksets
- Lambda functions and event source mappings

But only when the target resource has:
- `aws:ResourceTag/managed_by = terraform`

Exemptions:
- platform admins
- AWS managed admins
- stackset creation roles

### `ProtectRoles`
Protects critical IAM roles from modification/deletion, including:
- `AWSCloudFormationStackSetAdministrationRole`
- stackset roles
- `terraform-bootstrap-cicd-role`
- SSO AdministratorAccess roles

### `ProtectVPCFlowLogs`
Prevents deleting/modifying log groups/streams for:
- `aws-platform-flow-logs*`

Except for platform/admin/network roles.

### `DenyCreationAndAttachmentOfGP2`
Denies:
- `ec2:AttachVolume`
- `ec2:CreateVolume`
- `ec2:RunInstances`

when `ec2:VolumeType = gp2`, unless account is in `var.ebs_accounts_exceptions`.

Goal: enforce newer EBS volume types instead of `gp2`.

### `DenyUnencryptedRDS`
Denies `rds:CreateDBInstance` when:
- engine is not Aurora
- `rds:StorageEncrypted = false`

### `DenyUnencryptedAurora`
Denies `rds:CreateDBCluster` when Aurora storage encryption is false.

### `DenyUnencryptedEFS`
Denies `elasticfilesystem:CreateFileSystem` when encryption is false.

### `DenyCreatingDomains`
Denies `route53domains:RegisterDomain` for everyone.

Comment explains domains must be centrally managed.

---

## `scp_root4`

### `PreventIAMUserCreation`
Denies IAM user lifecycle actions like:
- create/delete/update user
- attach/detach policies
- create access keys
- add user to group

Unless caller is one of:
- platform admins
- AWS managed admins
- AWS AR roles
- IAM cleanup role
- SmartFacts Lambda roles

This enforces a no-manual-IAM-user model.

### `RequireSmartFactsToUseSelfServiceUsers`
Even for SmartFacts Lambda roles, deny user-related actions unless the target IAM user is under:
- `user/selfservice/*`

So SmartFacts can only manage a constrained namespace.

### `RequireSmartFactsToUseBoundaries`
For SmartFacts-created users, require the permissions boundary to match allowed `GeneralBoundaries` policies.

### `DenyOutsideRequestedRegions`
Despite the name, this denies requests when:
- principal is not in exempt admin roles
- `aws:RequestedRegion` equals one of `var.blocked_regions`

So it blocks specific disallowed regions.

---

# Greenfield OU SCPs

These apply only to certain organizational units.

---

## `greenfield_policy_common`

### `RequireImdsV2`
Denies `ec2:RunInstances` unless:
- `ec2:MetadataHttpTokens = required`

This enforces IMDSv2 on EC2 instances.

Attached to:
- corp
- online
- core
- sandbox
- shared

---

## `greenfield_policy_corp`

Attached only to `corp`.

### `PreventInternetConfig`
Denies creation/modification of internet-facing/network-edge services such as:
- WAF
- CloudFront
- Internet gateways
- VPN gateways/connections
- VPC peering
- Transit Gateway
- Elastic IP allocation/association
- Global Accelerator
- API Gateway REST API creation
- Network Firewall
- route-related actions

Except for:
- platform admins
- AWS managed admins
- network admins

This centralizes internet/network perimeter management.

### `DenyGatewayVpcEndpoints`
Denies `ec2:CreateVpcEndpoint` when service name matches:
- S3
- DynamoDB

Reason in comment: these are gateway endpoints and affect route tables, so they want them managed centrally via IaC.

Again exempting:
- platform admins
- AWS managed admins
- network admins

---

# Design pattern used throughout

The file consistently uses this pattern:

1. **Deny risky action**
2. Add **exceptions** using conditions on `aws:PrincipalArn`
3. Reuse role lists from `locals`

That means the organization is implementing a **centralized-control model**:
- most accounts/users are restricted
- specific admin/automation roles are exempt
- Terraform-managed and security-critical resources are protected from drift/manual change

---

# In plain English

This Terraform file is a **company-wide AWS guardrail policy set**. It is trying to ensure that teams cannot easily:

- bypass billing controls
- create AI/ML services
- use the root account broadly
- weaken security tooling
- leave the AWS organization
- buy Savings Plans/RIs without approval
- alter governance tags
- modify protected Terraform-managed platform resources
- use old `gp2` volumes
- create unencrypted RDS/Aurora/EFS resources
- create IAM users freely
- use blocked AWS regions
- expose internet/network infrastructure without central control

---

# One small thing to note

There’s a typo in a comment:

- `# SCSPs that are attached to the GreenField OU`

Probably meant:
- `# SCPs that are attached to the GreenField OU`

Also, the statement name `DenyOutsideRequestedRegions` is slightly misleading because the logic denies when the requested region **matches blocked regions**, not “outside allowed regions.”

If you want, I can also give you:
1. a **section-by-section annotated version of `scps.tf`**, or  
2. a **table mapping each SCP statement to its security purpose and exemptions**.