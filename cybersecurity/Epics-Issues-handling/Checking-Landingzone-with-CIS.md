# Azure Landing Zone vs CIS 1.4 for Greenfield

## Executive Summary

### Bottom line

The `azure-global-lz-vending-machine` repo by itself is **not enough** to prove CIS 1.4 alignment for a greenfield Azure landing zone. When assessed together with `azure-global-enterprise-scale`, `azure-core-infrastructure`, `Cloud-and-Infrastructure-Core`, and `backstage-systems`, the broader platform looks **materially closer to a defensible CIS-aligned implementation**.

### Closed or materially reduced gaps

- Azure Policy governance and management-group assignment patterns are strongly evidenced by `azure-global-enterprise-scale`.
- Remediation support is strongly evidenced by `azure-global-enterprise-scale` and `azure-core-infrastructure`.
- Defender for Cloud rollout capability is strongly evidenced by `azure-core-infrastructure` and `azure-global-enterprise-scale`.
- Network guardrails, diagnostics deployment patterns, RBAC guardrails, and public network access governance are evidenced upstream from the vending-machine layer.
- Architectural intent for centralized logging, PIM, RBAC, and service standards is documented in `Cloud-and-Infrastructure-Core`.

### Partially closed gaps

- Centralized monitoring and diagnostics are better supported than the vending-machine repo alone suggested, but live rollout breadth is still not fully provable from repo evidence.
- Service-hardening expectations for Storage, backup, IAM, and monitoring are documented, but enforcement linkage is not always explicit.
- Ownership and accountability are partially supported through `backstage-systems`, but that repo is not strong technical control evidence.

### Still open gaps

- No explicit source of truth proves that `CIS 1.4` is the named benchmark currently assigned and enforced end to end.
- No single control matrix maps each CIS 1.4 requirement to a specific policy assignment, remediation path, and live compliance view.
- Repo evidence proves design and implementation patterns better than live compliance state.

### Recommended decision statement

The safest conclusion is:

- `The landing zone ecosystem appears broadly capable of supporting a CIS-aligned greenfield Azure platform, but benchmark-specific proof for CIS 1.4 still requires a formal mapping and live compliance evidence.`

## Scope

This review is based on code and documentation in the repo below:

- https://github.com/volvo-cars/azure-global-lz-vending-machine

The assessment is a repo-based design review, not a live Azure validation. That means the conclusion is limited to what this repo clearly provisions or assigns. If a control is enforced elsewhere, such as an upstream enterprise-scale policy repo or Microsoft Defender for Cloud, this repo alone does not prove it.

## Overall Conclusion

The landing zone vending machine is **partially aligned** with a greenfield CIS 1.4 baseline, but it does **not** demonstrate full CIS 1.4 compliance on its own.

What the repo clearly does well:

- Creates new subscriptions and associates them to a management group.
- Applies core subscription bootstrap controls such as budget creation, subscription diagnostic settings to a central storage account, subscription owner group creation, and some PIM-related role handling.
- Applies network guardrail policy assignments for NSG and route table usage.
- Applies allowed-location restrictions in some online scenarios.
- Adds management locks to selected resource groups.

Why this is still not enough for CIS 1.4 greenfield:

- Most CIS control families require either broader Azure Policy coverage, Microsoft Defender for Cloud posture enforcement, or service-specific hardening for resources such as Key Vault, Storage, SQL, networking, logging, and compute.
- This repo mostly covers **subscription vending and initial bootstrap**, not a full end-to-end security baseline.
- Several controls appear to depend on external policy sets already defined elsewhere. That is acceptable architecturally, but it means this repo alone cannot be treated as evidence of full CIS conformance.

## Evidence Found In The Repo

The following control patterns are visible in the repository:

### Implemented or partially implemented

- Subscription creation and tagging.
- Management group association for the onboarded subscription.
- Subscription diagnostic settings forwarding activity logs to a central storage account.
- Subscription budget creation with email notifications.
- Azure AD group creation for the subscription owner and role assignment flow.
- PIM-related automation hooks for subscription access.
- Policy assignment for network guardrails that require NSG and route table attachment.
- Allowed-location policy assignment in some online patterns.
- Management locks on selected central resource groups.
- Registration of selected resource providers.

