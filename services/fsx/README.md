# Amazon FSx

[Back to index](../../README.md)

## Overview

- Fully managed **third-party file system** using SSD and HDD

- There are **4 types** of FSx solutions

---

## FSx for Windows File Server

- Native Microsoft Windows file system, full support **SMB, NTFS, Active Directory**

- At-rest encryption with **AWS KMS**, SMB encryption in transit and Kerberos authentication

- Compliance: **ISO, PCI-DSS, SOC, HIPAA eligible**

### Main features

- **User storage quotas**: limit the capacity of each user

- **DFS Namespaces**: merge many file systems into a common namespace → scale to hundreds of PB, tens of GB/s

- **Data deduplication**: reduces storage costs by eliminating duplicate data

- **Backups:** Automatic daily backups with a source-stated default retention of **7 days**; VSS provides application-consistent Windows backups

- **Windows shadow copies**: restore files/folders to previous versions

- Cache on-premises via **FSx File Gateway**

### Compute support

EC2, Amazon WorkSpaces, AppStream 2.0, VMware Cloud on AWS

### Migration

Use **RoboCopy** (Windows Robust File Copy) to migrate data + metadata (ownership, ACLs)

### Performance & Limitations

- SSD storage, source-listed maximum **64 TB/file system** and throughput up to **2 GB/s**; these are configuration-dependent source-era limits

- Maximum **100 file systems** by default in the source (can be increased to thousands)

- Source-listed total default storage: **512 TiB**, total throughput: **10 GBps**

---

## FSx for Lustre

- High-performance **parallel file system** for fast processing workloads

- **POSIX-compliant**, supports thousands of instances accessing simultaneously

- HDD options reduce costs by up to **80%** compared to SSD (for throughput-intensive workloads)

### Deployment Types

| Type | Description |
|----------------|----------------------------------------------|
| **Scratch** | Temporary, no replication, low cost |
| **Persistent** | Long-term, with replication |

### S3 integration

- When linking to S3 bucket: **S3 objects are displayed as files**

- Record the results back to S3

- Managed through **Data Repository Associations (DRA)**

- Use FSx as **"hot" storage**, S3 as **"cold" storage**

### Performance

- Sub-millisecond latency

- Throughput up to hundreds of **GB/s**, millions of **IOPS**

- Source example baseline: **200 MB/s per TB** of storage; throughput depends on deployment and configuration

### Access on-premises

Via **AWS Direct Connect or VPN** + parallel copy tools

---

## FSx for NetApp ONTAP

- Shared file storage, **sub-millisecond latency**, supports **Linux, Windows, macOS**

- Multi-protocol: **NFS, SMB, iSCSI**

### Structure

- **Storage Virtual Machines (SVMs)** + **Volumes**

- Endpoints: **Management** (SSH/REST API) + **Intercluster** (SnapMirror/FlexCache)

### Storage Tiering (automatically transfer cold data to capacity pool)

| Mode | Description |
|----|----|
| **Auto** | Migrate cold user data from active file system and snapshots |
| **Snapshot-only** | Only move data not associated with the active file system |
| **All** | Move all user data |
| **None** | Remain in primary tier |

### Special features

- **SnapLock**: WORM compliance

- **Data Compression/Deduplication**: saves storage

- Supports **Multi-AZ** deployments

---

## FSx for OpenZFS

- Maximum **1 million IOPS**, latency **hundreds of microseconds**

- Runs on **AWS Graviton processors**

- Migrate from on-premises ZFS **no need to edit code**

- Protocol: **NFS** (Linux, Windows, macOS, containers)

### Data Management

- **Z-Standard compression**

- **Instant point-in-time snapshots**

- **Data cloning**

- Backup stored on **S3**, supports cross-region backup

- Thin provisioning, user/group quotas, multiple volumes/file systems

- Supports **Multi-AZ** deployments

---

## Quick comparison of 4 types of FSx

|  | **Windows** | **Lustre** | **NetApp ONTAP** | **OpenZFS** |
|----|----|----|----|----|
| **Protocol** | SMB | Lustre (POSIX interface) | NFS, SMB, iSCSI | NFS |
| **OS** | Windows | Linux | Linux/Win/macOS | Linux/Win/macOS |
| **Use case** | Windows workloads, AD | HPC, ML, Big Data | Enterprise multi-protocol | ZFS migration |
| **S3 integration** | ❌ | ✅ | ❌ | ❌ |
| **Multi-AZ** | ✅ | ❌ | ✅ | ✅ |
| **Latency** | Milliseconds (source comparison) | Sub-millisecond | Sub-millisecond | Hundreds of microseconds |

---

| Services | Main protocol |
|----------------------------|---------------------|
| **FSx for Lustre** | **Lustre protocol** |
| FSx for Windows File Server | SMB |
| EFS | NFS |
| FSx for NetApp ONTAP | NFS, SMB, iSCSI |
| FSx for OpenZFS | NFS |

## Security (general)

- At-rest encryption: **AWS KMS**

- Identity-based auth via **Microsoft Active Directory** (SMB)

- **CloudTrail**: monitor and log API calls

- Compliance: **ISO, PCI-DSS, SOC, HIPAA eligible**

---

## Pricing

- Charged per **hour** based on:
  - **Storage capacity** (per GB-month)

  - **Throughput capacity** (per MBps-month)

  - **Backup storage** (per GB-month)

- Enable **data deduplication** to reduce backup costs

## Notes

### Choosing a file system

- **Windows File Server:** SMB shares, Active Directory integration, NTFS permissions, home directories, content management, and Windows web servers.
- **NetApp ONTAP:** Multi-protocol enterprise storage, including **iSCSI block access**, SMB, and NFS; supports Windows workloads requiring iSCSI and Multi-AZ availability. Snapshot, mirroring, and cloning capabilities support migration, databases, and development workflows.
- **Lustre:** Linux HPC, machine learning, and video rendering with a parallel POSIX file interface and direct S3 integration.
- **OpenZFS:** Low-latency NFS storage for databases, media processing, development/test, and compatible ZFS migrations.

### S3 objects as HPC files

A data repository association makes S3 objects visible to an HPC cluster as files in FSx for Lustre. For example:

```text
s3://my-data/input/a.csv  →  /mnt/fsx/input/a.csv
s3://my-data/input/b.csv  →  /mnt/fsx/input/b.csv
```

The cluster mounts FSx for Lustre, processes files with the Lustre client, and can export results to S3. This provides a high-performance file interface over a dataset held in object storage.

For hybrid file shares that need local access to S3-backed files, S3 File Gateway provides SMB or NFS access; it serves a different purpose from a Lustre HPC file system.

### Remote access to managed SMB shares

For a managed replacement of an on-premises file service accessed by remote employees and connected on-premises systems, create an **FSx SMB file share** and connect remote clients through a **client VPN**. This preserves file-share semantics without exposing storage publicly. Volume Gateway provides iSCSI block access rather than an SMB share.
