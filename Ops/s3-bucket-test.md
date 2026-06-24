I'll explore the workspace to understand how the existing analytics rules are structured before providing the steps and code.  

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/bootstrap/shared-services/logging)

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/bootstrap/shared-services/logging/README.md#1-1), lines 1 to 60

Searched for regex `Defender for Cloud|Analytics rule|ransomware|TeamID|ChannelID` (`**/bootstrap/shared-services/logging/**`), 20 results

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/bootstrap/shared-services/ms-defender-for-cloud)

The rule is already documented. Let me read the relevant section of the README template and understand how analytics rules are defined. 

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/bootstrap/shared-services/logging/docs/README.md.tftpl#980-980), lines 980 to 1200

Searched for regex `TeamID|ChannelID|IPCustomEntity|AccountCustomEntity|entityMappings|customDetails`, 20 results

Searched for regex `ransomware|TeamID|ChannelID` (`**/bootstrap/shared-services/logging/README_TEST.md`), 5 results

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/bootstrap/shared-services/logging/readme.tf)

Let me read the test README ransomware section and look at the example PR to understand the change pattern.  



Fetched https://github.com/volvo-cars/mb-aws-infrastructure_as_code/pull/2811/files

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/bootstrap/shared-services/logging/README_TEST.md#1084-1084), lines 1084 to 1140

Searched for regex `^#+ |Analytic|entity|EntityMapping|Custom Detail|Teams|notification` (`**/bootstrap/shared-services/logging/docs/README.md.tftpl`), 20 results

Searched for regex `^### |^## |Analytic|Scheduled|Alert rules|entityMappings|Entities` (`**/bootstrap/shared-services/logging/docs/README.md.tftpl`), 34 results

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/bootstrap/shared-services/logging/docs/README.md.tftpl#117-117), lines 117 to 230

I have enough context now. Let me quickly check the changelog convention and how the docs are regenerated.  

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/bootstrap/shared-services/logging/README.md#1-1), lines 1 to 60

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/CHANGELOG.md#1-1), lines 1 to 30

Let me verify whether the organizational CloudTrail captures S3 data events, since GetObject/PutObject are object-level events.   

Searched for regex `data_resource|event_selector|AWS::S3|data_event|advanced_event_selector|read_write_type` (`**/bootstrap/shared-services/logging/cloudtrail.tf`), no results

Searched for regex `event_selector|data_resource|S3|DataResource|include_management|advanced` (`**/bootstrap/shared-services/logging/cloudtrail.tf`), 3 results

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/bootstrap/shared-services/logging/cloudtrail.tf)

I now have a complete picture. Here's the important finding that shapes everything: the organizational CloudTrail in this repo logs **management events only** — it has no data event selector — so S3 `GetObject`/`PutObject` (object-level data events) are **not** currently captured and therefore never reach Sentinel/Defender. That has to be fixed for the rule to ever fire. Below is the full plan, the code, and exact locations.

## How the pieces fit together

- The analytics rule itself lives in **Microsoft Sentinel / Defender for Cloud** (Azure side), not in this repo. This repo is the **documentation source of truth** for the rule.
- The rule is already documented here: bootstrap/shared-services/logging/docs/README.md.tftpl under `#### S3 bucket suspicious ransomware activity`.
- README.md (prod) and README_TEST.md (test) are **auto-generated** from that `.tftpl` by readme.tf. Never edit the generated files directly — the header in README.md warns about this.
- Test account `sp.sauron.test` (`364046303400`) is a member of the AWS **test** org, so its CloudTrail flows into the central org trail bucket → Sentinel/Defender **test** tenant. Good for testing.
- The rule logic requires: `GetObject` (within `lookback = 2h`) then `PutObject` of the **same** bucket+key, where the `PutObject` is SSE‑KMS encrypted with a key id that does **not** contain the bucket owner account id (`kmsId !has RecipientAccountId`).

---

## Step 1 — Enable S3 data events on the org trail (required, otherwise the rule can never fire)

**Location:** bootstrap/shared-services/logging/cloudtrail.tf — add inside `resource "aws_cloudtrail" "organizational_trail"`.

⚠️ Adding `advanced_event_selector` **replaces** the default "all management events" behaviour, so you must re-declare a Management selector too, otherwise you lose management-event logging. Scope the data selector to **only the test bucket** to keep volume/cost negligible, and gate it to the test tenant.

```hcl
# Preserve existing management event logging (default behaviour) when
# advanced event selectors are used. Test tenant only.
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
# Needed so GetObject/PutObject reach Sentinel/Defender. Test tenant only.
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

`local.prod_environment` is already defined in readme.tf and is module-global, so it's usable here.

> Note: this is a deliberate trade-off — the central org trail currently does not record S3 data events anywhere. If the security team prefers not to alter the org trail, the alternative is a dedicated data-event trail, but its logs would land in a local bucket that Sentinel does **not** read, so the rule would not fire. The org-trail selector above is the only path that reaches the existing Sentinel/Defender pipeline.

---

## Step 2 — Create the test S3 bucket + KMS key in `364046303400`

These are throwaway test resources, so AWS CLI is cleanest (run after assuming a role in `sp.sauron.test`, region `eu-west-1`). If you prefer it codified, the persistent place would be a small `.tf` under a test workload folder, but CLI is recommended for an ephemeral test.

```powershell
$BUCKET = "sauron-test-ransomware-detection-364046303400"
$REGION = "eu-west-1"

# 1. Create the test bucket
aws s3api create-bucket --bucket $BUCKET --region $REGION `
  --create-bucket-configuration LocationConstraint=$REGION

# 2. Create a customer-managed KMS key (simulates the attacker's "own" key)
$KEYID = (aws kms create-key --description "Ransomware detection test key" `
  --query KeyMetadata.KeyId --output text)
