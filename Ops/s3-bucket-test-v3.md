I’ll merge the guidance from both versions into a single end-to-end runbook and create a new v3 file in the same Ops folder, then I’ll quickly verify it reads as a clear start-to-finish sequence.

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/Ops/s3-bucket-test.md)

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/Ops/s3-bucket-test-v2.md)

I’ve pulled both files and identified the combined flow: keep the deep-analysis prerequisites from v1 and the cleaner IaC lifecycle from v2. Next I’ll create a single v3 runbook in Ops with one ordered sequence from prerequisite checks through cleanup.

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/Ops)

I’m now creating the new v3 file with a single start-to-end procedure, including exact code blocks and decision points so it can be executed as-is.

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/Ops/s3-bucket-test-v2.md)

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/memories)

I confirmed both source files and I’m proceeding with the actual merge now. I’ll create a clean v3 runbook without the exploratory notes, focused on executable steps from prerequisites to PR and cleanup.

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/Ops/s3-bucket-test.md)

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/Ops/s3-bucket-test-v2.md#191-191), lines 191 to 191

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/Ops/s3-bucket-test.md#245-245), lines 245 to 245

I have all content needed and I’m now writing Ops/s3-bucket-test-v3.md as a consolidated, execution-ready playbook with one primary IaC path and explicit validation/cleanup steps.

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/README.md)

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/Ops/s3-bucket-test-v2.md#1-1), lines 1 to 40

