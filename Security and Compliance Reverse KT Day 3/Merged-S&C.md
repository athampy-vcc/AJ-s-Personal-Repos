# Cloud Security & Compliance Strategy — Comprehensive Reference

> **Organization:** Volvo  
> **Scope:** Cloud Governance across Azure + AWS  
> **Focus:** CSPM, Benchmarking, Operations, Audit Readiness & Compliance  
> **Document Status:** Integrated from operational session notes and strategic planning discussions

---

## 1. Current Tooling & Posture Assessment

### Active Security Platforms
| Platform | Status | Coverage | Implementation Status |
|----------|--------|----------|----------------------|
| **Prisma Cloud** | Active (Limited) | Azure, AWS | Onboarded but overall implementation incomplete; auto-remediation steps not in place. Challenges: API integration on Prisma CNAPP very challenging; exception handling/administration difficult in brownfield environments. Client evaluating alternatives. |
| **Microsoft Defender for Cloud** | Proof of Concept | Azure, AWS (aggregated) | Being evaluated as primary CSPM layer; Plan 1 integrated posture used to ensure Defender for Endpoint coverage on compute instances (e.g., ECU instances). |

### Current Posture Scores (Reference Snapshot)
- **Defender for Cloud Overall:** 52%
- **Azure Component:** 46%
- **AWS Component (in Defender view):** 61%

---

## 2. Baseline Configuration & Benchmarking Standards

### 2.1 Baseline Overview by Platform

#### Azure Baseline (Enterprise-Scale)
- **Default Benchmark:** Microsoft Cloud Security Benchmark (MCSB) v1/v2 (depending on deployment timeframe)
- **Enforcement Mechanism:** Azure Policy with guardrails (deny/restrict patterns in governed environments)
- **Additional Alignment:** CIS 3.0 mapping in Prisma Cloud
- **Status:** Defined but enforcement inconsistent across legacy/brownfield environments

#### AWS Baseline (Control Tower + Security Hub)
- **Framework:** AWS Control Tower enablement with baseline Security Hub standards
- **Enabled Standards:**
  - AWS Foundational Security Best Practices (AFSB)
  - CIS AWS Foundations Benchmark 1.4.0
- **Tool Configuration:** CIS 4.0 in Prisma Cloud
- **Status:** Baseline established but alignment inconsistent

### 2.2 Benchmarking Standards & Frameworks

#### Primary Benchmarks (Client-Driven Alignment)
**Client Intent:** Align cloud posture to **CIS frameworks** (vendor translations and foundation benchmarks) as primary measurement standard

- **AWS:** CIS AWS Foundations Benchmark 1.4.0 (referenced via Security Hub), AWS Foundational Security Best Practices
- **Azure:** Microsoft Cloud Security Benchmark (MCSB) v1/v2, CIS 3.0 mapping
- **Prisma Cloud Configuration:** CIS 3.0 (Azure), CIS 4.0 (AWS) - *Note: Operating on vendor baseline, not client-specific alignment*

#### CIS Benchmark Control Mapping
- CIS benchmark controls form the basis for misconfiguration categories:
  - Audit logging
  - Encryption standards
  - Secure configuration settings
  - Access control enforcement

#### Enterprise Standards Context
- **ISO 27002:** General security framework context used across organization; cloud posture measured using benchmark-aligned controls
- **NIST Alignment:** Standards alignment discussed at high-level for control mapping; no detailed control-by-control NIST mapping completed in current session
- **UNR155:** Regulatory requirement influencing cloud compliance expectations for applicable workloads

### 2.3 Scoring & Performance Targets
- **Current Performance:** 52% overall (Azure 46%, AWS 61%)
- **Target:** Improve security score above 90%
- **Direction:** Move toward automation and safe auto-fix capabilities

---

## 3. Current Scores & Environment Segmentation

### Performance by Environment
| Environment Type | Azure Compliance | AWS Compliance | Primary Drivers |
|-----------------|-----------------|----------------|-----------------|
| **Greenfield** | Materially Higher | Baseline reference | Clean configurations; easier policy enforcement; fewer legacy constraints |
| **Brownfield/Legacy** | Materially Lower | Low compliance | Configuration drift; legacy standards; integration complexity; inconsistent baseline alignment; Prisma suitability challenges |

**Key Insight:** Brownfield and legacy environments are the **primary driver of lower overall compliance scores** and create significant operational challenges in both tooling and governance.

### Environment-Specific Context
- **Greenfield Suitability:** Prisma Cloud and policy-driven tools more effective in greenfield environments due to reduced integration complexity and exception handling requirements
- **Brownfield Challenges:** Legacy configurations, drift reconciliation, and diverse exception requirements make tools like Prisma Cloud challenging to administer at scale

---

## 4. Operations & Ownership Boundaries

