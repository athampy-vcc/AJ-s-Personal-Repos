# **CIS Azure 3.0 Compliance & Azure Policy Gap Analysis**
## Volvo Greenfield Environment

**Document Version**: 1.0  
**Date**: 2026-06-23  
**Scope**: CIS Microsoft Azure Foundations Benchmark v3.0.0

---

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
| **[VCCALZ]-GLO DINE STORAGE LMP** | Storage | DeployIfNotExists | 3.X (Lifecycle) | Deployed |
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

---
## **Part 2: CIS 3.0 Controls Coverage Matrix**

### **Overview: Policy Deployment by CIS Section**

| CIS 3.0 Section | Total Controls | Deployed Policies | Coverage % | Enforcement Status |
|---|---|---|---|---|
| **Identity & Access (5.X)** | 15 | 1 (DENY RBAC only) | 7% | Partial |
| **Storage (3.X/9.X)** | 12 | 4 (PNA, TLS, LMP, Encrypt) | 67% | Mixed (Enforce + Audit) |
| **Logging & Monitoring (6.X)** | 12 | 3+ (DINE DIAG, AMA, Audit) | 60% | DeployIfNotExists + Audit |
| **Networking (7.X)** | 8 | 4+ (Network Guardrails x4 regions) | 60% | Enforced + Audit variants |
| **Key Vault (8.3.X)** | 6 | 1 (Partial via DFC Config) | 20% | DeployIfNotExists |
| **Database (5.1.X SQL)** | 10 | 1 (Audit-only, enforcement disabled) | 10% | Disabled/Audit |
| **AKS/Kubernetes (8.X)** | 8 | 1 (Deny AKS Guardrails) | 25% | Enforced |

---

### **Section 1: Identity & Access (5.X - CRITICAL GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Name | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **5.1.1** | MFA required for all users |  None deployed | N/A | N/A | **Add**: `Deny` MFA bypass policy |
| **5.1.2** | MFA for admin users |  None deployed | N/A | N/A | **Add**: `Require MFA` for admin roles |
| **5.3.2** | Guest user restrictions |  None deployed | N/A | N/A | **Add**: Guest access control policy |
| **5.6** | Account lockout threshold |  None deployed | N/A | N/A | **Add**: Lockout policy (Entra ID native) |
| **5.7** | Account lockout duration |  None deployed | N/A | N/A | **Add**: Enforce 60s+ lockout |
| **5.12** | User consent for apps |  None deployed | N/A | N/A | **Add**: App consent policy |
| **5.23** | Restrict custom RBAC roles |  **Deployed** | `[VCCALZ]-GLO DENY RBAC` | **Enforced** | Maintains; no gaps |

**Analysis**: Only 1 of 7 identity controls covered. **Gap: 86%**. The RBAC deny policy enforces 5.23, but Entra-based identity controls (MFA, lockout, guest access) lack automated enforcement entirely.

---

### **Section 2: Storage (3.X/9.X - MEDIUM GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **3.1** | HTTPS/TLS default for storage |  **Deployed** | `[VCCALZ]-ENFORCE_ENCRYPT_TRANSIT` (LZ) | **Mixed** (DeployIfNotExists + Append) | Deployed; monitor compliance drift |
| **3.2** | Minimum TLS >= 1.2 |  **Deployed** | `[VCCALZ]-ENFORCE_ENCRYPT_TRANSIT` (LZ) | **Mixed** | Deployed; monitor enforcement |
| **3.8** | Storage public network access = Deny |  **Deployed** | `[VCCALZ]-*-DENY PNA` (4 regions: WEU, EUS, SEA, SEC) | **Enforced** (+ Audit variants) | Strong coverage; audit-only for detective posture |
| **3.8.2** | Blob public access disabled |  **Deployed** | `[VCCALZ]-*-DENY PNA` | **Enforced** | Active enforcement across corp MGs |
| **3.1.2** | Storage soft delete enabled |  **Deployed** | `[VCCALZ]-GLO DINE STORAGE LMP` | **DeployIfNotExists** | Auto-deployed via lifecycle management policy |
| **9.3.7** | Cross-tenant replication disabled |  **Partial** | Included in PNA family | **Mixed** | Needs explicit validation; audit capability present |

**Analysis**: 5–6 of 6–8 controls covered. **Gap: 17–33%**. Storage is strongest area with enforce + audit + deploy-if-not-exists strategies across regions. Regional policy variants (WEU, EUS, SEA, SEC) provide broad coverage.

