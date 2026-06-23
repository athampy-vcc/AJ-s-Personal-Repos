## Updated Azure Gap Analysis Content

# CIS Azure 3.0 Compliance and Azure Policy Gap Analysis  
## Volvo Environment Policy Reconciliation (All Listed Assignments)

Document Version: 2.0  
Date: 2026-06-23  
Scope: CIS Microsoft Azure Foundations Benchmark v3.0.0  
Evidence base: azure-global-enterprise-scale implementation and deployment inventory, plus supporting Terraform in azure-core-infrastructure.

## Executive Conclusion

The attached assignment list is largely valid and corresponds to active Terraform-managed policies. The biggest correction needed in the prior report is that coverage must be shown policy-by-policy for all listed assignments, including regional corp, identity, landing zone, online, and platform scopes.

From the deep check:

- Implemented and evidenced: all major assignment families in the list, including DINE DNS, DENY PNA, DENY NET GUARDRAILS, DENY DATABRICKS, AUDIT PNA, AUDIT NET GUARDRAILS, IDENTITY MG GUADRAIL, LZ SQL policies, LZ network guardrails, AKS guardrails, ENFORCE_ENCRYPT_TRANSIT, ONLINE DENY GOVERNANCE.
- Not a CIS 3.0 direct control in many cases: governance-only policies (Databricks SKU/no public IP policy family, Online deny virtual network gateway, location governance controls).
- Remaining CIS 3.0 gaps are still concentrated in Entra identity controls, full activity-log alert set completeness, and some SQL-specific controls where assignments are audit-only or enforcement mode disabled.

## Evidence-backed Assignment Reconciliation (All Listed Policies)

Legend:
- Control mapping values are CIS 3.0 references when direct, otherwise Supporting or Indirect.
- Status values: Enforced, Audit only, DeployIfNotExists, Mixed, Governance-only.

