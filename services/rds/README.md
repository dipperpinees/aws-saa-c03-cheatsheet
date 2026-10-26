# Amazon RDS

[Back to index](../../README.md)

## Overview

Amazon RDS provides managed relational databases. AWS manages provisioning, patching, backups, recovery, and supported scaling and replication operations.

- Engines described in the source include **MySQL, PostgreSQL, MariaDB, Oracle, Microsoft SQL Server, and Amazon Aurora**.
- Standard RDS instances do not expose operating-system SSH access; **RDS Custom** is a separate option.
- Standard RDS storage uses **EBS**; Aurora has a different shared-storage architecture.
- Each database instance belongs to **one Availability Zone**. A Multi-AZ deployment uses multiple instances across AZs.

## Instance storage

| Storage type | Typical use |
|---|---|
| **gp2 / gp3 SSD** | General-purpose database workloads. |
| **io1 / io2 Provisioned IOPS SSD** | I/O-intensive production workloads requiring consistent IOPS. |
| **Magnetic / Standard** | Legacy storage described in the source; not recommended for new deployments. |

**Storage autoscaling** increases allocated storage as it fills, up to a configured maximum. Enable and configure it for the database rather than assuming it is active. The source cites a maximum of **64 TB**, depending on engine; storage limits are engine- and configuration-specific.

## Multi-AZ availability

For a traditional **Multi-AZ DB instance** deployment, RDS maintains a **synchronously replicated standby in another AZ in the same Region**.

- The standby improves availability and durability; it does not serve application reads.
- RDS automatically fails over for failures such as primary AZ unavailability, network loss, primary compute failure, or primary storage failure.
- Failover can also occur during maintenance, instance resizing, or a manually initiated failover.
- The source estimates **1–2 minutes** for failover; actual time depends on the engine and workload.
- The application uses the same **DNS endpoint** after failover and must reconnect. DNS is updated to point to the standby; the primary’s IP is not simply transferred.
- A pre-existing standby avoids waiting to create a replacement instance after the failure.

A **Multi-AZ DB cluster** is a different deployment model: **one writer and two readable instances across three AZs**. The traditional standby’s read restriction does not apply to those cluster readers.

## Read replicas

Read replicas provide **horizontal read scaling** through **asynchronous replication**, so replication lag is possible.

- Applications query a replica through its separate endpoint.
- A replica can be promoted to a standalone database, ending its replication relationship.
- Cross-Region replicas support remote reads and disaster-recovery plans, with promotion and application changes as appropriate.
- Traditional RDS read replicas do not automatically replace a failed primary in the same way as a Multi-AZ standby.
- Source engine families include MySQL, MariaDB, PostgreSQL, Oracle, SQL Server, and Aurora; support, replica chaining, and limits depend on the engine and version.
- The source quotes **5 read replicas** for standard RDS and **15** for Aurora. These are source-era figures, not universal current limits.
- Enable automated backups on the source where required for read replication. Cross-Region replication is ongoing replication, rather than simply copying snapshots.
- The source describes same-Region read-replica replication transfer as free and cross-Region transfer as charged; verify the applicable transfer terms.

| Characteristic | Traditional Multi-AZ DB instance | Read replica |
|---|---|---|
| Purpose | Availability and recovery. | Read scaling; can support a DR plan. |
| Replication | Synchronous. | Asynchronous. |
| Application endpoint | One endpoint follows the active primary. | Separate replica endpoint. |
| Read access to secondary | No. | Yes. |
| Automatic primary failover | Yes. | Requires the appropriate promotion/recovery workflow. |
| Cross-Region | No. | Supported for eligible configurations. |

For both availability and additional read capacity, combine an appropriate Multi-AZ deployment with read replicas, or select an applicable cluster model.

## Backups and restoration

### Automated backups

- Automated backups support **point-in-time recovery (PITR)** within the retention period, typically to a point up to approximately **5 minutes** before the present.
- The source lists **1–35 days** of retention, with **0** disabling automated backups for supported DB instance configurations.
- It cites **7 days** as a default; defaults vary with the creation method and deployment type.
- Daily backups run in a configured **backup window**, which is separate from the maintenance window.
- RDS manages backup storage in S3; customers do not manage these backups as ordinary files in their own bucket.
- At database deletion, choose whether to retain supported automated backups and whether to create a final snapshot. A final snapshot is distinct from retained automated backups.

### Manual snapshots

Manual snapshots remain after instance deletion and persist until deleted. They can be copied to another Region and shared with another account, subject to encryption and sharing restrictions.

Restoring a snapshot or performing PITR creates a **new DB instance**, rather than overwriting a running instance. The restored database has a new endpoint, so update the application connection settings during cutover.

## Encryption

- RDS encryption at rest uses **KMS** and **AES-256**.
- Enable encryption when creating the database; do not assume encryption can be switched on in place for an existing unencrypted instance.
- To encrypt an existing database: **snapshot → encrypted snapshot copy → restore a new database**.
- An encrypted database’s snapshots are encrypted. Its read replicas must also be encrypted, subject to engine-specific rules.
- Snapshot copies can use a different KMS key, including supported cross-account and cross-Region workflows.
- Use **SSL/TLS** for encryption in transit and the relevant parameter-group settings to require it.

## Security and networking

