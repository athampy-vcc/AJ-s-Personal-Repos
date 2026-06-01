The repo is designed around a few core Azure-native security controls, with Azure Policy/RBAC guardrails appearing to be the strongest “by design” layer.

From the repository evidence, the main native Azure security services and capabilities in use are:

1. Azure Policy
- Used extensively for deny/audit/configure guardrails and exemptions.
- Evidence:
  - `DR/Layer2/glo_deny_rbac.tf_` defines deny policies for custom role creation and privileged role assignment.
  - `gov/L1/policy-exemption/main.tf` generates Azure Policy exemption reporting.
  - `docs/internal/TS05_Lifecycle_Management_Policy.md` contains a custom Azure Policy example with `DeployIfNotExists` and `Audit`.
- Design intent:
  - Enforce compliant landing zones.
  - Prevent risky RBAC changes.
  - Automate configuration and compliance checks.

2. Azure RBAC
- RBAC is a central security design choice, especially for authorization to platform resources.
- Evidence:
  - Key Vault uses `enable_rbac_authorization = true` in the AI landing zone Terraform.
  - `docs/internal/TS02-LZ RBAC improvements.md` discusses improving the landing-zone RBAC model while denying overprivileged role assignments.
  - `DR/Layer2/glo_deny_rbac.tf_` explicitly restricts privileged roles.
- Design intent:
  - Prefer Azure RBAC over access policies where possible.
  - Limit privilege escalation.
  - Support secure self-service within landing zones.

3. Azure Key Vault
- Used for secrets management and protected with network ACLs plus RBAC.
- Evidence:
  - `gov/L2 - Offerings/ai-lz-project-online/template/main.tf`
  - `gov/L2 - Offerings/ai-lz-project-online/.../main.tf`
  - `gov/L2 - Offerings/reporting-functions/functions/appid/__init__.py` uses `azure.keyvault.secrets.SecretClient`.
- Security configuration seen:
  - `enable_rbac_authorization = true`
  - `network_acls { default_action = "Deny" }`
- Design intent:
  - Centralize secrets.
  - Avoid embedding secrets in app code.
  - Restrict network access to vaults.

4. Managed Identity / Microsoft Entra-backed identity
- System-assigned managed identities are widely used.
- Evidence:
  - AI Services resources use:
    - `identity { type = "SystemAssigned" }`
  - Azure ML workspace/hub/project resources also use system-assigned identities.
  - Function app in `reporting-functions/infra/main.tf` also uses managed identity.
  - `docs/internal/TS09-Provision-ServiceProvider-Identity.md` explicitly calls out “OIDC / Workload Identity Federation” and Easy Auth.
- Design intent:
  - Reduce secret-based authentication.
  - Use Azure-native identity for service-to-service access.
  - Avoid storing credentials in GitHub and runtime configs.

5. Microsoft Defender for Cloud
- Present as a security posture and workload protection service.
- Evidence:
  - `utilities/poc-dfc-cnapp/templates/template.tf` provisions `Microsoft.Security/pricings@2024-01-01` with `CloudPosture`.
  - `utilities/defender_report.md` reports “Microsoft Defender for Cloud Status Report”.
- Design intent:
  - Subscription-level cloud security posture management.
  - Defender plans and MDE integration visibility.
  - Security monitoring across subscriptions.

6. Azure Monitor / Application Insights / Log Analytics family
- Used for monitoring, reporting, and policy visibility.
- Evidence:
  - `gov/L2 - Offerings/reporting-functions/infra/main.tf` provisions `azurerm_application_insights`.
  - Policy workbook files (`DR/Layer2/eus_policy_workbook.tf_`, `sea_policy_workbook.tf_`) query policy assignments using workbook/KQL-style monitoring views.
  - Sentinel onboarding utility references `Microsoft.Insights/dataCollectionRules`.
- Design intent:
  - Operational visibility into compliance and workloads.
  - Telemetry for apps and policy posture.
  - Foundation for SIEM/SOC integrations.

7. Microsoft Sentinel-related onboarding
- Not a full Sentinel deployment proof by itself, but Sentinel-connected onboarding is present.
- Evidence:
  - `utilities/sentinel_dcr_onboarding/policy.yml`
  - `utilities/sentinel_dcr_onboarding/sentinel_onboarding_dry_run.py`
  - Uses allow-listed `Microsoft.Insights/dataCollectionRules` for onboarding.
- Design intent:
  - Controlled ingestion to monitoring/security analytics pipelines.
  - Guardrailed onboarding of VM data collection rules.

8. Storage security hardening
- Azure Storage is configured with strong secure defaults in several Terraform stacks.
- Evidence:
  - AI landing zone storage account:
    - `https_traffic_only_enabled = true`
    - `min_tls_version = "TLS1_2"`
    - `default_to_oauth_authentication = true`
    - `shared_access_key_enabled = false`
    - `allow_nested_items_to_be_public = false`
    - `network_rules { default_action = "Deny" }`
- Design intent:
  - Enforce encrypted transport.
  - Prefer Entra/OAuth over account keys.
  - Block public exposure and default network access.

9. Function App authentication controls
- Azure-native auth is used rather than only custom auth code.
- Evidence:
  - `gov/L2 - Offerings/reporting-functions/infra/main.tf` sets `WEBSITE_AUTH_AAD_ALLOWED_TENANTS`.
  - `docs/internal/TS09-Provision-ServiceProvider-Identity.md` explicitly states:
    - “Auth | Easy Auth + azp validation”
- Design intent:
  - Use Azure App Service / Function App authentication with Entra ID.
  - Reduce custom token validation logic.
  - Restrict allowed tenants.

What this means architecturally
This repo appears to treat Azure-native security in layers:

