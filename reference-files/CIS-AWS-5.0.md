# CIS AWS Foundations Benchmark v5.0.0 - Recommendations

| Section | No. | Recommendation | Automated/Manual |
|---|---:|---|---|
| 1 | 1.1 | Maintain current contact details | Manual |
| 1 | 1.2 | Ensure security contact information is registered | Manual |
| 1 | 1.3 | Ensure no 'root' user account access key exists | Automated |
| 1 | 1.4 | Ensure MFA is enabled for the 'root' user account | Automated |
| 1 | 1.5 | Ensure hardware MFA is enabled for the 'root' user account | Manual |
| 1 | 1.6 | Eliminate use of the 'root' user for administrative and daily tasks | Manual |
| 1 | 1.7 | Ensure IAM password policy requires minimum length of 14 or greater | Automated |
| 1 | 1.8 | Ensure IAM password policy prevents password reuse | Automated |
| 1 | 1.9 | Ensure MFA is enabled for all IAM users with console password | Automated |
| 1 | 1.10 | Do not setup access keys during initial user setup for all IAM users that have a console password | Manual |
| 1 | 1.11 | Ensure credentials unused for 45 days or greater are disabled | Automated |
| 1 | 1.12 | Ensure there is only one active access key available for any single IAM user | Automated |
| 1 | 1.13 | Ensure access keys are rotated every 90 days or less | Automated |
| 1 | 1.14 | Ensure IAM users receive permissions only through groups | Automated |
| 1 | 1.15 | Ensure IAM policies that allow full '*:*' administrative privileges are not attached | Automated |
| 1 | 1.16 | Ensure a support role has been created to manage incidents with AWS Support | Automated |
| 1 | 1.17 | Ensure IAM instance roles are used for AWS resource access from instances | Automated |
| 1 | 1.18 | Ensure expired SSL/TLS certificates stored in AWS IAM are removed | Automated |
| 1 | 1.19 | Ensure that IAM Access Analyzer is enabled for all regions | Automated |
| 1 | 1.20 | Ensure IAM users are managed centrally via identity federation or AWS Organizations for multi-account environments | Manual |
| 1 | 1.21 | Ensure access to AWSCloudShellFullAccess is restricted | Manual |
| 2.1 | 2.1.1 | Ensure S3 Bucket Policy is set to deny HTTP requests | Automated |
| 2.1 | 2.1.2 | Ensure MFA Delete is enabled on S3 buckets | Manual |
| 2.1 | 2.1.3 | Ensure all data in Amazon S3 has been discovered, classified and secured when required | Manual |
| 2.1 | 2.1.4 | Ensure S3 is configured with 'Block Public Access' enabled | Automated |
| 2.2 | 2.2.1 | Ensure that encryption-at-rest is enabled for RDS instances | Automated |
| 2.2 | 2.2.2 | Ensure Auto Minor Version Upgrade feature is enabled for RDS instances | Automated |
| 2.2 | 2.2.3 | Ensure that public access is not given to RDS instances | Automated |
| 2.2 | 2.2.4 | Ensure Multi-AZ deployments are used for enhanced availability in Amazon RDS | Manual |
| 2.3 | 2.3.1 | Ensure that encryption is enabled for EFS file systems | Automated |
| 3 | 3.1 | Ensure CloudTrail is enabled in all regions | Manual |
| 3 | 3.2 | Ensure CloudTrail log file validation is enabled | Automated |
| 3 | 3.3 | Ensure AWS Config is enabled in all regions | Automated |
| 3 | 3.4 | Ensure server access logging is enabled on the CloudTrail S3 bucket | Manual |
| 3 | 3.5 | Ensure CloudTrail logs are encrypted at rest using KMS CMKs | Automated |
| 3 | 3.6 | Ensure rotation for customer-created symmetric CMKs is enabled | Automated |
| 3 | 3.7 | Ensure VPC flow logging is enabled in all VPCs | Automated |
| 3 | 3.8 | Ensure object-level logging for write events is enabled for S3 buckets | Automated |
| 3 | 3.9 | Ensure object-level logging for read events is enabled for S3 buckets | Automated |
| 4 | 4.1 | Ensure unauthorized API calls are monitored | Manual |
| 4 | 4.2 | Ensure management console sign-in without MFA is monitored | Manual |
| 4 | 4.3 | Ensure usage of 'root' account is monitored | Manual |
| 4 | 4.4 | Ensure IAM policy changes are monitored | Manual |
| 4 | 4.5 | Ensure CloudTrail configuration changes are monitored | Manual |
| 4 | 4.6 | Ensure AWS Management Console authentication failures are monitored | Manual |
| 4 | 4.7 | Ensure disabling or scheduled deletion of customer created CMKs is monitored | Manual |
| 4 | 4.8 | Ensure S3 bucket policy changes are monitored | Manual |
| 4 | 4.9 | Ensure AWS Config configuration changes are monitored | Manual |
| 4 | 4.10 | Ensure security group changes are monitored | Manual |
| 4 | 4.11 | Ensure changes to Network Access Control Lists are monitored | Manual |
| 4 | 4.12 | Ensure changes to network gateways are monitored | Manual |
| 4 | 4.13 | Ensure route table changes are monitored | Manual |
| 4 | 4.14 | Ensure VPC changes are monitored | Manual |
| 4 | 4.15 | Ensure AWS Organizations changes are monitored | Manual |
| 4 | 4.16 | Ensure AWS Security Hub is enabled | Automated |
| 5.1 | 5.1.1 | Ensure EBS volume encryption is enabled in all regions | Automated |
| 5.1 | 5.1.2 | Ensure CIFS access is restricted to trusted networks | Automated |
| 5 | 5.2 | Ensure no Network ACLs allow ingress from 0.0.0.0/0 to remote server administration ports | Automated |
| 5 | 5.3 | Ensure no security groups allow ingress from 0.0.0.0/0 to remote server administration ports | Automated |
| 5 | 5.4 | Ensure no security groups allow ingress from ::/0 to remote server administration ports | Automated |
| 5 | 5.5 | Ensure the default security group of every VPC restricts all traffic | Automated |
| 5 | 5.6 | Ensure routing tables for VPC peering are least access | Manual |
| 5 | 5.7 | Ensure EC2 Instance Metadata Service only allows IMDSv2 | Automated |