| Assignment name | Scope | Implementation status | CIS 3.0 mapping | Control intent |
|---|---|---|---|---|
| [VCCALZ]-GLO DINE DIAG | VolvoCars | DeployIfNotExists | 6.1.1.2, 6.1.1.4 (indirect for some resources) | Deploy diagnostic settings baseline |
| [VCCALZ]-GLO DENY RBAC | VolvoCars | Enforced | 5.23 | Deny privileged/custom RBAC abuse paths |
| [VCCALZ]-DEPLOY DFC CONFIG | VolvoCars | DeployIfNotExists | 3.1.3.x family in CIS 3.0 mapping context | Deploy Defender security contact configuration |
| Enable Azure Monitor for Virtual Machine Scale Sets | VolvoCars | DeployIfNotExists (platform baseline) | Supporting | VMSS observability baseline |
| Deploy Microsoft Defender for Cloud configuration | VolvoCars | DeployIfNotExists | 3.1.3.x family | Defender baseline configuration |
| [VCCALZ]-WEU DINE DNS CONFIG | VolvoCars-corp | DeployIfNotExists | Supporting (private endpoint hardening architecture) | Auto-deploy private DNS zone groups |
| [VCCALZ]-WEU DENY PNA | VolvoCars-corp | Enforced | 3.8, 5.1.5 (service-dependent) | Deny public network access for covered PaaS types |
| [VCCALZ]-WEU DENY NET GUARDRAILS | VolvoCars-corp | Enforced | 7.1, 7.2, 7.11 (plus guardrail extensions) | Deny risky network constructs |
| [VCCALZ]-WEU DENY DATABRICKS | VolvoCars-corp | Enforced | Supporting | Deny non-compliant Databricks deployment patterns |
| [VCCALZ]-WEU AUDIT PNA | VolvoCars-corp | Audit only | 3.8, 5.1.5 (detective) | Detect PNA drift |
| [VCCALZ]-WEU AUDIT NET GUARDRAILS | VolvoCars-corp | Audit only | 7.x (detective) | Detect routing/network policy drift |
| [VCCALZ]-EUS DINE DNS CONFIG | VolvoCars-corp-amer | DeployIfNotExists | Supporting | Auto-deploy private DNS zone groups |
| [VCCALZ]-EUS DENY PNA | VolvoCars-corp-amer | Enforced | 3.8, 5.1.5 (service-dependent) | Deny public network access |
| [VCCALZ]-EUS DENY NET GUARDRAILS | VolvoCars-corp-amer | Enforced | 7.1, 7.2, 7.11 | Deny risky network patterns |
| [VCCALZ]-EUS DENY GOVERNANCE | VolvoCars-corp-amer | Enforced | Supporting | Enforce allowed locations and governance boundaries |
| [VCCALZ]-EUS DENY DATABRICKS | VolvoCars-corp-amer | Enforced | Supporting | Deny non-compliant Databricks |
| [VCCALZ]-EUS AUDIT PNA | VolvoCars-corp-amer | Audit only | 3.8, 5.1.5 (detective) | Detect PNA drift |
| [VCCALZ]-EUS AUDIT NET GUARDRAILS | VolvoCars-corp-amer | Audit only | 7.x (detective) | Detect network drift |
| [VCCALZ]-SEA DINE DNS CONFIG | VolvoCars-corp-sea | DeployIfNotExists | Supporting | Auto-deploy private DNS zone groups |
| [VCCALZ]-SEA DENY PNA | VolvoCars-corp-sea | Enforced | 3.8, 5.1.5 (service-dependent) | Deny public network access |
| [VCCALZ]-SEA DENY NET GUARDRAILS | VolvoCars-corp-sea | Enforced | 7.1, 7.2, 7.11 | Deny risky network patterns |
| [VCCALZ]-SEA DENY GOVERNANCE | VolvoCars-corp-sea | Enforced | Supporting | Location/governance controls |
| [VCCALZ]-SEA DENY DATABRICKS | VolvoCars-corp-sea | Enforced | Supporting | Databricks governance hardening |
| [VCCALZ]-SEA AUDIT PNA | VolvoCars-corp-sea | Audit only | 3.8, 5.1.5 (detective) | PNA drift detection |
| [VCCALZ]-SEA AUDIT NET GUARDRAILS | VolvoCars-corp-sea | Audit only | 7.x (detective) | Network drift detection |
| [VCCALZ]-SEC DINE DNS CONFIG | VolvoCars-corp-swdn | DeployIfNotExists | Supporting | Auto-deploy private DNS zone groups |
| [VCCALZ]-SEC DENY PNA | VolvoCars-corp-swdn | Enforced | 3.8, 5.1.5 (service-dependent) | Deny public network access |
| [VCCALZ]-SEC DENY NET GUARDRAILS | VolvoCars-corp-swdn | Enforced | 7.1, 7.2, 7.11 | Deny risky network patterns |
| [VCCALZ]-SEC DENY GOVERNANCE | VolvoCars-corp-swdn | Enforced | Supporting | Governance/location controls |
| [VCCALZ]-SEC DENY DATABRICKS | VolvoCars-corp-swdn | Enforced | Supporting | Databricks hardening controls |
| [VCCALZ]-SEC AUDIT PNA | VolvoCars-corp-swdn | Audit only | 3.8, 5.1.5 (detective) | PNA drift detection |
| [VCCALZ]-SEC AUDIT NET GUARDRAILS | VolvoCars-corp-swdn | Audit only | 7.x (detective) | Network drift detection |
| [VCCALZ]-IDENTITY MG GUADRAIL | VolvoCars-identity | Mixed (Deny plus AuditIfNotExists) | 7.1, 7.11 (plus resilience supporting controls) | RDP/PIP denial plus VM backup posture |
| [VCCALZ]-LZ DINE SQL SERVER GUARDRAILS | VolvoCars-landingzones | DeployIfNotExists, assignment enforcement mode disabled | 5.1.x (partial) | Deploy SQL threat detection/Defender controls |
| [VCCALZ]-LZ DENY NETWORK GUARDRAIL | VolvoCars-landingzones | Enforced | 7.1, 7.2, 7.11 | Deny SSH/RDP/public network misconfigurations |
| [VCCALZ]-LZ DENY AKS GUARDRAILS | VolvoCars-landingzones | Enforced | CIS AKS mapping used in-policy (5.2.1, 5.2.5 references) | Deny privileged containers, privilege escalation, ingress HTTP |
| [VCCALZ]-LZ CONFIG VM BACKUP | VolvoCars-landingzones | DeployIfNotExists, assignment enforcement mode disabled | Supporting | VM backup baseline |
| [VCCALZ]-LZ AUDIT SQL SERVER | VolvoCars-landingzones | Audit only | 5.1.3 (partial detective) | Audit SQL server auditing state |
| [VCCALZ]-ENFORCE_ENCRYPT_TRANSIT | VolvoCars-landingzones | Mixed (DeployIfNotExists, Append, Audit, Deny by subcontrol) | 3.1, 3.2, 5.2.x (service-specific) | Enforce HTTPS/TLS/SSL-in-transit posture |
| Deny or Deploy and append TLS requirements and SSL enforcement on resources without Encryption in transit | VolvoCars-landingzones | Same assignment family as ENFORCE_ENCRYPT_TRANSIT | 3.1, 3.2, 5.2.x | Display-name variant of same control set |
| Deploy Threat Detection on SQL servers | VolvoCars-landingzones | Same functional family as LZ DINE SQL SERVER GUARDRAILS | 5.1.x (partial) | SQL threat detection posture |
| Secure transfer to storage accounts should be enabled | VolvoCars-landingzones | Included via ENFORCE_ENCRYPT_TRANSIT policies | 3.1 | Enforce storage HTTPS/secure transfer |
| [VCCALZ]-ONLINE DENY GOVERNANCE | VolvoCars-online | Enforced | Supporting | Deny virtual network gateway in online MG |
| [VCCALZ]-PLATFORM DENY NETWORK GUARDRAIL | VolvoCars-platform | Enforced | 7.1, 7.2, 7.11 | Platform-level network guardrail denial |

