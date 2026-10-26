# AWS Security Hub

[Back to index](../../README.md)

AWS Security Hub centralizes security posture information and findings from AWS and third-party security services. It collects, aggregates, and prioritizes results to provide a common view of account security.

## Standards and compliance

Security Hub evaluates supported resources against standards such as:

- **CIS AWS Foundations Benchmark**
- **PCI DSS**
- **AWS Foundational Security Best Practices**

Findings and compliance scores help teams identify resources that require attention.

## Automation and multi-account administration

Use **EventBridge** to route selected findings to an **SNS** notification or a **Lambda** remediation workflow. For example, a critical finding can trigger an operator notification or a configured corrective action.

AWS Organizations supports centralized management through a designated administrator account, allowing security teams to view findings from member accounts.

## Related services

| Service | Main purpose |
| --- | --- |
| AWS Artifact | Download AWS compliance reports, such as PDF documents needed for audits |
| AWS Config | Record configuration-change history and evaluate resources against rules |
| Amazon GuardDuty | Detect threats, including cryptocurrency mining and suspicious attack activity |
| Security Hub | Consolidate findings and evaluate overall security posture against standards |
