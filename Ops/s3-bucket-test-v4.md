# S3 Ransomware Detection Test Runbook (v4)

> Single, execution-ready runbook to test the Sentinel/Defender analytics rule
> **"S3 bucket suspicious ransomware activity"**, keep the repo documentation in
> sync, validate alerting, and clean up safely.
>
> This v4 supersedes v3. It fixes the KMS GUID/ARN risk, completes the cleanup
> resource list, and makes the documentation update conditional.

---

## Scope & key facts

| Item | Value |
| --- | --- |
| AWS test account | `sp.sauron.test` (`364046303400`), region `eu-west-1` |
| Logging stack | `bootstrap/shared-services/logging` |
| Workload stack | `services/accounts/test/sp-sauron-test/eu-west-1` |
| Rule doc (template) | `bootstrap/shared-services/logging/docs/README.md.tftpl` |
| Generated docs (do NOT hand-edit) | `README.md` (prod), `README_TEST.md` (test) |

**Critical architecture facts (read before starting):**

1. Object-level S3 events (`GetObject`, `PutObject`) are **required** by the rule.
   The organizational CloudTrail today logs **management events only**, so these
   events are not captured. Without Step 1 the rule can never fire.
2. The analytics rule itself lives in **Microsoft Sentinel / Defender for Cloud
   (Azure side)**, deployed by the CDC / security team. This repo only
   **documents** the rule. Editing the `.tftpl` does **not** deploy a rule.
3. The rule fires only when the logged KMS key id does **not** contain the bucket
   owner account id (`kmsId !has RecipientAccountId`). This is the most fragile
   part of the test — see Step 4.

---

## Decision: is this a rule change or a test of the existing rule?

Confirm this **before** you start, because it decides whether Steps 6–7 apply.

- **Test existing rule only** → do Steps 0–5, 8, 9. **Skip Steps 6 and 7.**
- **Actual rule change is being adopted** (new KQL with `Description`,
  `IPCustomEntity`, `AccountCustomEntity`, `TeamID`, `ChannelID`) → do all steps,
  but run **Step 6 last**, after the rule is approved/deployed in Sentinel.

---

## Step 0 — Preconditions

1. You can assume a role into account `364046303400`.
2. Terraform and AWS CLI are installed and authenticated.
3. You are operating against the **test** tenant, not production.
4. The target analytics rule exists in Sentinel/Defender (AWS-Test variant).
5. You have confirmed the rule-change-vs-test decision above.

---

## Step 1 — Enable CloudTrail S3 data events (required)

**File:** `bootstrap/shared-services/logging/cloudtrail.tf`, inside
`resource "aws_cloudtrail" "organizational_trail"`.

> ⚠️ Adding `advanced_event_selector` **replaces** the default "all management
> events" behaviour. You must re-declare a Management selector, or management
> logging is lost. Scope the data selector to the test bucket only, and gate it
> to non-prod.

```hcl
# Preserve management event logging when advanced selectors are used. Test only.
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

# Capture S3 object-level (data) events ONLY for the ransomware test bucket.
dynamic "advanced_event_selector" {
  for_each = local.prod_environment ? [] : [1]
  content {
    name = "S3 object-level events for ransomware detection testing"
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
```

`local.prod_environment` is already defined in the logging stack and is usable here.

---

## Step 2 — Create temporary IaC test resources

**New file:** `services/accounts/test/sp-sauron-test/eu-west-1/ransomware_test.tf`

A dedicated file makes cleanup trivial (delete file → apply destroys it). The
stack's default provider already assumes the role in `364046303400`.

```hcl
# -----------------------------------------------------------------------------
# TEMPORARY — S3 ransomware detection rule test. REMOVE after testing.
# Account: sp-sauron-test (364046303400). All resources are force-destroyable.
# -----------------------------------------------------------------------------

resource "aws_s3_bucket" "ransomware_test" {
  bucket        = "sauron-test-ransomware-detection-364046303400"
  force_destroy = true # allow destroy even when (versioned) objects exist

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

# Attacker-controlled KMS key. Reference by GUID/alias at PutObject time.
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

# Baseline "victim" object the attacker will download then overwrite.
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
  description = "Use this GUID as --ssekms-key-id when simulating the attack"
  value       = aws_kms_key.ransomware_test.key_id
}
```

> If your lint/security pipeline (e.g. checkov) flags the test bucket for missing
> access logging / default encryption, add `#checkov:skip=...` comments matching
> the style already used in `cloudtrail.tf`. These are short-lived test resources.

---

## Step 3 — Terraform apply

1. Apply the **logging stack** change (CloudTrail selector) via its pipeline.
2. Apply the **workload stack** change (bucket, key, alias, seed object).
3. Confirm outputs are available:
   - `ransomware_test_bucket`
   - `ransomware_test_kms_key_id`

---

## Step 4 — Trigger the detection (runtime actions, manual)

Run with credentials/role for account `364046303400`, region `eu-west-1`.

```powershell
$BUCKET = terraform output -raw ransomware_test_bucket
$KEYID  = terraform output -raw ransomware_test_kms_key_id

# A) Attacker download -> GetObject
aws s3api get-object --bucket $BUCKET --key "victim/test-object.txt" downloaded.txt

# Small gap so PutObject TimeGenerated > GetObject StartTime
Start-Sleep -Seconds 60

# B) Attacker overwrite, SSE-KMS with the "attacker" key (by GUID, not ARN)
aws s3api put-object --bucket $BUCKET --key "victim/test-object.txt" `
  --body downloaded.txt `
  --server-side-encryption aws:kms `
  --ssekms-key-id $KEYID
