# Amazon S3

[Back to index](../../README.md)

## Overview

- **Amazon S3 (Simple Storage Service)** stores data as **objects** inside **buckets**.

- An **object** consists of data, a key, and metadata.

- **Key** is the unique name of the object in the bucket.

- Scales to very large datasets without preallocating bucket capacity.

## Buckets

- Each bucket allows:
  - Access control (create, delete, list objects).

  - View access logs.

  - Select Storage Region.

- **Bucket naming rules**:
  - Must be globally unique (DNS-compliant).

  - Cannot change name after creation.

  - The bucket name appears in the URL of the objects.

- Source-listed default quota: **10,000 buckets** per AWS account (can be increased to 1 million on request).
  - The source lists the first 2,000 buckets as free.

  - The source lists a monthly bucket fee above 2,000; check the applicable current pricing.

- Region cannot be changed after creation.

- **Directory Buckets**: New bucket type used for **S3 Express One Zone** (single-digit millisecond latency).

- Can host **static website** by configuring bucket.

- Empty a bucket, including its versions and delete markers, before deleting it. The source cites a console bulk-empty limit of **100,000 objects**; versioned buckets can be deleted through the CLI after all versions and markers are removed.

## Data consistency

- **Strong Read-After-Write Consistency** (strongest today):
  - After a successful object write, overwrite, or delete, subsequent GET/HEAD/LIST requests reflect the change.

  - Strong object read-after-write and list consistency applies in all Regions. Bucket configuration changes can have different propagation behavior.

- **Eventual Consistency** (some cases only):
  - Change bucket configuration (Versioning, Public Access Block...).

  - Delete bucket (may remain visible in the bucket list for a few seconds).

## Storage classes

### Frequent access

- **S3 STANDARD**: General-purpose storage for frequently accessed data.

- **S3 EXPRESS ONE ZONE**: Single-digit ms latency, using **Directory Bucket**, the source describes up to 10× faster access and 50% lower request cost than Standard for applicable workloads.

### Infrequent access

- **S3 STANDARD_IA**: Long-term data but rarely accessed, multi-AZ storage.

- **S3 ONEZONE_IA**: Cheaper, stores data in one AZ and does not provide resilience to loss of that AZ.

### Intelligent-Tiering

- Automatically switch between tiers according to access frequency.

- Tier: Frequent Access → Infrequent Access (after 30 days) → Archive Instant Access (after 90 days).

- Optional asynchronous archive tiers can be configured after **90 or 180 days**, as described in the source.

- **No retrieval fee**.

### Archival storage

- **S3 Glacier Instant Retrieval**: Access ms, used for data accessed once/quarter.

- **S3 Glacier Flexible Retrieval**: Accessed several times/year, retrieval from minutes to hours.

- **S3 Glacier Deep Archive**: Cheapest, accessed 1–2 times/year, retrieval 12 hours (Standard) or 48 hours (Bulk).

### Storage class comparison

The source records the following durability, availability, SLA, size, duration, and latency figures. Pricing and feature support can change; these figures are not a current pricing guarantee.

| Storage Class | Durability | Availability | SLA | AZs | Min size | Min duration | Retrieval fees | Latency | Lifecycle |
|----|----|----|----|----|----|----|----|----|----|
| **S3 Standard** | 11 9's | 99.99% | 99.9% | ≥3 | \- | \- | No | ms | Yes |
| **S3 Express One Zone** | 11 9's | 99.95% | 99.9% | 1 | 512 KB | 1 hour | No | single-digit ms | No |
| **S3 Intelligent-Tiering** | 11 9's | 99.99% | 99% | ≥3 | \- | 30 days | No | ms | Yes |
| **S3 Standard-IA** | 11 9's | 99.99% | 99% | ≥3 | 128 KB | 30 days | Yes | ms | Yes |
| **S3 One Zone-IA** | 11 9's | 99.5% | 99% | 1 | 128 KB | 30 days | Yes | ms | Yes |
| **Glacier Instant Retrieval** | 11 9's | 99.9% | 99% | ≥3 | 128 KB | 90 days | Yes | ms | Yes |
| **Glacier Flexible Retrieval** | 11 9's | 99.99% | 99% | ≥3 | 40 KB | 90 days | Yes | minutes–hours | Yes |
| **Glacier Deep Archive** | 11 9's | 99.99% | 99.9% | ≥3 | 40 KB | 180 days | Yes | hours | Yes |

**S3 on Outposts**: Store objects on an on-premise environment (AWS Outposts).