### Evidence that is weak, partial, or absent

- No clear assignment of a CIS 1.4 Azure initiative or equivalent umbrella policy set.
- No clear Microsoft Defender for Cloud onboarding or regulatory compliance mapping in this repo.
- No clear Log Analytics workspace onboarding for the new subscription in this repo.
- No broad resource diagnostic settings baseline beyond subscription activity logs.
- No clear policy remediation tasks or exemption workflow in this repo.
- No clear service baselines for Key Vault, Storage, SQL, private endpoints, or encryption-related settings.
- No clear baseline for DDoS, Azure Firewall, WAF, or deeper network inspection requirements.
- No clear evidence of secure-by-default hardening for workload resources that would be created after vending.

## Gap Analysis Against CIS 1.4 Greenfield Intent

## 1. Governance and policy baseline

**Current state**

- The repo applies targeted subscription policy assignments for network guardrails and, in some cases, allowed locations.
- The design assumes enterprise policy artifacts already exist and are referenced by policy set ID.

**Gap**

- There is no clear evidence here that a full CIS-aligned Azure Policy initiative is assigned at management group or subscription scope.
- There is no evidence that policy compliance, exemptions, and remediation are handled as part of the landing zone flow.

**Impact**

- For a greenfield landing zone, this is the biggest control gap because CIS alignment should be provable through policy assignment and compliance reporting from day one.

**Assessment**

- `Gap`

## 2. Identity and access management

**Current state**

- The repo creates an Azure AD security group for the subscription owner.
- It applies role assignment logic and includes PIM-related scripting and role management policy handling.

**Gap**

- This proves subscription access bootstrap, but not the broader CIS identity baseline.
- There is no evidence in this repo for tenant-wide protections such as conditional access, MFA posture enforcement, privileged access review cadence, or break-glass governance.
- There is no evidence of formal least-privilege role model coverage beyond the initial owner/subscription access pattern.

**Impact**

- Good starting point for greenfield onboarding, but not enough to claim CIS identity compliance.

**Assessment**

- `Partial`

## 3. Logging and monitoring

**Current state**

- The repo enables subscription diagnostic settings and sends activity log categories to a central storage account.
- Some monitoring-related constructs, such as an action group, are present.

**Gap**

- Sending subscription activity logs only to storage is weaker than a broader greenfield observability baseline.
- There is no clear evidence here of Log Analytics workspace onboarding, Microsoft Sentinel integration, alert standardization, or resource-level diagnostic settings for common PaaS services.
- There is no proof of retention, analytics, threat detection coverage, or centralized monitoring rules mapped to CIS.

**Impact**

- This is a material gap because greenfield CIS alignment normally expects auditable, centralized monitoring beyond basic activity log export.

**Assessment**

- `Partial to Gap`

## 4. Microsoft Defender for Cloud and security posture management

**Current state**

- The repo registers `Microsoft.Security` and contains legacy Qualys-oriented automation.

**Gap**

- There is no clear evidence in this repo that Defender for Cloud plans are enabled for the onboarded subscription.
- There is no clear evidence that the regulatory compliance dashboard or CIS 1.4 posture is part of the landing zone acceptance criteria.
- Qualys deployment is not a substitute for a modern CIS posture baseline in Azure.

**Impact**

- Without Defender for Cloud or equivalent central posture evidence, CIS conformance will be hard to prove and continuously track.

**Assessment**

- `Gap`

## 5. Networking

**Current state**

- The repo provisions network resources in some paths.
- It enforces NSG and route table attachment through policy assignment.
- It supports allowed-location controls in some scenarios.

**Gap**

- The repo does not show a broader CIS network baseline, such as standardized private endpoint usage, network watcher/flow logging coverage, DDoS strategy, firewall controls, or strong default denial patterns across all landing zone variants.
- The allowed-location policy is conditional and not universal across all patterns.
- The network guardrails are meaningful, but they are only one slice of the network control family.