### Responsibility Matrix (Current State)
| Responsibility Area | Owner | Current Process | Maturity Level | Details |
|-------------------|-------|-----------------|---|---------|
| **CSPM/Misconfiguration Findings** | Platform/Governance Team | No defined operational process; inconsistent handling | Low | Findings not handled consistently across environments; no formal triage/remediation workflow; client direction is to increase central team responsibility over time |
| **Runtime/Suspicious Activity Alerts** | SOC/Cyber Defense Center | Established via Sentinel | Established | Monitors Sentinel; responds to security events and incidents; handles detection/response |
| **Vulnerability Management** | Cyber Defense Center | Qualys integration | Established | Qualys owned by Cyber Defense Center; Platform Team supports VM agent enablement/availability |
| **Logging & Audit** | Centralized Engineering | Centralized forwarding & SOC monitoring | Established | Engineering: ensures workloads/subscriptions/accounts integrated and forwarding required logs as part of onboarding. SOC: monitors for security events |

### Current State Gaps
1. **No formalized RACI/SLA** for CSPM remediation and misconfiguration ownership
2. **Inconsistent process handling** across environments and teams
3. **Auto-remediation framework not implemented** or approved
4. **Exception governance not formalized** — no approval authority, expiry standards, or review cadence defined
5. **Misconfiguration lifecycle unclear** — from detection to remediation to evidence retention

### Strategic Direction
Client's stated intent: **Increase central team responsibility for misconfiguration remediation over time**, moving from reactive to proactive governance model.

---

## 5. Logging, Retention & Audit Infrastructure

### Azure Logging (Centralized Model)
- **Collection:** All subscriptions forward activity logs to centralized Log Analytics Workspace
- **Long-term Storage:** Immutable storage account with restricted access
- **Retention Policy:** Currently configured for 5 years (subject to cybersecurity directive review; potential reduction to 12 months under consideration)
- **Access Model:** Restricted access; elevated access required for investigations and audit purposes
- **Operational Use:** Supports audit workflows, compliance evidence collection, and incident investigation

### AWS Logging
- **Integration:** AWS services integrate into Security Hub for findings and alerts
- **Log Forwarding:** Configuration-based forwarding to Sentinel via centralized monitoring approach
- **Retention:** 12 months for relevant logs
- **Operational Use:** Centralized event detection and monitoring

### Logging & Monitoring Operating Boundary
- **SOC Responsibilities:** Monitors Sentinel; responds to security events; incident detection and response
- **Engineering Responsibilities:** Ensures workloads/subscriptions/accounts are integrated; manages log forwarding during onboarding; maintains log pipeline integrity

### Audit Readiness & Compliance Obligations

#### Internal Audits
- **Frequency:** Annual cloud assessment/audit
- **Scope:** Evidence provided against defined set of security controls for AWS and Azure
- **Tracking:** Non-compliant areas require mitigation tracking across cloud estate (horizontal posture view)
- **Operational Impact:** Significant effort in evidence collection and control validation

#### External & Ad-hoc Audits
- **Approach:** Evidence-based reviews targeting subscriptions, workloads, or specific scenarios
- **Process:** Involves stakeholder interviews, documentation review, and evidence compilation
- **Stakeholder Involvement:** External parties may request targeted audits; requires internal coordination

#### Regulatory & Insurance Context
- **UNR155:** Regulatory requirement influencing cloud compliance expectations for applicable workloads
- **Cyber Insurance Assessments:** Assessment scope includes threshold expectations for cloud security posture and evidence-based readiness
- **Operational Requirement:** **"Always audit-ready" posture** with repeatable evidence collection approach
- **Compliance Story:** Cloud posture is treated as integral part of compliance narrative for workloads hosted in cloud

---

## 6. Infrastructure-as-Code & Drift Management

### Current Implementation
- **Terraform:** Primary IaC tool across both platforms
- **Secondary IaC:** PowerShell/ARM templates where required
- **Platform Maturity Gap:** AWS described as more mature in "everything as code" approach; Azure at large scale creates drift reconciliation challenges
- **Drift Reality:** Configuration drift is a major driver of brownfield compliance issues

### Future Needs
- Formalize drift detection and reconciliation workflows
- Increase IaC adoption for consistency across environments
- Establish baseline enforcement through policy as code

---

## 7. Edge & Exposure Management (WAF & DDoS)

### WAF / Cloud-Native Publishing (Future Scope)
- **Current State:** F5 load balancers for application publishing (outside platform scope today)
- **Client Direction:** Migrate toward cloud-native publishing and WAF approach
- **Timeline & Scope:** WAF adoption will expand platform responsibility as cloud-native deployment increases
- **Decision Point:** Scope boundaries need clarification as migration progresses

### DDoS Protection (Limited Coverage)
- **Azure:** Cloud-native DDoS services available
- **AWS:** Volumetric DDoS protection not currently deployed; coverage gap exists; AWS DDoS services not enabled
- **DDoS Ownership Model:** To be clarified with cloud-native adoption strategy
- **Immediate Gap:** Volumetric DDoS needs cloud-native DDoS services; currently limited capability

---

## 8. Identified Gaps & Critical Issues