## APIs

- **REST API** (recommended): Use HTTP.

- **SOAP** (deprecated, only over HTTPS).

## Bucket configuration

| Subresources | Description |
|----|----|
| **location** | Specify Region |
| **policy & ACL** | Access permissions (default ACL disabled, use Bucket Policy) |
| **cors** | Allow Cross-Origin |
| **website** | Host static website |
| **logging** | Record access log |
| **event notification** | Send events to SNS, SQS, Lambda, EventBridge |
| **versioning** | **Best practice** – restore deleted/overwritten objects |
| **lifecycle** | Automatically change class or delete object |
| **replication** | CRR (Cross-Region) & SRR (Same-Region) |
| **tagging** | Tag for cost management |
| **object-lock** | WORM (not allowed to delete/overwrite) |
| **inventory** | Report list of objects |

## Objects

- Default **private**.

- Each object has: data + key + metadata.

- To change user-defined metadata, copy the object with replacement metadata.

- Supports objects up to **50 TB** (must use Multipart Upload if \> 5 GB).

- Additional checksum support: SHA-1, SHA-256, CRC32, CRC32C.

- **Conditional Writes**: Prevent accidental overwrites (If-None-Match, If-Match).

- **Tagging**: Maximum 10 tags/object.

- **Object Lock**: Retention period or Legal Hold (only used with Versioning).

- **Object Ownership**: Bucket Owner Enforced (default) – bucket owner owns all objects.

## S3 Select

- Query only the needed data from the object (CSV, JSON, Parquet).

- Save costs and increase speed.

## Lifecycle management

- **Transition actions:** Move objects to another storage class, subject to transition rules. Standard-to-Standard-IA transitions require at least **30 days**.

- **Expiration actions:** Delete eligible objects or versions according to lifecycle rules.

## Data integrity and metadata

- S3 automatically checks and corrects data errors (CRC checksum).

- **S3 Metadata Tables**: Automatically create Apache Iceberg tables containing metadata, queries using Athena, Redshift, Spark...

- **S3 Tables**: Bucket specializes in storing tabular data (Iceberg format), optimized for analytics & ML.

## Storage Browser

- Open-source component to embed web interface into application (React).

- Allows browsing, uploading, downloading, and deleting directly from the app.

## Pricing

- Only pay for what you use:
  - **Storage**: Capacity + storage class.

  - **Requests**: GET, PUT, LIST…

  - **Retrieval**: From IA/Glacier.

  - **Early Deletion**: Fee if deleted before min duration.

  - **Data transfer:** Inbound transfers to S3, S3-to-EC2 transfers in the same Region, and transfers from S3 to CloudFront have no S3 data transfer charge in the source model. Other transfers can be billed.

## Networking

- **Virtual Hosted-style** (recommended): `https://bucket.s3.region.amazonaws.com`

- **Path-style:** Legacy addressing style; virtual-hosted-style is recommended.

- **Zonal Endpoints** for Directory Buckets (S3 Express One Zone).

- **S3 Transfer Acceleration**: Accelerate long distance upload/download (using CloudFront edge locations).

## Security

- **Bucket Policies** (JSON, recommended).

- **ACLs**: Disabled by default (Bucket Owner Enforced).

- **IAM + ABAC** (use tags to control access).

- **S3 Access Grants:** Map data access to identities, including corporate directory identities through supported IAM Identity Center integration.

- **Versioning:** Preserves previous versions against accidental deletion or overwrite; required for features such as Object Lock and replication.

- **Encryption**:
  - **SSE-S3** (default).

  - **SSE-KMS** (with audit trail).

  - **DSSE-KMS** (2 layers).

  - **SSE-C** (you provide the key).

- **MFA Delete:** Requires MFA for changing the versioning state and permanently deleting object versions when configured.

- **Requester Pays:** The authenticated requester pays eligible request and data-transfer charges; the bucket owner continues to pay storage.

## Monitoring

- **CloudWatch**: Storage metrics (daily), Request metrics (1 minute).

- **CloudTrail:** Records management activity; enable appropriate S3 data events for object-level API auditing.

- **S3 Storage Lens**: Dashboard analytics across the organization.

## Static website hosting

- Enable in Properties → Bucket.

- Direct S3 website endpoints require public-read access, commonly through a bucket policy and compatible Block Public Access settings.

- Endpoint: region-specific website endpoint.

## Event notifications

- Send notifications when:
  - Object created, deleted, lifecycle transition...

