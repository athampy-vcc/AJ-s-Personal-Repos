# Cloud Security Operations Knowledge Transfer

## 1. Overview

This document provides a clear summary of the Cloud Security Operations Knowledge Transfer session conducted with Premkumar and Gusten. The purpose of this session was to help the operations team understand the current cloud security setup, responsibilities, tools involved, and how operations should be handled going forward.

Currently, the organization is in the process of building a structured security operations model, especially around CSPM (Cloud Security Posture Management), vulnerability handling, and governance processes.

## 2. Current Situation

As of now, there is no fully defined operational process for handling cloud security findings.

- There are no clear rules on which security findings must be handled immediately
- No standard process exists for incident creation and response
- Security responsibilities are mostly handled by application teams
- Tools are in place but not fully aligned operationally

Because of this, security operations are reactive instead of proactive.

## 3. Expected Operational Model

### Day 1 Operations
All accounts must be onboarded and integrated with the tools:

- Sentinel (logging and monitoring)
- Qualys (vulnerability management)
- CNAPP tools (Defender for Cloud or Prisma)

### Day 2 Operations
The team is responsible for implementing automated remediation wherever possible:

- Improve Cloud Security Score (>90%)
- Implement automated remediation wherever possible
- Reduce dependency on application teams
- Build a standardized governance model

## 4. Way of Working

### Old Approach
- Application teams fix security issues
- Operations team only monitors

### New Approach
- Operations team actively fixes issues
- Automation will be used wherever possible
- Application teams will only be involved when necessary

## 5. Handling Findings

### Low Severity Findings
- Auto-remediate wherever possible
- Notify stakeholders after remediation

### Medium Severity Findings
1. Notify application team
2. Provide self-remediation option (button/action)
3. Allow approximately 1 week window
4. If unresolved, the Ops team auto-remediates

### High and Critical Severity Findings
- Immediate action required
- Follow incident/change management process

## 6. Cybersecurity Portal Dashboard

The Cybersecurity Insights Portal provides a unified view of security findings across multiple platforms.

### Integrated Systems
The portal aggregates data from:

- Prisma Cloud
- Azure Defender for Cloud
- AWS Security Hub
- GitHub Security
- Qualys (vulnerability scanning tool)

### Vulnerability Handling
- The portal uses App ID as the source of truth
- Any security finding (vulnerability) linked to an App ID is automatically reflected
- The operations team needs to focus on vulnerabilities that are linked to our own App IDs

AWS Landing Zone - Global (non-china) - APP-4857  
Azure Landing Zone - Global (non-china) - APP-4767  
Azure Landing Zone - China - APP-8083  
Prisma Cloud - APP-6390  
Network Hub in Azure - APP-4727 (do not work)

---

## 7. ITSM & ServiceNow Integration

### Automatic Resource Discovery
Cloud resources from Azure and AWS are automatically discovered in ServiceNow using integration connectors.

### CI Mapping (Configuration Items)
Each resource is mapped as a CI using:

- Tags (owner, app ID, environment)
- LeanIX (application and business ownership)

### Auto Assignment
Based on mapping:

- CI Owner is assigned automatically
- Support group is mapped automatically

This helps in quickly assigning incidents/changes to the right team without manual lookup.

---

## 8. Exception Process

Sometimes rules cannot be followed due to business reasons.

In such cases:

- An exception request must be raised
- It must be approved by Premkumar / Gusten, Cybersecurity team
- Standard exceptions are predefined, but new ones require approval

## 9. Risk Management

Risks are tracked in a central system.

- If you find any major issue, raise it as a risk
- Risks are reviewed and mitigation plans are created
- Operations team can report risks, but ownership lies with leadership

## 10. Audit Responsibilities

The team will support multiple audits:

- Deloitte Audit
- ISO 27001
- Internal assessments

Responsibilities include:

- Providing evidence
- Supporting documentation
- Ensuring compliance

## 11. Monitoring Responsibilities

The operations team should monitor:

- Cloud service health
- Resource health
- Security alerts

In case of cloud provider issues (AWS/Azure outages):

- Inform business teams immediately
- Use Teams/Slack channels for communication