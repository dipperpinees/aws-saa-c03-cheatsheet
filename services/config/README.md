# AWS Config

[Back to index](../../README.md)

AWS Config records, audits, and evaluates AWS resource configuration. CloudTrail records API activity; Config records resource state and compliance.

## Configuration history and relationships

- Record configuration changes, such as a security group opening port 22 or an S3 bucket becoming public.
- Review a resource’s historical configuration, including how it differed a month earlier.
- Track relationships, such as an EC2 instance’s network interfaces and associated security groups.
- Evaluate rules such as requiring EBS volumes to be encrypted, and mark violating resources **NON_COMPLIANT**.

## Evaluation and remediation

1. Detect a recorded resource’s creation, modification, or deletion.
2. Save its configuration as a **configuration item (CI)**.
3. Evaluate relevant Config rules against that configuration or evaluate periodically, as supported by the rule.
4. Deliver configuration history to S3 and notifications through SNS or event integrations.
5. Optionally trigger configured remediation, such as closing an unauthorized open port or correcting a public S3 configuration.

For example, if an employee makes an S3 bucket public, CloudTrail records the API action, caller, and time. Config evaluates the resulting configuration, reports noncompliance, and can initiate a configured workflow to restore private access.

## Related monitoring services

| Service | Purpose | Information provided |
|---|---|---|
| AWS Config | Resource configuration and compliance | Current and historical configuration; whether rules are satisfied. |
| AWS CloudTrail | API activity audit | Who called an API, when, and from which IP address. |
| Amazon CloudWatch | Metrics, logs, and operational monitoring | CPU utilization, performance, and health signals. |

## Notes

### Managed rules for tags and identity controls

Managed rules reduce the custom code needed for common compliance checks:

- **`required-tags`** checks supported resources for required tag keys, such as `Environment` and `Owner`, and reports missing tags.
- **`iam-password-policy`** evaluates the account’s IAM password-policy settings, such as a **12-character minimum length**, special characters, and expiration requirements. It evaluates the policy rather than testing each user’s password.
- **`iam-user-mfa-enabled`** checks MFA for IAM users; root-account MFA is a separate compliance check.
- **`access-keys-rotated`** can check whether IAM access keys exceed a configured age, such as **90 days**.
- Managed rules can also evaluate CloudTrail configuration and ACM certificate expiration requirements.

Config is a **detective control**: it evaluates resources and configurations. SCPs and IAM policies are **preventive controls** that can deny supported actions, including creation requests lacking required tags when the service and action support the relevant conditions. Organization tag policies standardize tag keys and values; they do not replace a check for resources that lack tags. S3 Object Lock protects objects from deletion and is unrelated to tag-compliance scanning.

A scheduled Lambda and EventBridge solution can perform custom checks, but needs code, permissions, and scheduling that a suitable managed rule can avoid.

### Detect, route, and remediate old access keys

1. Configure `access-keys-rotated` with a **90-day** maximum age.
2. Route Config compliance-change events through **EventBridge** when a resource becomes `NON_COMPLIANT`.
3. Invoke a **Lambda** function to identify the affected access keys and call IAM APIs such as `UpdateAccessKey` to deactivate them or `DeleteAccessKey` to delete them, according to the remediation policy.

EventBridge routes events; it does not independently scan IAM and calculate every key’s age. AWS Batch is suited to batch compute workloads and adds unnecessary complexity for this small event-driven remediation task.

### Account password policy and certificate expiration

Configure password complexity and minimum length in the **account-wide IAM password policy**. A Config rule evaluates that policy; it does not enforce complexity during user creation or set a separate policy on every user.

For the source’s third-party certificates **imported into ACM**, configure the managed certificate-expiration check with a **30-day threshold** and send noncompliance notifications through **SNS**. Imported certificates require a renewal process; evaluating their expiration is separate from issuing or renewing a certificate.
