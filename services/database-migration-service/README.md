# Database Migration Service

[Back to index](../../README.md)

## Overview

AWS Database Migration Service (DMS) is a managed database migration and replication service. The source database can continue operating during migration, reducing downtime.

- **Homogeneous migration:** the same engine, such as Oracle to Oracle.
- **Heterogeneous migration:** different engines, such as Oracle to Aurora.
- At least one endpoint must be on an AWS service; the source excludes on-premises-to-on-premises replication.

## Migration patterns

- One-time migration into RDS or an EC2-hosted database.
- Continuous replication between a data center and AWS, in either supported direction.
- Data warehouse or data lake ingestion into **Redshift** or **S3**, including large-scale datasets.
- **Change data capture (CDC)** into S3, with date-based partitioning using transaction commit dates to build a time-based folder hierarchy.

## Sources and targets described in the source

Support depends on the engine, version, migration mode, and endpoint configuration. This table preserves the source inventory; rows are lists rather than a guarantee that every source pairs with every target.

| Sources | Targets |
|---|---|
| Oracle, SQL Server, MySQL, MariaDB, PostgreSQL | Oracle, SQL Server, MySQL, MariaDB, PostgreSQL |
| MongoDB, SAP ASE, IBM Db2 | SAP ASE, Aurora MySQL and PostgreSQL |
| Azure SQL Database and supported Microsoft Azure-hosted databases | Aurora Serverless, Redshift, S3, DynamoDB |
| Amazon DocumentDB, S3 | OpenSearch, Kinesis Data Streams, DocumentDB |
| Supported Google Cloud-hosted databases, OCI MySQL HeatWave | Neptune, Apache Kafka |

## Core resources and integrations

| Resource | Purpose |
|---|---|
| **Replication instance** | AWS-managed EC2 compute that runs replication software. |
| **Endpoint** | Connection settings for a data store, such as address, port, and credentials. |
| **Replication task** | Defines the data to migrate and the migration mode, such as full load or CDC. |
| **ARN** | Identifies a DMS resource. |

DMS integrates with **KMS** for encryption, **IAM** for access control, and **Secrets Manager** for database credentials.

## Deployment options

| Option | Management model |
|---|---|
| DMS Standard | Provision and size replication instances. |
| DMS Serverless | Automatically provisions and scales capacity for the replication workload; suitable for variable demand. |
| Homogeneous data migrations | Managed serverless migrations between like engines using native database tools. |

## Assessments and monitoring

- **Premigration assessments** validate source and target compatibility and identify potential failures, such as unsupported data types.
- **Enhanced monitoring** provides task health, table statistics, and performance metrics in a centralized dashboard.
- **DMS Fleet Advisor** historically inventoried on-premises database servers and recommended appropriately sized migration targets as a free managed feature. The source gives its end-of-support date as **May 20, 2026**.

## AWS Schema Conversion Tool

**AWS SCT** converts database schemas and code objects, including views, stored procedures, and functions. It can also analyze and convert embedded SQL in application source code. It is used when a heterogeneous migration requires schema and code changes.

### Migration combinations listed in the source

| Source | Target |
|---|---|
| SQL Server | Aurora MySQL/PostgreSQL, MariaDB, MySQL, PostgreSQL, SQL Server |
| MySQL | Aurora PostgreSQL, MySQL, PostgreSQL |
| Oracle | Aurora MySQL/PostgreSQL, MariaDB, MySQL, Oracle, PostgreSQL |
| PostgreSQL | Aurora MySQL, MySQL, PostgreSQL, Aurora PostgreSQL |
| IBM Db2 LUW | Aurora MySQL, MariaDB, MySQL, PostgreSQL, Aurora PostgreSQL |
| Sybase ASE | Aurora MySQL/PostgreSQL, MySQL, PostgreSQL |
| Oracle DW, SQL Server, Teradata, Netezza, Greenplum, Vertica | Redshift |
| Apache Cassandra | DynamoDB |

MySQL to Aurora MySQL does not require schema conversion with SCT.

### Basic schema copy

DMS can create missing target tables and primary keys. Basic schema copy does **not** migrate secondary indexes, foreign keys, or stored procedures. Use SCT or appropriate schema tooling for those additional objects.

## Pricing model

| Option | Billing basis described in the source |
|---|---|
| DMS Standard | Compute and log storage; inbound data transfer is free. |
| DMS Serverless | DMS Capacity Units (DCUs) per hour; **1 DCU = 2 GB RAM**. |
| Homogeneous migrations | Hours of migration-service use. |

## Notes

### Full load followed by ongoing replication

For an on-premises MySQL database replicated into **S3 CSV files**, configure a **full-load-and-CDC** replication task. DMS copies existing data and captures subsequent MySQL changes from the binary log. CSV is the default S3 output format described in the source.

For secure endpoint connections, add the appropriate **CA certificate** and configure the **DMS endpoint with SSL**. This provides managed ingestion without installing a custom replication service. DMS CDC can similarly replicate near-real-time changes from on-premises PostgreSQL into RDS for PostgreSQL.

### Moving provisioned Aurora to Serverless

The source’s flash-sale scenario uses a provisioned Aurora database that handles regular traffic but struggles with sudden demand. It proposes **DMS migration to a new Aurora Serverless database** to minimize disruption. This is a migration option rather than a claim that every Aurora version requires DMS; evaluate the engine version and supported conversion path.
