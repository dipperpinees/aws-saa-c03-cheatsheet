# DDoS Protection

[Back to index](../../README.md)

## Notes

### Layered protection

- **Amazon CloudFront:** Distributes content across global edge locations and helps absorb attack traffic before it reaches the origin.
- **AWS Shield Standard:** Provides default network and transport layer protection against common DDoS attacks (layers 3 and 4).
- **AWS WAF:** Filters application layer requests (layer 7) using rules based on IP addresses, headers, SQL injection patterns, and other request attributes.
- **Application Load Balancer:** Distributes requests across targets. Use WAF rules when application traffic needs filtering; an ALB alone does not determine whether every request is legitimate.
- **Auto Scaling:** Adds application capacity to handle traffic increases. Scaling supports availability but does not itself filter attack traffic.
- **Private subnets:** Keep databases and other private components from being directly reachable from the internet.

### Infrastructure choices and their limits

Elastic Fabric Adapter (EFA) accelerates communication between nodes for HPC and machine learning. It does not filter malicious traffic or provide DDoS protection.

Dedicated EC2 Instances run on hardware dedicated to one customer, providing hardware isolation and reducing exposure to other tenants' activity. This isolation does not prevent an internet attack from exhausting network bandwidth or instance resources.