- Governance layer:
  - Azure Policy
  - RBAC restrictions
  - Exemption reporting
- Identity layer:
  - Managed identities
  - Entra ID / Easy Auth
  - OIDC federation for CI/CD
- Secrets layer:
  - Azure Key Vault
- Network/data protection layer:
  - Deny-by-default network ACLs
  - Storage TLS/OAuth/public access restrictions
- Detection/monitoring layer:
  - Defender for Cloud
  - Application Insights / Azure Monitor
  - Sentinel DCR onboarding

Most clearly evidenced “native Azure security services used by design”
If I narrow it to the strongest, most explicit native security services in the repo, they are:

- Azure Policy
- Azure RBAC
- Azure Key Vault
- Managed Identity / Microsoft Entra ID integration
- Microsoft Defender for Cloud
- Azure Monitor / Application Insights
- Microsoft Sentinel-related DCR onboarding

A few important caveats
- I do not see strong evidence here of Azure Firewall, DDoS Protection, NSGs, WAF, or Private Endpoints being core patterns in the snippets returned.
- Some search results may be incomplete because code search tool responses are limited to 10 results.
- To inspect more code search hits in GitHub UI, use:
  - https://github.com/search?q=repo%3Avolvo-cars%2Fazure-core-infrastructure+%28%22Key+Vault%22+OR+%22Defender%22+OR+%22Sentinel%22+OR+%22Private+Endpoint%22+OR+%22Private+DNS%22+OR+%22Managed+Identity%22+OR+%22Azure+Firewall%22+OR+%22WAF%22+OR+%22DDoS%22+OR+%22NSG%22+OR+%22RBAC%22+OR+%22Policy%22+OR+%22Monitor%22+OR+%22Log+Analytics%22+OR+%22Security+Center%22%29&type=code

Key supporting snippets:

```hcl name=gov/L2 - Offerings/ai-lz-project-online/template/main.tf url=https://github.com/volvo-cars/azure-core-infrastructure/blob/2c7acfb06a51ee4b2ebbf26c8d330cc2e985c090/gov/L2%20-%20Offerings/ai-lz-project-online/template/main.tf#L28-L40
resource "azurerm_key_vault" "kv" {
  name                      = "kv-${local.name_prefix}"
  location                  = azurerm_resource_group.rg.location
  resource_group_name       = azurerm_resource_group.rg.name
  tenant_id                 = data.azurerm_client_config.current.tenant_id
  sku_name                  = "standard"
  purge_protection_enabled  = false
  enable_rbac_authorization = true
  network_acls {
    default_action = "Deny"
    bypass         = "AzureServices"
  }
}
```

```hcl name=gov/L2 - Offerings/ai-lz-project-online/591490fd-0db8-48e9-b5cc-79f20a2ab552/main.tf url=https://github.com/volvo-cars/azure-core-infrastructure/blob/2c7acfb06a51ee4b2ebbf26c8d330cc2e985c090/gov/L2%20-%20Offerings/ai-lz-project-online/591490fd-0db8-48e9-b5cc-79f20a2ab552/main.tf#L9-L24
resource "azurerm_storage_account" "sa" {
  name                            = "saailz${local.name_prefix}"
  location                        = azurerm_resource_group.rg.location
  resource_group_name             = azurerm_resource_group.rg.name
  account_tier                    = "Standard"
  account_replication_type        = "LRS"
  https_traffic_only_enabled      = true
  min_tls_version                 = "TLS1_2"
  default_to_oauth_authentication = true
  shared_access_key_enabled       = false
  allow_nested_items_to_be_public = false
  network_rules {
    default_action = "Deny"
    bypass         = ["AzureServices"]
  }
}
```

```hcl name=DR/Layer2/glo_deny_rbac.tf_ url=https://github.com/volvo-cars/azure-core-infrastructure/blob/2c7acfb06a51ee4b2ebbf26c8d330cc2e985c090/DR/Layer2/glo_deny_rbac.tf_#L1-L20
locals
glo_deny_rbac = {
    glo_deny_custom_role_creation       = "Custom role creations are not allowed"
    glo_deny_privileged_role_assignment = "Privileged role assignments are not allowed"
  }

  glo_deny_rbac_non_compliance_messages = {
    GloDenyCustomRoleCreation       = "Deny custom role definition creation"
    GloDenyPrivilegedRoleAssignment = "Deny some privileged role assignments"
  }
```

```markdown name=docs/internal/TS09-Provision-ServiceProvider-Identity.md url=https://github.com/volvo-cars/azure-core-infrastructure/blob/2c7acfb06a51ee4b2ebbf26c8d330cc2e985c090/docs/internal/TS09-Provision-ServiceProvider-Identity.md#L111-L125
| Decision | Choice | Rationale |
|----------|--------|-----------|
| Runtime | Azure Functions (Python) | Lightweight, event-driven, managed scaling |
| Auth | Easy Auth + azp validation | No custom token validation code; double-layer trust |
| Config | JSON file bundled in zip | Simple, auditable, no external dependency at runtime |
| Region | Resolved from subscription tag | Supports multi-region without config changes per provider |
| Resource lock | `CanNotDelete` via ARM REST API | `azure-mgmt-resource.locks` not available in runtime; REST call is sufficient |
| Lock permission | Custom role `28bf596f-...` | `Contributor` excludes `Microsoft.Authorization/locks/*` by design |
| CI/CD auth | OIDC / Workload Identity Federation | No secrets stored in GitHub |
| Branch testing | Deployment slots | Isolated endpoint, same infra, zero-downtime swap to production |
```

If you want, I can do a second pass and turn this into a clean table with:
- service
- where used in repo
- exact Terraform/resource evidence
- security purpose
- maturity/risk observation.