# Amazon EBS

[Back to index](../../README.md)

## Overview of Amazon EBS

- **Function:** Provides persistent block storage for EC2 instances.

- **Application:** Suitable as main storage for databases, file systems, applications that need to read/write randomly or continuously with large bandwidth (throughput-intensive).

- **Performance:** Achieve maximum performance immediately upon creation. **Note:** Volume restored from Snapshot requires initialization time (you can use Fast Snapshot Restore - FSR to bypass this delay).

- **Source-era defaults and quotas:** The source lists termination protection as off, **5,000 volumes**, and **10,000 snapshots per Region**. EC2 termination protection and EBS delete-on-termination are distinct settings; verify the applicable setting and account quotas before relying on these figures.

---

## Features

- **Multi-Attach:** Allows attaching a Provisioned IOPS volume (io1/io2) to multiple EC2 instances at the same time in an Availability Zone (AZ).

- **Elastic Volumes:** Increase capacity, change performance (IOPS) or volume type while running without rebooting. **Storage reduction is not supported.**

- **Data movement:** Volume exists only in one AZ. To move to another AZ or Region, you need to create a Snapshot and restore (Restore).

- **AWS Backup:** Built-in integration to automate backup schedules and manage data lifecycle.

- **Recycle Bin:** The source describes recovery of deleted volumes or snapshots; supported resource types must be checked before relying on this for volumes. It retains supported deleted resources that were accidentally deleted within a certain period of time.

- **Torn Write Prevention (TWP):** Supported on io2, prevents write data corruption when power is lost (very important for Databases like MySQL/MariaDB).

---

## EBS volume types

| **Volume Type** | **Name** | **Main features** | **Typical Use Case** |
|---|---|---|---|
| **General Purpose SSD** | **gp3** | Available with 3,000 IOPS and 125 MiB/s. Performance independent of capacity, low price. | Boot volume, Dev/Test, Virtual desktop, Medium sized database. |
| **General Purpose SSD** | **gp2** | Performance depends on capacity (3 IOPS/GiB) and credit accumulation mechanism. | Boot volume, low latency interactive application. |
| **Provisioned IOPS SSD** | **io2** | Designed for 99.999% durability. The source lists 64,000 IOPS for io2 and up to 64 TB for Block Express; these are source-era figures and depend on volume and instance support. | Heavy-duty databases, important systems need latency less than 1ms. |
| **Provisioned IOPS SSD** | **io1** | Provides stable IOPS, specify IOPS on initialization. | Workload requires intense I/O. |
| **Throughput Optimized HDD** | **st1** | Cheap magnetic drives, optimized for bandwidth (Throughput) instead of IOPS. | Big Data, Data warehouse, Log processing. |
| **Cold HDD** | **sc1** | Magnetic drive storage has the lowest cost. | Cold data, rarely accessed. |

---

## Encryption and security

- **Mechanism:** Using AWS KMS. Comprehensive encryption: data at rest, data transmitted between EC2 and EBS, snapshots and volumes created from that snapshot.

- **Rule:** You cannot directly encrypt an unencrypted volume (must copy data to a new encrypted volume).

- **Default:** The "EBS Encryption by Default" feature can be enabled to automatically encrypt every newly created volume.

---

## Snapshots and backup

- **Incremental backup:** Snapshot only saves data blocks that have changed compared to the most recent version, helping to save space and time.

- **Share & Copy:** Can share snapshots between accounts (need to use customer managed KMS key if encrypted) and copy to another Region.

- **EBS Snapshot Archive:** Cheap storage tier to save long-term snapshots (over 90 days) for compliance purposes, saving up to 75% of costs.

---

## Cost and performance

- **Charging:** Charged based on **allocated capacity** (provisioned), regardless of whether you use it all or not. Calculated in seconds (minimum 60s).

- **Unused Volume:** Volume has been Detached from EC2 **still charged** for storage. You must Delete to stop billing.

- **EBS-Optimized Instances:** Newer EC2s automatically optimize dedicated network bandwidth for EBS at no additional cost.

- **Monitor & Improve:** Monitor status (Ok, Warning, Impaired) and metrics (IOPS, latency) via CloudWatch. Use RAID 0 configuration on the OS to combine multiple volumes to maximize performance. Linux operating systems need to use the command `resize2fs` or `xfs_growfs` after increasing capacity using Elastic Volumes.

## Notes

### Automated snapshot retention

Amazon Data Lifecycle Manager manages EBS snapshots and EBS-backed AMIs. Select volumes or instances by tag, choose a schedule such as every 12 hours or daily, and set retention by age or count. The service has no additional charge; storage for the generated backups is billed. Retention policies prevent unnecessary accumulation of snapshots.

### IOPS ratios and queue length

For **io1**, the source uses a maximum provisioned IOPS-to-capacity ratio of **50:1**, subject to its stated **64,000 IOPS** ceiling and instance limits. A **10 GiB** volume therefore supports up to `10 × 50 = 500 IOPS` under that ratio. Provisioning 600 or 800 IOPS exceeds the ratio; 400 IOPS is valid but below that example's maximum.

The source compares an **io2 ratio of 500:1**, yielding `10 × 500 = 5,000 IOPS` for 10 GiB. This is a source-era comparison; io2 generation and Block Express limits require separate validation.

Queue length measures outstanding I/O requests. Low-latency SSD workloads benefit from avoiding excessive queueing. Throughput-oriented HDD workloads need enough outstanding sequential I/O to use their available throughput. The appropriate queue depth depends on the workload rather than a universal low or high value.

### Encryption and aggregate capacity

When EBS encryption by default is enabled in a Region, a new volume created from an unencrypted snapshot is encrypted. The existing snapshot itself remains unencrypted.

The source describes an Oracle database rehosted on a memory-optimized Linux EC2 instance, using a **1 TB io1 volume provisioned for 64,000 IOPS**, and recommends adding io1 volumes with an **LVM stripe** to improve aggregate storage performance. Its size/IOPS combination conflicts with the 50:1 ratio above if 1 TB is interpreted literally; treat it as an illustration of a single-volume ceiling rather than a validated configuration.

When a workload exceeds a single io1 volume's applicable limit, multiple EBS volumes can be combined through operating-system storage management such as LVM. Aggregate performance still depends on the instance's EBS bandwidth and IOPS limits; RAID 0 does not provide redundancy.
