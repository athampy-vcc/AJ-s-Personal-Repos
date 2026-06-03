## 🚨 Break Glass Account 

### 📌 Purpose
- Break Glass accounts are **emergency access mechanisms** used when:
  - ❌ SSO (Entra ID / IAM Identity Center) fails
  - ❌ Identity provider is unavailable
  - ✅ Need immediate access for:
    - Disaster recovery
    - Critical infrastructure fixes

---

### 👥 Structure
- Organization maintains:
  - **2 Break Glass IAM Users (AWS)**  
    - Used for redundancy (backup) 
- Each AWS account contains:
  - **Break Glass Role**
  - Trust relationship mapped to Break Glass users

---

### 🔐 Security Controls
- Access is protected with:
  - ✅ **Hardware MFA devices (mandatory)**
- Access is allowed:
  - Via CLI or Console (only exception for console login)
- Without MFA:
  - ❌ No access allowed at all   



### 🛡️ Monitoring & Alerting
- Every action is:
  - ✅ Monitored
  - ✅ Alerted
- Notifications are sent to:
  - SOC team
  - Microsoft Teams channel
- Events tracked by following actions:
  - Console login
  - Access key usage
  - Role assumption   


### 🏢 Account:
- Break Glass users are located in:
  - **Management account**
- Reason:
  - SCPs (Service Control Policies) do NOT apply to management account  
- This ensures:
  - ✅ Guaranteed administrative access during failure scenarios  

### 🌐 Azure Break Glass Setup
- Azure also has:
  - **Emergency admin account**
  - **Non-human account**
- Secured using:
  - ✅ Hardware-based authentication (e.g., YubiKey)
- Access requires:
  - Authorization validation via support process  

---

### 📦 Physical Security
- MFA devices are:
  - Stored in **secure data center**
- Access requires:
  - Identity validation
  - Secondary approval (manager-level)

---