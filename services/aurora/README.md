# Aurora

[Back to index](../../README.md)

The numerical limits and legacy features below reflect the source notes. Version-dependent values, historical Multi-Master behavior, and recovery timings are not guarantees for every current engine configuration.

## Overview

- Fully managed relational database, MySQL & PostgreSQL compatible

- Source performance comparison: up to 5× MySQL and 3× PostgreSQL

- Storage automatically increases to **128 TiB**, minimum 10GB

- Automatically patch errors, self-recovery, support cloning

------------------------------------------------------------------------

## DB Cluster

- **Primary DB instance**: read + write, only 1 per cluster

- **Aurora Replica**: read only, maximum **15 replicas**, supports auto failover

## Endpoints

| Type | Purpose |
|-------------------|---------------------------------|
| Cluster endpoints | Primary connection (read + write) |
| Reader endpoint | Load balancing for read-only |
| Custom endpoints | Optional instance group |
| Instance endpoint | Connect directly to a specific instance |

------------------------------------------------------------------------

## Storage & Reliability

- Data stored on **6 replicas** spanning **3 AZ**

- Automatically detect and fix disk errors

- The source describes **asynchronous crash recovery** as allowing the database to reopen quickly; it does not establish an immediate-recovery guarantee.

- **Dynamic resizing**: automatically reduces storage when deleting data

- Backup retention: **1–35 days**

- **Backtrack**: rewind the database up to **72 hours** (does not replace full backup)

------------------------------------------------------------------------

## High Availability

- Automatic failover: promote Replica or create new Primary

- Legacy reboot behavior described in the source: rebooting the primary also reboots replicas; behavior depends on the engine version.

- **Multi-AZ**: data synchronization, no I/O freeze

- **Historical Aurora Multi-Master** (MySQL 5.6): the source describes multiple read/write nodes for fault tolerance and zero-downtime recovery. This is historical feature guidance.

------------------------------------------------------------------------

## Aurora Global Database

- Spanning **multiple AWS Regions**

- One primary Region (read/write) with secondary Regions (read-only); the source illustrates one secondary Region.

- Source recovery targets: **RPO: 5 seconds**, **RTO: \< 1 minute** for a Region failure; actual recovery depends on the failure and configuration.

- Support managed planned failover

------------------------------------------------------------------------

## Aurora Serverless

### V2 (Recommended)

- Instant scale in **ACU** units (0.5 – 128 ACU)

- Supports Multi-AZ, Global DB, multiple Replicas, IAM Auth, Performance Insights

- Suitable: fluctuating workload, SaaS, multi-tenant

### V1 (Legacy, not recommended from January 8, 2025)

- Scale slower, increment larger

- Not supported: Aurora Replica, Backtrack, IAM Auth, cloning, Performance Insights

- Volume **always encrypted**

- Access only in VPC, no public IP

- Pay **per second**, only while in use

------------------------------------------------------------------------

## Scaling

- **Instance scaling**: change instance class

- **Read scaling**: add Aurora Replica

- **Aurora Auto Scaling**: automatically add/remove Replicas according to CloudWatch metrics (PostgreSQL)

## Compare Aurora Replica vs MySQL Replica

| Criterion | Aurora Replica | MySQL Replica (source comparison) |
|-------------------|---------------------|---------------------------------|
| Quantity | Maximum 15 | Maximum 5 |
| Replication lag | Milliseconds | Seconds |
| Automatic failover | Supported; shared-storage readers can be promoted | Separate MySQL replica promotion may be needed; replication lag can cause data loss |
| Impact on primary | Low | High |

------------------------------------------------------------------------

## Special features

- **RDS Data API**: call SQL over HTTPS, no persistent connections needed — ideal for Lambda/serverless

- **Zero-ETL with Redshift**: push Aurora data to Redshift in seconds, no ETL pipeline needed

- **Aurora PostgreSQL Limitless**: automatic horizontal sharding, exceeding storage & throughput limits

- **Parallel Query** (MySQL): optimize heavy queries by parallelizing I/O

- **Logical Replication** (PostgreSQL): replication to another DB via replication slot

------------------------------------------------------------------------

## Security

- IAM, VPC Security Group, TLS/SSL

- **IAM Database Authentication**: use token instead of password (MySQL & PostgreSQL)

- At-rest encryption using RDS Encryption

- PostgreSQL supports **Kerberos & Microsoft Active Directory**

## Monitoring

- RDS Events, Enhanced Monitoring, Performance Insights

- CloudWatch Metrics/Alarms/Logs

- CloudWatch Database Insights (for Limitless Database)

## Pricing

- Calculated according to: **DB instance hours, I/O requests, Backup storage, Data transfer**

- Form: **On-Demand** (hourly) or **Reserved** (1–3 years, discount)

[Aurora Multi-AZ architecture](aurora-multi-az/README.md)

## Notes

### Reacting to row changes

For a car-listing application that must remove sold vehicles and forward their details to distributed processing, use an Aurora native function or stored procedure that invokes Lambda. Configure the database logic to invoke it on the relevant `DELETE` or `UPDATE`, then have Lambda send an event to SQS for processing.

RDS event subscriptions describe operational events, such as restarts, backups, availability problems, and configuration changes. They do not report individual row deletions or updates.

### Separating production reads from reporting

For a cluster with differently sized instances, create custom endpoints that select the desired instance groups. A production read endpoint might include `r6g.xlarge` readers, while a reporting endpoint selects `t4g.medium` readers. Route each workload to its designated endpoint; default endpoints do not classify queries by business purpose. Continue to send writes to the writer.

If an existing Multi-AZ deployment already includes Aurora Replicas, send read traffic to those readers to reduce read load and its effect on write latency. Creating an additional replica is unnecessary solely to use read capacity that already exists.

### Intermittent workloads

Aurora Serverless is suitable for infrequent, intermittent, or unpredictable demand. The source example migrates an RDS MySQL application used for only **two hours at the end of each week** to an Aurora Serverless v2 MySQL-compatible cluster to match compute capacity to demand. Evaluate the applicable engine's minimum capacity and idle behavior when estimating cost.

### Restoring MySQL backups into Aurora

For a new Aurora MySQL-compatible test environment after terminating an RDS MySQL instance, a compatible final RDS DB snapshot can be migrated directly to Aurora. A `mysqldump` logical backup is another migration path: the source stages the dump in S3 before importing it into Aurora. Restore the SQL dump through a MySQL-compatible client; do not confuse this with a native S3 physical-backup restore or assume AWS DMS imports RDS snapshots or SQL dump files.

### Cross-Region disaster recovery

Aurora Global Database provides cross-Region replication and failover. One source scenario runs a critical payment workload in `ap-southeast-1` with disaster recovery in `ap-northeast-1`, requiring **RPO below 5 minutes** and **RTO no more than 15 minutes**. A global database with managed failover reduces the operational work compared with restoring replicated backups.

A second scenario has a **2 TB** Aurora MySQL database in `us-east-1`, recovery in `us-west-2`, **RTO of 10 minutes**, and **RPO of 5 minutes**. It uses a global database and an EventBridge rule invoking Lambda to promote the secondary cluster when a failure is detected. Such detection and promotion require explicit configuration; a Route 53 health check alone does not promote an Aurora database.
