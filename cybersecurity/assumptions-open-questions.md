# 📋 Assumptions & Open Questions

---

## ✅ Assumptions

| Item | Description |
|------|-------------|
| **SOC/Cyber Defense Center** | Owns runtime monitoring and response |
| **Cloud Platform/Governance Team** | Owns misconfiguration (CSPM) governance and remediation lifecycle |
| **Benchmarks** | CIS (AWS/Azure) and Azure MCSB mapping alignment (indicative) |

---

## ❓ Open Questions

> **Status:** Need Confirmation in Reverse-KT

1. **Tooling Direction**  
   Final CNAPP/CSPM tool choice — *Defender for Cloud vs mixed vs replacement?*

2. **Benchmark Versions**  
   Which CIS versions and Azure benchmark mappings are "score-defining"?

3. **Environment Split Reporting**  
   Confirm greenfield vs brownfield/legacy tracking requirements.

4. **Auto-Remediation Governance**  
   Define what is safe to auto-fix vs requires application approval.

5. **Exceptions Management**  
   - Approval authority
   - Expiry standards
   - Evidence requirements
   - Review cadence

6. **Ticketing Integration**  
   ServiceNow/Jira requirement for findings, changes, and exceptions.

7. **Scope Timing**  
   When do cloud-native publishing/WAF become in scope? DDoS ownership model?

---

**📝 Note:** Remove open questions from final documentation if deemed unnecessary after confirmation.

