# AWS DataSync

[Back to index](../../README.md)

The capabilities, transfer-mode coverage, and quotas below reflect the source notes; they are not a live quota check.

## Overview

- **Online** transfer service that automates and accelerates copying large datasets to and from AWS

- Up to **10 times** faster than conventional transfer tools in the source comparison

- In-transit encryption using **TLS**, integrity checking **both during and after transfer**

------------------------------------------------------------------------

## Supported source and destination examples

| Source/Destination | Details |
|-------------|-----------------------------------------------------|
| On-premises | NFS, SMB, HDFS, Object storage |
| AWS | S3, EFS, FSx for Windows |
| Other Cloud | Microsoft Azure (Blob/Files), Google Cloud Storage |

------------------------------------------------------------------------

## How it works (3 steps)

1. **Deploy agent** → associate with AWS account via Console/API

2. **Create task** → specify source, destination, transfer configuration

3. **Run task** → monitor via Console or **CloudWatch**

------------------------------------------------------------------------

## Key concepts

| Concept | Description |
|----|----|
| **Agent** | VM is used to read/write data at on-premises location |
| **Location** | Source or destination in transfer |
| **Task** | Includes 2 locations + transfer configuration (metadata, deleted files, permissions) |
| **Task Execution** | Once a task is run, there are 5 transition phases + 2 terminal statuses |

------------------------------------------------------------------------

## Transfer Modes

| Mode | Description |
|----|----|
| **Basic Mode** | Standard, supports all locations, maximum **50 million files/task** |
| **Enhanced Mode** | Source coverage: S3 → S3, higher performance, **unlimited** number of objects |

------------------------------------------------------------------------

## Features

- 1 agent can saturate the transmission line **10 Gbps**

- Auto-scale cloud resources for large transfers

- Support **task scheduling** (run periodically, detect changes)

- **Include/Exclude filters**: limit files/folders transfer

- Support **VPC endpoints (PrivateLink)**: transfer directly to VPC

- Support **IPv6**, **FIPS endpoints**, **Kerberos authentication** for SMB

- Can **update S3, EFS, FSx locations** without recreating

- Supports S3 storage classes: Standard, Intelligent-Tiering, Standard-IA, One Zone-IA, **S3 Glacier Flexible Retrieval, S3 Glacier Deep Archive**

------------------------------------------------------------------------

## DataSync Agent

- Need to **activate** with activation key on AWS console before use

- Must activate in **same region** as S3/EFS source/destination

- Run on-premises as **VM**

- Can run **multiple agents** simultaneously

- Need to configure **firewall** to allow connection to AWS endpoints

------------------------------------------------------------------------

## Compare with other services

| Compare | DataSync is used when | The other service is used when |
|----|----|----|
| **vs AWS CLI** | Needs automation, faster, retry/monitoring | Small, simple transfer |
| **vs Snowball/Snowball Edge** | Transfer **online**, over a network connection | **Offline**, limited bandwidth, disconnected environment |
| **vs Storage Gateway File Gateway** | **Migrate** data to S3 | After migrate: retain access + ongoing updates |
| **vs S3 Transfer Acceleration** | Not using S3 API, transfer NFS/SMB/EFS/FSx | The app has integrated S3 API, uploading large files |
| **vs AWS Transfer for SFTP** | Transfer NFS/SMB/S3/EFS/FSx automatically | Using **SFTP** to exchange data with third parties |

------------------------------------------------------------------------

## Use Cases

- **Migration**: S3, EFS, FSx for Windows

- **Hybrid processing**: on-premises ↔ AWS for data to be processed

- **Cold data archival**: transfer cold data from on-premises to **S3 Glacier Flexible Retrieval / S3 Glacier Deep Archive**

- **NAS backup/replication**: replicate NAS data to S3

------------------------------------------------------------------------

## Limits

| Parameters | Limit |
|------------------------------------------|----------------------------------------|
| Tasks per account per region | **100** |
| Files per task (Basic Mode) | **50 million** |
| Files per task (Enhanced Mode) | **Unlimited** (S3-to-S3) |
| Minimum RAM for task \> 20 million files | **64 GB** RAM for VM |
| Throughput per task | **10 Gbps** |

------------------------------------------------------------------------

## Pricing

- Charge **per GB** data copy (flat fee includes network acceleration, validation, automation)

- Additional standard fees for: **S3, EFS, FSx, KMS** requests/storage/transfer

- Copy data from AWS to on-premises: standard **AWS Data Transfer** fee charged

- Using VPC endpoints: charge **AWS PrivateLink**

- Charge **CloudWatch Logs/Events/Metrics** as standard


## Notes

### Replication over Direct Connect

For automated, accelerated transfers between an on-premises network and AWS storage over an existing Direct Connect connection, deploy a DataSync agent and configure its service endpoint and routing to use that connection. A VPC endpoint is appropriate for private VPC connectivity; the Direct Connect setup must match the endpoint type. Direct Connect does not automatically route all DataSync traffic privately merely because the connection exists.

DataSync is intended for bulk migration and scheduled replication. Storage Gateway supplies ongoing hybrid storage access, such as file access backed by AWS storage or virtual tape backups. Those access patterns differ from a managed high-throughput copying task.
