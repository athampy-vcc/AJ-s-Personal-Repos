Yes — based on this repo, the strongest Azure delivery plan is to **make Microsoft Defender for Cloud the management-group level control plane**, then **phase out Prisma onboarding and access**, with separate handling for **existing subscriptions** and **new subscriptions coming from the vending machine**.

## Executive recommendation

For **Azure only**, the plan should be:

- **Keep / strengthen Defender for Cloud at management-group scope**
- **Stop onboarding subscriptions to Prisma**
- **Migrate existing subscriptions in waves to Defender for Cloud plans, policies, contacts, and export settings**
- **Update the vending-machine path so every new subscription is born compliant with Defender for Cloud**
- **After validation, remove Prisma Terraform, secrets, and RBAC**

The repo already shows that:
1. **Prisma onboarding is actively managed by Terraform** under `code/L2/cnapp-azure`
2. **Defender for Cloud is already present as an Azure Policy initiative** at management-group level

So this is not a greenfield design — it is a **controlled replacement / standardization exercise**.

## What I found in the repo

### 1) Prisma CNAPP is explicitly onboarded from this repo
These modules onboard Azure subscriptions into Prisma:

```hcl name=code/L2/cnapp-azure/L1-Legacy/main.tf url=https://github.com/volvo-cars/azure-global-enterprise-scale/blob/dc989a91245e354e06fbbf52f789ca989c417824/code/L2/cnapp-azure/L1-Legacy/main.tf#L4-L71
azure_spn  = "cnappspn"
cnapp_keys = "cnapp"

prisma_features = {
  disable_on_destroy  = true
  monitor_flow_logs   = true
  agentless_scanning  = "enabled"
  remediation         = "disabled"
  serverless_scanning = "enabled"
  auto_protect        = "enabled"
}

resource "prismacloud_cloud_account_v2" "azure_account_onboarding" {
  for_each           = local.subscriptions
  disable_on_destroy = local.prisma_features["disable_on_destroy"]
  azure {
    client_id  = jsondecode(data.azurerm_key_vault_secret.cnapp["azure_spn"].value)["App ID"]
    account_id = each.key
    enabled    = true
```

```hcl name=code/L2/cnapp-azure/L1-Brownfield/main.tf url=https://github.com/volvo-cars/azure-global-enterprise-scale/blob/dc989a91245e354e06fbbf52f789ca989c417824/code/L2/cnapp-azure/L1-Brownfield/main.tf#L1-L68
resource "prismacloud_cloud_account_v2" "azure_account_onboarding_brownfield" {
  for_each           = local.brownfield_subscriptions
  disable_on_destroy = local.prisma_features["disable_on_destroy"]
  azure {
    client_id  = jsondecode(data.azurerm_key_vault_secret.cnapp["azure_spn"].value)["App ID"]
    account_id = each.key
    enabled    = true
```

Also, Prisma credentials are stored in Key Vault-backed Terraform inputs:

```hcl name=code/L2/cnapp-azure/L0-Foundation/variables.tf url=https://github.com/volvo-cars/azure-global-enterprise-scale/blob/dc989a91245e354e06fbbf52f789ca989c417824/code/L2/cnapp-azure/L0-Foundation/variables.tf#L1-L11
variable "cnapp_keys" {
  type        = string
  description = "CNAPP keys"
  sensitive   = true
}

variable "cnapp_spn" {
  type        = string
  description = "CNAPP SPN details"
  sensitive   = true
}
```

And the provider uses Prisma directly:

```hcl name=code/L2/cnapp-azure/L1-Platform/provider.tf url=https://github.com/volvo-cars/azure-global-enterprise-scale/blob/dc989a91245e354e06fbbf52f789ca989c417824/code/L2/cnapp-azure/L1-Platform/provider.tf#L1-L37
provider "prismacloud" {
  username = jsondecode(data.azurerm_key_vault_secret.cnapp["cnapp_keys"].value)["Access Key ID"]
  password = jsondecode(data.azurerm_key_vault_secret.cnapp["cnapp_keys"].value)["Secret Key"]
  url      = "api.eu.prismacloud.io"
}
```

### 2) Defender for Cloud already exists in the target operating model
This repo already assigns a management-group level initiative for MDFC:

```json name=root/volvocars (volvocars)/microsoft.authorization_policyassignments-deploy-mdfc-config.json url=https://github.com/volvo-cars/azure-global-enterprise-scale/blob/dc989a91245e354e06fbbf52f789ca989c417824/root/volvocars%20(volvocars)/microsoft.authorization_policyassignments-deploy-mdfc-config.json#L1-L78
{
  "name": "Deploy-MDFC-Config",
  "properties": {
    "Scope": "/providers/Microsoft.Management/managementGroups/VolvoCars",
    "DisplayName": "Deploy Microsoft Defender for Cloud configuration",
    "Description": "Deploy Microsoft Defender for Cloud and Security Contacts and Auto Deploy MMA agent",
```