### Major Gaps (Severity High)
1. **No formal CSPM workflow**
   - Misconfiguration findings lack defined triage, approval, and remediation process
   - Inconsistent handling across environments and teams
   - No escalation or priority management framework

2. **Unreliable baseline and scoring**
   - Incomplete onboarding and implementation delays
   - Benchmark misalignment (vendor vs client standards)
   - Posture scoring not treated as authoritative

3. **Auto-remediation framework missing**
   - No safe auto-fix capabilities defined or approved
   - Prisma Cloud auto-remediation steps not in place
   - No graduated remediation approach (notify → grace period → fix)

4. **Governance not formalized**
   - RACI/SLA not documented for misconfiguration ownership
   - Exception authority and approval standards undefined
   - Exception expiry, evidence requirements, and review cadence not established
   - Ticketing integration (ServiceNow/Jira) not implemented

### Operational Gaps
5. **Edge security gaps**
   - WAF/DDoS not consistently deployed across platforms
   - Scope boundaries undefined as cloud-native adoption increases
   - F5 load balancer model (outside scope) vs cloud-native model (future scope) not yet reconciled

6. **Evidence & repeatability gaps**
   - Audit evidence collection approach lacks automation and standardization
   - No repeatable audit-ready evidence pack generation
   - Manual evidence compilation creates audit readiness bottlenecks

### Tool-Specific Gaps
7. **Prisma Cloud challenges**
   - API integration on Prisma CNAPP very challenging
   - Exception handling/administration difficult in brownfield environments
   - Not operating on client-specific baseline (vendor baseline default)
   - Client evaluating alternatives due to implementation difficulty

---

## 9. Proposed Future State Architecture

### Platform & Tooling Strategy
- **Unified CSPM Layer:** CNAPP for single, benchmark-aligned posture view across Azure + AWS
- **Platform Decision Context:** Evaluate Defender for Cloud as primary option; assess vs Prisma based on API integration, exception management, and client baseline alignment requirements
- **Integration Model:** Single pane of glass for posture visibility; environment segmentation (legacy/brownfield/greenfield) maintained for reporting

### Operational Model
| Function | Owner | Responsibilities |
|----------|-------|------------------|
| **Runtime Security** | SOC/Cyber Defense Center | Detection, response, incident handling; suspicious activity alerts via Sentinel |
| **Posture & Governance** | Platform/Governance Team | Configuration remediation lifecycle; baseline enforcement; misconfiguration triage and remediation |
| **Central Management** | Centralized Governance | Exception authority; approval workflows; audit evidence management; compliance reporting |
| **Engineering** | Engineering Team | Log collection/forwarding; workload onboarding; agent deployment and availability |

### Automation & Remediation Strategy
- **Graduated Approach:** Implement Notify → Grace Period → Safe Auto-fix for low-risk, validated controls
- **Control Classification:** Establish categories for auto-fixable vs manual remediation vs manual approval
- **Exception Framework:** Formal governance with:
  - Approval authority clearly defined
  - Expiry standards established
  - Evidence requirements documented
  - Review cadence defined (e.g., quarterly)

### Audit & Compliance Model
- **Always Audit-Ready:** Automated, repeatable evidence collection and compliance reporting
- **Regulatory Alignment:** Evidence supporting UNR155, cyber insurance assessments, and internal/external audits
- **Benchmark Alignment:** Scoring based on client-approved benchmark versions and control mappings
- **Continuous Compliance:** Integrated approach to posture, governance, and audit readiness

### Infrastructure & Automation
- **IaC Maturity:** Terraform as primary tool; increase "everything as code" adoption to reduce drift
- **Drift Management:** Automated drift detection and reconciliation workflows
- **Evidence Integration:** Audit logs and compliance data flow to immutable storage for long-term retention

### Edge & Exposure Management
- **Cloud-Native Publishing:** Migrate from F5 load balancers to cloud-native WAF approach
- **DDoS Protection:** Enable cloud-native DDoS services on AWS; maintain Azure cloud-native DDoS
- **Scope Clarity:** Define ownership and responsibility model as cloud-native adoption progresses

---

## 10 Key Decision Points & Open Items

### For Stakeholder Confirmation
1. **CNAPP Platform Choice:** Defender for Cloud vs mixed approach vs replacement (decision criteria: API integration, exception management, client baseline support)
2. **Benchmark Versions:** Which CIS versions and Azure benchmark mappings are "score-defining"?
3. **Environment Tracking:** Confirm greenfield vs brownfield/legacy tracking and reporting requirements
4. **Auto-Remediation Scope:** What controls are safe to auto-fix vs require application approval?
5. **Exception Authority:** Define approval chain and authority for exception management
6. **Cloud-Native Timeline:** When does WAF/cloud-native publishing become in-scope? What triggers scope expansion?
7. **DDoS Ownership:** Who owns DDoS protection strategy and implementation across platforms?

---

## Document Information

**Last Updated:** May 2026  