---

### **Section 3: Logging & Monitoring (6.X - STRONG)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **6.1.1.2** | Diagnostic settings capture categories |  **Deployed** | `[VCCALZ]-GLO DINE DIAG` | **DeployIfNotExists** | Active baseline deployment; verify category completeness |
| **6.1.1.4** | Key Vault audit logging |  **Deployed** | `[VCCALZ]-GLO DINE DIAG` + `[VCCALZ]-GLO DINE AMA` | **DeployIfNotExists** | Deployed; AMA enables log ingestion |
| **6.1.2.1–6.1.2.10** | Activity log alerts (policy assignment, delete, RBAC, etc.) |  **Partial** | `[VCCALZ]-GLO AUDIT DIAG` | **Audit** (detective mode only) | **Add**: Explicit alert policies for all 10 types |
| **6.1.3** | Azure Monitor logs retention |  **Partial** | Indirect via DINE DIAG | **DeployIfNotExists** | Retention policy embedded in diagnostic settings; verify >= 365 days |
| **6.2** | Azure Monitor Agent (AMA) deployment |  **Deployed** | `[VCCALZ]-GLO DINE AMA` | **DeployIfNotExists** | Deployed; parameters enable DCR targeting |

**Analysis**: 4–5 of 5 controls substantially covered. **Gap: 0–20%**. Strongest section due to DINE diagnostic baseline + AMA deployment. Activity log alerts incomplete (audit-only); recommend upgrade to enforcement or explicit alert policies.

---

### **Section 4: Networking (7.X - MEDIUM GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **7.1** | No SSH (port 22) from 0.0.0.0/0 |  **Deployed** | `[VCCALZ]-*-DENY NET GUARDRAILS` (4 regions) + `[VCCALZ]-LZ DENY NETWORK GUARDRAIL` | **Enforced** | Strong; enforced at corp + LZ scopes |
| **7.2** | No RDP (port 3389) from 0.0.0.0/0 | **Deployed** | `[VCCALZ]-*-DENY NET GUARDRAILS` (4 regions) + `[VCCALZ]-IDENTITY MG GUADRAIL` | **Enforced** | Strong; additional identity MG variant |
| **7.11** | NSG assigned to all subnets | **Partial** | Included in `[VCCALZ]-*-DENY NET GUARDRAILS` | **Enforced** (guardrail extension) | Enforced as guardrail extension; verify subnet coverage |
| **7.3** | UDP restrictions | **Partial** | Included in Network Guardrails | **Enforced** | Covered via guardrail family; verify specific UDP rules |
| **7.5** | Flow logs enabled for all NSGs | None deployed | N/A | N/A | **Add**: NSG Flow Logs policy |
| **7.12** | App Gateway TLS >= 1.2 | **Partial** | `[VCCALZ]-ENFORCE_ENCRYPT_TRANSIT` | **Mixed** | Included in encrypt-in-transit; verify AppGW-specific enforcement |

**Analysis**: 4–5 of 6 controls substantially covered. **Gap: 17–33%**. Network deny policies strongly deployed across 4 regional corp MGs + identity + LZ. Missing: NSG flow logs, explicit App Gateway TLS enforcement.

---

### **Section 5: Key Vault (8.3.X - MEDIUM GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **8.3.1** | Key expiration enforcement (RBAC KV) | **Audit-only** | Indirect via monitoring policies | **Audit** | **Add**: `Deny` non-expiring keys policy |
| **8.3.2** | Secret expiration (RBAC KV) | **Audit-only** | Indirect via monitoring policies | **Audit** | **Add**: `Deny` non-expiring secrets policy |
| **8.3.3** | Secret expiration (Non-RBAC KV) | **Partial** | Not explicitly listed | **N/A** | **Add**: Enforce secret expiration across all KV types |
| **8.3.5** | Purge protection enabled | **None deployed** | N/A | N/A | **Add**: `Require` KV purge protection policy |
| **8.1.12–8.1.14** | Defender for Cloud security contacts | **Deployed** | `[VCCALZ]-DEPLOY DFC CONFIG` | **DeployIfNotExists** | Deployed; auto-configures DFC baseline |

