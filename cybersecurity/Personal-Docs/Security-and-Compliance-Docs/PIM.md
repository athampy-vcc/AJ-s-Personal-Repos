## 🔐 Privileged Identity Management (PIM) 

### 📌 Core Concept
- PIM is used to provide **Just-in-Time (JIT)** and **temporary elevated access** instead of permanent high privileges.
- Users do NOT have continuous admin access; they must **request elevation when needed**.

---

### 👤 Default Access Model
- Every user/group has:
  - ✅ **Permanent Role:** Reader (baseline access)
  - ⬆️ **Eligible Role:** Subscription Owner (via PIM)
- Users can elevate their access from Reader → Owner **only when required**.   

---

### ⏱️ Time-Bound Access Control
- Elevated access is:
  - Limited to **maximum 8 hours** for application teams
  - Users can choose smaller duration (e.g., 30 mins)
- For platform/cloud team:
  - **Owner access → limited to 1 hour**
  - **Contributor access → up to 12 hours** 

---

### ✅ Approval Process
- **Application Teams:**
  - No strict approval required (pre-approved model in landing zones)
- **Platform Teams:**
  - Require **peer approval before elevation**
  - Done via:
    - Teams channel notification
    - Quick peer validation (manual but controlled)  

---

### 🔄 Access Flow
1. User requests PIM elevation
2. (If platform team) → Peer approval required
3. Access granted temporarily
4. After time expires → Access automatically reverts to Reader


---

### 🚫 Restrictions in RBAC via PIM
- Users are NOT allowed to:
  - Assign highly privileged roles (e.g., Owner at large scope, reservation roles)
  - Create custom roles freely but followed to  least privillege access, 
  if over permissive access is there CNAPP Compliance will gnerate the notification to eliminate the access
- Instead:
  - Platform team performs **assessment before granting custom roles**   

