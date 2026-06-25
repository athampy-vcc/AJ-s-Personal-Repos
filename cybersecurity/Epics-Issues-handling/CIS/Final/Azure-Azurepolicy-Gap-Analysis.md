# **CIS Azure 3.0 Compliance & Azure Policy Gap Analysis**
## Volvo Greenfield Environment

**Document Version**: 1.0  
**Date**: 2026-06-25  
**Scope**: CIS Microsoft Azure Foundations Benchmark v3.0.0

---
## Overview
This gap analysis shows that the current Azure Policy baseline in the Volvo Greenfield environment provides a solid foundation for governance, networking guardrails, storage protection, and logging/monitoring controls, but it does not yet deliver full CIS Microsoft Azure Foundations Benchmark v3.0.0 coverage. The strongest implementation areas are preventive and detective controls for network exposure, diagnostic settings deployment, tagging/governance enforcement, and selected storage security requirements. These controls demonstrate that core platform guardrails are already established at root, regional, and landing zone management group scopes.

## Executive Summary

### Current State
- **Policies Deployed**: 20+ custom Azure policies at management group (root) and landing zone scopes
- **Deployment Level**: Primarily at VolvoCars root MG, corp regional MGs, and greenfield landing zone MG
- **Coverage**: ~35% of CIS 3.0 requirements have direct policy implementation
- **Gap**: 65% of CIS 3.0 controls lack automated enforcement through Azure policies

### Key Findings
1. **Strong Areas**: RBAC governance, diagnostic logging (DINE), storage security, AKS guardrails
2. **Weak Areas**: Identity & Access Management (Entra ID), SQL/Database controls, App Service
3. **Missing**: ~60+ CIS 3.0 controls without policy mapping or remediation

---

## Part 1: Deployed Policies by Volvo Repos

### **Root Management Group (Global) Deployments**
**Scope**: `/providers/microsoft.management/managementgroups/VolvoCars` (definition_location)  
**Assignment Scope**: Same

| Policy Initiative | Category | Effect | CIS 3.0 Alignment | Status |
|---|---|---|---|---|
| **[VCCALZ]-GLO DINE AMA** | Monitoring | DeployIfNotExists | 6.X (Agent Mgmt) | Deployed |
| **[VCCALZ]-GLO DINE QUALYS** | Compliance | DeployIfNotExists | 8.X (Vulnerability) | Deployed |
| **[VCCALZ]-GLO DINE STORAGE LMP** | Storage | DeployIfNotExists | 4.X (Storage lifecycle) | Deployed |
| **[VCCALZ]-GLO DINE ENVTYPE** | Tagging | DeployIfNotExists | 2.X (Governance) | Deployed |
| **[VCCALZ]-GLO TAG MANDATORY** | Tagging | Deny | 2.X (Governance) | Deployed |
| **[VCCALZ]-GLO TAG INHERITANCE** | Tagging | Audit/Deny | 2.X (Governance) | Deployed |
| **[VCCALZ]-GLO DENY GOVERNANCE** | Governance | Deny | Multiple | Deployed |
| **[VCCALZ]-GLO AUDIT DIAG** | Monitoring | Audit | 6.X (Compliance) | Deployed |
| **[VCCALZ]-GLO TAG LOWER** | Tagging | Modify | 2.X (Governance) | Deployed |
| **[VCCALZ]-GLO TAG REQUIRED** | Governance | Deny | Multiple | Deployed |
| **[VCCALZ]-GLO DINE AHB** | Monitoring | DeployIfNotExists | 6.X (Compliance) | Deployed |


## Evidence-backed Assignment Reconciliation (All Listed Policies)

Legend:
- Control mapping values are CIS 3.0 references when direct, otherwise Supporting or Indirect.
- Status values: Enforced, Audit only, DeployIfNotExists, Mixed, Governance-only.

