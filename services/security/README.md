# AWS Security Services

[Back to index](../../README.md)

| Service | Purpose | Protection scope | Capabilities |
|---|---|---|---|
| **AWS WAF** | Filter malicious web requests | Layer 7 HTTP/HTTPS on supported integrations such as API Gateway, ALB, and CloudFront | Web ACLs, SQL injection and XSS rules, bot controls, rate-based rules |
| **AWS Firewall Manager** | Manage security policies centrally across accounts | AWS Organizations | Organization-wide policy deployment and administration |
| **AWS Shield Standard** | Automatic protection against common DDoS attacks at no additional charge | Network and transport layers (3 and 4) | Default DDoS protection |
| **AWS Shield Advanced** | Enhanced DDoS protection | Eligible CloudFront, Route 53, load balancer, Global Accelerator, and Elastic IP resources | Response-team support and eligible DDoS cost protection, subject to service terms |
| **AWS Network Firewall** | Inspect traffic routed through firewall endpoints | VPC inbound, outbound, and east-west traffic; network through application layer inspection | Stateful rules, domain filtering, intrusion detection/prevention |