```

> ⚠️ **KMS key id caveat — read this before concluding pass/fail.**
> The rule fires only when `kmsId !has RecipientAccountId`. Because the key and
> bucket are both in `364046303400`, success depends on what CloudTrail actually
> logs for `x-amz-server-side-encryption-aws-kms-key-id`:
>
> - Logs the **GUID** → no account id → rule fires. ✅
> - Logs the **full key ARN** → contains `364046303400` → rule does **not** fire. ❌
>
> **Do not declare the test failed without checking the logged value in Step 5.**
> If the full ARN is logged, retry with the **alias**
> (`--ssekms-key-id alias/sauron-test-ransomware-detection`), or use a KMS key in
> a **different account** to faithfully mimic cross-account ransomware.

---

## Step 5 — Verify ingestion and rule behaviour

In Sentinel **Logs**, confirm events arrived and inspect the logged `kmsId`:

```kql
AWSCloudTrail
| where TimeGenerated > ago(3h)
| where EventName in ("GetObject","PutObject")
| where RequestParameters has "sauron-test-ransomware-detection-364046303400"
| extend kmsId = tostring(parse_json(RequestParameters).["x-amz-server-side-encryption-aws-kms-key-id"])
| project TimeGenerated, EventName, kmsId, RecipientAccountId, SourceIpAddress
```

Confirm in order:

1. Both `GetObject` and `PutObject` events are present.
2. **`kmsId` does not contain `RecipientAccountId`** (otherwise revisit Step 4).
3. Running the rule's KQL manually returns a hit.
4. The scheduled rule **"S3 bucket suspicious ransomware activity AWS-Test"**
   generates an incident.
5. Teams routing (`TeamID` / `ChannelID`) behaves as expected.

> Allow for CloudTrail delivery (typically up to ~15 min) plus Sentinel/Defender
> ingestion before the rule evaluates (`queryFrequency`/`queryPeriod` is `P1D`).

---

## Step 6 — Update rule documentation (CONDITIONAL; do this LAST)

Only if the rule is **actually being changed and is approved/deployed in Sentinel**.
If you are only testing the existing rule, **skip Steps 6 and 7**.

**File:** `bootstrap/shared-services/logging/docs/README.md.tftpl`, under
`#### S3 bucket suspicious ransomware activity`. Replace the existing ```` ```kql ````
block with the approved client version:

```kql
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
```

Optional hardening (some data events have an empty `UserIdentityArn`) — add before
the `UserIdentityUserName` extend, matching the pattern used by other rules:

```kql
| extend UserIdentityArn = iif(isempty(UserIdentityArn), tostring(parse_json(Resources)[0].ARN), UserIdentityArn)
```

> The `TeamID` / `ChannelID` values match the existing **AWS Notifications** Teams
> channel already referenced in this template — they are correct, not arbitrary.

---

## Step 7 — Regenerate generated docs (only if Step 6 was done)

Do **not** hand-edit `README.md` / `README_TEST.md`. Run Terraform in the logging
stack so the `local_file` resources regenerate them from the template.

---

## Step 8 — Changelog and PR

1. Add a `CHANGELOG.md` entry using the repo's issue/PR + assignee format.
2. Open a PR to `develop`.
3. In the PR description, clearly label:
   - **Temporary:** CloudTrail data-event selector (Step 1).
   - **Temporary:** `ransomware_test.tf` resources (Step 2).
   - **Permanent (if applicable):** README template KQL update (Step 6).
   - Cleanup plan after validation (Step 9).
   - Reminder that the live rule is deployed on the Sentinel/Defender side.

---

## Step 9 — Cleanup after successful test

Preferred: **delete the file and let the pipeline destroy it.**

1. Delete `services/accounts/test/sp-sauron-test/eu-west-1/ransomware_test.tf`
   and run the normal pipeline apply.

Alternative: **targeted destroy of all test resources** (only if you cannot use
the file-delete flow):

```powershell
terraform destroy `
  -target=aws_s3_object.ransomware_test_victim `
  -target=aws_s3_bucket_versioning.ransomware_test `
  -target=aws_s3_bucket_public_access_block.ransomware_test `
  -target=aws_kms_alias.ransomware_test `
  -target=aws_s3_bucket.ransomware_test `
  -target=aws_kms_key.ransomware_test
```

> ⚠️ Do **NOT** run an untargeted `terraform destroy` on this stack — it also
> contains real IAM/OIDC/GitHub/backend resources.

2. Revert the Step 1 CloudTrail data-event selector (unless the team decides to
   keep ongoing test coverage).
3. Keep the documentation/KQL update **only** if the rule change was adopted.
4. The KMS key enters its 7-day deletion window; the bucket and objects are
   removed immediately via `force_destroy`.

---

## Final execution checklist

- [ ] Decided: rule change vs. test of existing rule.
- [ ] CloudTrail data events enabled for the test bucket (Step 1).
- [ ] Temporary bucket, key, alias, seed object created by IaC (Steps 2–3).
- [ ] `GetObject` then `PutObject` simulation executed (Step 4).
- [ ] Events visible in `AWSCloudTrail`, **`kmsId` has no account id** (Step 5).
- [ ] Analytics rule produced an incident; Teams routing verified (Step 5).
- [ ] (If rule change) README template KQL updated and docs regenerated (Steps 6–7).
- [ ] PR opened with changelog and temporary/permanent items labelled (Step 8).
- [ ] Temporary infra and CloudTrail selector removed/reverted (Step 9).