| Assignment name | Scope | Implementation status | CIS 3.0 mapping | Control intent |
|---|---|---|---|---|
| [VCCALZ]-GLO DINE DIAG | VolvoCars | DeployIfNotExists | 6.1.1, 6.1.2, 6.1.4 (indirect for some resources) | Deploy diagnostic settings baseline |
| [VCCALZ]-GLO DENY RBAC | VolvoCars | Enforced | 2.23 | Deny privileged/custom RBAC abuse paths |
| [VCCALZ]-DEPLOY DFC CONFIG | VolvoCars | DeployIfNotExists | 3.1.3.x family in CIS 3.0 mapping context | Deploy Defender security contact configuration |
| Enable Azure Monitor for Virtual Machine Scale Sets | VolvoCars | DeployIfNotExists (platform baseline) | Supporting | VMSS observability baseline |
| Deploy Microsoft Defender for Cloud configuration | VolvoCars | DeployIfNotExists | 3.1.3.x family | Defender baseline configuration |
| [VCCALZ]-WEU DINE DNS CONFIG | VolvoCars-corp | DeployIfNotExists | Supporting (private endpoint hardening architecture) | Auto-deploy private DNS zone groups |
| [VCCALZ]-WEU DENY PNA | VolvoCars-corp | Enforced | 4.6, 4.7 (service-dependent) | Deny public network access for covered PaaS types |
| [VCCALZ]-WEU DENY NET GUARDRAILS | VolvoCars-corp | Enforced | 7.1, 7.2, 7.3, 7.4 (plus guardrail extensions) | Deny risky network constructs |
| [VCCALZ]-WEU DENY DATABRICKS | VolvoCars-corp | Enforced | Supporting | Deny non-compliant Databricks deployment patterns |
| [VCCALZ]-WEU AUDIT PNA | VolvoCars-corp | Audit only | 4.6, 4.7 (detective) | Detect PNA drift |
| [VCCALZ]-WEU AUDIT NET GUARDRAILS | VolvoCars-corp | Audit only | 7.x (detective) | Detect routing/network policy drift |
| [VCCALZ]-EUS DINE DNS CONFIG | VolvoCars-corp-amer | DeployIfNotExists | Supporting | Auto-deploy private DNS zone groups |
| [VCCALZ]-EUS DENY PNA | VolvoCars-corp-amer | Enforced | 4.6, 4.7 (service-dependent) | Deny public network access |
| [VCCALZ]-EUS DENY NET GUARDRAILS | VolvoCars-corp-amer | Enforced | 7.1, 7.2, 7.3, 7.4 | Deny risky network patterns |
| [VCCALZ]-EUS DENY GOVERNANCE | VolvoCars-corp-amer | Enforced | Supporting | Enforce allowed locations and governance boundaries |
| [VCCALZ]-EUS DENY DATABRICKS | VolvoCars-corp-amer | Enforced | Supporting | Deny non-compliant Databricks |
| [VCCALZ]-EUS AUDIT PNA | VolvoCars-corp-amer | Audit only | 4.6, 4.7 (detective) | Detect PNA drift |
| [VCCALZ]-EUS AUDIT NET GUARDRAILS | VolvoCars-corp-amer | Audit only | 7.x (detective) | Detect network drift |
| [VCCALZ]-SEA DINE DNS CONFIG | VolvoCars-corp-sea | DeployIfNotExists | Supporting | Auto-deploy private DNS zone groups |
| [VCCALZ]-SEA DENY PNA | VolvoCars-corp-sea | Enforced | 4.6, 4.7 (service-dependent) | Deny public network access |
| [VCCALZ]-SEA DENY NET GUARDRAILS | VolvoCars-corp-sea | Enforced | 7.1, 7.2, 7.3, 7.4 | Deny risky network patterns |
| [VCCALZ]-SEA DENY GOVERNANCE | VolvoCars-corp-sea | Enforced | Supporting | Location/governance controls |
| [VCCALZ]-SEA DENY DATABRICKS | VolvoCars-corp-sea | Enforced | Supporting | Databricks governance hardening |
| [VCCALZ]-SEA AUDIT PNA | VolvoCars-corp-sea | Audit only | 4.6, 4.7 (detective) | PNA drift detection |
| [VCCALZ]-SEA AUDIT NET GUARDRAILS | VolvoCars-corp-sea | Audit only | 7.x (detective) | Network drift detection |
| [VCCALZ]-SEC DINE DNS CONFIG | VolvoCars-corp-swdn | DeployIfNotExists | Supporting | Auto-deploy private DNS zone groups |
| [VCCALZ]-SEC DENY PNA | VolvoCars-corp-swdn | Enforced | 4.6, 4.7 (service-dependent) | Deny public network access |
| [VCCALZ]-SEC DENY NET GUARDRAILS | VolvoCars-corp-swdn | Enforced | 7.1, 7.2, 7.3, 7.4 | Deny risky network patterns |
| [VCCALZ]-SEC DENY GOVERNANCE | VolvoCars-corp-swdn | Enforced | Supporting | Governance/location controls |
| [VCCALZ]-SEC DENY DATABRICKS | VolvoCars-corp-swdn | Enforced | Supporting | Databricks hardening controls |
| [VCCALZ]-SEC AUDIT PNA | VolvoCars-corp-swdn | Audit only | 4.6, 4.7 (detective) | PNA drift detection |
| [VCCALZ]-SEC AUDIT NET GUARDRAILS | VolvoCars-corp-swdn | Audit only | 7.x (detective) | Network drift detection |
| [VCCALZ]-IDENTITY MG GUADRAIL | VolvoCars-identity | Mixed (Deny plus AuditIfNotExists) | 7.1, 7.2 (plus resilience supporting controls) | RDP/SSH/PIP denial plus VM backup posture |
| [VCCALZ]-LZ DINE SQL SERVER GUARDRAILS | VolvoCars-landingzones | DeployIfNotExists, assignment enforcement mode disabled | 5.1.x (partial) | Deploy SQL threat detection/Defender controls |
| [VCCALZ]-LZ DENY NETWORK GUARDRAIL | VolvoCars-landingzones | Enforced | 7.1, 7.2, 7.3, 7.4 | Deny SSH/RDP/UDP/HTTP(S) misconfigurations |
| [VCCALZ]-LZ DENY AKS GUARDRAILS | VolvoCars-landingzones | Enforced | Supporting (no direct CIS Azure Foundations 3.0 AKS control IDs) | Deny privileged containers, privilege escalation, ingress HTTP |
| [VCCALZ]-LZ CONFIG VM BACKUP | VolvoCars-landingzones | DeployIfNotExists, assignment enforcement mode disabled | Supporting | VM backup baseline |
| [VCCALZ]-LZ AUDIT SQL SERVER | VolvoCars-landingzones | Audit only | 5.1.3 (partial detective) | Audit SQL server auditing state |
| [VCCALZ]-ENFORCE_ENCRYPT_TRANSIT | VolvoCars-landingzones | Mixed (DeployIfNotExists, Append, Audit, Deny by subcontrol) | 4.1, 4.15, 9.1, 9.4 (service-specific) | Enforce HTTPS/TLS/SSL-in-transit posture |
| Deny or Deploy and append TLS requirements and SSL enforcement on resources without Encryption in transit | VolvoCars-landingzones | Same assignment family as ENFORCE_ENCRYPT_TRANSIT | 4.1, 4.15, 9.1, 9.4 | Display-name variant of same control set |
| Deploy Threat Detection on SQL servers | VolvoCars-landingzones | Same functional family as LZ DINE SQL SERVER GUARDRAILS | 5.1.x (partial) | SQL threat detection posture |
| Secure transfer to storage accounts should be enabled | VolvoCars-landingzones | Included via ENFORCE_ENCRYPT_TRANSIT policies | 4.1 | Enforce storage HTTPS/secure transfer |
| [VCCALZ]-ONLINE DENY GOVERNANCE | VolvoCars-online | Enforced | Supporting | Deny virtual network gateway in online MG |
| [VCCALZ]-PLATFORM DENY NETWORK GUARDRAIL | VolvoCars-platform | Enforced | 7.1, 7.2, 7.3, 7.4 | Platform-level network guardrail denial |