**Impact**

- This area is better than most others in the repo, but still incomplete against a full CIS-aligned greenfield standard.

**Assessment**

- `Partial`

## 6. Resource hardening for Storage, Key Vault, databases, and compute

**Current state**

- I did not find clear evidence in this repo that the landing zone directly deploys or hardens these service types as part of a reusable greenfield baseline.

**Gap**

- No clear baseline for storage account hardening such as public access restrictions, secure transfer, private access strategy, minimum TLS, or shared key restrictions.
- No clear baseline for Key Vault soft delete, purge protection, private access, or network ACL requirements.
- No clear baseline for SQL, VM, or other workload-service hardening controls that CIS commonly expects to be governed.

**Impact**

- If these baselines are handled later by application teams, the landing zone is not fully secure-by-default for greenfield onboarding.

**Assessment**

- `Gap`

## 7. Secure-by-default operating model

**Current state**

- The repo standardizes naming, tagging, management group placement, and a few mandatory controls during vending.

**Gap**

- The repo does not establish a complete secure-by-default subscription blueprint that guarantees CIS-aligned controls before workloads arrive.
- Several important controls appear to be deferred to downstream processes, external repositories, or manual governance.

**Impact**

- For greenfield, the landing zone should act as the first enforcement point. Right now it looks more like a provisioning accelerator with selected guardrails.

**Assessment**

- `Gap`

## Priority Gaps

These are the most important gaps if the target is CIS 1.4 alignment for greenfield subscriptions:

| Priority | Gap | Why it matters |
|---|---|---|
| High | No provable CIS-aligned initiative assignment | Without a benchmark-aligned initiative, compliance is not demonstrable. |
| High | No Defender for Cloud baseline in repo evidence | Weakens posture visibility and continuous compliance tracking. |
| High | Logging is limited mostly to subscription activity logs | Not enough for broader monitoring and investigation requirements. |
| High | No clear service-level hardening baselines | Key Vault, Storage, SQL, and compute controls are not visible as secure defaults. |
| Medium | Network controls are partial, not comprehensive | NSG/RT and location restrictions help, but do not cover the full network family. |
| Medium | Reliance on external policy artifacts is not documented as a compliance dependency | Makes the landing zone hard to audit as a standalone greenfield solution. |

## Recommended Next Actions

1. Treat this repo as the **subscription vending layer**, not as the full CIS implementation evidence.
2. Identify the upstream repo or platform layer where the full Azure Policy and Defender for Cloud baseline is assigned.
3. Prove whether a CIS 1.4 or equivalent initiative is assigned automatically when a new subscription is attached to the target management group.
4. Add explicit greenfield acceptance checks for:
	- policy assignment status
	- Defender for Cloud plan enablement
	- regulatory compliance visibility
	- Log Analytics onboarding
	- required diagnostic settings
	- mandatory service hardening baselines
5. If those controls are not handled elsewhere, add them to the landing zone flow or to a mandatory post-vending enforcement stage.

## Additional Repo Review

I reviewed these additional repos to see whether they close the earlier evidence gaps:

1. `azure-global-enterprise-scale`
2. `azure-core-infrastructure`
3. `Cloud-and-Infrastructure-Core`
4. `backstage-systems`

### Summary verdict

Yes, these repos materially improve the compliance story, but not equally.

- `azure-global-enterprise-scale` is the **strongest repo** for clearing the biggest CIS gaps.
- `azure-core-infrastructure` is the **second most useful repo**, mainly for Defender for Cloud, remediation, tagging, and operational compliance evidence.
- `Cloud-and-Infrastructure-Core` helps mostly with **target-state architecture, standards, and intended controls**, not direct enforcement proof.
- `backstage-systems` helps with **ownership and accountability evidence**, but contributes very little to proving CIS technical enforcement.

## Repo-by-repo impact on the gaps

## 1. azure-global-enterprise-scale

**Value**

- This repo closes a large part of the earlier uncertainty around Azure Policy governance.
- It clearly shows enterprise-scale policy definition, initiative creation, assignment, and remediation patterns.

