# Amazon EMR

[Back to index](../../README.md)

## Overview

- **EMR (Elastic MapReduce):** A **managed cluster** service running open source big data frameworks.

- **Mechanism:** Uses distributed computing to process terabytes or petabytes of data.

## Frameworks

- **Apache Spark:** In-memory distributed data processing.

- **Apache Hive:** Use SQL to query data on distributed storage.

- **Hadoop (MapReduce):** Core platform for splitting and processing data.

- **Presto/Trino:** High-speed interactive SQL queries.

| **Features** | **AWS Glue (Serverless)** | **Amazon EMR (Cluster)** |
|---|---|---|
| **Essence** | Automated, serverless ETL service. | A managed cluster for big-data processing. |
| **Administration** | AWS manages the underlying infrastructure. | Select instance types, capacity, and cluster configuration. |
| **How to use** | Drag and drop or write simple Python/Scala scripts. | Configure processing frameworks such as Spark, Hive, and Presto. |
| **Cost** | Pay for ETL compute while jobs run; the source describes per-second billing. | Cluster compute and EMR charges; the source describes hourly cluster billing and savings from Spot. |
| **Startup speed** | Serverless job startup varies. | Cluster provisioning typically takes several minutes. |

The billing granularity above preserves the source comparison; actual charges and minimum durations depend on the service mode and resource configuration.

## Notes

### Analytics service selection

EMR runs open-source distributed processing frameworks such as Spark, Hadoop, Hive, and Presto/Trino. Redshift provides a managed data warehouse for high-performance SQL analytics and BI tools. Glue provides serverless ETL and cataloging; Athena queries data in S3 without a provisioned query cluster. Glue with Athena is useful for preparing and querying data when usage is intermittent. Selection depends on data layout, query patterns, and operational requirements.

For structured and semi-structured data collected in an S3 data lake, the source combines **EMR processing with Redshift storage of the processed output**. EMR runs the big-data frameworks; BI tools then query the Redshift results using standard SQL.