---
## **Part 2: CIS 3.0 Controls Coverage Matrix**

### **Overview: Policy Deployment by CIS Section**

| CIS 3.0 Section | Total Controls | Deployed Policies | Coverage % | Enforcement Status |
|---|---|---|---|---|
| **Identity & Access (2.X)** | 15 | 1 (DENY RBAC only) | 7% | Partial |
| **Storage (4.X)** | 12 | 4 (PNA, TLS, LMP, Encrypt) | 67% | Mixed (Enforce + Audit) |
| **Logging & Monitoring (6.X)** | 12 | 3+ (DINE DIAG, AMA, Audit) | 60% | DeployIfNotExists + Audit |
| **Networking (7.X)** | 8 | 4+ (Network Guardrails x4 regions) | 60% | Enforced + Audit variants |
| **Key Vault (3.3.X)** | 6 | 1 (Partial via DFC Config) | 20% | DeployIfNotExists |
| **Database (5.1.X SQL)** | 10 | 1 (Audit-only, enforcement disabled) | 10% | Disabled/Audit |
| **AKS/Kubernetes (Supporting Mapping)** | 8 | 1 (Deny AKS Guardrails) | 25% | Enforced |

---

### **Section 1: Identity & Access (2.X - CRITICAL GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Name | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **2.1.3 / 2.2.5** | MFA required for all users |  None deployed | N/A | N/A | **Add**: Conditional Access policy for all users |
| **2.1.2 / 2.2.4** | MFA for privileged/admin users |  None deployed | N/A | N/A | **Add**: Conditional Access policy for admin groups |
| **2.15 / 2.16** | Guest user restrictions |  None deployed | N/A | N/A | **Add**: Guest invite and access restriction policies |
| **2.6** | Account lockout threshold |  None deployed | N/A | N/A | **Add**: Entra lockout threshold policy |
| **2.7** | Account lockout duration |  None deployed | N/A | N/A | **Add**: Entra lockout duration policy |
| **2.12 / 2.13** | User consent for apps |  None deployed | N/A | N/A | **Add**: App consent governance policy |
| **2.23** | Restrict custom subscription administrator roles |  **Deployed** | `[VCCALZ]-GLO DENY RBAC` | **Enforced** | Maintain; no direct gap |

