I'll explore the repository structure and analyze this SCP configuration for you.   

Searched codebase for "SCP service control policies AWS organizations", 12 results

Read [](file:///c%3A/Users/ajin.thampy/OneDrive%20-%20Volvo%20Cars/Volvo%20docs/Volvo-Cars-Infosys-Laptop/volvo-repos/mb-aws-infrastructure_as_code/bootstrap/shared-services/security/scps.tf#1-1), lines 1 to 100

Let me look at the organizational unit structure and how these policies are attached:  

Read [](file:///c%3A/Users/ajin.thampy/OneDrive%20-%20Volvo%20Cars/Volvo%20docs/Volvo-Cars-Infosys-Laptop/volvo-repos/mb-aws-infrastructure_as_code/bootstrap/shared-services/security/greenfield_ou.tf#1-1), lines 1 to 60

Read [](file:///c%3A/Users/ajin.thampy/OneDrive%20-%20Volvo%20Cars/Volvo%20docs/Volvo-Cars-Infosys-Laptop/volvo-repos/mb-aws-infrastructure_as_code/bootstrap/shared-services/security/README.md#1-1), lines 1 to 80

Perfect! Now I have all the context. Let me provide you with a comprehensive explanation.

## **Simple Explanation of SCPs in This Code**

### **What are SCPs?**
**Service Control Policies (SCPs)** are AWS's way of saying "you can't do this" across your entire organization. Think of them as guardrails that prevent certain actions, no matter what individual permissions a user has. Even if someone has admin access, an SCP can block them.

---

### **The Architecture at Volvo Cars**

The code creates a **hierarchical security model** with multiple organizational units (OUs):

```
AWS Organization Root
├── volvocars-core (shared infrastructure)
├── volvocars-shared (shared services)
├── volvocars-corp (corporate workloads)
├── volvocars-online (online workloads)
├── volvocars-sandbox (testing environment)
└── volvocars-containment (restricted environment)
```

---

### **What SCPs Are Being Added?**

The file creates **6 SCPs** that enforce security rules:

#### **1-4. Root Policies (Applied to ENTIRE Organization)**

**`root_policy1`** - Prevents Financial & Billing Damage
- ❌ Block billing changes (no payment methods, tax changes, regions blocking)
- ❌ Block expensive AI/ML services (Bedrock, SageMaker, etc.) 
- ✅ **Exceptions**: Platform admins, Marketplace admins (can use marketplace services)
- ✅ Allow emergency root access only from management account

**`root_policy2`** - Protects Core Security Infrastructure  
- ❌ Block security tool deletions (SecurityHub, GuardDuty, AWS Config, CloudTrail)
- ❌ Block IAM tampering (can't delete root access keys, SAML providers)
- ❌ Block organization changes (can't leave org or move accounts)
- ✅ **Exceptions**: Platform admins, AWS admins (the approved way to make changes)

**`root_policy3`** - Protects Infrastructure-as-Code Resources
- ❌ Block deletion of CloudFormation stacks/Lambda functions tagged with `managed_by=terraform`
- ❌ Block deletion of critical IAM roles (terraform roles, SSO roles)
- ❌ Block deletion of VPC flow logs
- ❌ Block old storage types (gp2 volumes, unencrypted RDS/Aurora/EFS)
- ❌ Block Route53 domain creation (must be centrally managed)
- ✅ **Exceptions**: Platform admins, Terraform CI/CD roles (automation is allowed)

**`root_policy4`** - Restricts IAM User Management
- ❌ Block manual IAM user creation (no one creates users manually)
- ❌ Block region access outside approved regions
- ✅ **Exceptions**: Platform admins, SmartFactory Lambda role (automated user provisioning), IAM cleanup roles

#### **5-6. Greenfield Policies (Applied to Specific OUs)**

**`greenfield_policy_common`** - Basic Security for Workloads  
- ❌ EC2 instances must use IMDSv2 (modern metadata service, not vulnerable to local attacks)
- Applied to: ALL workload OUs (core, corp, sandbox, online, shared, containment)

**`greenfield_policy_corp`** - Additional Controls for Corporate Workloads
- ❌ Block internet connectivity features (WAF, CloudFront, API Gateway public endpoints, VPN creation)
- ❌ Block S3 gateway VPC endpoints (must go through Terraform for route table management)
- ✅ **Exceptions**: Network admins, Platform admins (they can set up networking)

---

### **How It Works - The Role Exclusion System**

The code defines **role groups** that get exceptions:

```hcl
platform_administrator_roles          # Terraform CI/CD roles for automation
aws_administrator_managed_roles       # CloudFormation StackSet roles
network_administrator_roles           # Network team roles
finops_administrator_roles            # Cost/savings plan admins
terraform_cicd_roles                  # All three Terraform layers (bootstrap/foundation/services)
iam_cleanup_roles                     # Automated cleanup automation
```

**Example**: If `root_policy1` blocks billing changes, but the condition says `aws:PrincipalArn NOT LIKE "arn:aws:iam::*:role/terraform-bootstrap-cicd-role"`, then:
- ✅ Terraform CI/CD role **CAN** make billing changes
- ❌ Everyone else **CANNOT**

---

### **How It Applies to the Repository**

This is part of the **bootstrap layer** (`/bootstrap/` folder):

1. **Bootstrap layer** (this file) → Creates SCPs and applies them
2. **Foundation layer** (`/foundation/`) → Builds networking, security services, centralized logging
3. **Services layer** (`/services/`) → Deploys workload-specific resources
4. **Each uses Terraform CI/CD roles** that are exempted from certain SCPs

The SCPs create a "belt and suspenders" approach:
- **SCPs**: Organizational-level denials (can't be bypassed)
- **IAM boundaries**: Account-level limits on what roles can do
- **Terraform ownership**: Only Terraform can modify core infrastructure

---

### **Real-World Example**

Developer in `volvocars-corp` OU tries to create a public API endpoint:
1. ❌ `greenfield_policy_corp` has `"Deny: apigateway:CreateRestApi"` 
2. ❌ Developer gets **Access Denied** — even if their IAM role has full access
3. ✅ Network admin **can** do this (exception for `network_administrator_roles`)

Another example - automated cleanup system deletes unused IAM users:
1. ✅ `iam_cleanup_roles` is in exception list for `root_policy4`
2. ✅ Lambda function can run `iam:DeleteUser` successfully
3. ❌ Regular employees cannot (no exception in root_policy4)

---

This creates a **secure-by-default** architecture where risky actions are blocked at the organization level, while approved automation and administrative roles maintain the required functionality.