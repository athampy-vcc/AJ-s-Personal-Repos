I’m going to scan all five Volvo Azure platform repositories for concrete implementation files where Azure Policy is defined or assigned, then I’ll return direct GitHub reference links.

Searched volvo-cars/azure-global-enterprise-scale for "azurerm_policy_assignment OR policyDefinitionId OR policy_set_definition OR policy_assignment language:HCL", no results

Searched volvo-cars/azure-global-lz-vending-machine for "azurerm_policy_assignment OR policyDefinitionId OR policy_set_definition OR policy_assignment language:HCL", no results

Searched volvo-cars/azure-core-infrastructure for "azurerm_policy_assignment OR policyDefinitionId OR policy_set_definition OR policy_assignment language:HCL", no results

Searched volvo-cars/Cloud-and-Infrastructure-Core for "azurerm_policy_assignment OR policyDefinitionId OR policy_set_definition OR policy_assignment language:HCL", no results

Searched volvo-cars/backstage-systems for "azurerm_policy_assignment OR policyDefinitionId OR policy_set_definition OR policy_assignment language:HCL", no results

Initial keyword syntax returned no hits, so next I’m running broader per-repo searches to capture both Terraform and documentation references to Azure Policy.

Searched volvo-cars/azure-global-enterprise-scale for "Azure Policy", 100 results

Searched volvo-cars/azure-global-lz-vending-machine for "Azure Policy", 19 results

Searched volvo-cars/azure-core-infrastructure for "Azure Policy", 100 results

Searched volvo-cars/Cloud-and-Infrastructure-Core for "Azure Policy", 32 results

Searching 'volvo-cars/backstage-systems' for 'Azure Policy'

I now have strong hits in three repos; next I’m narrowing to exact policy resource definitions and assignment files so I can give precise, implementation-grade links.

Searching 'volvo-cars/azure-global-enterprise-scale' for 'resource "azurerm_management_group_policy_assignment" language:HCL'

Searching 'volvo-cars/azure-global-enterprise-scale' for 'source = "./modules/alz_policy_assginment" language:HCL'

Searching 'volvo-cars/azure-core-infrastructure' for 'resource "azurerm_management_group_policy_assignment" language:HCL'

Searching 'volvo-cars/azure-core-infrastructure' for 'source = "./modules/alz_policy_assginment" language:HCL'

Searching 'volvo-cars/azure-global-lz-vending-machine' for 'resource "azurerm_subscription_policy_assignment" language:HCL'

The lexical search endpoint is strict on query syntax, so I’m retrying with simpler tokens to extract the same evidence reliably.

Searching 'volvo-cars/azure-global-enterprise-scale' for 'azurerm_management_group_policy_assignment'

Searching 'volvo-cars/azure-global-enterprise-scale' for 'alz_policy_assginment'

Searching 'volvo-cars/azure-core-infrastructure' for 'azurerm_management_group_policy_assignment'

Searching 'volvo-cars/azure-core-infrastructure' for 'alz_policy_assginment'

Searching 'volvo-cars/azure-global-lz-vending-machine' for 'azurerm_subscription_policy_assignment'

Search API started returning failures, so I’m switching to semantic repo search to finish coverage, especially for the remaining repositories.

Searched volvo-cars/backstage-systems for "Azure Policy Terraform policy assignment management group subscription", 64 results

Searched volvo-cars/cloud-and-infrastructure-core for "Azure Policy assignment Terraform implementation", 64 results

Searched volvo-cars/azure-global-lz-vending-machine for "azurerm_subscription_policy_assignment network.tf Variables.tf", 64 results

Searched volvo-cars/azure-core-infrastructure for "DR/Layer2 Azure Policy definitions initiatives assignments terraform modules", 64 results

Searched volvo-cars/azure-global-enterprise-scale for "code/L1 policies assignment terraform alz_policy_assginment", 64 results

Azure Policy configuration locations with reference links are below, grouped across all 5 repositories.

