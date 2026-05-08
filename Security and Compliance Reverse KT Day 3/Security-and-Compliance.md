# Cloud Security & Compliance Strategy

> **Scope:** Volvo Cloud Governance (Azure + AWS)  
> **Focus:** CSPM, Benchmarking, Operations, and Audit Readiness  
> **Key Challenge:** Posture scoring unreliable; policies/baseline not aligned to client benchmark

---

## 1. Current Tooling & Posture Assessment

### Active Platforms
| Platform | Status | Coverage | Implementation Status & Challenges |
|----------|--------|----------|-------------------------------------|
| **Prisma Cloud** | Active | Azure, AWS | Onboarded but overall implementation incomplete. Exception handling/administration difficult in brownfield environments; API integration challenging. Client evaluating alternatives. |
| **Microsoft Defender for Cloud** | PoC (In Progress) | Azure, AWS (aggregated) | Being evaluated as primary CSPM layer for unified posture visibility. Overall score snapshot: 52% (Azure 46%, AWS 61%). |
| **Microsoft Defender for Endpoint** | Active | Compute instances | Coverage verification (ECU instances); integrated with posture assessment. |

### Critical Finding
**Posture scoring is not considered valid/reliable** due to:
- Incomplete onboarding and implementation
- Policies/baseline not aligned to client benchmark
- Legacy and brownfield workloads creating drift
- Exception governance gaps in diverse environments

---

## 2. Benchmarking Strategy & Standards Alignment

### Client Intent & Direction
Align cloud posture to **CIS frameworks** (vendor translations and foundation benchmarks) as the primary measurement standard across both platforms.

### Active Benchmarks by Platform
- **AWS:** CIS AWS Foundations Benchmark 1.4.0 (Security Hub), AWS Foundational Security Best Practices (AFSB), and Control Tower baseline
- **Azure:** Microsoft Cloud Security Benchmark (MCSB) v1/v2, CIS 3.0 alignment (depending on deployment timeframe)

### Enterprise Standards Context
- **ISO 27002:** General security framework context used across organization
- **UNR155:** Regulatory requirement influencing cloud compliance expectations for applicable workloads
- **Cyber Insurance Assessments:** Evidence-based readiness threshold expectations

### Baseline Configuration
- **Azure:** Enterprise-scale deployments with Azure Policy guardrails (deny/restrict patterns in governed environments)
- **AWS:** Control Tower framework with Security Hub standards enablement

---

## 3. Current Security Posture & Environment Segmentation

### Posture Scores (Reference Context)
- **Defender for Cloud Overall:** 52%
- **Azure Component:** 46%
- **AWS Component (in Defender view):** 61%

### Environment Performance
| Environment Type | Azure Compliance | AWS Compliance | Primary Drivers |
|------------------|------------------|-----------------|-----------------|
| **Greenfield** | Materially Higher | Baseline reference | Clean configurations; easier policy enforcement |
| **Brownfield/Legacy** | Lower | Low compliance | Configuration drift; legacy standards; integration complexity; inconsistent baseline alignment |

**Key Insight:** Brownfield and legacy environments are the **primary driver of lower overall compliance scores**.

### Scoring Target
- **Goal:** Improve security score above 90%
- **Direction:** Move toward automation and safe auto-fix capabilities

---

## 4. Operations & Ownership Boundaries (Current State)

### Defined Responsibilities
| Responsibility Area | Owner | Current Process | Maturity |
|-------------------|-------|-----------------|----------|
| **CSPM/Misconfiguration Findings** | Platform/Governance Team | No defined operational process; not handled consistently today | Low — needs formalization |
| **Runtime/Suspicious Activity Alerts** | SOC/Cyber Defense Center | Handled via Sentinel monitoring and incident response | Established |
| **Vulnerability Management** | Cyber Defense Center | Qualys integration; Platform Team supports VM agent enablement/availability | Established |
| **Logging & Audit** | Centralized Engineering | Log collection, forwarding, retention; SOC monitors for security events | Established |

### Strategic Direction
Client's stated intent: **Increase central team responsibility for misconfiguration remediation over time** (from reactive to proactive governance).

### Current State Gaps
- No formalized RACI/SLA for CSPM remediation
- Misconfiguration findings not handled consistently across environments
- No approval authority, exception management, or auto-remediation framework defined
- Exception governance lacks expiry standards and review cadence

---

## 5. Logging, Retention & Audit Infrastructure

### Azure Logging (Centralized Model)
- **Collection:** All subscriptions forward activity logs to centralized Log Analytics Workspace
- **Long-term Storage:** Immutable storage account (restricted access; elevated access required for investigations)
- **Retention Policy:** Currently configured for 5 years (subject to cybersecurity directive review; potential reduction to 12 months)
- **Operational Use:** Central management and immutability support audit and compliance workflows

