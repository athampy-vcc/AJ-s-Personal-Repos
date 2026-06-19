# CIS Microsoft Azure Benchmark — Control Coverage Across Versions

Comparison of which security policies (controls) appear in each CIS Azure Benchmark version, as exported from the CNAPP tool. Use this to decide which version to adopt and to spot controls that disappear in newer versions.

> **Important interpretation note:** These counts reflect the policies the CNAPP tool has mapped/implemented for each benchmark version — not necessarily the full official CIS benchmark scope. A blank cell means the tool does not associate that policy with that version (the control may be absent, renamed, moved to Level 2, or simply not yet mapped by the tool). Validate any critical gap against the official CIS PDF for that version before deciding.

**Legend:** ✓ = policy present in that version · (blank) = not present / not mapped

## Policy count by version

| Metric | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Policies present | 69 | 77 | 74 | 49 | 52 | 53 | 56 | 34 | 39 |

Total distinct policies across all versions: **103**

## Control coverage matrix

### App Service

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure App Service Web app authentication is off | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure App Service Web app client certificate is disabled | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure App Service Web app doesn't have a Managed Service Identity | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |  |
| Azure App Service Web app doesn't redirect HTTP to HTTPS | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure App Service Web app doesn't use HTTP 2.0 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure App Service Web app doesn't use latest .Net Core version | ✓ |  |  |  |  |  |  |  |  |
| Azure App Service Web app doesn't use latest Java version | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |  |
| Azure App Service Web app doesn't use latest PHP version | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |  |
| Azure App Service Web app doesn't use latest Python version | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |  |
| Azure App Service Web app doesn't use latest TLS version | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure App Service basic authentication enabled |  |  |  |  |  |  | ✓ |  |  |
| Azure App Services FTP deployment is All allowed | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure App Services Remote debugging is enabled |  |  |  |  |  |  | ✓ |  |  |
| Azure Microsoft Defender for Cloud is set to Off for App Service | ✓ | ✓ | ✓ |  |  |  |  |  |  |

### Compute / VM

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure VM OS disk is encrypted with the default encryption key instead of ADE/CMK | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure VM data disk is encrypted with the default encryption key instead of ADE/CMK | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Virtual Machine (Windows) secure boot feature is disabled |  |  |  |  |  | ✓ | ✓ |  |  |
| Azure Virtual Machine vTPM feature is disabled |  |  |  |  |  | ✓ | ✓ |  |  |
| Azure Virtual Machines are not utilising Managed Disks | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure Virtual machine scale sets are not utilising Managed Disks | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure disk data access authentication mode not enabled |  |  |  |  |  |  | ✓ |  |  |
| Azure disk is unattached and is encrypted with the default encryption key instead of ADE/CMK | ✓ | ✓ | ✓ |  |  |  |  |  |  |

### Containers

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure AKS enable role-based access control (RBAC) not enforced | ✓ | ✓ | ✓ |  |  |  |  |  |  |

### Database - Other

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure Cosmos DB key based authentication is enabled |  |  |  |  | ✓ | ✓ | ✓ |  |  |

### Database - SQL

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure Activity log alert for Create or update SQL server firewall rule does not exist | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Activity log alert for Delete SQL server firewall rule does not exist | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Microsoft Defender for Cloud is set to Off for Azure SQL Databases | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Microsoft Defender for Cloud is set to Off for SQL servers on machines |  | ✓ | ✓ |  |  |  |  |  |  |
| Azure MySQL database flexible server SSL enforcement is disabled |  |  |  |  |  |  | ✓ |  |  |
| Azure MySQL database flexible server using insecure TLS version |  |  |  | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure PostgreSQL flexible server secure transport parameter is disabled |  |  |  |  |  |  | ✓ |  |  |
| Azure SQL Server ADS Vulnerability Assessment 'Send scan reports to' is not configured | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure SQL Server ADS Vulnerability Assessment Periodic recurring scans is disabled | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure SQL Server ADS Vulnerability Assessment is disabled | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure SQL Server allow access to any Azure internal resources | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure SQL Server audit log retention is less than 91 days | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure SQL Server auditing is disabled | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure SQL database Transparent Data Encryption (TDE) encryption disabled | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure SQL server Defender setting is set to Off | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure SQL server TDE protector is not encrypted with BYOK (Use your own key) | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure SQL server public network access setting is enabled |  |  |  |  |  |  | ✓ |  |  |

### Defender for Cloud

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure Microsoft Defender for Cloud MCAS integration Disabled | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Microsoft Defender for Cloud WDATP integration Disabled | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Microsoft Defender for Cloud is set to Off for Servers | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Microsoft Defender for Cloud security alert email notifications is not set | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Microsoft Defender for Cloud security contact additional email is not set | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Microsoft Defender for Cloud system updates monitoring is set to disabled |  |  |  |  | ✓ | ✓ | ✓ |  |  |