The initiative parameters show current baseline choices:
- security contact email
- central Log Analytics workspace
- export resource group
- selected plans enabled/disabled

```json name=root/volvocars (volvocars)/microsoft.authorization_policyassignments-deploy-mdfc-config.json url=https://github.com/volvo-cars/azure-global-enterprise-scale/blob/dc989a91245e354e06fbbf52f789ca989c417824/root/volvocars%20(volvocars)/microsoft.authorization_policyassignments-deploy-mdfc-config.json#L20-L73
"Parameters": {
  "emailSecurityContact": {
    "value": "cld-DL-AzureEA@grp.volvocars.com"
  },
  "logAnalytics": {
    "value": "/subscriptions/802310bb-d53d-4624-a7d0-99dfca2a613a/resourceGroups/VolvoCars-mgmt/providers/Microsoft.OperationalInsights/workspaces/VolvoCars-law"
  },
  "ascExportResourceGroupName": {
    "value": "VolvoCars-asc-export"
  },
  "ascExportResourceGroupLocation": {
    "value": "westeurope"
  },
  "enableAscForServers": {
    "value": "DeployIfNotExists"
  },
  "enableAscForSql": {
    "value": "Disabled"
  },
  ...
}
```

The repo documentation also confirms this initiative is part of the intended assignment structure:

```markdown name=docs/Azure_Custom_Policies_Refactor.md url=https://github.com/volvo-cars/azure-global-enterprise-scale/blob/dc989a91245e354e06fbbf52f789ca989c417824/docs/Azure_Custom_Policies_Refactor.md#L63-L74
| Deploy Microsoft Defender for Cloud configuration | VolvoCars | Deploy Microsoft Defender for Cloud configuration | VolvoCars | VolvoCars |
```

### 3) Prisma has MG-level RBAC today
This means decommissioning Prisma is not only a tooling change, but also an identity/RBAC cleanup:

```markdown name=code/L0/role_assignments/role_assignments.md url=https://github.com/volvo-cars/azure-global-enterprise-scale/blob/dc989a91245e354e06fbbf52f789ca989c417824/code/L0/role_assignments/role_assignments.md#L1-L6
| /providers/Microsoft.Management/managementGroups/81fa766e-a349-4867-8bf4-ab35e250a08f | spn-cnapp-azure-integration | CNAPP_Reader | SPN | This SPN is used by Prisma Cloud CNAPP to read Azure resources  |
```

---

## Recommended Azure delivery plan

# 1. Delivery plan for existing subscriptions

## Objective
Move already-onboarded Azure subscriptions from **Prisma-managed CNAPP/CSPM** to **Defender for Cloud as the only Azure-native CSPM/CNAPP control plane**.

## Phase 1 — Assess and freeze
**Goal:** stop the estate from drifting while migration is planned.

### Activities
- Freeze onboarding of any additional Azure subscriptions into Prisma
- Inventory all Azure subscriptions currently covered by:
  - `L1-Legacy`
  - `L1-Brownfield`
  - `L1-Greenfield`
  - `L1-Platform`
- Map each subscription to:
  - management group
  - environment (`prod` / `nonprod`)
  - operating model
  - business owner / `owner-appid`
- Identify which Prisma capabilities are actually being consumed today:
  - CSPM findings
  - agentless scanning
  - serverless scanning
  - flow log monitoring
  - any alerting / SIEM forwarding / ticketing integrations

### Why this matters in this repo
The current onboarding logic is segmented by lifecycle type and tags, so migration should preserve those same cohorts and not be done as one big bang.

---

## Phase 2 — Define Defender target state
**Goal:** establish the Azure baseline that will replace Prisma.

### Defender target state should include
- **Management group level assignment** of Defender configuration
- **Security contacts** centrally configured
- **Defender plans** enabled by approved workload type
- **Recommendations / regulatory / secure score ownership model**
- **Export path** for findings to central workspace / downstream SOC tooling
- **Policy remediation path** for subscriptions that miss configuration

### Based on repo, the target state already partially exists
Current initiative:
- Scope: `VolvoCars` management group
- Security contact configured
- Log Analytics workspace defined
- Export resource group defined
- Some Defender plans enabled/disabled centrally

### Recommendation
Before migration, review and update the initiative so it reflects the **future standard**, not legacy defaults.  
The current description references **auto deploy MMA agent**, which is dated and should be modernized as part of the transition narrative.

---

## Phase 3 — Pilot migration for existing subscriptions
**Goal:** validate operationally before broad rollout.