- Destination: SNS, SQS, Lambda, EventBridge.

## Replication

- Automatically copy objects between buckets.

- Requirements: Both buckets must have Versioning enabled.

- **S3 Replication Time Control (RTC):** Provides a **15-minute** replication threshold with an SLA for eligible objects; it is not a guarantee that every object always completes within that interval.

## Batch Operations

- Manage billions of objects at the same time (copy, tag, restore from Glacier...).

## Storage Lens

Provides storage analytics dashboards, insights, and recommendations across accounts and the organization.

---

## Notes

### WORM retention and versioning

**S3 Object Lock requires versioning** and protects individual object versions. Uploading an object under an existing key creates another version; it does not mutate a locked prior version. Configure retention for the required versions rather than assuming that enabling Object Lock alone retains everything indefinitely.

- **Governance mode:** Principals with `s3:BypassGovernanceRetention` and the required bypass request can override retention.
- **Compliance mode:** A protected version cannot be overwritten or deleted, even by the account root user, until its retention period expires. Use a **one-year compliance retention period** when root must also be restricted for exactly one year.
- **Legal hold:** Remains active until explicitly removed; it has no built-in expiration date. Principals with `s3:PutObjectLegalHold` can apply or remove it.

For the source's regulatory archive design, preserve versions in S3 Standard, apply compliance retention, and transition eligible data to **Glacier Deep Archive after three years** when it is accessed only once a year. A lifecycle transition changes storage class while retention continues to protect the version.

### VPC access and centralized endpoint controls

A **VPC-only S3 Access Point** restricts access through that access point to a specified VPC. Coordinate its policy with the bucket policy so another path does not bypass the intended restriction. This can be combined with Object Lock for confidential tax documents needing both network restriction and WORM retention.

A **gateway endpoint policy** centrally restricts requests using that endpoint, for example allowing only `s3:GetObject` and `s3:PutObject` against trusted bucket ARNs. It avoids editing many bucket policies for that endpoint's allowed destinations. It does not independently grant access or restrict requests that bypass the endpoint.

### Per-team access points

For **20 teams in 20 organization accounts** sharing one bucket, use access points with separate names, policies, and network controls. SCPs can constrain access-point administration across organization accounts but do not grant object permissions.

| Access point | Intended access |
|---|---|
| `sales-ap` | Read/write only `sales/` |
| `finance-ap` | Access only `finance/` |
| `analytics-ap` | Read the data needed for analytics |
| `vpc-only-ap` | Access only from its designated VPC |

Access points are not inherently private; VPC-only access is an explicit configuration.

### Lifecycle and migration choices

For a global application on EC2 Auto Scaling, use S3 object storage to share uploaded files independently of individual EC2 instances. Transition files older than **two years (730 days)** to Standard-IA when immediate access is needed, or an appropriate Glacier class when retrieval delay is acceptable. Standard-IA is designed for infrequent access with rapid retrieval; Intelligent-Tiering provides automatic access-tier management. EBS is AZ-scoped block storage, and RAID 0 increases failure exposure without providing shared multi-region object storage.

Objects can be written directly into supported destination storage classes; they do not universally need to start in Standard. The source's DataSync example transfers historical on-premises records directly to **Glacier Deep Archive**, avoiding an unnecessary 30-day Standard period.

| Lifecycle transition example | Earliest source age rule |
|---|---|
| Standard → Standard-IA | 30 days |
| Standard → Glacier Flexible Retrieval or Deep Archive | No general 30-day object-age wait |
| Standard-IA → Glacier | No additional general object-age wait; minimum storage-duration charges still apply |

Minimum billable storage duration is separate from lifecycle eligibility. Object size, supported transitions, and applicable minimum-size rules also matter.

For a **250 TB** archive over a **100 Mbps** internet connection, the source selects multiple Snowball devices for offline import into S3. Actual device availability, capacity, shipping time, and cost must be evaluated for a deployment.

### Small-file retention scenario

The source scenario migrates millions of **10 KB** files with DataSync, accesses them once or twice during the first year with immediate availability, then retains an archive for at least **seven further years**. Its selected design stores objects in **Standard-IA** and transitions them to **Deep Archive after one year**, retaining them for seven years. Standard-IA bills a minimum of **128 KB per object**, so the small-file overhead is material. This scenario's source selection should not be generalized into a cheapest-storage rule without calculating object-size charges and archive access requirements.

### Glacier retrieval capacity