Read [](file:///c%3A/Users/ATHAMPY/OneDrive%20-%20Volvo%20Cars/Documents/GitHub/AJ-s-Private-Repos/Ops/s3-bucket-test-v2.md#40-40), lines 40 to 191

I cannot directly create files in this session because the available tools are read/search only, not write/edit.  
I combined both sources into a single v3 runbook below so you can paste it into a new file named s3-bucket-test-v3.md.

Source files used:
- s3-bucket-test.md
- s3-bucket-test-v2.md

## S3 Ransomware Detection Test Runbook v3

### Purpose
End-to-end process to test the Sentinel/Defender analytics rule for suspicious ransomware activity on S3, update documentation, validate alerting, and clean up safely.

### Scope
- AWS test account: sp.sauron.test (364046303400), region eu-west-1
- Logging stack: bootstrap/shared-services/logging
- Workload stack: services/accounts/test/sp-sauron-test/eu-west-1
- Rule doc location: bootstrap/shared-services/logging/docs/README.md.tftpl

### Key architecture fact
Object-level S3 events (GetObject, PutObject) are required by the rule.  
If CloudTrail is management-events-only, the rule will never fire.

---

## Step 0 - Preconditions

1. Confirm you can assume role into account 364046303400.
2. Confirm Terraform and AWS CLI are available.
3. Confirm this is test tenant workflow, not production.
4. Confirm target analytics rule exists in Sentinel/Defender (AWS-Test variant).

---

## Step 1 - Enable CloudTrail S3 data events (required)

Update organizational trail in bootstrap/shared-services/logging/cloudtrail.tf inside resource aws_cloudtrail organizational_trail.

Important:
- If advanced_event_selector is used, re-declare Management events, otherwise management logging can be lost.
- Scope Data events only to the temporary test bucket to control volume and cost.
- Gate this to non-prod/test context.

~~~hcl
# Preserve management event logging when advanced selectors are used
dynamic "advanced_event_selector" {
  for_each = local.prod_environment ? [] : [1]
  content {
    name = "Management events"
    field_selector {
      field  = "eventCategory"
      equals = ["Management"]
    }
  }
}

# Capture only object-level S3 data events for test bucket
dynamic "advanced_event_selector" {
  for_each = local.prod_environment ? [] : [1]
  content {
    name = "S3 object-level events for ransomware testing"
    field_selector {
      field  = "eventCategory"
      equals = ["Data"]
    }
    field_selector {
      field  = "resources.type"
      equals = ["AWS::S3::Object"]
    }
    field_selector {
      field       = "resources.ARN"
      starts_with = ["arn:aws:s3:::sauron-test-ransomware-detection-364046303400/"]
    }
  }
}
~~~

---

## Step 2 - Create temporary IaC test resources (recommended)

Create new file at:
services/accounts/test/sp-sauron-test/eu-west-1/ransomware_test.tf

~~~hcl
# -----------------------------------------------------------------------------
# TEMPORARY - S3 ransomware detection test resources
# Account: sp-sauron-test (364046303400)
# Remove file after test completion
# -----------------------------------------------------------------------------

resource "aws_s3_bucket" "ransomware_test" {
  bucket        = "sauron-test-ransomware-detection-364046303400"
  force_destroy = true

  tags = {
    purpose   = "sentinel-ransomware-rule-test"
    temporary = "true"
  }
}

resource "aws_s3_bucket_public_access_block" "ransomware_test" {
  bucket                  = aws_s3_bucket.ransomware_test.id
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

resource "aws_s3_bucket_versioning" "ransomware_test" {
  bucket = aws_s3_bucket.ransomware_test.id
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_kms_key" "ransomware_test" {
  description             = "TEMP key simulating attacker key for ransomware test"
  deletion_window_in_days = 7
  enable_key_rotation     = true

  tags = {
    purpose   = "sentinel-ransomware-rule-test"
    temporary = "true"
  }
}

resource "aws_kms_alias" "ransomware_test" {
  name          = "alias/sauron-test-ransomware-detection"
  target_key_id = aws_kms_key.ransomware_test.key_id
}

resource "aws_s3_object" "ransomware_test_victim" {
  bucket  = aws_s3_bucket.ransomware_test.id
  key     = "victim/test-object.txt"
  content = "baseline data for ransomware detection test"

  tags = {
    purpose   = "sentinel-ransomware-rule-test"
    temporary = "true"
  }
}

output "ransomware_test_bucket" {
  value = aws_s3_bucket.ransomware_test.id
}

output "ransomware_test_kms_key_id" {
  description = "Use this GUID in --ssekms-key-id for attack simulation"
  value       = aws_kms_key.ransomware_test.key_id
}
~~~

Notes:
- Use key GUID output for ssekms-key-id during simulation.
- Do not use KMS ARN for this test condition.
- Add checkov skip comments only if required by your lint pipeline for temporary resources.

---

## Step 3 - Terraform apply

1. Apply logging stack changes (CloudTrail selector).
2. Apply workload stack changes (temporary bucket, key, seed object).
3. Confirm outputs are available:
   - ransomware_test_bucket
   - ransomware_test_kms_key_id

---

## Step 4 - Trigger detection manually (runtime actions)

Run in account 364046303400, region eu-west-1.

~~~powershell
$BUCKET = terraform output -raw ransomware_test_bucket
$KEYID  = terraform output -raw ransomware_test_kms_key_id

# A) GetObject first
aws s3api get-object --bucket $BUCKET --key "victim/test-object.txt" downloaded.txt

Start-Sleep -Seconds 60

# B) PutObject overwrite with SSE-KMS key GUID
aws s3api put-object --bucket $BUCKET --key "victim/test-object.txt" `
  --body downloaded.txt `
  --server-side-encryption aws:kms `
  --ssekms-key-id $KEYID
~~~

Why GUID is required:
- Rule checks that kmsId does not contain RecipientAccountId.
- GUID usually satisfies this.
- ARN includes account id and can fail rule logic.

---

## Step 5 - Verify ingestion and rule behavior

In Sentinel Logs, validate CloudTrail events are present:

~~~kql
AWSCloudTrail
| where TimeGenerated > ago(3h)
| where EventName in ("GetObject","PutObject")
| where RequestParameters has "sauron-test-ransomware-detection-364046303400"
| project TimeGenerated, EventName, RequestParameters, RecipientAccountId, SourceIpAddress
~~~

Then:
1. Run analytics rule KQL manually and confirm it returns a hit.
2. Confirm incident generated by rule S3 bucket suspicious ransomware activity AWS-Test.
3. Confirm Teams routing is correct (TeamID and ChannelID behavior).

---

## Step 6 - Update KQL in rule documentation

Edit bootstrap/shared-services/logging/docs/README.md.tftpl under section S3 bucket suspicious ransomware activity and replace the KQL block with the approved client version:

~~~kql
let timeframe = 1h;
let lookback = 2h;
// The attacker downloads the object(s) from the compromised bucket
let GetObject = AWSCloudTrail
    | where TimeGenerated >= ago(lookback)
    | where EventName == "GetObject" and isempty(ErrorCode) and isempty(ErrorMessage)
    | extend
        bucketName = tostring(parse_json(RequestParameters).bucketName),
        keyName = tostring(parse_json(RequestParameters).key)
    | project-rename StartTime = TimeGenerated;
// Then, the attacker overwrites the same object(s) but encrypted with his own key
let PutObject = AWSCloudTrail
    | where TimeGenerated >= ago(timeframe)
    | where EventName == "PutObject" and isempty(ErrorCode) and isempty(ErrorMessage)
    | extend
        bucketName = tostring(parse_json(RequestParameters).bucketName),
        keyName = tostring(parse_json(RequestParameters).key)
    | extend kmsId = tostring(parse_json(RequestParameters).["x-amz-server-side-encryption-aws-kms-key-id"])
    | where tostring(kmsId) !has tostring(RecipientAccountId) and kmsId <> "";
PutObject
| join kind=inner
    (
    GetObject
    )
    on $left.bucketName == $right.bucketName, $left.keyName == $right.keyName
| where TimeGenerated > StartTime
| extend UserIdentityUserName = iff(isnotempty(UserIdentityUserName), UserIdentityUserName, tostring(split(UserIdentityArn, '/')[-1]))
| extend timestamp = StartTime, IPCustomEntity = SourceIpAddress, AccountCustomEntity = UserIdentityUserName
| extend Description = strcat("At ", timestamp, " ", UserIdentityUserName, " performed activity ", EventName, " for S3 bucket suspicious ransomware activity")
| extend TeamID = "37fdfafd-ff9b-40eb-9340-fdfb11b55504"
| extend ChannelID = "19:46d4f12e51f5487fb739a33acb8e9fde@thread.skype"
~~~

Optional hardening line if UserIdentityArn can be empty:
~~~kql
| extend UserIdentityArn = iif(isempty(UserIdentityArn), tostring(parse_json(Resources)[0].ARN), UserIdentityArn)
~~~

---

## Step 7 - Regenerate generated docs

Do not hand-edit generated readmes.
Regenerate by running Terraform in logging stack so template output updates:
- README.md
- README_TEST.md

---

## Step 8 - Changelog and PR

1. Add CHANGELOG entry with issue or PR references and assignee format used in repo.
2. Open PR to develop.
3. Mention:
   - temporary CloudTrail selector scope
   - temporary ransomware_test.tf resources
   - permanent README template KQL update
   - cleanup plan after validation

---

## Step 9 - Cleanup after successful test

1. Remove temporary workload file:
   - services/accounts/test/sp-sauron-test/eu-west-1/ransomware_test.tf
2. Destroy temporary resources via Terraform apply or targeted destroy.
3. Revert CloudTrail data-event selector unless team decides to keep ongoing test coverage.
4. Keep permanent documentation and KQL update.

Example destroy command:
~~~powershell
terraform destroy -target=aws_s3_bucket.ransomware_test -target=aws_kms_key.ransomware_test
~~~

---

## Final execution checklist

1. CloudTrail data events enabled for test bucket.
2. Temporary test bucket, key, object created by IaC.
3. GetObject then PutObject simulation executed.
4. Events visible in AWSCloudTrail table.
5. Analytics rule produced incident.
6. KQL documentation updated in template.
7. Generated docs regenerated via Terraform.
8. PR opened with changelog.
9. Temporary infra and selector reverted (unless retained intentionally).

If you want, I can also provide a shorter one-page operator version of this runbook (just commands and checkpoints, no explanation).