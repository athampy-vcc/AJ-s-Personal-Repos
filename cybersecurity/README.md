# Cloud Governance – Reverse KT + CNAPP Improvement Plan

## Purpose
This repository captures the Reverse-KT outputs and the delivery plan to improve cloud security posture across Azure and AWS using a CNAPP/CSPM-led approach.

## Source
Content is derived from the client-provided KT transcript ("KT for Cloud Governance").

## Client-Stated Baseline (Current State)
- Prisma Cloud is onboarded for Azure and AWS, but onboarding/implementation is not complete.
- Scoring/baseline is not reliable because policies/baseline are not aligned to the client benchmark.
- Misconfiguration findings (CSPM) do not currently have an operational process and are not handled consistently.
- Runtime/suspicious activity and vulnerability management are handled by SOC/Cyber Defense Center; platform team ensures onboarding/integration and agent deployment support.
- Target outcome is to improve security posture/score to >90 and move toward automation/auto-fix.

## What’s inside
- `docs/reverse-kt/` – Reverse KT summary, assumptions, open questions
- `docs/operating-model/` – RACI, misconfig workflow, exception handling, audit readiness
- `docs/benchmarks/` – Benchmark/control mapping placeholders (CIS/MCSB/etc.)
- `roadmap/` – 30/60/90 plan, epics/backlog, KPIs/OKRs
- `automation/` – future remediation playbooks, policy-as-code, integrations
- `templates/` – exception and change request templates
- `.github/` – issue templates and PR template

## Outcomes (90 days)
1. Benchmark-aligned posture measurement (Azure + AWS)
2. Misconfiguration triage + ownership model
3. Safe auto-remediation framework (Notify → Grace → Auto-fix)
4. Audit readiness pack and evidence model
