# AWS Resource Access Manager

[Back to index](../../README.md)

AWS Resource Access Manager (RAM) shares supported resources with accounts in an **AWS Organization** or with **external AWS accounts**.

## Notes

### Central resource sharing across accounts

Use **AWS Organizations** to organize the company’s accounts and **AWS RAM** to share centrally provisioned resources where supported. Examples include:

- Transit gateways.
- AWS License Manager configurations.
- Route 53 Resolver rules.

This allows multiple accounts to use shared infrastructure rather than independently procuring equivalent resources. Control Tower provides account governance, and IAM provides permissions; neither replaces RAM’s resource-sharing function.