### Identity / Entra ID

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure AD Users can consent to apps accessing company data on their behalf is enabled | ✓ | ✓ |  | ✓ |  |  |  |  | ✓ |
| Azure Active Directory Guest users found | ✓ | ✓ | ✓ |  |  |  |  |  | ✓ |
| Azure Active Directory MFA is not enabled for user |  |  |  |  |  |  |  | ✓ | ✓ |
| Azure Active Directory Security Defaults is disabled | ✓ | ✓ |  | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Microsoft Entra ID account lockout duration less than 60 seconds |  |  |  |  |  |  | ✓ | ✓ | ✓ |
| Azure Microsoft Entra ID account lockout threshold greater than 10 |  |  |  |  |  |  | ✓ | ✓ | ✓ |
| Azure SQL server not configured with Active Directory admin authentication | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |
| Azure subscription permission for Microsoft Entra tenant is set to 'Allow everyone' |  |  |  |  |  |  |  |  | ✓ |

### Key Vault

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure Key Vault Key has no expiration date (Non-RBAC Key vault) | ✓ | ✓ | ✓ |  | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Key Vault Key has no expiration date (RBAC Key vault) | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Key Vault Purge protection is not enabled |  |  |  |  |  |  |  |  | ✓ |
| Azure Key Vault audit logging is disabled |  | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Key Vault is not recoverable | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |
| Azure Key Vault secret has no expiration date (Non-RBAC Key vault) | ✓ | ✓ | ✓ |  | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Key Vault secret has no expiration date (RBAC Key vault) | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Key vaults diagnostics logs are disabled | ✓ | ✓ | ✓ | ✓ |  |  |  |  |  |
| Azure Microsoft Defender for Cloud is set to Off for Key Vault | ✓ | ✓ | ✓ |  |  |  |  |  |  |

### Logging & Monitoring

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure Activity log alert for Create or update security solution does not exist | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Activity log alert for Create policy assignment does not exist | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Activity log alert for Delete security solution does not exist | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Activity log alert for delete policy assignment does not exist |  | ✓ |  | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Monitor Diagnostic Setting does not captures appropriate categories |  | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Monitoring log profile is not configured to export activity logs |  | ✓ | ✓ | ✓ |  |  |  |  |  |

### Networking

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure Activity log alert for Create or update network security group does not exist | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |  |
| Azure Activity log alert for Create or update network security group rule does not exist | ✓ | ✓ | ✓ |  |  |  |  |  | ✓ |
| Azure Activity log alert for Create or update public IP address rule does not exist |  |  |  |  | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Activity log alert for Delete network security group does not exist | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Activity log alert for Delete network security group rule does not exist | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Activity log alert for Delete public IP address rule does not exist |  |  |  |  | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Application Gateway is configured with SSL policy having TLS version 1.1 or lower |  |  |  |  |  |  |  |  | ✓ |
| Azure Load Balancer diagnostics logs are disabled | ✓ | ✓ | ✓ | ✓ |  |  |  |  |  |
| Azure Network Security Group allows all traffic on RDP Port 3389 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Network Security Group allows all traffic on SSH port 22 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Network Security Group having Inbound rule overly permissive to HTTP(S) traffic |  |  |  |  | ✓ | ✓ |  |  |  |
| Azure Network Security Group having Inbound rule overly permissive to all traffic on UDP protocol |  | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Network Watcher Network Security Group (NSG) flow logs retention is less than 90 days | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Virtual Network subnet is not configured with a Network Security Group |  |  |  |  |  |  |  |  | ✓ |

### Storage

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure Microsoft Defender for Cloud is set to Off for Storage | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Storage Account 'Trusted Microsoft Services' access not enabled | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Storage Account Container with activity log has BYOK encryption disabled |  | ✓ | ✓ |  |  |  |  |  |  |
| Azure Storage Account default network access is set to 'Allow' | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Storage Account using insecure TLS version |  |  |  | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Storage Account without Secure transfer enabled | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Storage account Encryption Customer Managed Keys Disabled | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Storage account container storing activity logs is publicly accessible |  | ✓ | ✓ | ✓ | ✓ |  |  |  |  |
| Azure Storage account containing VHD OS disk is not encrypted with CMK |  | ✓ | ✓ |  |  |  |  |  |  |
| Azure Storage account is not configured with private endpoint connection |  |  |  | ✓ | ✓ | ✓ |  |  |  |
| Azure Storage account not configured with SAS expiration policy |  |  |  |  |  |  | ✓ |  |  |
| Azure Storage account soft delete is disabled | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Storage account with cross tenant replication enabled |  |  |  |  |  | ✓ | ✓ | ✓ | ✓ |
| Azure storage account has a blob container with public access | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |

### Subscription / RBAC

| Control / Policy | v1.2.0 | v1.3.0 | v1.3.1 | v1.5.0 | v2.0.0 | v2.1.0 | v3.0.0 | v4.0.0 | v5.0.0 |
|---|---|---|---|---|---|---|---|---|---|
| Azure Custom Role Administering Resource Locks not assigned | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure Microsoft Defender for Cloud email notification for subscription owner is not set | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Azure Resource Group does not have a resource lock | ✓ | ✓ | ✓ |  |  |  |  |  |  |
| Azure SQL Server ADS Vulnerability Assessment 'Also send email notifications to admins and subscription owners' is disabled | ✓ | ✓ | ✓ | ✓ | ✓ |  |  |  |  |
| Azure subscriptions with custom roles are overly permissive | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |

## Analysis & recommendation

### How coverage trends across versions

The number of mapped policies rises through the v1.x line (peaking at v1.3.0 with 77), then drops sharply from v1.5.0 onward, reaching its lowest at v4.0.0 (34) before recovering slightly at v5.0.0 (39). This shape is characteristic of *tool mapping maturity* rather than benchmark scope: newer benchmarks are typically mapped by the CNAPP vendor more conservatively and progressively, while older benchmarks have had years to accumulate mapped checks. Treat the high v1.3.x counts with caution — many of those are legacy controls CIS itself has since restructured.

### Stable core controls (present in every version, 18)

These are the controls the tool maps consistently across all nine versions — the dependable baseline regardless of which version you pick:

- Azure Activity log alert for Create or update SQL server firewall rule does not exist
- Azure Activity log alert for Create or update security solution does not exist
- Azure Activity log alert for Create policy assignment does not exist
- Azure Activity log alert for Delete SQL server firewall rule does not exist
- Azure Activity log alert for Delete network security group does not exist
- Azure Activity log alert for Delete security solution does not exist
- Azure Key Vault Key has no expiration date (RBAC Key vault)
- Azure Key Vault secret has no expiration date (RBAC Key vault)
- Azure Microsoft Defender for Cloud email notification for subscription owner is not set
- Azure Microsoft Defender for Cloud security alert email notifications is not set
- Azure Microsoft Defender for Cloud security contact additional email is not set
- Azure Network Security Group allows all traffic on RDP Port 3389
- Azure Network Security Group allows all traffic on SSH port 22
- Azure Storage Account default network access is set to 'Allow'
- Azure Storage Account without Secure transfer enabled
- Azure Storage account soft delete is disabled
- Azure storage account has a blob container with public access
- Azure subscriptions with custom roles are overly permissive

### Controls in v3.0.0 but NOT in v5.0.0 (25)

If you move to the newest mapped version (v5.0.0), these v3.0.0 controls are no longer flagged by the tool. Review whether each is genuinely retired by CIS or just not yet mapped, and add compensating custom policies where the risk matters to you:

- Azure Activity log alert for Create or update network security group does not exist
- Azure App Service Web app doesn't redirect HTTP to HTTPS
- Azure App Service Web app doesn't use HTTP 2.0
- Azure App Service Web app doesn't use latest TLS version
- Azure App Service basic authentication enabled
- Azure App Services FTP deployment is All allowed
- Azure App Services Remote debugging is enabled
- Azure Cosmos DB key based authentication is enabled
- Azure Key Vault is not recoverable
- Azure Microsoft Defender for Cloud system updates monitoring is set to disabled
- Azure MySQL database flexible server SSL enforcement is disabled
- Azure MySQL database flexible server using insecure TLS version
- Azure PostgreSQL flexible server secure transport parameter is disabled
- Azure SQL Server allow access to any Azure internal resources
- Azure SQL Server audit log retention is less than 91 days
- Azure SQL Server auditing is disabled
- Azure SQL database Transparent Data Encryption (TDE) encryption disabled
- Azure SQL server not configured with Active Directory admin authentication
- Azure SQL server public network access setting is enabled
- Azure Storage account not configured with SAS expiration policy
- Azure Virtual Machine (Windows) secure boot feature is disabled
- Azure Virtual Machine vTPM feature is disabled
- Azure Virtual Machines are not utilising Managed Disks
- Azure Virtual machine scale sets are not utilising Managed Disks
- Azure disk data access authentication mode not enabled

### Recommendation

**For the most current security posture, adopt v5.0.0** — it is the latest CIS Azure Foundations Benchmark and includes net-new controls the older versions lack: Key Vault purge protection, Application Gateway TLS 1.1-or-lower detection, subnet-without-NSG detection, and the Entra tenant 'Allow everyone' permission check.

However, **do not rely on v5.0.0 mapping alone**. Because the tool maps far fewer policies to v5.0.0 (39) than to v3.0.0 (56), running only v5.0.0 in the CNAPP would leave several meaningful checks unmonitored — notably much of the SQL auditing/TDE family, App Service hardening (HTTP→HTTPS redirect, latest TLS, FTP deployment), VM/disk encryption with CMK, and several Defender-for-Cloud plan checks.

**Practical approach:**

1. Set **v5.0.0 as your primary baseline** for reporting and compliance alignment.
2. **Also enable v3.0.0** (or v2.1.0) in parallel to retain coverage of the SQL, App Service, VM-encryption, and Defender controls that v5.0.0 mapping currently omits.
3. For any control in the "in v3.0.0 but not v5.0.0" list above that matters to your environment, confirm against the official CIS v5.0.0 PDF whether it was truly removed or merged — and if it's still relevant, add a **custom policy** so it stays monitored.

Avoid the v1.x versions as a primary baseline: although they show the highest policy counts, they are the oldest benchmarks and contain controls CIS has since deprecated or restructured.