**Analysis**: Only 1 of 7 identity controls covered. **Gap: 86%**. The RBAC deny policy aligns to 2.23, while Entra-based identity controls (MFA, lockout, guest access) still lack automated enforcement.

---

### **Section 2: Storage (4.X - MEDIUM GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **4.1** | Secure transfer required for storage |  **Deployed** | `[VCCALZ]-ENFORCE_ENCRYPT_TRANSIT` (LZ) | **Mixed** (DeployIfNotExists + Append) | Deployed; monitor compliance drift |
| **4.15** | Storage minimum TLS >= 1.2 |  **Deployed** | `[VCCALZ]-ENFORCE_ENCRYPT_TRANSIT` (LZ) | **Mixed** | Deployed; monitor enforcement |
| **4.6 / 4.7** | Storage public network access restricted and default deny |  **Deployed** | `[VCCALZ]-*-DENY PNA` (4 regions: WEU, EUS, SEA, SEC) | **Enforced** (+ Audit variants) | Strong coverage; audit-only variants remain detective |
| **4.17** | Blob anonymous access disabled |  **Deployed** | `[VCCALZ]-*-DENY PNA` | **Enforced** | Active enforcement across corp MGs |
| **4.10** | Storage soft delete enabled |  **Deployed** | `[VCCALZ]-GLO DINE STORAGE LMP` | **DeployIfNotExists** | Auto-deployed via lifecycle management policy |
| **4.16** | Cross-tenant replication disabled |  **Partial** | Included in PNA/guardrail family | **Mixed** | Needs explicit validation; audit capability present |

**Analysis**: 5–6 of 6–8 controls covered. **Gap: 17–33%**. Storage is strongest area with enforce + audit + deploy-if-not-exists strategies across regions. Regional policy variants (WEU, EUS, SEA, SEC) provide broad coverage.

---

