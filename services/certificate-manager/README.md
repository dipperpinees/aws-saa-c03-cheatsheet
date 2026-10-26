# AWS Certificate Manager

[Back to index](../../README.md)

## Certificate expiration monitoring

### Event-driven notifications

Use EventBridge rules to route ACM expiration or AWS Health events to an SNS topic for email or SMS notifications. This avoids repeatedly scanning every certificate.

The source describes daily expiration-related events starting **45 days** before expiration and names `AWS_ACM_RENEWAL_STATE_CHANGE`. Expiration warnings and renewal-state changes are different event concepts; verify the event type and threshold for the intended integration instead of assuming this code represents every expiration notification.

### CloudWatch metrics

ACM publishes the **`DaysToExpiry`** metric for certificate expiration monitoring. Configure a CloudWatch alarm to notify SNS when the remaining lifetime crosses a threshold such as **30 days**. This integrates certificate health into an existing dashboard and alarm system.

### Compliance and security monitoring

- **AWS Config:** Use the managed `acm-certificate-expiration-check` rule with a threshold such as **30 days**. Certificates approaching expiration are marked noncompliant. Centralized Config arrangements can support visibility across multiple accounts without custom checking code.
- **AWS Security Hub:** Consolidate security findings, including certificate-expiration checks that use AWS Config, into a common security view.

### Custom checks and manual inspection

A Lambda function can run on a daily EventBridge schedule, such as **08:00**, to list certificates and inspect their expiration details. The source names `aws acm list-certificates`; obtain each certificate's `NotAfter` detail with the appropriate describe operation. Custom code can send formatted notifications to email, Slack, or Teams.

For occasional manual checks, inspect the **Expiration date** column in the ACM console. Manual inspection requires someone to check regularly and does not provide reliable unattended warnings.

## Notes

### Thirty-day warnings for ALB certificates

For websites running on EC2 fleets behind Application Load Balancers with ACM HTTPS certificates, use either expiration-event notifications through EventBridge and SNS or monitoring based on `DaysToExpiry` with an SNS notification threshold of 30 days. A scheduled daily evaluation requires a target that reads the metric and performs the check; an EventBridge schedule does not query CloudWatch metrics by itself.