**What it helps prove**

- Management-group-based Azure Policy design and inheritance.
- Broad deny, audit, and deploy-if-not-exists policy architecture.
- Policy assignment at management group, subscription, resource group, and resource scope.
- Support for remediation tasks in the core Terraform module.
- Presence of greenfield landing zone guardrails for corp and online archetypes.
- Presence of global governance controls such as deny RBAC, deny governance, diagnostics deployment, public network access auditing, AKS guardrails, DNS/private DNS patterns, and network guardrails.

**Key evidence themes found**

- `vccalz_glo_deny_governance.tf` shows global deny governance assignment.
- `vccalz_glo_deny_rbac.tf` shows RBAC guardrail assignment.
- `vccalz_glo_dine_diag.tf` shows deploy-if-not-exists diagnostics policy assignment.
- `vccalz_deploy_dfc_config.tf` shows Defender for Cloud configuration assignment.
- `modules/initiative_assignment/main.tf` shows remediation task support.
- `remediations/main.tf` shows management-group remediation execution.
- `deployed_policies_landing_zone.md` shows deployed policy assignment inventory.
- `docs/azure_policy_structure.md` and `docs/Azure-Policy-Setup.md` show intended policy placement and assignment model.

**Gaps it reduces**

- Strongly reduces the gap around missing benchmark-wide Azure Policy coverage.
- Strongly reduces the gap around compliance/remediation proof.
- Partially reduces the gap around diagnostics and public network access governance.
- Partially reduces the gap around network and RBAC guardrails.

**What remains unresolved even after this repo**

- This repo shows broad control enforcement patterns, but it still does not by itself prove that a named `CIS 1.4` initiative is the governing benchmark.
- It improves technical enforcement evidence far more than benchmark-version traceability.

**Assessment**

- `Major gap clearer`

## 2. azure-core-infrastructure

**Value**

- This repo materially strengthens the operational security story, especially around Defender for Cloud, remediation, tagging, and posture management.

**What it helps prove**

- Microsoft Defender for Cloud can be enabled through management-group policy initiative assignment.
- Defender plan rollout is designed to fan out to subscriptions automatically.
- Remediation workflows exist, including auto-remediation and observability around remediation.
- Log Analytics and Application Insights are used in remediation/operations platforms.
- Tag governance and ownership hygiene are actively managed.

**Key evidence themes found**

- `modules/defender-for-cloud-enterprise/main.tf` defines custom Defender plan policies at management-group scope.
- `modules/defender-for-cloud-enterprise/README.md` describes MG-root assignment, enforcement mode, and remediation task support.
- `DR/Layer2/vccalz_deploy_dfc_config.tf` shows broad Defender for Cloud configuration coverage including servers, containers, SQL, Key Vault, App Service, and export to Log Analytics.
- `utilities/auto-remediation-solution/README.md` shows Azure-native remediation using Functions, Resource Graph, App Insights, and Log Analytics.
- `docs/cost-savings.md` confirms mandatory tagging enforcement and remediation patterns.

**Gaps it reduces**

- Strongly reduces the gap around Defender for Cloud enablement evidence.
- Strongly reduces the gap around remediation and compliance operations.
- Partially reduces the logging/monitoring gap.
- Partially reduces the ownership/tagging governance gap.

**What remains unresolved even after this repo**

- Some Defender settings in sampled code are still disabled in places, so this repo shows capability and rollout patterns more than universal enforcement proof.
- It is strong evidence for posture management, but not a clean one-to-one CIS benchmark mapping.

**Assessment**

- `Major gap clearer`

## 3. Cloud-and-Infrastructure-Core

**Value**

- This repo is useful for validating what the greenfield architecture is supposed to enforce.
- It helps turn some earlier assumptions into documented platform standards.

**What it helps prove**

- Greenfield architecture expects centralized management and monitoring.
- A centralized Log Analytics and Sentinel-oriented design exists.
- Azure Policy is the intended governance mechanism across the management-group hierarchy.
- Greenfield IAM design expects PIM, least privilege, and managed identities.
- Platform standards exist for Storage, backup, monitoring, network topology, and brownfield-to-greenfield assessment.