### **Section 3: Logging & Monitoring (6.X - STRONG)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **6.1.1 / 6.1.2** | Diagnostic settings exist and capture categories |  **Deployed** | `[VCCALZ]-GLO DINE DIAG` | **DeployIfNotExists** | Active baseline deployment; verify category completeness |
| **6.1.4** | Key Vault audit logging |  **Deployed** | `[VCCALZ]-GLO DINE DIAG` + `[VCCALZ]-GLO DINE AMA` | **DeployIfNotExists** | Deployed; AMA enables log ingestion |
| **6.1.2.1–6.1.2.10** | Activity log alerts (policy assignment, delete, RBAC, etc.) |  **Partial** | `[VCCALZ]-GLO AUDIT DIAG` | **Audit** (detective mode only) | **Add**: Explicit alert policies for all 10 types |
| **6.4** | Azure Monitor resource logging enabled broadly |  **Partial** | Indirect via DINE DIAG | **DeployIfNotExists** | Retention/category coverage should be validated per service |
| **Supporting** | Azure Monitor Agent (AMA) deployment |  **Deployed** | `[VCCALZ]-GLO DINE AMA` | **DeployIfNotExists** | Deployed; supports 6.x observability outcomes |

**Analysis**: 4–5 of 5 controls substantially covered. **Gap: 0–20%**. Strongest section due to DINE diagnostic baseline + AMA deployment. Activity log alerts incomplete (audit-only); recommend upgrade to enforcement or explicit alert policies.

---

### **Section 4: Networking (7.X - MEDIUM GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **7.1** | RDP (3389) from Internet restricted |  **Deployed** | `[VCCALZ]-*-DENY NET GUARDRAILS` (4 regions) + `[VCCALZ]-IDENTITY MG GUADRAIL` | **Enforced** | Strong; enforced at corp + identity scopes |
| **7.2** | SSH (22) from Internet restricted | **Deployed** | `[VCCALZ]-*-DENY NET GUARDRAILS` (4 regions) + `[VCCALZ]-LZ DENY NETWORK GUARDRAIL` | **Enforced** | Strong; enforced at corp + LZ scopes |
| **7.4** | HTTP(S) from Internet evaluated/restricted | **Partial** | Included in `[VCCALZ]-*-DENY NET GUARDRAILS` | **Enforced** (guardrail extension) | Verify full coverage and exceptions |
| **7.3** | UDP restrictions | **Partial** | Included in Network Guardrails | **Enforced** | Covered via guardrail family; verify specific UDP rules |
| **7.5** | Flow logs enabled for all NSGs | None deployed | N/A | N/A | **Add**: NSG Flow Logs policy |
| **7.6 / 7.7** | Network Watcher enabled and public IPs periodically reviewed | **Partial** | No explicit assignment evidence | **N/A / Supporting** | Add explicit policy/automation for periodic review controls |

**Analysis**: 4–5 of 6 controls substantially covered. **Gap: 17–33%**. Network deny policies are strongly deployed across 4 regional corp MGs + identity + LZ. Missing: NSG flow logs, and stronger evidence for Network Watcher and periodic public IP review controls.

---

### **Section 5: Key Vault (3.3.X - MEDIUM GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **3.3.1 / 3.3.2** | Key expiration enforcement (RBAC and non-RBAC KV) | **Audit-only** | Indirect via monitoring policies | **Audit** | **Add**: Deny non-expiring keys policy |
| **3.3.3 / 3.3.4** | Secret expiration (RBAC and non-RBAC KV) | **Partial** | Not explicitly listed | **N/A** | **Add**: Enforce secret expiration across all KV types |
| **3.3.5** | Purge protection enabled (recoverable vault) | **None deployed** | N/A | N/A | **Add**: Require KV purge protection policy |
| **3.1.12–3.1.14** | Defender for Cloud security contacts and notifications | **Deployed** | `[VCCALZ]-DEPLOY DFC CONFIG` | **DeployIfNotExists** | Deployed; auto-configures DFC baseline |

