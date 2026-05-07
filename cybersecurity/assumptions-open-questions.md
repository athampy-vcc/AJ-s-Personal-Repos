# Assumptions & Open Questions

## Assumptions
- SOC/Cyber Defense Center owns runtime monitoring and response.
- Cloud platform/governance team owns misconfiguration (CSPM) governance and remediation lifecycle.
- Benchmarks to align (indicative): CIS (AWS/Azure), and Azure MCSB mapping.

## Open Questions (Need Confirmation in Reverse-KT)
1. Tooling direction: final CNAPP/CSPM tool choice (Defender for Cloud vs mixed vs replacement).
2. Benchmark versions: which CIS versions and which Azure benchmark mappings are “score-defining”.
3. Environment split reporting: confirm greenfield vs brownfield/legacy tracking requirements.
4. Auto-remediation governance: define what is safe to auto-fix vs requires app approval.
5. Exceptions: approval authority, expiry standards, evidence needed, and review cadence.
6. Ticketing integration: ServiceNow/Jira requirement for findings, changes, and exceptions.
7. Scope timing: when cloud-native publishing/WAF becomes in scope, and DDoS ownership model.

Note: Will have to remove above Open questions if not needed
