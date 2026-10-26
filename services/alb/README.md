# Application Load Balancer

[Back to index](../../README.md)

## Notes

### Redirect HTTP to HTTPS

For an ALB with an HTTP listener on port 80 and an HTTPS listener on port 443, change the existing port-80 listener’s action from forwarding to **redirect**. Requests to `http://domain.com` then receive an HTTP **301 or 302 redirect** to `https://domain.com` on port 443. Edit the existing listener rather than creating another listener on the same port.

**TLS offloading** means the ALB handles HTTPS/TLS termination instead of the EC2 application servers. If encryption/decryption saturates the web servers, import the existing certificate into **ACM** and configure the ALB’s **HTTPS listener** to use it. This moves that TLS processing from the application instances to the load balancer.

### Gradual migration between on-premises and AWS

Two approaches can divide web traffic between existing on-premises servers and AWS:

| Approach | Configuration | Considerations |
|---|---|---|
| Route 53 weighted routing | Create records for the same name, such as `app.company.com`, pointing to the on-premises endpoint and AWS ALB. Equal weights such as 50 and 50 give each record an equal selection probability. | Simple to configure; DNS caching affects the observed traffic distribution. |
| ALB weighted target groups | Point the domain to one ALB and forward to an AWS target group and an on-premises IP target group with equal weights. | Splits requests at the load balancer, avoiding DNS-cache effects on the weighting. |

An on-premises target must use a reachable **private IP address**, with connectivity such as **Direct Connect or VPN**. ALB target types include EC2 instance IDs, IP addresses, and Lambda functions.

Route 53 failover routing serves an active-passive design: the primary receives traffic while healthy and the secondary takes over after failure. Weighted routing serves proportional traffic distribution. API Gateway canary deployments also support gradual traffic shifts.

Global Accelerator endpoint weights distribute traffic among supported AWS endpoints, such as ALBs, NLBs, EC2 instances, and Elastic IP addresses; an on-premises IP address cannot be registered directly as an endpoint.

### Centralized appliance inspection

Use a **Gateway Load Balancer (GWLB)** to distribute traffic through a fleet of firewalls, IDS/IPS, deep-packet inspection systems, or other third-party security appliances. A **GWLB endpoint** connects a workload VPC to that inspection service, including across accounts.

```text
Workload VPC in service account
        ↓
GWLB endpoint
        ↓
Gateway Load Balancer in networking account
        ↓
Security appliance fleet
        ↓
Destination service or VPC
```
