# DynamoDB

[Back to index](../../README.md)

Some limits and setup requirements below come from historical source notes. They are retained as source references, not verified current quotas.

## Overview

- NoSQL database, high performance, flexible expansion

- At-rest encryption, on-demand backup, point-in-time recovery **35 days**

- Data stored on **SSD**, automatically replicated across multiple AZs

- Support **multi-region, multi-master**

------------------------------------------------------------------------

## Core Components

### Data structure

- **Tables** → **Items** → **Attributes** (nested up to **32 levels**)

- Schemaless; the source lists a historical **256 tables per Region** limit.

### Primary Key (must be scalar)

| Type | Description |
|----|----|
| **Partition key** | Single key, hash to identify partition |
| **Partition key + Sort key** | Composite key, same partition key → sort by sort key |

### Secondary Indexes

| Type | Partition key | Sort key | Limit |
|-----------------|---------------|-----------|---------------|
| **GSI** (Global) | Can differ from the base table | Can differ from the base table | Maximum **20** in the source |
| **LSI** (Local) | Same as the base table | Different sort-key attribute from the base table | Maximum **5** |

### DynamoDB Streams

- Record event: **add / edit / delete** item

- **24-hour** storage, organized by **shards**

- Combine with **Lambda** to create automatic triggers

- Used for: replication, materialized views, data analysis

------------------------------------------------------------------------

## Throughput Management

| Mode | Description |
|-----------------|----------------------------------------------------------|
| **Provisioned** | Self-define RCU/WCU, can throttle, free tier |
| **On-Demand** | Automatically scale, pay-per-request, no planning needed |
| **Reserved** | Pay once in advance, long-term commitment, save costs |
| **Auto Scaling** | Customize RCU/WCU according to target utilization % |

> The source lists a limit of **4 billing-mode switches per 24 hours**; switch limits depend on direction and service rules.

### Capacity Units

| Type | Rules |
|----|----|
| **1 RCU** | 1 strongly consistent read/s or 2 eventually consistent reads/s, item ≤ 4KB |
| **1 WCU** | 1 write/s, items ≤ 1KB |

- Throttle → HTTP **400** + `ProvisionedThroughputExceededException`

------------------------------------------------------------------------

## Read Consistency

- **Eventually consistent** (default): can read old data, costs **0.5 RCU**