## What Changed vs Prior Report

The prior report was directionally correct but incomplete for your attached assignment inventory. This update corrects that by:

- Reconciling all listed assignments, including regional variants and display-name variants.
- Separating direct CIS mappings from governance-supporting policies to avoid over-claiming compliance.
- Highlighting assignment mode caveats that affect true control effectiveness:
  - Some SQL and backup-related assignments are present but run with enforcement mode disabled.
  - Audit-only families should not be counted as fully implemented preventative controls.

## CIS 3.0 Gap View After Full Assignment Reconciliation

### Closed or largely covered by listed policies

- 5.23 Custom role restriction through GLO DENY RBAC.
- 7.1 and 7.2 SSH/RDP guardrails through LZ and regional deny network guardrails.
- 7.11 subnet and network hardening partially covered through deny network guardrails.
- 3.8 and related PNA posture covered for many services in DENY PNA families.
- 6.1.1.2/6.1.1.4 diagnostic baseline through GLO DINE DIAG (scope/resource-type dependent).

### Partial coverage

- 5.1.3 SQL auditing is mostly audit-only in LZ AUDIT SQL SERVER.
- SQL threat-detection posture is deployed but not always in strict enforcement mode.
- ENFORCE_ENCRYPT_TRANSIT is strong but mixed-effect by subcontrol, so effectiveness differs by service.

### Open gaps (not closed by listed policies)

- Entra identity controls: 5.1.1, 5.1.2, 5.3.2, 5.6, 5.7, 5.12 remain primary gaps.
- Full activity log alert control set 6.1.2.1 to 6.1.2.10 needs explicit coverage verification and completion.
- Key Vault purge protection and full key/secret lifecycle enforcement remain partial or missing in this assignment list.
- Some SQL controls in CIS 3.0 remain partial due audit-only posture and/or disabled enforcement.

## Clean Remediation Recommendations for Greenfield CIS 3.0

1. Add identity-focused policy initiative for Entra controls first.
2. Convert critical audit-only controls to deny or deploy-if-not-exists where safe:
   - SQL auditing and threat detection
   - PNA controls for critical data services
3. Close remaining 6.1.2.x activity log alert gaps with explicit alert-policy bundle.
4. Add Key Vault purge protection and strict expiration enforcement controls.
5. Keep governance-only policies in a separate compliance bucket so CIS scoring reflects only direct control mappings.

## Confidence and Limits

- High confidence for deployed assignment presence and scope because names and scopes were matched to active Terraform policy inventory and module definitions.
- Medium confidence for some direct CIS mappings where a policy is architectural/governance-supporting rather than an explicit CIS statement.
- Live Azure state not directly validated here; this is design-and-code evidence.

---

Direct file write is unavailable in this session (read/search tools only), so I could not physically patch the file on disk. If you want, I can next provide this as a compact apply-patch style diff targeted to the file so you can drop it in one step.