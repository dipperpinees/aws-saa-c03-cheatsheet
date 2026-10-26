# Kinesis

[Back to index](../../README.md)

## Overview

The source groups **four streaming services** under the Kinesis family; some have since been renamed. It includes historical limits and features, which are not verified current quotas. Together they collect, process and analyze real-time streaming data.

------------------------------------------------------------------------

## 1. Kinesis Video Streams

- Stream video directly from the device to AWS Cloud

- **Serverless**, no need to manage infrastructure

- **TLS** encryption when transmitting + **AWS KMS** when storing

- Store according to custom retention period, indexed by timestamp

### Components

| Component | Description |
|--------------|---------------------------------------------------------------|
| **Producer** | Source pushes data into stream |
| **Fragment** | Self-contained sequence of video frames |
| **Chunk** | Fragment plus producer and Kinesis metadata |
| **Consumer** | Application to read data from stream |

### Video Playback

- **HLS**: watch live playback

- **GetMedia API**: self-build application processing, low latency real-time

### Metadata

- **Nonpersistent**: attach ad hoc to each fragment

- **Persistent**: continuously attached to subsequent fragments

> **Pricing**: calculated based on volume data ingest + store + consume

------------------------------------------------------------------------

## 2. Kinesis Data Streams

- Ingest and process large-scale streaming data, hundreds of thousands of producers

- Save data **24 hours** by default, the source lists an extended-retention maximum of **7 days** (a historical limit)

- The source lists a maximum record size of **10 MiB**, but its record-definition table below says **1 MB**. These conflict; check the API and service version before relying on either value.

### Concepts

| Component | Description |
|----|----|
| **Shard** | The basic throughput unit, append-only log |
| **Data Record** | Sequence number + Partition key + Data blob (max **1 MB**) |
| **Partition Key** | Determine which shard receives record |
| **Sequence Number** | Unique ID, assigned by Kinesis when PutRecord |

### Shard Throughput

| Direction | Do not use enhanced fan-out | Use enhanced fan-out |
|-----------|------------------------------------------|----------------------------------------|
| **Input** | 1 MB/s or 1,000 records/s | 1 MB/s |
| **Output** | 2 MB/s (shared between consumers) | **2 MB/s individual** per consumer |

### Limit

- Each shard: maximum **5 read transactions/s**

- Historical source default shard limit: **500** (US East, US West, EU Ireland) / **200** (remaining regions)

- The source describes no fixed application-level count of streams or shards, but account and Region service quotas still apply.

### Pricing

- Charged by **shard/hour** + **PUT Payload Units/million**

- Enhanced fan-out: additional fee **consumer-shard/hour** + **GB data retrieval**

- Extended retention: additional fees per **shard/hour**

---

## 3. Data Firehose (Amazon Data Firehose)

- Load streaming data into data stores **fully automatically**, fully managed

- Automatically scale, no management required

- Support batch, compress, encrypt before loading

### Destinations

S3, Redshift, Elasticsearch, **HTTP endpoints**, Datadog, Splunk, New Relic, MongoDB

### Data Delivery Flow

| Destination | How |
|-------------------|--------------------------------------------------------|
| **S3** | Buffer → merge records → upload object |
| **Redshift** | Deliver to S3 first → run **COPY command** |
| **Elasticsearch** | Buffer → bulk request |
| **Splunk** | Buffer **5 MB** or **60 seconds** |

### Main features

- Convert format **JSON → Parquet or ORC** before saving to S3

- Invoke **Lambda** to transform data before delivering

- Retry Lambda **3 times** if failed

- Default buffer: **3 MB**

### Stream Sources

Kinesis Data Streams, Amazon MSK, Kinesis Agent, SDK, CloudWatch Logs/Events, AWS IoT

### Limit

- Maximum **50 delivery streams/region/account**

- Maximum record **1,000 KiB** (before base64-encoding)

> **Pricing**: according to data ingest volume + format conversion fee (if used)

------------------------------------------------------------------------

## 4. Stream analytics

The source combines historical Kinesis Data Analytics SQL applications with the service now named Amazon Managed Service for Apache Flink. The SQL mode described below is historical and should not be assumed available for new deployments.

- Analyze real-time streaming data using **SQL** or **Java (Apache Flink)**

- **Serverless**, automatic scaling, sub-second latency

### Historical SQL mode

- Supports **ANSI SQL** standard

- 2 types of input: **Streaming source** (continuous) + **Reference source** (static, to enrich data)

- Self-identify JSON, CSV formats

### Java Mode (Apache Flink)

- Based on **Apache Flink** open source

- Supports **Apache Beam Java SDK**

- **Exactly-once processing**

- State is always **encrypted** and saved incrementally

- Upload code to **S3** to deploy

- Support to backup/restore applications via API

### Capacity

- Unit: **KPU (Kinesis Processing Unit)** = **4 GB RAM** + compute + network

- Java apps: **50 GB running storage/KPU**

### Pricing

- Charged by **KPU/hour** (average)

- Java apps: add **1 KPU** for orchestration + storage fee + backup

------------------------------------------------------------------------

## Security (general)

- **AWS KMS**: at-rest encryption

- **TLS**: in-transit encryption

- **IAM**: access control management

- **VPC endpoint**: keeps traffic in the AWS network

- **CloudTrail**: log API calls

- **CloudWatch**: monitoring metrics

## Notes

### Real-time IoT ingestion and fast data access

For sensors producing large streams of records that need real-time processing and millisecond data access, ingest with **Kinesis Data Streams**, process records with **Lambda**, and store the results in **DynamoDB**. This separates streaming ingestion from low-latency database access.

Data Firehose is useful for managed delivery into storage and analytics destinations, including log archives. Redshift serves analytical and reporting workloads; DynamoDB, DAX, or ElastiCache address different low-latency application access patterns. Choose the stream processor and destination according to latency and query requirements rather than treating all streaming and database services as interchangeable.