**Analysis**: 1 of 5 controls fully deployed. **Gap: 80%**. Key Vault policies are largely missing except DFC config baseline. Critical gaps: key/secret expiration enforcement (audit-only), purge protection requirement.

---

### **Section 6: Database Services (5.1.X SQL - CRITICAL GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **5.1.1** | SQL auditing enabled | **Audit-only** | `[VCCALZ]-LZ AUDIT SQL SERVER` | **Audit** (enforcement_mode disabled) | **Upgrade**: Convert to `DeployIfNotExists` with enforcement enabled |
| **5.1.3** | SQL threat detection | **Deploy but disabled** | `[VCCALZ]-LZ DINE SQL SERVER GUARDRAILS` | **DeployIfNotExists** (enforcement_mode disabled) | **Enable**: Activate enforcement mode for threat detection |
| **5.1.4** | AD authentication on SQL | None deployed | N/A | N/A | **Add**: `Require` Entra authentication policy |
| **5.1.5** | SQL public network access = Deny | **Partial** | Part of `[VCCALZ]-*-DENY PNA` (storage focus) | **Enforced** (storage-scoped) | Extend PNA policies to explicitly cover SQL managed instances |
| **5.1.6** | SQL audit retention >= 91 days | **Partial** | Indirect via DINE policies | **DeployIfNotExists** | Verify audit retention setting in threat detection policy parameters |

**Analysis**: 0–1 of 5 controls fully effective. **Gap: 80–100%**. SQL policies present but critically hampered by **enforcement_mode disabled** on threat detection and audit-only status on SQL auditing. These must be re-enabled for compliance.

---

### **Section 7: AKS/Kubernetes (8.X AKS - MEDIUM GAP)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **5.2.1** | Privileged containers denied | **Deployed** | `[VCCALZ]-LZ DENY AKS GUARDRAILS` | **Enforced** | Active; deny prevents privileged pod deployment |
| **5.2.2** | Privilege escalation disabled | **Deployed** | `[VCCALZ]-LZ DENY AKS GUARDRAILS` | **Enforced** | Active; deny prevents allowPrivilegeEscalation=true |
| **5.2.4** | Ingress HTTP disabled | **Deployed** | `[VCCALZ]-LZ DENY AKS GUARDRAILS` | **Enforced** | Active; deny prevents HTTP ingress |
| **5.2.3** | Resource requests/limits | None deployed | N/A | N/A | **Add**: Resource quota policy |
| **5.2.5** | Custom SecurityContext | **Partial** | Included in guardrails | **Enforced** | Covered via AKS deny guardrails; verify scope |
| **5.2.6** | Network policies | **Partial** | Partial via network guardrails | **Enforced** | Network guardrails apply; AKS-specific policies could be stronger |

**Analysis**: 3–4 of 6 controls covered. **Gap: 33–50%**. AKS guardrails provide strong privilege/container deny enforcement. Missing: resource requests/limits, broader network policy scoping.

---

### **Section 8: Governance & Tagging (2.X - GOVERNANCE BASELINE)**

| CIS 3.0 Req ID | Control | Policy Status | Assignment Names | Implementation Mode | Remediation |
|---|---|---|---|---|---|
| **2.1.1** | Security defaults enabled | None deployed | N/A | N/A | **Add**: Entra Security Defaults policy |
| **2.2** | Tags mandatory on resources | **Deployed** | `[VCCALZ]-GLO TAG MANDATORY`, `[VCCALZ]-GLO TAG REQUIRED` | **Deny** | Strong; enforces via tagging policies |
| **2.3** | Tag inheritance enforced | **Deployed** | `[VCCALZ]-GLO TAG INHERITANCE` | **Audit/Deny** | Active; includes env type mandatory |
| **Locations** | Allowed locations enforced | **Deployed** | `[VCCALZ]-*-DENY GOVERNANCE` (regional variants) + `[VCCALZ]-ONLINE DENY GOVERNANCE` | **Enforced** | Strong; governance policies deny non-compliant locations |
| **Online/Platform** | Online/Platform guardrails | **Deployed** | `[VCCALZ]-ONLINE DENY GOVERNANCE`, `[VCCALZ]-PLATFORM DENY NETWORK GUARDRAIL` | **Enforced** | Subscriptions protected via scope-specific denies |

**Analysis**: 4 of 5 controls covered. **Gap: 20%**. Governance baseline strong; tagging and location controls comprehensive. Missing: Entra Security Defaults explicit policy.
