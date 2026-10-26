# AWS Storage Gateway

[Back to index](../../README.md)

## Overview

- Connect **hybrid storage** between on-premises and AWS Cloud

- Use standard storage protocols (NFS, SMB, iSCSI)

- Store files as **S3 objects**, archive tapes in **S3 Glacier storage classes**, and back up block volumes as **EBS snapshots**

- HIPAA eligible, **PCI DSS** compliant

---

## Gateway types

|  | **S3 File Gateway** | **Volume Gateway** | **Tape Gateway** |
|----|----|----|----|
| **Protocol** | NFS v3/4.1, SMB v2/3 | iSCSI | iSCSI (VTL) |
| **Hosted at AWS** | Amazon S3 | Amazon S3 (EBS Snapshots) | S3 Glacier / Glacier Deep Archive |
| **Use case** | File sharing, S3 access | Block storage | Long-term Backup/Archive |

---

## S3 File Gateway

- Deploy as VM: **VMware ESXi, Hyper-V, Linux KVM**

- Direct access to S3 from any AWS service

- Supports S3 storage classes: **Standard, Standard-IA, One Zone-IA, Intelligent-Tiering**

- Support **S3 Object Lock (WORM)**: file edits/deletes/renames → create new version, do not delete old version

- Manage S3 data with: **lifecycle policies, cross-region replication, versioning**

- Maximum local cache: **64 TB**

---

## Volume Gateway

Provisioning block storage volumes mounted via **iSCSI**

### Cached Volumes

- Data is stored on **S3**, cached locally in **frequently accessed** parts

- Size: **1 GiB – 32 TiB**/volume

- Maximum **32 volumes**/gateway

### Stored Volumes

- All data is saved **locally**, backed up async to S3 as snapshots

- Size: **1 GiB – 16 TiB**/volume

- Maximum **32 volumes**/gateway, total capacity **512 TiB**/gateway

> Supports **detach/attach volumes** between gateways to migrate or upgrade hardware

---

## Tape Gateway

- Expose backups as **virtual tapes** and archive tapes in supported S3 Glacier storage classes.

- **VTL (Virtual Tape Library)** interface compatible with current backup software

- Supports **S3 Glacier Deep Archive** → reduces long-term storage costs by up to **75%**

- Supports **WORM** and **Tape Retention Lock**

- Virtual tape size: **100 GiB – 15 TiB**

- Maximum **1,500 tapes/gateway**, total **1 PiB**

---

## Hosting options

The source lists these deployment options; platform and appliance availability depend on the current AWS offer.

- VM on-premises: **VMware ESXi, Hyper-V, KVM**

- VM on **VMware Cloud on AWS**

- **Hardware appliance** on-premises

- **AMI on EC2 instance**

> Gateway saves data in **AWS Region where gateway is activated**. Local cache uses **read-through and write-back** to reduce latency.

---

## File Share (S3 File Gateway)

- Create **NFS or SMB** file share via Console or API

- Can access S3 bucket of **other AWS account**

- Support **ACLs on SMB shares** for security control

---

## Security

- **AWS KMS**: encrypts data recorded to virtual tape

- **CHAP**: iSCSI authentication using a challenge-response exchange

- **IAM**: authentication and access control

---

## Pricing

- Charged according to: **storage type, usage capacity, number of requests, data transfer out**

- Tape Gateway: only charged **actual recorded data**, not based on tape capacity


## Notes

### Hybrid file access with local cache

S3 File Gateway caches frequently accessed files locally; caching is not limited to Volume Gateway. For an on-premises simulation application with data stored in S3, this can retain low-latency access to its working set without keeping the entire dataset on premises.

### Migrating an NFS workload fully to AWS

For a legacy application moving entirely to AWS that must keep using **NFS** and needs scalable, highly available shared storage, **Amazon EFS** is the source's selected design. S3 File Gateway is primarily a hybrid file interface with local caching; FSx for Windows File Server uses SMB rather than the NFS interface required by this application.
