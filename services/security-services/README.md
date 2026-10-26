# Security Services

[Back to index](../../README.md)

## Governance and compliance

| Service | Purpose |
|---|---|
| **AWS Artifact** | Download AWS compliance reports, such as SOC, PCI, and ISO documentation, for auditors. |
| **AWS Audit Manager** | Collect evidence to support compliance assessments, including frameworks related to HIPAA and GDPR. |
| **AWS Config** | Record resource configuration changes and assess compliance. Use CloudTrail to identify who made an API change. |

## Infrastructure and network protection

| Service | Purpose |
|---|---|
| **AWS WAF** | Filter web application requests, including SQL injection and cross-site scripting attacks. |
| **AWS Shield** | DDoS protection. Shield Standard is included for AWS customers without an additional Shield charge. |
| **AWS Network Firewall** | Inspect and filter traffic routed through the firewall in a VPC. |

## Threat and vulnerability detection

| Service | Purpose |
|---|---|
| **Amazon GuardDuty** | Detect suspicious behavior, including cryptocurrency mining, brute-force activity, and account compromise. |
| **Amazon Inspector** | Scan supported EC2 instances, ECR container images, and Lambda functions for software vulnerabilities such as CVEs. |
| **Amazon Macie** | Discover sensitive data, such as financial or personal information, in S3 and identify related data-security risks. |

## Identity, keys, and secrets

| Service | Purpose |
|---|---|
| **AWS IAM** | Manage identities, groups, roles, and permissions for AWS resources. |
| **AWS KMS** | Manage encryption keys, including keys used to protect data at rest. |
| **AWS Secrets Manager** | Store credentials such as database passwords and API keys, and automate rotation for supported configurations. |

## Central management

- **AWS Security Hub** aggregates security findings from services such as GuardDuty, Inspector, and Macie. Its security scores summarize the results of enabled security controls.
- **AWS Organizations service control policies (SCPs)** set permission boundaries for member accounts. For example, an SCP can restrict EC2 operations in disallowed Regions.
