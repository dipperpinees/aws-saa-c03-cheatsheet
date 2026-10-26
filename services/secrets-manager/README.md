# AWS Secrets Manager

[Back to index](../../README.md)

AWS Secrets Manager stores application secrets and supports managed rotation workflows, including credentials for services such as RDS and Redshift. Systems Manager Parameter Store is another option for configuration values and secrets.

## Notes

### Secure configuration without a rotation requirement

For an EC2 application using an Aurora MySQL database, configuration may include database hostnames, environment settings, product keys, and database passwords. Store sensitive values as **`SecureString` parameters in Systems Manager Parameter Store**, encrypted using **AWS KMS**, and update the application to retrieve them with appropriate permissions.

This is a cost-effective source example when the requirement is encrypted storage and retrieval rather than automatic credential rotation. Parameter Store suits configuration that changes infrequently.

### Cost and feature comparison

| Service | Cost model in the source | Useful capabilities |
| --- | --- | --- |
| Parameter Store | Standard parameter storage and standard-throughput access have no additional Parameter Store charge within applicable limits | Configuration values and KMS-encrypted `SecureString` secrets |
| Secrets Manager | Source example: **$0.40 per secret per month**, plus charges per **10,000 API requests** | Secret lifecycle management and automatic rotation integrations |

These prices reflect the source comparison rather than a current regional quote. KMS usage, advanced parameters, higher throughput, and other applicable services can add costs. Evaluate rotation and lifecycle needs along with storage price rather than assuming one service is always cheaper for every workload.