Executive answer: policy-as-code implementation is primarily in azure-global-enterprise-scale, azure-core-infrastructure, and azure-global-lz-vending-machine. Cloud-and-Infrastructure-Core is mostly architecture/process documentation, and backstage-systems does not show central Azure Policy configuration code.

1. volvo-cars/azure-global-enterprise-scale
- Core policy definition resource:
  https://github.com/volvo-cars/azure-global-enterprise-scale/tree/main/code/L1/modules/definition/main.tf
- Core initiative resource:
  https://github.com/volvo-cars/azure-global-enterprise-scale/tree/main/code/L1/modules/initiative/main.tf
- Core assignment resource logic (MG/Sub/RG/Resource assignment + remediation):
  https://github.com/volvo-cars/azure-global-enterprise-scale/tree/main/code/L1/modules/initiative_assignment/main.tf
- ALZ wrapper that wires definition + initiative + assignment:
  https://github.com/volvo-cars/azure-global-enterprise-scale/tree/main/code/L1/modules/alz_policy_assginment/main.tf
- Example active policy set assignment module usage:
  https://github.com/volvo-cars/azure-global-enterprise-scale/tree/main/code/L1/vccalz_deny_public_network_access.tf
- Inventory of Terraform-managed policy assignments:
  https://github.com/volvo-cars/azure-global-enterprise-scale/tree/main/code/L1/deployed_policies_landing_zone.md
- Policy governance guide:
  https://github.com/volvo-cars/azure-global-enterprise-scale/tree/main/docs/Azure-Policy-Setup.md

2. volvo-cars/azure-core-infrastructure
- Core policy definition resource:
  https://github.com/volvo-cars/azure-core-infrastructure/tree/main/DR/Layer2/modules/definition/main.tf
- Core initiative resource:
  https://github.com/volvo-cars/azure-core-infrastructure/tree/main/DR/Layer2/modules/initiative/main.tf
- Core assignment resource logic:
  https://github.com/volvo-cars/azure-core-infrastructure/tree/main/DR/Layer2/modules/initiative_assignment/main.tf
- ALZ wrapper orchestration:
  https://github.com/volvo-cars/azure-core-infrastructure/tree/main/DR/Layer2/modules/alz_policy_assginment/main.tf
- Example assignment module usage:
  https://github.com/volvo-cars/azure-core-infrastructure/tree/main/DR/Layer2/vccalz_glo_deny_rbac.tf
- Layer 2 scope/documentation:
  https://github.com/volvo-cars/azure-core-infrastructure/tree/main/DR/Layer2/README.md

3. volvo-cars/azure-global-lz-vending-machine
- Subscription policy assignment for guardrails and allowed locations:
  https://github.com/volvo-cars/azure-global-lz-vending-machine/tree/main/terraform/deployments/network.tf
- Extranet path with subscription policy assignment:
  https://github.com/volvo-cars/azure-global-lz-vending-machine/tree/main/Extranet/deployment.tf
- Policy set definition ID wiring (input variable):
  https://github.com/volvo-cars/azure-global-lz-vending-machine/tree/main/terraform/deployments/variables.tf
- Alternate ASVM20 path with subscription policy assignment:
  https://github.com/volvo-cars/azure-global-lz-vending-machine/tree/main/src/asvm20/deployment/network.tf

4. volvo-cars/Cloud-and-Infrastructure-Core
- Evidence found is mainly design/process documentation, not central Terraform policy deployment modules:
  https://github.com/volvo-cars/Cloud-and-Infrastructure-Core/tree/main/docs/target-architecture/pce/Guardrails.md
  https://github.com/volvo-cars/Cloud-and-Infrastructure-Core/tree/main/docs/target-architecture/pce/MG%26SubsOrganization.md
- This repo describes policy intent and governance model, but implementation appears to live in the repos above.

5. volvo-cars/backstage-systems
- No clear centralized Azure Policy configuration implementation found in this repo during search.
- This repo appears to be service catalog/docs oriented rather than platform policy deployment.

Confidence and limit
- High confidence on where policy code is configured as design/code evidence.
- Not a live Azure state confirmation. These links prove repository configuration patterns, not runtime assignment success in tenant.