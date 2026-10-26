# AWS Application Migration Service

[Back to index](../../README.md)

AWS Application Migration Service (AWS MGN) supports **rehosting**, or lift-and-shift migration, of servers to AWS. It is suited to legacy Linux and Windows virtual machines when the goal is to retain the existing operating system, applications, and data while minimizing application changes and migration downtime.

## Replication and cutover

1. Install the **AWS Replication Agent** on source servers.
2. Replicate data continuously at the **block level** to an AWS staging area while the source application continues running.
3. Launch **test instances** and validate application behavior.
4. Perform cutover to native EC2 instances when testing is complete.

Continuous replication keeps the target close to the source state. Cutover still requires a planned interruption; its duration depends on the application, replication status, and validation process rather than a guaranteed restart time.

## Notes

### Choosing migration tools

| Tool | Purpose |
|---|---|
| **Application Migration Service** | Replicate and rehost complete servers with their OS and applications. |
| **Application Discovery Service** | Inventory server configuration and application dependencies for migration planning; does not perform server replication. |
| **DataSync** | Transfer files and objects between supported storage endpoints such as NFS, SMB, S3, and EFS; does not convert an entire VM into an EC2 server. |
| **VM Import/Export** | Import supported VM images, such as OVA/VHD, into AWS. An image export/import workflow requires separate planning for writes and changes occurring after the export. |

The source contrasts MGN with the legacy Server Migration Service (SMS). MGN's agent-based continuous replication supports the low-downtime rehosting workflow described here.
