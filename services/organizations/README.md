# AWS Organizations

[Back to index](../../README.md)

## Notes

### Multi-account management

AWS Organizations provides centralized management for multiple AWS accounts. Group accounts by business unit or environment such as development, test, and production using **organizational units (OUs)**.

**Consolidated billing** combines member-account charges through a management account and can provide eligible aggregated usage discounts. Integrate **IAM Identity Center** with the corporate directory for centralized workforce authentication and access to authorized accounts.

### Service control policies

SCPs define permission guardrails for member accounts and OUs. They can restrict actions such as deleting S3 resources or creating resources in unapproved Regions, even for member-account administrators. They do not grant access, do not restrict management-account principals, and have documented exceptions such as service-linked roles.

### Root email and alternate contacts

Give each account a suitable root email address delivered to an administrator distribution list or shared mailbox monitored by a small set of responsible administrators. This prevents alerts being missed when one person is unavailable while limiting recipients to administrators. Configure **billing, security, and operations alternate contacts** through the console or programmatically. Accounts must retain distinct root email addresses.

### Reserved Instance discount sharing

For the source scenario, establish an organization in the account that purchased the EC2 Reserved Instance and invite the other accounts. Enable appropriate Reserved Instance discount sharing in billing preferences so eligible usage across those accounts can use unused RI discounts. Sharing is a billing benefit; AWS Resource Access Manager does not share EC2 Reserved Instances in this way.
