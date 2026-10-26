# Amazon Data Lifecycle Manager

[Back to index](../../README.md)

Amazon Data Lifecycle Manager (DLM) automates the creation, retention, and deletion of Amazon EBS snapshots according to lifecycle policies. This reduces manual backup work and unnecessary storage while supporting retention requirements.

## Features and cost

- Schedule hourly or daily backups of EBS volumes and EC2 instances.
- Delete older snapshots automatically according to retention policies.
- Configure policies through the AWS Management Console, CLI, or API.
- DLM has no additional service charge; snapshot storage is billed separately.

Sources: [Amazon EBS Data Lifecycle Manager](https://aws.amazon.com/ebs/data-lifecycle-manager/), [discussion comparing DLM and AWS Backup](https://www.reddit.com/r/aws/comments/oa2pcg/ebs_aws_backup_vs_aws_lifecycle_manager/?tl=vi).
