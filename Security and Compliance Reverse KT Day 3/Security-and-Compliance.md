# Security and Compliance (Volvo)

## 1. Baselines

### 1.1 Prisma Cloud baseline
- Prisma Cloud is operating on a vendor baseline (not Volvo-specific baseline).
- CIS compliance levels configured in Prisma Cloud:
  - Azure: CIS 3.0
  - AWS: CIS 4.0
- Prisma Cloud posture/measurement is not treated as Volvo-benchmark aligned when baseline mapping is not Volvo-specific.
- Prisma Cloud is described as more suitable for greenfield environments than brownfield/legacy-heavy estates, mainly due to integration and exception-handling complexity.

### 1.2 Azure baseline (enterprise-scale)
- Azure enterprise-scale deployments include Microsoft Cloud Security Benchmark (MCSB) v1/v2 as a default baseline depending on deployment timeframe.
- Azure Policy is used as the primary mechanism to enforce baseline configuration guardrails (deny/restrict patterns in governed environments).

### 1.3 AWS baseline (Control Tower + Security Hub)
- AWS Control Tower framework enablement includes baseline Security Hub standards.
- AWS Security Hub standards enabled include:
  - AWS Foundational Security Best Practices (AFSB)
  - CIS AWS Foundations Benchmark 1.4.0

---

## 2. Defender for Cloud scope

### 2.1 What Defender for Cloud is used for (in this context)
- Defender for Cloud is used as a CSPM view for security posture and misconfiguration visibility across environments.
- Security posture is viewed in an aggregated manner; environment segmentation was discussed (legacy / brownfield / greenfield).

### 2.2 Current scoring snapshot (as observed during the discussion)
- Defender for Cloud (overall): 52%
- Azure component: 46%
- AWS component (in Defender view): 61%

### 2.3 Misconfiguration vs runtime threats (scope boundary)
- Misconfiguration findings (CSPM): handled via governance and configuration remediation patterns.
- Runtime threat detection and response (suspicious activity, logs, incidents): handled by SOC/Cyber Defense Center.

### 2.4 Defender for Endpoint integration note
- Defender for Cloud (Plan 1 / integrated posture) was referenced in the context of ensuring Defender for Endpoint coverage on compute instances (e.g., ECU instances).

---

## 3. CIS / NIST standards alignment

### 3.1 CIS alignment (primary benchmark reference in session)
- CIS benchmarks were the primary standards discussed for cloud configuration posture:
  - AWS: CIS AWS Foundations Benchmark 1.4.0 (Security Hub)
  - Prisma: CIS 3.0 Azure and CIS 4.0 AWS (tool configuration)
- CIS benchmark controls were referenced as the basis for misconfiguration categories (e.g., audit logging, encryption, secure configuration settings).

### 3.2 NIST alignment (session note)
- Standards alignment was discussed at a high level in the context of control mapping.
- No detailed control-by-control mapping to NIST was reviewed in this session; alignment was referenced as a standards lens.

### 3.3 Additional framework context
- ISO 27002 was referenced as a general security framework context used across the organization, with cloud posture measured using benchmark-aligned controls.

---

## 4. Logging and monitoring

### 4.1 Operating boundary: Engineering vs SOC
- SOC monitors Sentinel and responds to security events.
- Engineering scope is to ensure workloads/subscriptions/accounts are integrated and forwarding the required logs as part of onboarding.

### 4.2 Azure logging design (centralized)
- Azure subscriptions forward logs to a centralized Log Analytics Workspace managed centrally.
- Long-term retention is supported using an immutable storage account.
- Access to immutable storage is restricted; elevated access is used when access is required (e.g., investigations).

### 4.3 Retention snapshot (as stated)
- Azure long-term retention: configured for 5 years (noted as current configuration at the time of discussion).
- AWS retention: described as 12 months for relevant logs.

### 4.4 AWS integration
- AWS services integrate into Security Hub for findings.
- Logs are forwarded to Sentinel via configuration-based forwarding for centralized monitoring.

---

## 5. Cloud Audits

### 5.1 Internal audits
- An internal cloud assessment/audit occurs annually.
- Evidence is provided against a defined set of security controls for both AWS and Azure.
- Non-compliant areas require mitigation tracking across the cloud estate (horizontal posture view).

### 5.2 External / ad-hoc audits
- External parties may request audits targeting a subscription, workload, or scenario.
- These are evidence-based and involve stakeholder interviews and documentation review.

### 5.3 Regulatory context
- UNR155 was referenced as a regulatory requirement influencing cloud compliance expectations for workloads that must comply.
- Cloud posture is treated as part of the compliance story for workloads hosted in the cloud.

### 5.4 Cyber insurance assessment context
- Cyber insurance assessments were referenced as having threshold expectations.
- Evidence-based readiness was emphasized as an operational requirement (audit-ready posture).
