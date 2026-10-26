# Network Access Control Lists

[Back to index](../../README.md)

## Notes

### Blocking known source IP addresses

Security groups contain **allow** rules only. For example, if SSH port 22 is open to `0.0.0.0/0`, a security group cannot add an explicit exception denying `1.2.3.4`.

A **network ACL (NACL)** supports both **allow and deny** rules at the subnet boundary. To temporarily block identified sources of SSH brute-force attacks against public EC2 instances, add inbound deny rules for their IP addresses to the subnet’s NACL.

NACL rules are evaluated from the **lowest rule number upward**. A rule such as **10** takes precedence over matching rules with higher numbers. Place the deny rule before any broader matching allow rule.