**Key evidence themes found**

- `docs/target-architecture/pce/MG&SubsOrganization.md` documents the landing zone management-group model and policy-based governance.
- `docs/target-architecture/pce/Management&Monitoring.md` documents centralized logging, diagnostic logging, retention, and Azure Monitor Agent expectations.
- `docs/target-architecture/pce/IAM.md` documents PIM and least-privilege expectations.
- `docs/technical/cloud/BF-to-GF (draft proposal_v1.0).md` documents LAW, RBAC, policy, tagging, and region checks during greenfield alignment.
- `docs/technical/storage/az-storage-account.md` and related standards documents define expectations for private endpoints, TLS, audit logging, secure transfer, shared key avoidance, and lifecycle/retention.

**Gaps it reduces**

- Partially reduces the logging/monitoring gap by documenting the intended baseline.
- Partially reduces the identity and secure-by-default gap by documenting standards.
- Partially reduces the storage/service-hardening gap by documenting required patterns.

**What remains unresolved even after this repo**

- This repo is mostly architectural and standards-driven, not direct technical enforcement evidence.
- It tells us what should be true, but less often proves where it is enforced in Azure.

**Assessment**

- `Useful supporting evidence`

## 4. backstage-systems

**Value**

- This repo helps mainly with system ownership metadata and accountability.

**What it helps prove**

- Systems are expected to declare an owner.
- Ownership is tied to Entra ID groups in the catalog model.
- Some systems include platform, observability, or architecture metadata.

**Key evidence themes found**

- `README.md` defines owner requirements in `system.yaml`.
- Many systems are undocumented placeholders, which limits evidence quality.

**Gaps it reduces**

- Slightly reduces the governance-accountability gap.

**What remains unresolved even after this repo**

- It does not materially prove CIS technical controls.
- It is not useful for Azure Policy, Defender, diagnostics, network guardrails, or service-level hardening proof.

**Assessment**

- `Low-value for CIS proof`

## Revised conclusion after reviewing all repos

The earlier conclusion should be refined:

- Based on the vending-machine repo **alone**, CIS 1.4 compliance could not be claimed.
- Based on the **combined repo set**, the platform looks much closer to a defensible greenfield CIS-aligned architecture.

The strongest combined picture is:

- `azure-global-lz-vending-machine` provides subscription bootstrap.
- `azure-global-enterprise-scale` provides enterprise Azure Policy enforcement and remediation patterns.
- `azure-core-infrastructure` provides Defender for Cloud, operational governance, remediation, and tagging/compliance support.
- `Cloud-and-Infrastructure-Core` provides the target-state architecture and service standards that explain the intended greenfield baseline.

## Revised remaining gaps

Even with these repos, the following still need explicit proof if the goal is to say `matches CIS 1.4 benchmark` rather than `looks broadly CIS-aligned`:

| Remaining gap | Why it still matters |
|---|---|
| No explicit benchmark trace showing `CIS 1.4` as the active governing initiative | The repos show policy patterns and guardrails, but benchmark traceability is still indirect. |
| No single source of truth mapping each CIS 1.4 recommendation to policy assignment or live compliance evidence | This is needed for an auditable benchmark claim. |
| Limited proof of live enforcement state versus designed capability | Repo code proves intent and implementation, but not live tenant compliance percentages. |
| Service-hardening standards exist in docs, but enforcement linkage is not always explicit | Standards and policy/code need a clearer join for audit evidence. |

## Updated answer

Yes. These repos do help clear the gaps, especially `azure-global-enterprise-scale` and `azure-core-infrastructure`.

If you use all four repos together, the story changes from:

- `landing zone vending machine is not sufficient`

to:

- `the broader platform appears capable of supporting a CIS-aligned greenfield landing zone, but a benchmark-specific mapping and live compliance proof are still needed before claiming full CIS 1.4 alignment`

## Repo-to-gap traceability table

