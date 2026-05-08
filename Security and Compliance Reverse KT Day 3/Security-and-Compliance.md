# Cloud Security & Compliance Strategy

> **Scope:** Volvo Cloud Governance (Azure + AWS)  
> **Focus:** CSPM, Benchmarking, Operations, and Audit Readiness

---

## 1. Current Tooling & Posture

### Cloud Security Platforms
| Platform | Status | Coverage | Notes |
|----------|--------|----------|-------|
| **Prisma Cloud** | Active | Azure, AWS | Onboarding incomplete; exception handling challenging in brownfield environments; client evaluating alternatives |
| **Microsoft Defender for Cloud** | PoC | Azure, AWS (aggregated) | Evaluation as primary CSPM layer; overall score: 52% (Azure 46%, AWS 61%) |
| **Microsoft Defender for Endpoint** | Active | Compute | Coverage verification on ECU instances; integrated with posture assessment |

### Key Challenges
- Posture scoring unreliable due to incomplete onboarding and benchmark misalignment
- No formal CSPM triage/remediation workflow defined
- Exception administration difficult in diverse/brownfield environments

---

## 2. Benchmarking & Standards Alignment

### Primary Benchmarks
- **AWS:** CIS AWS Foundations Benchmark 1.4.0 (via Security Hub) and AWS Foundational Security Best Practices
- **Azure:** Microsoft Cloud Security Benchmark (MCSB) v1/v2 with CIS 3.0 mapping
- **Enterprise Framework:** ISO 27002 (general context); UNR155 (regulatory compliance requirement)
- **Scoring Target:** >90% security score; move toward automation/auto-fix

### Baseline Configuration
- **Azure:** Enterprise-scale deployments with Azure Policy for guardrails (deny/restrict patterns)
- **AWS:** Control Tower framework with Security Hub standards baseline

---

## 3. Current Scores & Environment Segmentation

| Environment | AWS Score | Azure Score | Key Driver |
|-------------|-----------|-------------|-----------|
| Greenfield | — | Higher compliance | Clean configurations; easier to enforce policy |
| Brownfield/Legacy | Low compliance | Materially lower | Configuration drift; legacy standards; integration complexity |

*Note: Brownfield environments are the primary driver of lower overall compliance scores.*

---

## 4. Operations & Ownership Boundaries

### Role Separation
| Responsibility | Owner | Details |
|----------------|-------|---------|
| **CSPM/Misconfiguration** | Platform/Governance Team | Configuration remediation lifecycle; no formal process yet; increasing central responsibility over time |
| **Runtime/Detection/Response** | SOC/Cyber Defense Center | Suspicious activity, security events, incident response via Sentinel |
| **Vulnerability Management** | Cyber Defense Center | Qualys integration; Platform Team supports VM agent enablement |
| **Logging/Audit** | Centralized Engineering | Log collection, forwarding, retention; SOC monitors for security events |

### Current State Gaps
- No formalized RACI/SLA for misconfiguration remediation
- Inconsistent handling of findings across environments
- Auto-remediation framework not implemented

---

## 5. Logging & Audit Infrastructure

### Azure Logging (Centralized)
- **Collection:** All subscriptions forward logs to centralized Log Analytics Workspace
- **Long-term Storage:** Immutable storage account with restricted access
- **Retention:** Currently 5 years (subject to cybersecurity directive review, potentially reduced to 12 months)
- **Access:** Elevated access required for investigation/audit purposes

### AWS Logging
- **Integration:** Security Hub for findings; logs forwarded to Sentinel via configuration-based forwarding
- **Retention:** 12 months for relevant logs

### Audit Readiness
- **Internal Audits:** Annual cloud assessment with evidence-based control validation across AWS and Azure
- **External/Ad-hoc Audits:** Evidence-based reviews targeting subscriptions, workloads, or specific scenarios
- **Regulatory Context:** Compliance evidence required for UNR155 and cyber insurance assessments
- **Operational Requirement:** "Always audit-ready" posture with repeatable evidence collection

---

## 6. Infrastructure-as-Code & Drift Management

### Current State
- **Terraform:** Primary IaC tool
- **Secondary IaC:** PowerShell/ARM where required
- **Maturity Gap:** AWS more mature in "everything as code" approach; Azure at scale creates drift reconciliation challenges

### Future Consideration
- Formalize drift detection and reconciliation workflows
- Increase IaC adoption for consistency

---

## 7. Edge & Exposure Management (WAF & DDoS)

### WAF / Cloud-Native Publishing
- **Current:** F5 load balancers for app publishing (outside platform scope)
- **Direction:** Client intent to migrate toward cloud-native publishing/WAF approach
- **Scope Change:** WAF adoption will expand platform responsibility as cloud-native deployment increases

### DDoS Protection
- **Azure:** Cloud-native DDoS services available
- **AWS:** Volumetric DDoS protection not currently deployed; coverage limited

---

## 8. Identified Gaps & Recommendations

### Critical Gaps
1. No formal CSPM triage/remediation workflow
2. Baseline/scoring unreliable due to onboarding delays and benchmark misalignment
3. Auto-remediation framework not implemented
4. RACI/SLA/governance for misconfiguration ownership not formalized
5. Edge security gaps (WAF/DDoS not consistently deployed)
6. Audit evidence collection approach lacks repeatability

### Recommended Actions
- Finalize CNAPP/CSPM platform decision (Defender for Cloud vs mixed vs replacement)
- Implement Notify → Grace Period → Safe Auto-fix workflow for low-risk controls
- Formalize approval authority and exception management with expiry/review standards
- Establish ticketing integration (ServiceNow/Jira) for findings and exceptions
- Build audit-ready evidence pack and reporting automation
- Clarify scope boundaries for WAF and DDoS as cloud-native adoption increases

---

## 9. Proposed Future State

- **Single CSPM Layer:** CNAPP for unified posture view across Azure + AWS (benchmark-aligned)
- **Clear Ownership:** SOC owns runtime/detection/response; Platform Team owns posture/misconfiguration remediation lifecycle
- **Automation:** Implement graduated remediation (notify → grace period → safe auto-fix) for validated controls
- **Audit Readiness:** Maintain repeatable evidence collection and compliance reporting
- **Risk Management:** Formal exception governance with approval authority, expiry standards, and review cadence
