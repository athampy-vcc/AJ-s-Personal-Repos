Possible Sprint QC features for this EPIC in `volvo-cars/azure-core-infrastructure` are:

### Best candidates for Sprint QC
1. **Enable CNAPP remediation as a controlled rollout**
   - The CNAPP Terraform for Greenfield/Brownfield/Legacy/Platform already defines remediation as a feature, but it is currently disabled in locals.
   - This makes a good sprint-sized feature if scoped to one environment first.
   - Evidence:
     - `gov/L2 - Offerings/cnapp-azure/L1-Greenfield/main.tf`
     - `gov/L2 - Offerings/cnapp-azure/L1-Brownfield/main.tf`
     - `gov/L2 - Offerings/cnapp-azure/L1-Legacy/main.tf`
     - `gov/L2 - Offerings/cnapp-azure/L1-Platform/main.tf`

```hcl name=gov/L2 - Offerings/cnapp-azure/L1-Greenfield/main.tf url=https://github.com/volvo-cars/azure-core-infrastructure/blob/fd17be3bcf5202a24e51eb3736bf404fd095c8ba/gov/L2%20-%20Offerings/cnapp-azure/L1-Greenfield/main.tf#L1-L15
locals {
  subscriptions = yamldecode(file("${path.module}/../../../configs/greenfield_inventory.yaml"))
  kv_secret_names = {
    azure_spn  = "cnappspn"
    cnapp_keys = "cnapp"
  }
  prisma_features = {
    disable_on_destroy  = true
    monitor_flow_logs   = false
    agentless_scanning  = "enabled"
    remediation         = "disabled"
    serverless_scanning = "enabled"
    auto_protect        = "enabled"
```

   **Why it fits the EPIC:** supports governance alignment for legacy/brownfield migration and standard guardrails.
   **Suggested sprint scope:** enable for Greenfield first, then Brownfield.

2. **Turn on CNAPP flow-log monitoring**
   - `monitor_flow_logs` is explicitly set to `false` in the same CNAPP onboarding stacks.
   - This is a clear feature toggle already modeled in code.
   - Good candidate if security visibility is part of QC.
   - Evidence in same files above.

3. **Expand auto-remediation from P1 into more governance controls**
   - There is already an active auto-remediation platform with Azure Functions, queue-based processing, workbook visibility, audit logging, and multiple managed identities.
   - The docs explicitly say P2/P3 flows are “prepared for later,” which suggests roadmap-ready work.
   - Evidence:
     - `utilities/auto-remediation-solution/README.md`
     - `utilities/auto-remediation-solution/docs/ARCHITECTURE_FLOW.md`

````markdown name=utilities/auto-remediation-solution/docs/ARCHITECTURE_FLOW.md url=https://github.com/volvo-cars/azure-core-infrastructure/blob/b159f7fc07292168849703d979ae4abaa1750035/utilities/auto-remediation-solution/docs/ARCHITECTURE_FLOW.md#L8-L17
Plain summary:
- Find drift.
- Make a work item.
- Run a fix.
- Check the result.
- Log the run.
- Alert on fails.
- Keep P1 safe.
- Gate P3.
````

   **Feature ideas under this theme:**
   - P2 delayed remediation flow
   - P3 approval-based remediation flow via Teams
   - additional remediation packs for governance drift
   - standardized dead-letter retry/reporting

4. **Productize Key Vault soft-delete auto-remediation**
   - The IAM for a dedicated Key Vault remediation identity already exists, including `Key Vault Contributor` and reader scopes.
   - Workbook queries also already reference `kv_enable_soft_delete_batch_complete`.
   - This suggests an in-progress or partially implemented feature that could be completed in Sprint QC.
   - Evidence:
     - `utilities/auto-remediation-solution/terraform-online/iam.tf`
     - `utilities/auto-remediation-solution/terraform-online/workbook.tf`

```hcl name=utilities/auto-remediation-solution/terraform-online/iam.tf url=https://github.com/volvo-cars/azure-core-infrastructure/blob/b159f7fc07292168849703d979ae4abaa1750035/utilities/auto-remediation-solution/terraform-online/iam.tf#L41-L61
resource "azurerm_role_assignment" "kv_enable_soft_delete_remediate_reader_subscription" {
  for_each             = var.enable_user_assigned_identities ? toset(local.kv_enable_soft_delete_remediate_scope) : toset([])
  scope                = "/subscriptions/${each.value}"
  role_definition_name = "Reader"
  principal_id         = azurerm_user_assigned_identity.kv_enable_soft_delete_remediate[0].principal_id
}

resource "azurerm_role_assignment" "kv_enable_soft_delete_remediate_kv_contributor_subscription" {
  for_each             = var.enable_user_assigned_identities ? toset(local.kv_enable_soft_delete_remediate_scope) : toset([])
  scope                = "/subscriptions/${each.value}"
  role_definition_name = "Key Vault Contributor"
  principal_id         = azurerm_user_assigned_identity.kv_enable_soft_delete_remediate[0].principal_id
}
```

   **Why it fits:** directly improves compliance posture in brownfield/legacy subscriptions.