### Suggested pilot groups
Run in this order:
1. **Non-prod greenfield / corp or online**
2. **Brownfield self-managed subscriptions**
3. **Legacy shared / unknown architecture**
4. **Prod dedicated subscriptions**

### What to do in pilot
For each pilot wave:
- Ensure Defender initiative is inherited and compliant
- Confirm subscriptions show expected Defender plans and recommendations
- Validate security contacts and export settings
- Validate alert routing / SOC visibility
- Compare Prisma vs Defender findings for 2–4 weeks
- Record known control gaps:
  - controls Prisma had but Defender may not match 1:1
  - workload areas requiring extra Azure-native services beyond Defender

### Deliverable from pilot
A signed-off **control mapping**:
- Prisma capability
- Defender equivalent
- gap / partial / exact replacement
- mitigation if gap exists

This is crucial because “replace Prisma completely” is a **business and control coverage decision**, not only a Terraform change.

---

## Phase 4 — Broad migration for existing subscriptions
**Goal:** move all existing subscriptions to Defender as primary and then sole platform.

### Execution sequence
#### Wave A — Enable and validate Defender baseline everywhere
- Confirm MG-level assignment reaches all in-scope subscriptions
- Remediate noncompliant subscriptions
- Standardize Log Analytics / export / contacts
- Confirm owners and security ops have access and operational procedures

#### Wave B — Stop Prisma as operational source of truth
- Disable new alerting / integrations that depend on Prisma being primary
- Move dashboards / reporting to Defender / Sentinel / Log Analytics based reporting
- Agree cutover date with SOC / cloud security / platform ops

#### Wave C — Remove Prisma onboarding from code
Remove or retire:
- `code/L2/cnapp-azure/L1-Legacy`
- `code/L2/cnapp-azure/L1-Brownfield`
- `code/L2/cnapp-azure/L1-Platform`
- any `prismacloud_*` resources
- Prisma provider blocks
- Key Vault secrets for Prisma credentials
- related Terraform state usage and pipelines

#### Wave D — Remove Prisma access in Azure
- remove `spn-cnapp-azure-integration`
- remove `CNAPP_Reader` assignment
- remove any app registrations / enterprise apps no longer needed
- remove secret rotation / vault entries tied to Prisma

---

## Existing subscriptions: recommended delivery plan summary

### Workstream 1 — Governance
- Approve Defender as strategic Azure CSPM/CNAPP
- Define minimum Defender plans to enable
- Approve risk treatment for any non-1:1 Prisma gaps

### Workstream 2 — Technical baseline
- Update MG-level Defender initiative
- Validate policy remediation identity and permissions
- Standardize export and monitoring destinations

### Workstream 3 — Migration
- Pilot in non-prod
- Expand by cohort: brownfield, legacy, prod
- Run Prisma and Defender in parallel temporarily
- Exit Prisma only after control sign-off

### Workstream 4 — Decommission
- Remove Terraform modules and providers
- Remove secrets and service principal access
- Update operational documentation and support model

---

# 2. Delivery plan for new subscriptions via vending machine

## Objective
Ensure every newly vended Azure subscription is **Defender-first on day 0**, with **no Prisma onboarding at all**.

## What the repo suggests
The repo uses discovery / classification patterns for newly onboarded or grouped subscriptions, for example `greenfield.py` classifies subscriptions under landing zones:

```python name=code/L2/cnapp-azure/L1-Greenfield/greenfield.py url=https://github.com/volvo-cars/azure-global-enterprise-scale/blob/dc989a91245e354e06fbbf52f789ca989c417824/code/L2/cnapp-azure/L1-Greenfield/greenfield.py#L8-L18
query = f"""
 resourcecontainers
    | where type == 'microsoft.resources/subscriptions'
    | where properties.managementGroupAncestorsChain contains 'VolvoCars-landingzones' 
    | where properties.state == 'Enabled'
```

That means the intended model is already **management group inheritance + subscription discovery**.  
For the vending machine, the best delivery model is therefore **policy-driven inheritance**, not per-subscription Prisma onboarding.

## Target future-state for vending machine
Every new subscription should, immediately after creation:
- land in the correct management group
- inherit the Defender for Cloud initiative
- inherit security contacts
- inherit required policy assignments
- have export / monitoring settings deployed automatically
- be excluded from any Prisma onboarding pipeline

## Recommended vending-machine flow

### Step 1 — Management group placement is mandatory
The vending machine must place the subscription into the correct MG at creation time.  
This is the most important dependency because the repo’s security model is MG-centric.

### Step 2 — Defender policy assignment must be inherited automatically
The current `Deploy-MDFC-Config` assignment at `VolvoCars` MG should become the standard onboarding control for all newly vended subscriptions.

