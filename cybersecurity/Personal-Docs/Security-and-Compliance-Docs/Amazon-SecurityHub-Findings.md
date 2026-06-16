# Amazon Findings

## Executive Summary

| Metric              |   Value |
|:--------------------|--------:|
| Total findings      |      47 |
| Critical findings   |       2 |
| High findings       |       7 |
| Medium findings     |      19 |
| Low findings        |      19 |
| Total failed checks |     881 |
| Total passed checks |    1487 |

## Findings by Severity

| Severity   |   Findings |   Failed Checks |   Passed Checks |
|:-----------|-----------:|----------------:|----------------:|
| Critical   |          2 |              14 |             701 |
| High       |          7 |              42 |              44 |
| Medium     |         19 |             582 |             399 |
| Low        |         19 |             243 |             343 |

## Findings by Service

| Service        |   Findings |   Critical |   High |   Medium |   Low |   Failed Checks |
|:---------------|-----------:|-----------:|-------:|---------:|------:|----------------:|
| SSM            |          3 |          2 |      0 |        1 |     0 |              21 |
| Inspector      |          4 |          0 |      4 |        0 |     0 |              28 |
| S3             |          6 |          0 |      1 |        3 |     2 |              86 |
| EC2            |          2 |          0 |      1 |        1 |     0 |              14 |
| GuardDuty      |          1 |          0 |      1 |        0 |     0 |               1 |
| IAM            |          6 |          0 |      0 |        3 |     3 |             140 |
| SecretsManager |          3 |          0 |      0 |        3 |     0 |              11 |
| CloudFormation |          2 |          0 |      0 |        2 |     0 |             337 |
| KMS            |          2 |          0 |      0 |        2 |     0 |              18 |
| CloudTrail     |          1 |          0 |      0 |        1 |     0 |              21 |
| Lambda         |          1 |          0 |      0 |        1 |     0 |               8 |
| Athena         |          1 |          0 |      0 |        1 |     0 |               7 |
| Macie          |          1 |          0 |      0 |        1 |     0 |               7 |
| CloudWatch     |         14 |          0 |      0 |        0 |    14 |             182 |

## Top Findings by Severity and Failed Checks

| ID               | Title                                                                   | Severity   |   Failed Checks |   Passed Checks | Service        |
|:-----------------|:------------------------------------------------------------------------|:-----------|----------------:|----------------:|:---------------|
| SSM.4            | SSM documents should not be public                                      | Critical   |               7 |             701 | SSM            |
| SSM.7            | SSM documents should have the block public sharing setting enabled      | Critical   |               7 |               0 | SSM            |
| EC2.182          | Block public access settings should be enabled for Amazon EBS snapshots | High       |               7 |               0 | EC2            |
| Inspector.1      | Amazon Inspector EC2 scanning should be enabled                         | High       |               7 |               0 | Inspector      |
| Inspector.2      | Amazon Inspector ECR scanning should be enabled                         | High       |               7 |               0 | Inspector      |
| Inspector.3      | Amazon Inspector Lambda code scanning should be enabled                 | High       |               7 |               0 | Inspector      |
| Inspector.4      | Amazon Inspector Lambda standard scanning should be enabled             | High       |               7 |               0 | Inspector      |
| S3.8             | S3 general purpose buckets should block public access                   | High       |               6 |              38 | S3             |
| GuardDuty.1      | GuardDuty should be enabled                                             | High       |               1 |               6 | GuardDuty      |
| CloudFormation.4 | CloudFormation stacks should have associated service roles              | Medium     |             180 |               0 | CloudFormation |

## Full Findings Table

