Good question — yes, doing it fully as IaC changes the approach, and it actually makes the "remove everything after testing" requirement much cleaner (a single `terraform destroy`). Let me check where account-scoped resources for the test tenant live in this repo so I point you to the right place.  

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/services/accounts)

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/config/test)

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/services/accounts/test)

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/config/test/accounts)

There's a dedicated `sp-sauron-test` workload folder. Let me look at its structure to match conventions.   

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/services/accounts/test/sp-sauron-test)

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/services/accounts/test/sp-sauron-test/eu-west-1)

There's already an s3.tf and a full Terraform stack for `sp-sauron-test`. Let me read the existing files to match conventions exactly.     

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/services/accounts/test/sp-sauron-test/eu-west-1/s3.tf)

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/services/accounts/test/sp-sauron-test/eu-west-1/providers.tf)

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/services/accounts/test/sp-sauron-test/eu-west-1/main.tf)

Read [](file:///c%3A/Github/mb-aws-infrastructure_as_code/services/accounts/test/sp-sauron-test/eu-west-1/locals.tf)

I have what I need. Here's the revised, fully-IaC answer.

## Short answer

Yes — going full IaC changes the **resource** parts (Steps 2 in my earlier plan), and it actually makes your cleanup requirement clean: everything lands in one Terraform stack and is removed with a single `terraform destroy` (or by reverting the PR and letting the pipeline destroy). 

What **cannot** be IaC is the *attack simulation itself* — the `GetObject` then `PutObject` API calls are runtime actions, not resources. Terraform provisions the bucket/key/seed object; you still trigger the two API calls by hand (CLI) to make the rule fire.

The good news: this account already has a real Terraform stack at services/accounts/test/sp-sauron-test/eu-west-1, with its own providers.tf, main.tf backend, and an existing s3.tf. That's exactly where the test resources belong.

---

## What changes vs. the CLI approach

| Item | CLI approach (before) | IaC approach (now) |
|---|---|---|
| Test S3 bucket | `aws s3api create-bucket` | `aws_s3_bucket` resource |
| Attacker KMS key | `aws kms create-key` | `aws_kms_key` resource |
| Baseline/seed object | `aws s3api put-object` | `aws_s3_object` resource |
| `GetObject` + `PutObject` simulation | CLI | **Stays CLI** (runtime action, not a resource) |
| Cleanup | manual delete commands | `terraform destroy` / revert PR |

---

## Step 1 — CloudTrail S3 data events (unchanged, still required)

Same as before, in bootstrap/shared-services/logging/cloudtrail.tf. Without this the object-level events never reach Sentinel. Keep the ARN scoped to the bucket name you create in Step 2.

---

## Step 2 — New IaC test resources (the part that changes)

**Location:** new file services/accounts/test/sp-sauron-test/eu-west-1/ransomware_test.tf (a dedicated file makes the PR-revert/cleanup trivial — delete the file, destroy applies).

Key cleanup design choices: `force_destroy = true` on the bucket (so `destroy` works even with objects), and a short `deletion_window_in_days` on the KMS key.

```hcl
# -----------------------------------------------------------------------------
# TEMPORARY — S3 ransomware detection rule test (issue/PR: <link>)
# Account: sp-sauron-test (364046303400). REMOVE this file after testing.
# All resources are force-destroyable so `terraform destroy` cleans up fully.
# -----------------------------------------------------------------------------

resource "aws_s3_bucket" "ransomware_test" {
  bucket        = "sauron-test-ransomware-detection-364046303400"
  force_destroy = true # allow destroy even when objects exist

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

# Attacker-controlled KMS key. Reference it by GUID at PutObject time so the
# logged kmsId does NOT contain account id 364046303400 -> satisfies the rule's
# `kmsId !has RecipientAccountId` condition.
resource "aws_kms_key" "ransomware_test" {
  description             = "TEMP key simulating attacker key for ransomware rule test"
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

# Baseline "victim" object the attacker will later download + overwrite.
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

The default provider in this stack already assumes the role in `364046303400` (see main.tf), so no extra provider wiring is needed. The repo's `default_tags` from locals.tf are applied automatically.

> Note: you may get a checkov/security-lint finding for a bucket without logging/SSE-by-default. Since this is a short-lived test bucket, add the usual `#checkov:skip=...` comments (matching the style already used in bootstrap/shared-services/logging/cloudtrail.tf) rather than leaving them unaddressed.

---

## Step 3 — Trigger the detection (stays manual / CLI)

After `terraform apply`, grab the key GUID from the output and run the two API calls. These are *actions*, so there's no IaC equivalent:

```powershell
$BUCKET = terraform output -raw ransomware_test_bucket
$KEYID  = terraform output -raw ransomware_test_kms_key_id

# A) GetObject (attacker download)
aws s3api get-object --bucket $BUCKET --key "victim/test-object.txt" downloaded.txt
Start-Sleep -Seconds 60
# B) PutObject overwrite, encrypted with attacker key (by GUID, not ARN)
aws s3api put-object --bucket $BUCKET --key "victim/test-object.txt" `
  --body downloaded.txt --server-side-encryption aws:kms --ssekms-key-id $KEYID
```

(Run these with credentials/role for `364046303400`, region `eu-west-1`.)

---

## Step 4 — Doc update (unchanged)

Same as before: update the KQL block in bootstrap/shared-services/logging/docs/README.md.tftpl, then regenerate README.md/README_TEST.md via `terraform apply` in the logging stack. Don't hand-edit the generated files.

---

## Step 5 — Cleanup (this is where IaC pays off)

Because everything is a resource, removal is deterministic:

1. **Test resources** — delete `ransomware_test.tf` (or revert the PR) and run:
   ```powershell
   terraform destroy -target=aws_s3_bucket.ransomware_test -target=aws_kms_key.ransomware_test
   ```
   or simply remove the file and `terraform apply` so the pipeline destroys them. `force_destroy = true` ensures the bucket (incl. versioned objects) is removed; the KMS key enters a 7-day deletion window.
2. **CloudTrail data-event selector** — revert the Step 1 change in cloudtrail.tf once testing is confirmed, unless the team wants to keep data-event coverage.
3. **Doc + KQL** — keep these (that's the permanent "update with any changes" deliverable). Only the test infra and the CloudTrail selector are temporary.

---

### Files touched
| File | Type | Lifecycle |
|---|---|---|
| services/accounts/test/sp-sauron-test/eu-west-1/ransomware_test.tf (new) | IaC test resources | **Temporary** — delete after test |
| bootstrap/shared-services/logging/cloudtrail.tf | S3 data event selector | **Temporary** — revert after test |
| bootstrap/shared-services/logging/docs/README.md.tftpl | Rule KQL doc | **Permanent** |
| README.md / README_TEST.md | Auto-generated | regenerate, don't hand-edit |
| CHANGELOG.md | Changelog | Permanent |

Want me to go ahead and create the `ransomware_test.tf` file and stage the cloudtrail.tf + `.tftpl` edits now?