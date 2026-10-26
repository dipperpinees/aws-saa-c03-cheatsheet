# Amazon GuardDuty

[Back to index](../../README.md)

Amazon GuardDuty continuously detects threats to AWS accounts, workloads, and data. It combines machine learning, anomaly detection, and threat intelligence from AWS and its partners to identify suspicious activity.

## Data sources

Foundational threat detection is managed and does not require installing software on each EC2 instance. It analyzes service telemetry such as:

- **VPC Flow Logs:** Network communication and suspicious traffic.
- **CloudTrail management events:** Potential account compromise, unusual API activity, or changes to permissions.
- **DNS query logs:** Communication with malicious domains, including command-and-control infrastructure.

Additional protection capabilities use sources such as **S3 data events**, **EKS audit logs**, and **RDS login events**. Enable the appropriate protection plan for the resources being monitored. Foundational log analysis differs from features such as Runtime Monitoring, which can require an agent, and Malware Protection, which scans storage.

## Findings

Examples include:

1. **Reconnaissance:** Port scans or attempts to discover vulnerable services.
2. **Instance compromise:** Cryptocurrency mining or an instance participating in attacks.
3. **Account abuse:** Suspicious sign-ins, log deletion, or IAM permission changes.
4. **Malware:** Malicious software detected on scanned EBS volumes through Malware Protection.

## Remediation and administration

GuardDuty findings report detected threats. To automate a response, route findings through **EventBridge** to **Lambda**, which can quarantine resources, update controls, or perform another approved action.

AWS Organizations supports centralized multi-account administration through a designated GuardDuty administrator. Pricing depends on the enabled protection capabilities and analyzed data; the source emphasizes log volume in GB. Foundational analysis uses AWS telemetry rather than running an inspection process inside the application's EC2 guest, avoiding that form of host overhead. This does not imply every optional protection feature has zero overhead.

## Related services

| Service | Purpose |
| --- | --- |
| GuardDuty | Detect active threats and suspicious behavior |
| Amazon Inspector | Assess software vulnerabilities and exposure |
| Amazon Macie | Discover sensitive data, including PII, in S3 |
| AWS WAF | Filter web requests and mitigate application-layer attacks |
| AWS Trusted Advisor | Recommend improvements based on AWS best practices |

## Notes

### Suspicious access to encrypted S3 documents

Encryption alone does not detect unauthorized access attempts. For an application continuously sending encrypted documents to S3, enable GuardDuty's S3 protection capability to identify malicious activity and suspicious access patterns. Pair detection with the policies and controls needed for the organization's compliance requirements.

### Email notifications by finding severity

To notify an operator about **medium- and high-severity findings**, create an EventBridge rule matching GuardDuty finding events and filtering the desired severity range. Set an **SNS topic** as the target and subscribe the operator's email address. This uses finding events directly; it does not require inventing a native GuardDuty CloudWatch metric or alarm.