| ID               | Title                                                                                                   | Service        | Status   | Severity   |   Failed Checks |   Unknown Checks |   Not Available Checks |   Passed Checks |
|:-----------------|:--------------------------------------------------------------------------------------------------------|:---------------|:---------|:-----------|----------------:|-----------------:|-----------------------:|----------------:|
| SSM.4            | SSM documents should not be public                                                                      | SSM            | Failed   | Critical   |               7 |                0 |                      0 |             701 |
| SSM.7            | SSM documents should have the block public sharing setting enabled                                      | SSM            | Failed   | Critical   |               7 |                0 |                      0 |               0 |
| EC2.182          | Block public access settings should be enabled for Amazon EBS snapshots                                 | EC2            | Failed   | High       |               7 |                0 |                      0 |               0 |
| Inspector.1      | Amazon Inspector EC2 scanning should be enabled                                                         | Inspector      | Failed   | High       |               7 |                0 |                      0 |               0 |
| Inspector.2      | Amazon Inspector ECR scanning should be enabled                                                         | Inspector      | Failed   | High       |               7 |                0 |                      0 |               0 |
| Inspector.3      | Amazon Inspector Lambda code scanning should be enabled                                                 | Inspector      | Failed   | High       |               7 |                0 |                      0 |               0 |
| Inspector.4      | Amazon Inspector Lambda standard scanning should be enabled                                             | Inspector      | Failed   | High       |               7 |                0 |                      0 |               0 |
| S3.8             | S3 general purpose buckets should block public access                                                   | S3             | Failed   | High       |               6 |                0 |                      0 |              38 |
| GuardDuty.1      | GuardDuty should be enabled                                                                             | GuardDuty      | Failed   | High       |               1 |                0 |                      0 |               6 |
| CloudFormation.4 | CloudFormation stacks should have associated service roles                                              | CloudFormation | Failed   | Medium     |             180 |                0 |                      0 |               0 |
| CloudFormation.3 | CloudFormation stacks should have termination protection enabled                                        | CloudFormation | Failed   | Medium     |             157 |                0 |                      0 |               0 |
| IAM.3            | IAM users' access keys should be rotated every 90 days or less                                          | IAM            | Failed   | Medium     |              63 |                0 |                      0 |               0 |
| IAM.8            | Unused IAM user credentials should be removed                                                           | IAM            | Failed   | Medium     |              28 |                0 |                      0 |              14 |
| S3.5             | S3 general purpose buckets should require requests to use SSL                                           | S3             | Failed   | Medium     |              26 |                0 |                      0 |              18 |
| CloudTrail.5     | CloudTrail trails should be integrated with Amazon CloudWatch Logs                                      | CloudTrail     | Failed   | Medium     |              21 |                0 |                      0 |               0 |
| S3.9             | S3 general purpose buckets should have server access logging enabled                                    | S3             | Failed   | Medium     |              16 |                0 |                      0 |               6 |
| KMS.1            | IAM customer managed policies should not allow decryption actions on all KMS keys                       | KMS            | Failed   | Medium     |              14 |                0 |                      0 |             301 |
| IAM.22           | IAM user credentials unused for 45 days should be removed                                               | IAM            | Failed   | Medium     |              14 |                0 |                      0 |               7 |
| S3.1             | S3 general purpose buckets should have block public access settings enabled                             | S3             | Failed   | Medium     |              12 |                0 |                      0 |               0 |
| Lambda.2         | Lambda functions should use supported runtimes                                                          | Lambda         | Failed   | Medium     |               8 |                0 |                      0 |               9 |
| Athena.4         | Athena workgroups should have logging enabled                                                           | Athena         | Failed   | Medium     |               7 |                0 |                      0 |               0 |
| EC2.172          | EC2 VPC Block Public Access settings should block internet gateway traffic                              | EC2            | Failed   | Medium     |               7 |                0 |                      0 |               0 |
| Macie.1          | Macie should be enabled                                                                                 | Macie          | Failed   | Medium     |               7 |                0 |                      0 |               0 |
| SSM.6            | SSM Automation should have CloudWatch logging enabled                                                   | SSM            | Failed   | Medium     |               7 |                0 |                      0 |               0 |
| SecretsManager.1 | Secrets Manager secrets should have automatic rotation enabled                                          | SecretsManager | Failed   | Medium     |               5 |                0 |                      0 |               6 |
| SecretsManager.4 | Secrets Manager secrets should be rotated within a specified number of days                             | SecretsManager | Failed   | Medium     |               5 |                0 |                      0 |               6 |
| KMS.4            | AWS KMS key rotation should be enabled                                                                  | KMS            | Failed   | Medium     |               4 |                0 |                      0 |              22 |
| SecretsManager.3 | Remove unused Secrets Manager secrets                                                                   | SecretsManager | Failed   | Medium     |               1 |                0 |                      0 |              10 |
| IAM.21           | IAM customer managed policies that you create should not allow wildcard actions for services            | IAM            | Failed   | Low        |              14 |                0 |                      0 |             301 |
| IAM.2            | IAM users should not have IAM policies attached                                                         | IAM            | Failed   | Low        |              14 |                0 |                      0 |              28 |
| CloudWatch.1     | A log metric filter and alarm should exist for usage of the root" user"                                 | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.10    | Ensure a log metric filter and alarm exist for security group changes                                   | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.11    | Ensure a log metric filter and alarm exist for changes to Network Access Control Lists (NACL)           | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.12    | Ensure a log metric filter and alarm exist for changes to network gateways                              | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.13    | Ensure a log metric filter and alarm exist for route table changes                                      | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.14    | Ensure a log metric filter and alarm exist for VPC changes                                              | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.4     | Ensure a log metric filter and alarm exist for IAM policy changes                                       | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.5     | Ensure a log metric filter and alarm exist for CloudTrail configuration changes                         | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.6     | Ensure a log metric filter and alarm exist for AWS Management Console authentication failures           | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.7     | Ensure a log metric filter and alarm exist for disabling or scheduled deletion of customer created CMKs | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.8     | Ensure a log metric filter and alarm exist for S3 bucket policy changes                                 | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| CloudWatch.9     | Ensure a log metric filter and alarm exist for AWS Config configuration changes                         | CloudWatch     | Failed   | Low        |              14 |                0 |                      0 |               0 |
| S3.13            | S3 general purpose buckets should have Lifecycle configurations                                         | S3             | Failed   | Low        |              13 |                0 |                      0 |               9 |
| S3.20            | S3 general purpose buckets should have MFA delete enabled                                               | S3             | Failed   | Low        |              13 |                0 |                      0 |               5 |
| CloudWatch.2     | Ensure a log metric filter and alarm exist for unauthorized API calls                                   | CloudWatch     | Failed   | Low        |               7 |                0 |                      0 |               0 |
| CloudWatch.3     | Ensure a log metric filter and alarm exist for Management Console sign-in without MFA                   | CloudWatch     | Failed   | Low        |               7 |                0 |                      0 |               0 |
| IAM.17           | Ensure IAM password policy expires passwords within 90 days or less                                     | IAM            | Failed   | Low        |               7 |                0 |                      0 |               0 |
