# Reverse KT Summary – Cloud Governance (Azure + AWS)

## 1) Tooling & Current Posture
- Prisma Cloud is onboarded for Azure and AWS, but the overall onboarding/implementation is not complete.
- Current posture scoring is not considered valid/reliable because policies/baseline are not aligned to the client benchmark.
- Exception handling and administration are difficult in diverse/brownfield environments; client is evaluating moving away from Prisma.
- Microsoft Defender for Cloud POC is ongoing (not completed).

## 2) Benchmarking Direction
- Client intent is to align cloud posture to CIS frameworks (vendor translations/foundation benchmarks).
- AWS posture is referenced through Security Hub/CIS baseline views.
- Azure posture is referenced using enterprise-scale and Microsoft Cloud Security Benchmark (MCSB) alignment (depending on deployment/version).

## 3) Operations & Ownership Boundary (Current)
- Misconfiguration/CSPM findings: no defined operational process; not handled consistently today.
- Runtime/suspicious activity alerts: handled by SOC/Cyber Defense Center.
- Vulnerability management: Qualys is owned by Cyber Defense Center; platform team supports VM agent enablement/availability.
- Client’s stated direction is to increase central team responsibility for misconfiguration remediation over time.

## 4) Current Scores / Baselines (as stated in KT)
- AWS: example scores referenced (e.g., Security Hub baseline and CIS 1.4.0 compliance figures) with brownfield being a major driver of low compliance.
- Azure: greenfield environments are materially higher than brownfield/legacy; client tracks compliance using this split.
- Target: improve security score above 90 and move toward automation/auto-fix.

## 5) Edge / Exposure (WAF & DDoS)
- WAF/app publishing is currently via F5 load balancers and not in platform scope today.
- Client intends to move toward cloud-native publishing/WAF approach; scope may expand as it becomes cloud-native.
- DDoS coverage is limited; volumetric DDoS needs cloud-native DDoS services; AWS DDoS protection is not deployed.

## 6) Logging / Audit Readiness
- Azure activity logs flow to central Log Analytics workspace and immutable storage.
- Retention was described as long-term and may be reduced (e.g., to 12 months) based on cybersecurity directive.
- Client expects “always audit ready” posture via internal and ad-hoc audits.

## 7) IaC / Drift Reality
- Terraform is primary IaC; PowerShell/ARM used where required.
- AWS is described as more mature in “everything as code”.
- Azure at large scale makes drift reconciliation more challenging.

---

## Gaps Identified
1. No formal misconfiguration (CSPM) triage/remediation workflow.
2. Baseline/scoring not reliable due to incomplete onboarding and benchmark misalignment.
3. No auto-remediation framework implemented.
4. Central team responsibility is increasing but RACI/SLA/governance is not formalized.
5. Edge security gaps exist (WAF/DDoS not consistently covered).
6. Audit readiness requires repeatable evidence collection approach.

---

## Proposed Future State (High-Level)
- CNAPP becomes the posture layer for Azure + AWS (single view, benchmark-aligned).
- SOC remains owner for runtime/detection/response; platform team owns posture/misconfig lifecycle.
- Implement Notify → Grace Period → Safe Auto-fix for low-risk controls.
- Maintain audit-ready evidence pack and reporting.
