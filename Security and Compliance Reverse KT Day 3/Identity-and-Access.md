# Identity & Access (Volvo)

## 1. Break-glass accounts

### 1.1 AWS break-glass and root access
- AWS spoke accounts:
  - IAM root user is disabled across spoke accounts (central governance pattern).
- AWS main account:
  - A break-glass user exists in the main account.
  - IAM root user exists in the main account.
- MFA controls:
  - Break-glass and main account root are protected using hardware MFA.
  - The hardware MFA device is physically stored in a Volvo data center.

### 1.2 AWS access delivery model (SSO)
- Access is provided using SSO integrated with Entra ID.
- Users typically receive standard access roles through SSO (e.g., read-only and administrative).
- Local IAM users are restricted; access is expected to be delivered through SSO.
- If additional access patterns are required, they are handled via SSO role configuration rather than local IAM users.

### 1.3 Azure identity boundary
- Entra ID is managed by a separate IAM team.
- Engineering responsibilities include:
  - RBAC implementation during onboarding
  - Ensuring least privilege patterns are applied via defined access models
  - Supporting just-in-time access patterns where applicable through governance controls

---

## 2. Engineering scope (Engg scope)

### 2.1 In-scope for Engineering (platform governance perspective)
Engineering scope covers platform-level identity and access enablement patterns, such as:
- Subscription/account onboarding identity setup (RBAC/roles)
- Ensuring SSO/RBAC models are applied as part of onboarding automation
- Governance controls that affect access posture (least privilege patterns, role exposure controls)
- Central governance enforcement patterns where identity configuration is treated as code/configuration

### 2.2 Out of scope for Engineering (owned by other functions)
The following are owned outside engineering scope:
- Entra ID tenant administration and lifecycle (IAM team ownership)
- SOC operational monitoring and incident response (SOC/Cyber Defense Center ownership)
- Workload-level application access design inside application teams’ resources (application/workload owner ownership)

### 2.3 Responsibility split summary
- Engineering (platform): “enable and enforce the access pattern”
- IAM team: “operate Entra ID platform”
- Application teams: “own workload configurations and internal access requirements”
- SOC/Cyber Defense: “monitor threats and respond to runtime incidents”
