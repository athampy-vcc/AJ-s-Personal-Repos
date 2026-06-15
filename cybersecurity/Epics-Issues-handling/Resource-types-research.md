

Executive conclusion: from code evidence across all 5 Volvo repos, only 2 of your 5 resource types are currently referenced in implementation artifacts, and one of those is mostly permission/identity wiring rather than direct resource deployment.

1. Active, strong usage: `microsoft.managedidentity/userassignedidentities`
2. Active, limited usage: `microsoft.security/automations` (read permission only, not deployment)
3. No implementation evidence found: `microsoft.azureactivedirectory/b2cdirectories`
4. No implementation evidence found: `microsoft.azureactivedirectory/ciamdirectories`
5. No implementation evidence found: `microsoft.sentinelplatformservices/sentinelplatformservices`

Design note vs live-state note: this is repository design evidence only. It does not prove what is currently deployed in Azure subscriptions.

Evidence by repo

- `volvo-cars/azure-core-infrastructure`
  - `userAssignedIdentities`: widespread in Terraform/policy/runtime code for UAMI creation, policy assignment identities, and role assignment scopes.
  - Purpose evidence: identity for policy remediations (DINE), Azure Monitor Agent flows, and automation/function access patterns.
  - `security/automations`: appears in CNAPP custom role actions as `.../automations/read` (permission surface for scanners/integration), not as resource creation.
  - No evidence for B2C directories, CIAM directories, or Sentinel Platform Services resource type.

- `volvo-cars/azure-global-enterprise-scale`
  - `userAssignedIdentities`: heavily used in L1 policy/identity patterns, especially UAMI for DINE AMA, DNS/NSG flows, and cross-scope role assignments.
  - Purpose evidence: reusable non-human identity for policy deployment/remediation and monitoring stack onboarding.
  - `security/automations`: present in CNAPP reader/custom role action set as read permission.
  - No evidence for B2C directories, CIAM directories, or Sentinel Platform Services resource type.

- `volvo-cars/azure-global-lz-vending-machine`
  - No direct usage of your listed resource types in provisioning logic.
  - Repo focuses on subscription vending, RBAC/PIM, baseline policy/diagnostics, provider registration.
  - No evidence for all 5 exact resource types in deployment definitions.

- `volvo-cars/Cloud-and-Infrastructure-Core`
  - Architecture/standards/documentation repo; contains guidance references to managed identity and Sentinel concepts.
  - No implementation evidence for your exact 5 resource type strings in deployable IaC modules.
  - So this repo is documentation context, not source of these resource deployments.

- `volvo-cars/backstage-systems`
  - Service catalog/docs repository; no evidence of these Azure resource type deployments.

Cross-repo correlation

- Implemented controls:
  - UAMI-based identity model is implemented in platform IaC repos (`azure-core-infrastructure`, `azure-global-enterprise-scale`), especially for policy initiatives/remediation and automation.
- Documented vs implemented:
  - `Cloud-and-Infrastructure-Core` documents identity/security posture and Sentinel architecture recommendations, but does not implement these resource types directly.
- Not observed platform-wide:
  - B2C, CIAM, and Sentinel Platform Services resource providers are not represented in the searched implementation repos.

Purpose of each resource type (based on platform intent + observed usage)

- `microsoft.azureactivedirectory/b2cdirectories`
  - Typical purpose: customer-facing external identity tenant for B2C journeys.
  - Observed here: no repo implementation evidence.

- `microsoft.azureactivedirectory/ciamdirectories`
  - Typical purpose: Entra External ID / CIAM tenant for consumer/partner identity.
  - Observed here: no repo implementation evidence.

- `microsoft.managedidentity/userassignedidentities`
  - Purpose in these repos: stable reusable workload identities decoupled from resource lifecycle; used by policy assignments, AMA/DCR integrations, remediation/automation workloads, and cross-scope RBAC.

- `microsoft.security/automations`
  - Typical purpose: Defender for Cloud/Security Center workflow automations.
  - Observed here: only `read` action permission included in CNAPP role definitions, suggesting visibility/access requirement rather than declaring automation resources.

- `microsoft.sentinelplatformservices/sentinelplatformservices`
  - Typical purpose: Sentinel platform-level service resources.
  - Observed here: no repo implementation evidence.

Gap view

- Closed:
  - UAMI usage and purpose are clearly implemented and traceable.
- Partial:
  - Security Automations only as read permission in role definitions; no direct automation resource deployment found in these 5 repos.
- Open:
  - B2C directories, CIAM directories, Sentinel Platform Services not found in IaC implementation.

Confidence and limits

- Confidence: high for repository evidence on `userAssignedIdentities`; medium-high for “not found” items (broad cross-repo searches done, but absence in code is not proof of absence in runtime).
- Limits:
  - Could be deployed manually, from private repos, or via pipelines not represented in these repositories.
  - Live Azure resource graph/portal validation is needed for runtime truth.

Action plan

1. Run Azure Resource Graph tenant-wide queries for these 5 resource types to confirm live deployment footprint.
2. Map live resources back to deployment source (repo/pipeline/manual) using tags, deployment history, and policy assignment metadata.
3. If needed, I can prepare exact ARG queries and a reconciliation template (resource type -> subscription -> RG -> deployment source).

Identity: microsoft.managedidentity/userassignedidentities

https://github.com/volvo-cars/azure-core-infrastructure/blob/main/DR/Layer2/umi.tf#L1
UAMI resources created for policy assignment and remediation identities.

https://github.com/volvo-cars/azure-core-infrastructure/blob/main/utilities/auto-remediation-solution/terraform-online/identity.tf#L1
Multiple UAMIs created for scan/remediation/DCR onboarding functions.

https://github.com/volvo-cars/azure-core-infrastructure/blob/main/utilities/auto-remediation-solution/auto_remediation_function_app/shared/identity_manager.py#L191
Runtime lifecycle manager for creating/deleting UAMIs and assigning RBAC.

https://github.com/volvo-cars/azure-global-enterprise-scale/blob/main/code/L1/umi.tf#L1
Central UAMI definitions and RBAC assignments used by platform policies.

https://github.com/volvo-cars/azure-global-enterprise-scale/blob/main/code/L1/umi.tf#L144
UAMI for DINE AMA initiative and role assignments.

https://github.com/volvo-cars/azure-global-enterprise-scale/blob/main/code/L1/umi.tf#L191
Additional UAMI usage for Qualys and DCR-related roles.

Security: microsoft.security/automations

https://github.com/volvo-cars/azure-core-infrastructure/blob/main/gov/L2%20-%20Offerings/cnapp-azure/L0-Foundation/locals.tf#L267
Includes Microsoft.Security/automations/read in custom role permissions.

https://github.com/volvo-cars/azure-global-enterprise-scale/blob/main/code/L2/cnapp-azure/L0-Foundation/main.tf#L283
Includes Microsoft.Security/automations/read in CNAPP reader actions.