For an archive audit requiring retrieval in **under 15 minutes** with available expedited capacity, the source combines **Expedited retrieval** with **provisioned retrieval capacity** for Glacier Flexible Retrieval.

- Expedited requests typically complete in **1–5 minutes** for archives below **250 MB**.
- Standard retrieval takes approximately **3–5 hours**; Bulk takes approximately **5–12 hours** in the source's Glacier Flexible Retrieval comparison.
- One provisioned capacity unit provides at least **three expedited retrievals per five minutes** and up to **150 MB/s** of retrieval throughput. It addresses expedited capacity availability; it is not a blanket guarantee for every possible archive size or deadline.

Range retrieval reduces bytes requested, while query filtering reduces returned data; neither alone reserves expedited capacity.

### Detailed event types

Direct S3 event notifications support **SNS, SQS, and Lambda**; S3 can also send events through **EventBridge**. Amazon MQ is not a direct S3 notification destination. Use `s3:ObjectCreated:*` for new objects and `s3:ObjectRemoved:Delete` for permanent deletion, including deletion of a particular version. `DeleteMarkerCreated` alone does not cover permanent version deletion.

| Event type | Meaning |
|---|---|
| `s3:ObjectCreated:*` | Any supported successful object creation operation |
| `s3:ObjectCreated:Put` | PUT creates an object |
| `s3:ObjectCreated:Post` | POST creates an object |
| `s3:ObjectCreated:Copy` | COPY creates an object |
| `s3:ObjectCreated:CompleteMultipartUpload` | A multipart upload is completed |
| `s3:ObjectRemoved:*` | Supported deletion events, including delete markers |
| `s3:ObjectRemoved:Delete` | Object or object version is permanently deleted |
| `s3:ObjectRemoved:DeleteMarkerCreated` | A delete marker is created in a versioned bucket |
| `s3:ObjectRestore:Post` | Archive restoration is initiated |
| `s3:ObjectRestore:Completed` | Archive restoration is completed |
| `s3:ReducedRedundancyLostObject` | S3 detects loss of an object in the legacy Reduced Redundancy Storage class |
| `s3:Replication:OperationFailedReplication` | Replication of an eligible object fails |
| `s3:Replication:OperationMissedThreshold` | Eligible RTC replication exceeds the 15-minute threshold |
| `s3:Replication:OperationReplicatedAfterThreshold` | Eligible object replicates after the RTC threshold |
| `s3:Replication:OperationNotTracked` | Eligible object's replication is no longer tracked by replication metrics |

Failed API operations do not produce these successful creation/deletion events. The `ObjectRemoved` family does not report automatic lifecycle deletions; lifecycle-specific event types serve that purpose. There is no `s3:ObjectAdded:*` event type.

### Transfer cost and Requester Pays

For conversion of **200 TB** of vendor-format backups, run the vendor's software on EC2 in the **same Region** as the S3 bucket, then write results back to S3. This avoids the cross-region S3-to-EC2 transfer charge in the source's model. Compute, storage, API requests, and other applicable charges remain; the source's illustrative cross-region rate of **$0.02/GB** is not a universal current rate.

For sharing **5 TB** from `us-east-1` with a European partner, **Requester Pays** shifts eligible download and request charges to the requester while the owner retains storage charges. Anonymous requests are unsupported. The requester authenticates using an AWS account and acknowledges the charge, for example through `x-amz-request-payer: requester`. Permissions must still authorize access.

### Public assets and CORS

S3 buckets and objects are private by default. Where direct public access is intended, a bucket policy can allow `s3:GetObject` to `Principal: "*"` for the relevant objects, including future uploads. This requires compatible Block Public Access settings. Public-read object ACLs are a legacy alternative only where ACLs are enabled; new Bucket Owner Enforced buckets disable ACLs by default.

**CORS does not grant permissions.** It controls browser cross-origin behavior. An unauthorized request still receives **403 Access Denied** even if the CORS rule permits its origin.

### Request-rate scaling

The source gives at least **3,500 PUT/COPY/POST/DELETE** or **5,500 GET/HEAD requests per second per partitioned prefix**. Its example of **2,000 PUT** and **3,500 GET per second** is within those published rates and does not require randomizing key prefixes.

Multiple prefixes can distribute higher request rates: two prefixes provide a planning example of **11,000 GET requests per second**. S3 scales request handling, and abrupt increases may require gradual scaling; prefix counts are not a guarantee of instant throughput. Random-prefix advice in pre-2018 designs should not be applied as a universal requirement.