| Gap area | azure-global-lz-vending-machine | azure-global-enterprise-scale | azure-core-infrastructure | Cloud-and-Infrastructure-Core | backstage-systems | Outcome |
|---|---|---|---|---|---|---|
| Subscription bootstrap | Strong | Limited | Limited | Context only | No value | `Closed for provisioning evidence` |
| Policy governance baseline | Weak on its own | Strong | Partial | Partial | No value | `Materially closed` |
| Policy remediation and enforcement operations | Weak on its own | Strong | Strong | Context only | No value | `Materially closed` |
| Defender for Cloud rollout | Weak on its own | Partial | Strong | Partial | No value | `Materially closed` |
| Logging and diagnostics baseline | Partial | Partial | Partial | Strong on design | No value | `Partially closed` |
| RBAC and PIM guardrails | Partial | Strong | Partial | Strong on design | Partial owner metadata | `Partially closed` |
| Network guardrails | Partial | Strong | Partial | Strong on design | No value | `Materially closed` |
| Public network access restrictions | Weak on its own | Strong | Partial | Strong on standards | No value | `Partially closed` |
| Storage and service hardening standards | Weak on its own | Partial | Partial | Strong on standards | No value | `Partially closed` |
| Ownership and accountability | Partial via tags/app IDs | Partial | Partial | Partial | Strongest of the four | `Partially closed` |
| Explicit CIS 1.4 benchmark traceability | Weak | Partial | Partial | Partial | No value | `Still open` |
| Live tenant compliance proof | No value | Partial | Partial | No value | No value | `Still open` |

## Working CIS 1.4 mapping sheet

This is a working evidence matrix using **paraphrased CIS control themes**, not benchmark text. It is intended to show whether the repo set gives credible implementation evidence for the main control areas expected in a CIS 1.4 greenfield Azure baseline.

