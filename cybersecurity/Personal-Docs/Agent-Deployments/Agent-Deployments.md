

## 1. Qualys Agent
**Location:** `bootstrap/shared-services/qualys-cloud-agent/`

**How it works — Lambda + SSM approach:**
- A **Lambda function** (`qualys_cloud_agent_rollout.py`) runs the deployment
- The Lambda connects to each EC2 instance using **AWS Systems Manager (SSM)** — no SSH or direct access needed
- It calls an SSM Document called `QualysCloudAgentSSMDocument` (hosted by Qualys in account `805950163170`) and passes credentials retrieved from **AWS Secrets Manager**
- It uses **10 parallel threads** to install across many EC2 instances at once
- Supports Windows, Linux, and macOS
- Uses **cross-account role assumption** to deploy into multiple AWS accounts from a central shared-services account

> **Simple terms:** A Lambda function wakes up, grabs Qualys credentials from a secret vault, then tells SSM "go install the Qualys agent on these machines using these credentials."

---

## 2. CloudWatch Agent
**Location:** `bootstrap/accounts/prod/*/eu-west-1/cloudwatch.tf`

**How it works — Terraform + SSM Associations:**
- Terraform creates two **SSM Associations** (think of these as recurring scheduled tasks on EC2 instances):
  1. **Install step** — uses the AWS-managed document `AWS-ConfigureAWSPackage` to install `AmazonCloudWatchAgent` daily
  2. **Configure step** — uses a custom SSM document (`PCE-AmazonCloudWatch-ManageAgentBasedOnOS`) to apply Linux/Windows-specific config stored in **SSM Parameter Store**
- Both run on a `rate(1 day)` schedule, targeting **all EC2 instances** in the account

> **Simple terms:** Terraform sets up a daily cron-like job that tells SSM: "make sure CloudWatch agent is installed and configured on every EC2 in this account." Separate configs exist for Linux and Windows.

---

## 3. Microsoft Defender for Cloud Agent
**Location:** `bootstrap/shared-services/ms-defender-for-cloud/`

**How it works — Terraform + CloudFormation + Azure Arc:**
- Terraform creates AWS-side **IAM roles** that Microsoft Defender needs to read/scan the AWS environment:
  - `DefenderForCloud-ArcAutoProvisioning` — deploys the **Azure Arc agent** on EC2 instances
  - `DefenderForCloud-DefenderForServers` — enables Defender for Servers protection
  - `DefenderForCloud-AgentlessScanner` — scans disks without needing an agent installed
- A **CloudFormation template** is deployed to wire up the AWS-to-Azure integration
- An `azurerm` Terraform provider manages the Azure side (connecting the AWS account to Microsoft Defender)
- Features enabled: CSPM, agentless scanning, sensitive data discovery, and CIEM (permissions management)

> **Simple terms:** Terraform sets up permission roles so Microsoft Defender (Azure-based) can "look into" the AWS account. It registers the AWS account with Azure via Arc, allowing Defender to scan and protect EC2 instances from the Azure side.

---

## Common Pattern Across All Three

| Mechanism | Qualys | CloudWatch | Defender |
|---|---|---|---|
| Deployment trigger | Lambda function | Terraform/SSM | Terraform/CloudFormation |
| Agent delivery | SSM Send Command | SSM Association | Azure Arc / Agentless |
| Credentials storage | Secrets Manager | Parameter Store | IAM Roles |
| Multi-account | Yes (role assumption) | Per-account TF | Yes (central shared-services) |
| Scheduling | Event-driven | Daily (`rate(1 day)`) | Continuous (Arc) |