- Place databases in **private subnets** where direct internet access is unnecessary.
- Use **security groups** for database access control. Subnet NACLs also affect connectivity but do not replace the database’s security-group rules.
- **IAM database authentication** uses an authentication token valid for **15 minutes** for supported MySQL and PostgreSQL-family engines. Configure the database user and IAM permissions as well as enabling the feature.
- **Secrets Manager** stores and can rotate database credentials.

## RDS Proxy

RDS Proxy is a managed connection pooler between applications and supported RDS or Aurora databases.

- Reuses database connections to reduce connection churn and exhaustion, particularly for Lambda and other short-lived clients.
- Supports IAM authentication and Secrets Manager integration.
- Uses private **VPC connectivity**, rather than a public proxy endpoint.
- Can improve failover behavior; the source cites approximately **66% shorter failover time**, which is an illustrative benefit rather than a universal guarantee.
- The source lists MySQL, PostgreSQL, MariaDB, SQL Server, Aurora MySQL, and Aurora PostgreSQL; verify engine/version and Region support.

## Parameter groups and option groups

A **parameter group** configures database settings such as **`max_connections`** and **`innodb_buffer_pool_size`**. Dynamic parameters can apply without a restart; static parameters require a reboot.

An **option group** enables eligible engine-specific features, such as Oracle or SQL Server Transparent Data Encryption (TDE), or MySQL memcached.

## Monitoring

- **CloudWatch metrics:** `CPUUtilization`, `DatabaseConnections`, `FreeStorageSpace`, `FreeableMemory`, `ReadIOPS`, `WriteIOPS`, `ReadLatency`, `WriteLatency`, and applicable `ReplicaLag` metrics.
- **Enhanced Monitoring:** agent-based operating-system metrics, including memory and processes, at granularity down to **1 second**, delivered to **CloudWatch Logs**.
- **Performance Insights:** database load analysis and identification of expensive or bottleneck queries. The source describes **7 days** of free retention with paid longer retention; this is a source-era feature/pricing description.
- **RDS events:** status notifications for failover, backup, and maintenance, including delivery through **SNS**.

## Maintenance and RDS Custom

Specify a **maintenance window** or use the default chosen by AWS. Eligible minor engine updates can use **Auto Minor Version Upgrade**. Major version upgrades require explicit planning and testing.

Patching can cause downtime. Multi-AZ maintenance can update the standby, fail over, and then update the former primary, reducing the disruption rather than guaranteeing no interruption.

**RDS Custom for Oracle or SQL Server** provides supported operating-system and database customization. It allows underlying host access, such as SSH where applicable. Pause the relevant RDS automation while making supported customizations to avoid automation conflicts.

## Standard RDS and Aurora

The following comparison preserves the source’s architecture distinctions and numerical examples. Capacity, timing, and cost figures vary with engine versions and configuration.

| Characteristic | Standard RDS | Aurora |
|---|---|---|
| Storage | EBS associated with database instances. | Shared cluster storage; the source quotes growth up to **128 TB**. |
| Replication | Synchronous Multi-AZ standby or asynchronous read replica, by deployment type. | **Six storage copies across three AZs**. |
| Failover example | Approximately **1–2 minutes**. | Approximately **30 seconds**. |
| Replica count quoted in source | **5**. | **15**. |
| Cost comparison quoted in source | Lower baseline in its comparison. | Approximately **20% higher**; actual cost depends on workload and configuration. |
| Serverless option described | Provisioned instances. | **Aurora Serverless v2**. |

## Pricing model

Charges can include **DB instance-hours, storage in GB-months, applicable I/O charges, data transfer, and backup storage beyond the included allocation**.

- The source describes an included backup allocation equal to the database’s provisioned storage, with additional backup storage charged separately; the exact allocation depends on the applicable service terms.
- A traditional primary-plus-standby Multi-AZ deployment pays for both instances and corresponding resources. Its source shorthand is **double instance cost**; cluster deployment pricing differs.
- The source cites Reserved Instance commitments of **1–3 years** and savings of **up to 69%** over On-Demand prices, depending on the purchase option.

## Notes

### Enforce encrypted SQL Server connections

For the source’s **RDS for SQL Server** application:

1. Download the **Amazon RDS root CA certificate**, install the relevant trust chain on the EC2 application servers, and configure the client to use and validate TLS.
2. In the RDS parameter group, set **`rds.force_ssl`** to **`true`** and **reboot** for the static setting to take effect in this configuration.

Opening port 443 alone does not encrypt the database protocol. TDE encrypts data at rest, and IAM authentication is not a substitute for TLS encryption.

### Encrypt a busy database without losing changes

For a frequently changing unencrypted **RDS PostgreSQL** database, restore an encrypted snapshot copy into a new database and use **DMS** to synchronize subsequent changes from the source. Cut over the application only after synchronization and a controlled final write transition; snapshot restoration alone omits later writes.

### Long retention and recent point-in-time recovery

The source’s Oracle compliance scenario requires **120-day backup retention** and recovery to any point in the preceding **10 days**. Its proposed configuration uses **35-day RDS automated-backup retention** for PITR and **AWS Backup** for additional backups retained **120 days**. These are two distinct retention needs; a long-retained periodic backup does not by itself supply every intermediate recovery point.