**Analysis**: 1 of 5 controls fully deployed. **Gap: 80%**. Key Vault policies are largely missing except DFC config baseline. Critical gaps: key/secret expiration enforcement (audit-only), purge protection requirement.

---

### **Section 6: Database Services (5.1.X SQL - CRITICAL GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **5.1.1** | SQL auditing enabled | **Audit-only** | `[VCCALZ]-LZ AUDIT SQL SERVER` | **Audit** (enforcement_mode disabled) | **Upgrade**: Convert to `DeployIfNotExists` with enforcement enabled |
| **5.1.3** | SQL TDE protector encrypted with customer-managed key | **None/Partial** | No explicit assignment evidence in listed set | **N/A** | **Add**: Enforce CMK-backed TDE protector policy |
| **5.1.4** | AD authentication on SQL | None deployed | N/A | N/A | **Add**: `Require` Entra authentication policy |
| **5.1.5** | SQL data encryption set to On | **Partial** | Indirect via SQL guardrail family | **DeployIfNotExists** (limited evidence) | Add explicit SQL database encryption policy mapping |
| **5.1.6** | SQL audit retention >= 91 days | **Partial** | Indirect via DINE policies | **DeployIfNotExists** | Verify audit retention setting in threat detection policy parameters |

**Analysis**: 0–1 of 5 controls fully effective. **Gap: 80–100%**. SQL policies present but critically hampered by **enforcement_mode disabled** on threat detection and audit-only status on SQL auditing. These must be re-enabled for compliance.

---

### **Section 7: AKS/Kubernetes (Supporting, Non-Direct in CIS Azure 3.0)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **Supporting** | Privileged containers denied | **Deployed** | `[VCCALZ]-LZ DENY AKS GUARDRAILS` | **Enforced** | Strong AKS posture, but no direct CIS Azure Foundations 3.0 control ID |
| **Supporting** | Privilege escalation disabled | **Deployed** | `[VCCALZ]-LZ DENY AKS GUARDRAILS` | **Enforced** | Strong AKS posture, mapped as supporting control |
| **Supporting** | Ingress HTTP disabled | **Deployed** | `[VCCALZ]-LZ DENY AKS GUARDRAILS` | **Enforced** | Strong AKS posture, mapped as supporting control |
| **5.2.x note** | CIS 5.2.x in this benchmark refers to PostgreSQL controls, not AKS | N/A | N/A | N/A | Keep AKS guardrails, but track separately from CIS Azure Foundations IDs |

**Analysis**: AKS controls are tracked as supporting controls because CIS Azure Foundations 3.0 does not provide direct AKS control IDs in this benchmark. Guardrails provide strong enforcement value, but should be reported separately from direct CIS scoring.

---

### **Section 8: Governance & Tagging (Supporting Baseline)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **2.1.1** | Security defaults enabled | None deployed | N/A | N/A | **Add**: Entra Security Defaults policy |
| **Supporting** | Tags mandatory on resources | **Deployed** | `[VCCALZ]-GLO TAG MANDATORY`, `[VCCALZ]-GLO TAG REQUIRED` | **Deny** | Strong governance baseline, not a direct CIS 2.x requirement |
| **Supporting** | Tag inheritance enforced | **Deployed** | `[VCCALZ]-GLO TAG INHERITANCE` | **Audit/Deny** | Active governance baseline, not a direct CIS 2.x requirement |
| **Locations** | Allowed locations enforced | **Deployed** | `[VCCALZ]-*-DENY GOVERNANCE` (regional variants) + `[VCCALZ]-ONLINE DENY GOVERNANCE` | **Enforced** | Strong; governance policies deny non-compliant locations |
| **Online/Platform** | Online/Platform guardrails | **Deployed** | `[VCCALZ]-ONLINE DENY GOVERNANCE`, `[VCCALZ]-PLATFORM DENY NETWORK GUARDRAIL` | **Enforced** | Subscriptions protected via scope-specific denies |

**Analysis**: Governance baseline is strong (especially tagging and location controls), but only a subset maps directly to CIS 2.x controls. Missing direct control remains 2.1.1 Security Defaults.