### Step 3 — Post-provisioning validation gate
Add a validation checkpoint in the vending-machine workflow:
- subscription is under the correct MG
- Defender plans applied
- security contact exists
- export configuration exists
- no exemption granted unless approved

### Step 4 — Remove Prisma from the vending path
Any automation that currently:
- inventories new subscriptions for Prisma
- pushes them into Prisma account groups
- assigns Prisma integration identities
must be retired.

### Step 5 — Operational acceptance
Before handing the subscription to the app/team:
- Defender compliance baseline must be green or within approved exceptions
- security operations visibility must be confirmed

---

## New subscriptions: recommended operating model

### Day-0 controls
- MG inheritance
- Defender initiative assignment
- security contact
- data export
- baseline compliance checks

### Day-1/2 controls
- workload-specific Defender plans if approved
- remediation of missing agents/extensions where required
- exceptions governance

### What should not happen anymore
- no Prisma SPN assignment
- no Prisma account onboarding
- no Prisma credential dependency
- no parallel CNAPP onboarding workflow for Azure

---

# Recommended delivery plan you can present in the QC call

## Option to present as a 2-track plan

### Track A — Existing Azure subscriptions
1. **Freeze Prisma expansion**
2. **Assess current Prisma-covered subscriptions**
3. **Confirm Defender target baseline at VolvoCars MG**
4. **Run pilot in non-prod**
5. **Run Prisma + Defender in parallel for validation**
6. **Migrate existing subscriptions in waves**
7. **Move reporting/operations to Defender**
8. **Decommission Prisma code, secrets, and RBAC**

### Track B — New Azure subscriptions via vending machine
1. **Make MG placement mandatory**
2. **Apply Defender by inheritance at subscription creation**
3. **Add post-vending compliance validation**
4. **Block any Prisma onboarding**
5. **Hand over only after Defender baseline is active**

---

## Suggested timeline

### Phase 0: 1–2 weeks
- inventory
- control mapping
- ownership agreement
- freeze Prisma onboarding

### Phase 1: 2–4 weeks
- tune Defender initiative and parameters
- validate export / monitoring
- define pilot subscriptions

### Phase 2: 2–6 weeks
- pilot migration
- parallel run and compare findings
- close technical gaps

### Phase 3: 4–8 weeks
- broad rollout to existing subscriptions by wave
- switch operational reporting to Defender

### Phase 4: 1–2 weeks
- Prisma decommission in Azure
- remove SPN, secrets, Terraform, and runbooks

---

## Key risks to call out

- **Control parity risk:** Defender may not exactly mirror every Prisma feature
- **Operational change risk:** SOC/reporting teams may still rely on Prisma dashboards or workflows
- **Legacy config risk:** the current Defender initiative appears older in places and may need modernization
- **Scope drift risk:** if vending machine allows subscriptions outside governed MGs, inheritance breaks
- **Decommission risk:** removing Prisma access too early could create temporary visibility gaps

---

## Concrete repo-based recommendations

1. **Treat `Deploy-MDFC-Config` as the strategic Azure baseline**
   - currently assigned at `VolvoCars` MG  
   - source: `microsoft.authorization_policyassignments-deploy-mdfc-config.json`

2. **Plan retirement of `code/L2/cnapp-azure/*` Prisma onboarding modules**
   - especially `L1-Legacy`, `L1-Brownfield`, `L1-Platform`

3. **Remove Prisma secrets and provider dependency**
   - `cnapp_keys`
   - `cnapp_spn`
   - `prismacloud` provider

4. **Remove Prisma RBAC**
   - `spn-cnapp-azure-integration`
   - `CNAPP_Reader`

5. **Make vending machine rely on MG inheritance, not tool-specific onboarding**
   - consistent with how subscriptions are already classified/discovered in the repo

---

## Important note on repo review completeness
The code search results are **limited and may be incomplete**.  
To inspect more matches directly in GitHub UI, use:

- Prisma search:  
  https://github.com/search?q=repo%3Avolvo-cars%2Fazure-global-enterprise-scale+%28%22Prisma%22+OR+%22Defender+for+Cloud%22+OR+%22Microsoft+Defender+for+Cloud%22+OR+%22CNAPP%22+OR+%22CSPM%22%29&type=code
- Defender search:  
  https://github.com/search?q=repo%3Avolvo-cars%2Fazure-global-enterprise-scale+%28%22Deploy+Microsoft+Defender+for+Cloud+configuration%22+OR+%22Microsoft.Security%22+OR+%22defender+for+cloud%22+OR+%22securitycenter%22%29&type=code

If you want, I can turn this into a **1-page executive delivery plan** or a **QC meeting slide-ready version** with:
- scope
- current state
- target state
- phased plan
- risks / dependencies / decisions needed.