- **Strongly consistent**: latest data, costs **1 RCU** for the read size described above. With eventually consistent global-table replication, a strong read in one Region does not make replication to another Region strongly consistent. The source separately describes **Multi-Region Strong Consistency (MRSC)** under [Global Tables](#global-tables); the cross-Region restriction is not universal across consistency modes.

------------------------------------------------------------------------

## Main operations

### Read operations

| API | Description |
|----------------|----------------------------------------------------|
| `GetItem` | Read 1 item |
| `BatchGetItem` | Read up to **100 items** from multiple tables |
| `Query` | Read multiple items with the same partition key, maximum **1MB** |
| `Scan` | Read entire table, maximum **1MB**/request |

### Write Operations

| API | Description |
|-----------------|------------------------------------------|
| `PutItem` | Record 1 item |
| `UpdateItem` | Update 1 item (supports atomic counter) |
| `DeleteItem` | Delete 1 item |
| `BatchWriteItem` | Record up to **25 items** |

> Query/Scan both support **filter expression**, **pagination**, **Limit**

------------------------------------------------------------------------

## Transactions

- **ACID** ensures data integrity

- `TransactWriteItems`: Put/Update/Delete/ConditionCheck group

- `TransactGetItems`: Get group

- Historical source limit: **25 items** and **4 MB** per transaction.

------------------------------------------------------------------------

## TTL (Time To Live)

- Automatically delete expired items, without consuming throughput

------------------------------------------------------------------------

## Global Tables

- Multi-region, multi-master, DynamoDB automatically creates and synchronizes replicas

- Each region has only **1 replica table**

- For eventually consistent global-table replication, conflict resolution uses **last writer wins**.

- **MRSC** (Multi-Region Strong Consistency): requires exactly **3 regions**, RPO = 0

- Historical source replica-creation requirements: same name and partition key, Streams with new and old images enabled, and an empty table. Requirements depend on global-table version and consistency mode.

------------------------------------------------------------------------

## Backup & Restore

- Backup does not affect throughput

- Backups retain table data and index/configuration information, including GSIs, LSIs, and provisioned-capacity settings. Reconfigure dependent settings such as Streams after restoring; a backup does not preserve a live stream of events.

- **Cannot** overwrite existing table when restoring → always create new table

- Can restore to another region

------------------------------------------------------------------------

## DynamoDB Accelerator (DAX)

- In-memory cache, **microsecond** response

- API-compatible with DynamoDB (less code changes)

- Suitable for: **read-heavy**, bursty workloads

- **Inappropriate**: strongly consistent reads, write-intensive

- Horizontal scale: maximum **10 read replicas**

- Vertical scale: create a new cluster with a different node type

- HA: minimum **3 nodes** across multiple AZs

------------------------------------------------------------------------

## Security

- At-rest encryption using **AWS KMS, AES-256**

- Encryption at rest is enabled for DynamoDB tables and cannot be disabled. The source describes encryption as a creation-only choice; that should not be read as a restriction against changing a supported encryption-key configuration.

- Access via **IAM policies** (identity-based)

- **Web Identity Federation** support via AWS STS

------------------------------------------------------------------------

## Monitoring

- **CloudWatch Alarms/Logs/Events**

- **CloudWatch Contributor Insights**: detect the partition key that is throttled the most

- **CloudTrail**: records supported API activity with details such as source IP, principal, and time; configure data-event logging when required.

------------------------------------------------------------------------

## Pricing

- **First 25 GB free**/month

- Charge: storage (GB), WCU/RCU, Data Transfer Out

- Additional fees: DAX, Global Tables, On-demand Backup, PITR, Restore, Streams

------------------------------------------------------------------------

## Best Practices summary

- Design the schema **based on access patterns** first

- Single-table design can serve many access patterns, but choose the table layout from application requirements rather than minimizing table count as an absolute rule.

- Partition keys must be **evenly distributed** to avoid "hot partitions"

- Use **Adaptive Capacity** (on by default) to handle irregular workloads

- Use **sort key** to organize data in a hierarchical structure

- Limit the number of indexes, avoid indexing write-heavy tables

## Notes

### Partition-key cardinality and throughput

Choose partition keys with many distinct values, such as `User_ID`, `Order_ID`, or `Device_ID`. DynamoDB hashes these values to distribute data among physical partitions. When traffic is also spread across those keys, the workload can use provisioned throughput more evenly and avoid hot partitions. Cardinality alone does not prevent a hot key if most traffic still targets one value.

### Private access and cross-account backup

For an application in EKS/Fargate accessing DynamoDB and DAX in `us-east-1`, create a **DynamoDB gateway endpoint** and associate it with the appropriate route tables to keep DynamoDB service calls off the public internet. This addresses DynamoDB connectivity; DAX has its own VPC connectivity configuration.

Use **AWS Backup** to automate copies of DynamoDB backups to another AWS account for long-term retention and disaster recovery. PITR supplies recovery points within its retention window, but does not itself implement an automated cross-account backup-copy plan.

### Caching a global mobile application

For a serverless mobile game with millions of daily users, DAX can reduce eventually consistent read latency from milliseconds to **microseconds**. Configure provisioned-capacity Auto Scaling and suitable maximum RCU/WCU values so the table can handle traffic peaks. A table created using the AWS CLI does **not** receive Auto Scaling merely by default; configure it explicitly. Console creation workflows may offer enabled defaults.

API Gateway in front of Lambda can manage client traffic, authentication, and caching of frequently requested responses, such as game event information. Caching reduces repeated Lambda invocations and database reads. Global tables can place replicas closer to users in different Regions, reducing geographic access latency.

### Triggering validation after writes

When ECS processes financial data and writes results to DynamoDB, enable DynamoDB Streams and configure a Lambda event source mapping to react to new items. Streams records item-level `INSERT`, `MODIFY`, and `REMOVE` events. This allows validation logic to run without modifying the existing ECS producer solely to send a separate notification.