| CIS 1.4 control theme | What good looks like in practice | Primary evidence repos | Status | Notes |
|---|---|---|---|---|
| Management-group governance | Subscriptions inherit mandatory policy from the correct management-group branch | `azure-global-lz-vending-machine`, `azure-global-enterprise-scale`, `Cloud-and-Infrastructure-Core` | `Partial to strong` | Vending machine attaches subscriptions; enterprise-scale proves inherited governance model. |
| Policy initiative assignment | Benchmark-relevant policy sets are assigned at the right scopes | `azure-global-enterprise-scale` | `Strong` | Multiple initiatives and assignments are clearly implemented. |
| Policy remediation | Non-compliant resources can be remediated automatically or through managed workflows | `azure-global-enterprise-scale`, `azure-core-infrastructure` | `Strong` | Native remediation support and operational remediation workflows are both present. |
| RBAC guardrails | Privileged role assignment is constrained and governed | `azure-global-enterprise-scale`, `Cloud-and-Infrastructure-Core`, `azure-global-lz-vending-machine` | `Partial` | Evidence exists for RBAC guardrails and PIM patterns, but not full tenant-wide proof. |
| PIM and privileged access | Human elevation is time-bound and role usage is constrained | `azure-global-lz-vending-machine`, `Cloud-and-Infrastructure-Core` | `Partial` | Design is clear; full operational proof is indirect. |
| Mandatory tagging and ownership | Subscriptions and resources carry mandatory ownership metadata | `azure-global-lz-vending-machine`, `azure-core-infrastructure`, `backstage-systems` | `Strong` | Tagging is enforced in platform patterns; ownership metadata is supported separately. |
| Activity log export | Subscription activity logs are exported centrally | `azure-global-lz-vending-machine` | `Strong` | Clearly implemented in vending-machine flow. |
| Centralized monitoring workspace | Logs and monitoring data land in centralized analytics workspaces | `azure-core-infrastructure`, `Cloud-and-Infrastructure-Core` | `Partial` | Architecture is strong, but broad rollout evidence is not complete from repo data alone. |
| Diagnostic settings for Azure resources | Core services emit diagnostics to centralized destinations | `azure-global-enterprise-scale`, `Cloud-and-Infrastructure-Core` | `Partial` | DINE diagnostics patterns exist, but coverage breadth is not fully proven. |
| Defender for Cloud baseline | Defender plans are enabled and managed at scale | `azure-core-infrastructure`, `azure-global-enterprise-scale` | `Strong` | Both repos contain MG-driven DFC rollout patterns. |
| Regulatory compliance visibility | Security posture is visible through a benchmark/compliance dashboard | `azure-core-infrastructure` | `Partial` | Defender posture capability is evident, but live CIS 1.4 dashboard assignment is not explicitly proven. |
| Network segmentation guardrails | NSG, route table, and landing-zone network controls are enforced | `azure-global-lz-vending-machine`, `azure-global-enterprise-scale`, `Cloud-and-Infrastructure-Core` | `Strong` | This is one of the best-covered areas across the repos. |
| Allowed region restrictions | Resource deployment is constrained to approved geographies | `azure-global-lz-vending-machine`, `azure-global-enterprise-scale` | `Partial to strong` | Clear evidence exists, but not every pattern is universal. |
| Public network access restrictions | PaaS resources are audited or denied when exposed publicly | `azure-global-enterprise-scale`, `Cloud-and-Infrastructure-Core` | `Partial to strong` | Strong policy pattern evidence exists upstream. |
| Private access patterns | Private endpoints, private DNS, or equivalent secure connectivity are standard | `azure-global-enterprise-scale`, `Cloud-and-Infrastructure-Core` | `Partial` | Good standards and some policy evidence exist; platform-wide enforcement proof is incomplete. |
| Storage hardening | Public access, secure transfer, TLS, auth model, and lifecycle protections are governed | `Cloud-and-Infrastructure-Core`, `azure-global-enterprise-scale` | `Partial` | Strong standards exist, but enforcement linkage is not always explicit in repo evidence. |
| Key Vault hardening | Key Vault security plans, purge protections, and access controls are governed | `azure-core-infrastructure`, `Cloud-and-Infrastructure-Core` | `Partial` | Defender and standards evidence exists; direct greenfield enforcement proof is incomplete. |
| SQL and data service hardening | Encryption, private access, logging, and identity controls are governed | `azure-core-infrastructure`, `Cloud-and-Infrastructure-Core` | `Partial` | Standards and Defender plans exist, but the tenant-wide enforcement picture is incomplete. |
| AKS and workload guardrails | Kubernetes and other higher-risk workloads are governed through policy | `azure-global-enterprise-scale` | `Partial to strong` | AKS guardrail policy sets exist. |
| Secure-by-default greenfield onboarding | New subscriptions enter the platform with mandatory controls from day one | All except `backstage-systems` | `Partial to strong` | The combined pattern is strong, but benchmark-specific proof is still missing. |
| Explicit benchmark traceability | Each CIS 1.4 requirement maps to a specific control and evidence source | None as a single source | `Open` | This remains the main audit gap. |

## Executive view by status

### Closed or materially closed

- Subscription vending and greenfield bootstrap
- Management-group policy assignment model
- Network guardrails
- Remediation capability
- Defender for Cloud rollout capability
- Mandatory tagging and ownership metadata at platform level

### Partially closed

- PIM and RBAC operating model
- Centralized diagnostics and monitoring depth
- Public network access governance
- Private access and private DNS patterns
- Storage, Key Vault, and SQL hardening enforcement linkage
- Regulatory compliance reporting proof

### Still open

- Explicit `CIS 1.4` benchmark assignment traceability
- Single source of truth for control-to-policy mapping
- Live Azure compliance proof for the intended benchmark

## Final Verdict

Based on the **vending-machine repo alone**, the landing zone is **not yet sufficient to claim CIS 1.4 greenfield compliance**.

Based on the **combined repo set**, the platform evidence is significantly stronger and closes much of the earlier uncertainty around Azure Policy governance, Defender for Cloud, remediation, and architectural standards. Even so, I would still stop short of saying it `matches CIS 1.4 benchmark` until there is an explicit control-by-control CIS mapping and live compliance evidence showing which assignments are active and compliant in Azure.
