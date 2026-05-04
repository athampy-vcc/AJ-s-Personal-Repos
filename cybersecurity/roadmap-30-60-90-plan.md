# 30/60/90 Day Plan – CNAPP Improvement Program

## Goals (90 Days)
- Establish valid, benchmark-aligned posture scoring for Azure + AWS.
- Implement a misconfiguration lifecycle (triage → assign → remediate/exception → validate).
- Move posture toward target score >90 with measurable trend.
- Implement a Safe Auto-Remediation framework (Notify → Grace → Auto-fix).
- Create “audit-ready always” evidence collection approach.

---

## Day 0–30: Baseline + Process Foundation
### Deliverables
1. Baseline & Measurement Validity
   - Define benchmark scope (CIS + Azure benchmark/MCSB mapping).
   - Define what contributes to posture score.
   - Establish greenfield vs brownfield/legacy reporting split.

2. Misconfiguration Operating Model v1
   - Create triage workflow states (New → Triage → Assigned → Remediated/Exception → Verified).
   - Define ownership boundary (SOC vs Platform vs App teams).
   - Define severity model and SLA targets.

3. Top Findings Discovery
   - Identify top failing controls (Pareto).
   - Identify controls suitable for “safe fixes” (no workload impact).

4. GitHub Program Setup
   - Repo structure created.
   - Issue templates + tracking labels created.

---

## Day 31–60: Quick Wins + Safe Auto-Remediation Design
### Deliverables
1. Safe Fix Allow-List v1
   - Create list of controls safe to auto-remediate (low/no app impact).
   - Define rollback patterns and verification steps.

2. Notify → Grace → Auto-Fix Workflow
   - Notification mechanism to app owners.
   - Grace period rules (by severity).
   - Auto-remediation execution for allow-listed controls.

3. Ticketing Integration Design (If required)
   - Auto-ticket for non-safe controls.
   - Auto-ticket for exception requests requiring approval.

4. Dashboards / Metrics
   - Score trend dashboard (overall + greenfield vs brownfield).
   - Remediation aging dashboard (0–7 / 8–30 / 31–90 / 90+).
   - Automation rate dashboard.

---

## Day 61–90: Scale + Audit Evidence Pack
### Deliverables
1. Scale Policy-as-Code / Guardrails
   - Expand policies/controls as code (Terraform-first).
   - Drift management approach and reporting.

2. Audit Readiness Pack
   - Evidence checklist mapped to control families.
   - Repeatable evidence capture steps (exports/screenshots/logs/links).
   - Internal annual assessment playbook + ad-hoc audit playbook.

3. Edge Security Alignment Plan (WAF/DDoS)
   - Document roadmap support for cloud-native publishing/WAF migration.
   - DDoS gap assessment + recommended ownership/actions.
