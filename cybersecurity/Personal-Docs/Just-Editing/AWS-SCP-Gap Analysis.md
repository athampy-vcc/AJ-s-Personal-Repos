I’ll review the CIS mapping file against CIS 3.0 coverage patterns, then cross-check the current SCPs in the referenced `scps.tf` so I can propose only additive, practical policy gaps with clear “why this improves score” guidance.



Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/cybersecurity/Epics-Issues-handling/CIS/CIS-AWS-Prisma-Policy-Control-Mapping.md)

Fetched https://github.com/volvo-cars/mb-aws-infrastructure_as_code/blob/develop/bootstrap/shared-services/security/scps.tf

I’ve extracted the mapping successfully; next I’ll fetch the SCP source via raw GitHub URL so suggestions align with what is already enforced.



Fetched https://raw.githubusercontent.com/volvo-cars/mb-aws-infrastructure_as_code/develop/bootstrap/shared-services/security/scps.tf

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/cybersecurity/Personal-Docs/Just-Editing/SCP%27s-Explained.md)

I confirmed the SCP summary notes from your local explainer; now I’m pulling exact line anchors for key CIS 3.0 gaps so you get actionable, traceable recommendations.

Searched for regex `Manual check|Not supported|EC2.2|Config.1|KMS.4|CloudTrail.1|IAM.27|IAM.28|S3.1|RDS.2|RDS.13|EC2.8` (`**/cybersecurity/Epics-Issues-handling/CIS/CIS-AWS-Prisma-Policy-Control-Mapping.md`), 200 results

Searched for regex `root_policy1|root_policy2|root_policy3|root_policy4|greenfield_policy_common|greenfield_policy_corp|IMDSv2|Block manual IAM user creation|Block deletion of VPC flow logs|Block old storage types` (`**/cybersecurity/Personal-Docs/Just-Editing/SCP's-Explained.md`), 14 results

I have enough evidence now and will produce a prioritized gap list with CIS 3.0-aligned SCP additions; first I’ll quickly check memory so I can capture reusable recommendations for this repo.

Read memory

Created memory file [](file:///c%3A/Users/ATHAMPY/AppData/Roaming/Code/User/workspaceStorage/0d25b9ce87e105aa1be664203db29bf8/GitHub.copilot-chat/memory-tool/memories/repo/security-notes.md)

**Top Findings (CIS 3.0 Review, ordered by severity)**

1. High: Logging and alerting controls are still manual, so score uplift is inconsistent.
Evidence: CIS-AWS-Prisma-Policy-Control-Mapping.md, CIS-AWS-Prisma-Policy-Control-Mapping.md, CIS-AWS-Prisma-Policy-Control-Mapping.md
Impact: CIS 3.0 section 3.x style checks can remain failing across accounts because they depend on per-account setup.

2. High: S3 public exposure control is mapped to CIS 3.0, but not clearly enforced by the current SCP summary.
Evidence: CIS-AWS-Prisma-Policy-Control-Mapping.md, current SCP summary list: SCP's-Explained.md, SCP's-Explained.md
Impact: Public bucket misconfigurations can still happen and hurt CIS score quickly.

3. High: RDS hardening controls in CIS 3.0 appear uncovered by current SCP themes.
Evidence: public RDS: CIS-AWS-Prisma-Policy-Control-Mapping.md, minor upgrades: CIS-AWS-Prisma-Policy-Control-Mapping.md, current SCP themes: SCP's-Explained.md
Impact: 2.3.x failures are common and usually high-visibility in Security Hub/CNAPP.

4. Medium: IAM hardening controls introduced in later CIS versions are mapped, but not explicitly represented in your SCP summary.
Evidence: CloudShellFullAccess mapping: CIS-AWS-Prisma-Policy-Control-Mapping.md, Access Analyzer mapping: CIS-AWS-Prisma-Policy-Control-Mapping.md
Impact: Privilege escalation and external sharing risks remain harder to control centrally.

5. Medium: Network baseline controls (default SG lockdown, admin ports) may be partially detective-only today.
Evidence: default SG control mapping: CIS-AWS-Prisma-Policy-Control-Mapping.md, admin ports/NACL mapping: CIS-AWS-Prisma-Policy-Control-Mapping.md
Impact: recurring findings from temporary “open for testing” rules.

---

**Recommended Policies To Add (easy, high-value first)**

1. S3 Public Access Guardrail SCP
Scope: root or all workload OUs.
What to deny:
- disable Block Public Access at account or bucket level
- public ACL patterns on buckets/objects
- bucket policy changes that create broad public exposure (allow exceptions only for approved central roles)
CIS uplift: 2.1.4 and related S3 exposure findings.
Why easy: one SCP with explicit exception roles gives immediate prevention across accounts.

2. RDS Public Access Guardrail SCP
What to deny:
- create/modify DB with PubliclyAccessible=true
- internet-facing DB security group exposure for sensitive ports (if applicable)
CIS uplift: 2.3.3
Why easy: very direct deny conditions; low blast radius if exception roles are already standardized.

3. RDS Auto Minor Upgrade Enforcement SCP
What to deny:
- modify DB with AutoMinorVersionUpgrade=false
CIS uplift: 2.3.2
Why easy: one condition-based deny; prevents drift after creation.

4. IAM CloudShellFullAccess Restriction SCP
What to deny:
- attaching or creating policies that include AWSCloudShellFullAccess for non-approved principals
CIS uplift: 1.22
Why easy: policy attachment actions are centralized and straightforward to scope with role exceptions.

5. IAM Access Analyzer Protection SCP
What to deny:
- disabling/deleting analyzer resources outside security-admin roles
CIS uplift: 1.20
Why easy: protects a key detective control from accidental or malicious disablement.

6. Default Security Group/North-South Admin Port Guardrail SCP
What to deny:
- adding inbound rules to default SG
- opening 22/3389 to 0.0.0.0/0 and ::/0 except approved break-glass/network roles
CIS uplift: 5.1 and 5.4 family style controls
Why easy: very common pattern; immediate reduction in noisy findings.

---

**Security Gaps vs Fix Path (simple execution plan)**

1. Gap: Manual check rows for logging alarms.
Fix: deploy a centralized Terraform module (not SCP) for all required CloudWatch metric filters/alarms via StackSets.
Why this helps: closes a big chunk of “manual check” drift at org scale.

2. Gap: Data exposure controls are not fully preventive.
Fix: add S3 and RDS preventive SCPs above.
Why this helps: prevents creation of risky resources instead of cleaning up later.

3. Gap: IAM hygiene controls can be bypassed by policy attachment choices.
Fix: add CloudShell and Access Analyzer protection SCPs.
Why this helps: reduces privilege misuse and keeps key detective tooling alive.

4. Gap: Network misconfigurations reappear.
Fix: add default SG and admin-port deny SCPs.
Why this helps: blocks the most frequent accidental exposure patterns.

---

**Practical priority order for fastest score gain**

1. S3 Public Access Guardrail
2. RDS Public + Auto Minor Upgrade Guardrails
3. Default SG/Admin Port Guardrail
4. IAM CloudShell + Access Analyzer Protection
5. Centralized CloudWatch metric filter baseline (Terraform/StackSet, not SCP)

I could not directly fetch the private referenced scps.tf from GitHub in this environment, so I based the “already-covered vs missing” comparison on your local SCP analysis doc at SCP's-Explained.md. If you want, I can next draft ready-to-paste SCP statement blocks in Terraform style for each of the 5 SCP additions.