Write-Host "KMS Key Id (GUID): $KEYID"

# 3. Seed a baseline object (normal, default encryption)
"baseline data" | Out-File -Encoding ascii test-object.txt
aws s3api put-object --bucket $BUCKET --key "victim/test-object.txt" --body test-object.txt
```

**Why the GUID matters:** in Step 3 you must reference the key by its **GUID** (`$KEYID`), not its full ARN. CloudTrail logs the value you pass as `x-amz-server-side-encryption-aws-kms-key-id`. A GUID does not contain `364046303400`, so the rule's `kmsId !has RecipientAccountId` condition is satisfied. (Passing the full ARN would contain the account id and the rule would **not** trigger.)

---

## Step 3 — Trigger the detection: GetObject, then PutObject (encrypted)

```powershell
# A) Attacker downloads the object  -> GetObject
aws s3api get-object --bucket $BUCKET --key "victim/test-object.txt" downloaded.txt

# (optional) small gap so PutObject TimeGenerated > GetObject StartTime
Start-Sleep -Seconds 60

# B) Attacker overwrites the SAME key, encrypted with "their own" KMS key (by GUID)
aws s3api put-object --bucket $BUCKET --key "victim/test-object.txt" `
  --body downloaded.txt `
  --server-side-encryption aws:kms `
  --ssekms-key-id $KEYID
```

Order and constraints that satisfy the KQL:
- `GetObject` before `PutObject`, same `bucketName` + `keyName`.
- `PutObject` uses SSE‑KMS with a key id not containing the account id.
- Both within the windows (`PutObject` ≤ 1h, `GetObject` ≤ 2h). The rule's `queryPeriod`/`queryFrequency` is `P1D`, so allow time for CloudTrail delivery (typically up to ~15 min to the bucket) plus Sentinel/Defender ingestion before it evaluates.

---

## Step 4 — Verify ingestion and the alert

1. Confirm the events landed in Log Analytics (Sentinel **Logs**), scoped to your bucket:
   ```kql
   AWSCloudTrail
   | where TimeGenerated > ago(3h)
   | where EventName in ("GetObject","PutObject")
   | where RequestParameters has "sauron-test-ransomware-detection-364046303400"
   | project TimeGenerated, EventName, RequestParameters, RecipientAccountId, SourceIpAddress
   ```
   If nothing shows, data events aren't flowing yet — recheck Step 1 was applied/deployed.
2. Run the rule's KQL manually in **Logs** to confirm it returns a row.
3. Confirm the scheduled analytics rule **"S3 bucket suspicious ransomware activity AWS-Test"** produces an incident.
4. Per the doc's requirement, alerts are communicated via the AWS Notifications Teams channel — that's exactly the `TeamID`/`ChannelID` the client added (see Step 5).

---

## Step 5 — Update the rule documentation (the "update with any changes" part)

**Location:** bootstrap/shared-services/logging/docs/README.md.tftpl, inside `#### S3 bucket suspicious ransomware activity`. Replace the existing ```kql ... ``` block (currently lines ~1118–1133, ending `| extend timestamp = StartTime`) with the client's version:

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

Notes:
- I kept the `UserIdentityArn` reference exactly as the client gave it. If you want to be defensive (some data events have an empty `UserIdentityArn`), add this line before the `UserIdentityUserName` extend, matching the pattern used by the other rules in this file: `| extend UserIdentityArn = iif(isempty(UserIdentityArn), tostring(parse_json(Resources)[0].ARN), UserIdentityArn)`.
- I fixed two cosmetic spacing issues in the client's `strcat` (`"...UserName,"` → `" performed activity "` / `" for S3..."`) so the alert text reads correctly. Confirm with the client if they want the literal original spacing.

**Do not edit** README.md or README_TEST.md — regenerate them by running `terraform apply` (or `terraform plan` + the `local_file` resources) in bootstrap/shared-services/logging, exactly as PR #2811 did.

---

## Step 6 — Changelog, PR, and cleanup

1. Add an entry to the root CHANGELOG.md following the existing format (issue/PR links, assignee).
2. Open the PR against `develop` (the existing-rules link your manager gave points at the `develop` branch), referencing the rule name and PR #2811 as the pattern.
3. The actual Sentinel rule change (the new KQL with entity mappings + Teams `TeamID`/`ChannelID`) must be applied on the Azure/Sentinel side by the CDC/security team — this repo only documents it.
4. Tear down test resources when done:
   ```powershell
   aws s3 rm "s3://$BUCKET" --recursive
   aws s3api delete-bucket --bucket $BUCKET --region $REGION
   aws kms schedule-key-deletion --key-id $KEYID --pending-window-in-days 7
   ```
   Also revert the Step 1 CloudTrail selector once testing is complete if it was only meant to be temporary (or keep it if the team wants ongoing detection coverage in test).

---

### Summary of files to change
| Purpose | File | Action |
|---|---|---|
| Capture S3 data events so rule can fire | bootstrap/shared-services/logging/cloudtrail.tf | Add scoped `advanced_event_selector` blocks (Step 1) |
| Update rule KQL doc | bootstrap/shared-services/logging/docs/README.md.tftpl | Replace ransomware KQL block (Step 5) |
| Regenerated docs | README.md / README_TEST.md | Auto-generated via `terraform apply` — do not hand-edit |
| Changelog | CHANGELOG.md | Add entry |
| Test bucket / KMS / events | account `364046303400` | CLI (Steps 2–3), no repo code |

Want me to apply any of these edits now (you said not to for the moment), or draft the PR description text?