5. **Finish DCR onboarding as a reusable governance service**
   - DCR onboarding already has:
     - function flow
     - runbook
     - identity model
     - Terraform-managed RBAC
   - A good Sprint QC feature would be to make onboarding fully self-service for selected subscription groups / management groups.
   - Evidence:
     - `utilities/auto-remediation-solution/docs/DCR_ONBOARDING_RUNBOOK.md`
     - `utilities/auto-remediation-solution/docs/IDENTITY.md`

6. **Build the EPIC dashboard / real-time reporting asked for in the EPIC**
   - The EPIC explicitly asks for real-time reporting and tracking.
   - This repo already contains:
     - reporting functions
     - workbooks
     - regional reporting workflow
     - CNAPP coverage reporting pipeline
   - So a sprint feature could be to unify these into one dashboard for:
     - migration progress
     - governance compliance
     - remediation status
     - exception status
   - Evidence:
     - `gov/L2 - Offerings/reporting-functions/infra/main.tf`
     - `utilities/auto-remediation-solution/terraform-online/workbook.tf`
     - `README.md`
     - `.github/workflows/region-reporting.yml`

7. **Improve brownfield/legacy inventory classification**
   - The EPIC’s first deliverable is inventory and classification.
   - The repo already has scheduled generation of:
     - legacy inventory
     - brownfield inventory
     - docs output
   - A strong feature is adding richer metadata and classification tags such as:
     - migration candidate
     - decommission candidate
     - compliance owner
     - exception status
     - criticality
   - Evidence:
     - `.github/workflows/cnapp-brownfield.yml`
     - `.github/workflows/cnapp-legacy.yml`
     - `README.md`

8. **Automate owner/governance assignment workflows**
   - There are already workflows for:
     - updating SG object IDs
     - PIM deployment
     - Defender governance rule creation
   - These can be extended into a more complete owner onboarding/governance package for newly discovered brownfield subscriptions.
   - Evidence:
     - `.github/workflows/azure-prisma-sg-owner.yml`
     - `.github/workflows/pim-gf.yml`
     - `.github/workflows/governance-rule.yml`

### Recommended prioritization for Sprint QC
If you want the most realistic sprint-sized features, I’d prioritize these 4:

1. **CNAPP remediation rollout for one environment**
2. **Key Vault soft-delete remediation completion**
3. **Unified compliance/remediation workbook dashboard**
4. **Enhanced brownfield/legacy inventory classification**

### Why these are strongest
- They align directly with the EPIC deliverables.
- They build on code that already exists, so delivery risk is lower.
- They are visible, demonstrable sprint outcomes.
- They support migration/decommission decisions and governance alignment.

### Suggested feature backlog statements
You could phrase them like this:

- **Feature 1:** Enable Prisma remediation for Greenfield subscriptions with guarded rollout and validation.
- **Feature 2:** Implement Key Vault soft-delete detection and remediation across selected brownfield scopes.
- **Feature 3:** Create a unified workbook/dashboard for compliance, remediation, and migration progress reporting.
- **Feature 4:** Enhance legacy/brownfield inventory generation with migration/decommission classification metadata.
- **Feature 5:** Extend auto-remediation platform with approval-gated P2/P3 remediation workflow.
- **Feature 6:** Operationalize DCR onboarding as a standardized service for selected management groups.

### Important note
The code search results are limited and may be incomplete. You can view more workflow/code matches in GitHub UI here:
- Workflows: https://github.com/volvo-cars/azure-core-infrastructure/search?q=path%3A.github%2Fworkflows&type=code
- Auto-remediation: https://github.com/volvo-cars/azure-core-infrastructure/search?q=path%3Autilities%2Fauto-remediation-solution&type=code

Also, the EPIC link you shared appears to point to `volvo-cars/public-cloud-service` item `93`, while the repo under review is `volvo-cars/azure-core-infrastructure`. I only found one likely related open EPIC in `public-cloud-service`:

```list type="issue"
data:
- url: "https://github.com/volvo-cars/public-cloud-service/issues/74"
  repository: "volvo-cars/public-cloud-service"
  state: "open"
  draft: false
  title: "🏔️ [EPIC] - Reduce Legacy and Brownfield footprint to Zero"
  number: 74
  created_at: "2026-03-12T09:13:16Z"
  closed_at: ""
  merged_at: ""
  labels:
  - "Cloud Audit"
  - "Infosys-team"
  author: "psharma0712"
  comments: 0
  assignees_avatar_urls:
  - "https://avatars.githubusercontent.com/u/81426363?v=4"
```

If you want, I can do a second pass and turn this into a **Sprint QC proposal table** with:
- feature
- business value
- implementation area
- dependency
- effort estimate
- recommendation level.