### AWS Logging
- **Integration:** Security Hub for findings and alerts
- **Log Forwarding:** Configuration-based forwarding to Sentinel for centralized monitoring
- **Retention:** 12 months for relevant logs

### Audit Readiness Expectations
- **Internal Audits:** Annual cloud assessment with evidence-based control validation across AWS and Azure; non-compliant areas tracked for mitigation
- **External/Ad-hoc Audits:** Evidence-based reviews targeting subscriptions, workloads, or specific scenarios; involve stakeholder interviews and documentation review
- **Operational Requirement:** **"Always audit-ready" posture** with repeatable evidence collection and reporting approach
- **Regulatory Context:** Evidence support for UNR155 compliance and cyber insurance assessment requirements

---

## 6. Infrastructure-as-Code & Drift Management

### Current State
- **Terraform:** Primary IaC tool across both platforms
- **Secondary IaC:** PowerShell/ARM templates where required
- **Platform Maturity:** AWS described as more mature in "everything as code" approach
- **Scale Challenge:** Azure at large scale makes drift reconciliation more challenging

### Future Needs
- Formalize drift detection and reconciliation workflows
- Increase IaC adoption for consistency and compliance baseline enforcement

---

## 7. Edge & Exposure Management (WAF & DDoS)

### WAF / Cloud-Native Publishing (Out of Current Scope)
- **Current State:** F5 load balancers for application publishing (outside platform scope today)
- **Client Direction:** Migrate toward cloud-native publishing and WAF approach
- **Scope Expansion Timeline:** WAF adoption will expand platform responsibility as cloud-native deployment increases

### DDoS Protection (Limited Coverage)
- **Azure:** Cloud-native DDoS services available
- **AWS:** Volumetric DDoS protection not currently deployed; coverage gap exists
- **Future Consideration:** Align DDoS ownership model with cloud-native adoption strategy

---

## 8. Identified Gaps & Recommendations

### Critical Gaps
1. **No formal CSPM workflow:** Misconfiguration findings lack triage, approval, and remediation process
2. **Unreliable baseline/scoring:** Incomplete onboarding and benchmark misalignment undermine posture visibility
3. **No auto-remediation framework:** Safe auto-fix capabilities not defined or implemented for low-risk controls
4. **Governance not formalized:** RACI/SLA/exception authority/approval standards not documented
5. **Edge security gaps:** WAF/DDoS not consistently deployed; scope boundaries undefined as cloud-native adoption increases
6. **Repeatability gaps:** Audit evidence collection and reporting approach lacks automation and standardization

### Recommended Actions (Priority Order)
1. **Finalize CNAPP/CSPM platform decision:** Defender for Cloud vs mixed-tool vs replacement approach
2. **Formalize CSPM governance:** Define approval authority, exception standards (expiry, evidence requirements, review cadence), and ticketing integration (ServiceNow/Jira)
3. **Implement graduated remediation:** Notify → Grace Period → Safe Auto-fix workflow for validated low-risk controls
4. **Standardize audit evidence:** Build automated audit-ready evidence pack and reporting for internal/external compliance
5. **Clarify edge security scope:** Define WAF and DDoS ownership model aligned with cloud-native publishing roadmap
6. **Establish baseline alignment:** Confirm benchmark versions (CIS, MCSB) and "score-defining" control mappings

---

## 9. Proposed Future State

### Platform & Tooling
- **Unified CSPM Layer:** CNAPP providing single, benchmark-aligned posture view across Azure + AWS
- **Decision Context:** Evaluate Defender for Cloud as primary vs supplemented with Prisma/alternative based on API integration and exception management requirements

### Operational Model
- **SOC:** Remains owner of runtime/detection/response; handles security events and incidents
- **Platform Team:** Owns posture, misconfiguration remediation lifecycle, and baseline enforcement
- **Central Governance:** Manages exception authority, approval workflows, and audit readiness

### Automation & Remediation
- **Graduated Approach:** Implement Notify → Grace Period → Safe Auto-fix for low-risk, validated controls
- **Exception Framework:** Formal governance with approval authority, expiry standards, evidence requirements, and review cadence
- **Ticketing Integration:** ServiceNow/Jira for findings, changes, and exceptions tracking

### Audit & Compliance
- **Always Audit-Ready:** Repeatable evidence collection and compliance reporting
- **Regulatory Alignment:** Maintain audit-ready evidence supporting UNR155, cyber insurance assessments, and internal/external audit requirements
- **Benchmark Alignment:** Ensure RACI clarity and scoring based on client-approved benchmark versions and control mappings
