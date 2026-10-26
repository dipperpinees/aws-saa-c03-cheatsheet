# CloudTrail

[Back to index](../../README.md)

------------------------------------------------------------------------

The feature coverage and numerical quotas below reflect the source notes; they are not a live inventory of all supported resources or current limits.

## Basic concepts

- Record supported **actions of users, roles, and AWS services** performed via Console, CLI, SDK, and API — as **Events**.

- **Enabled by default** when creating an AWS account.

- CloudTrail focuses on **auditing API activity**.

- **Event History**: view, search, download **90 days** of recent activities — for free.

- **CloudTrail Lake**: aggregate, immutably store, and query activity logs using SQL.

- **CloudTrail Insights**: detect unusual activities (spikes in resource provisioning, bursts of IAM actions...).

------------------------------------------------------------------------

## Additional features

- **Network activity events** record supported API activity through VPC endpoints. The source associates Lambda, Comprehend, and IoT FleetWise with this section, but that list is not a verified network-event support matrix.

- **Advanced event selectors**: log data events for Bedrock, SageMaker, Aurora, GameLift, Q, Step Functions...

- **CloudTrail Lake dashboards**: custom dashboards, maximum **10 widgets** per dashboard.

- **Resource-based policies** on Lake event data stores: support **cross-account querying**.

- **Enriched events**: management/data events can be enriched with resource tag keys and IAM global condition keys.

- **VPC endpoint policies**: support private network access.

- **Encryption**: log files can use **SSE-KMS**. Digest files have a separate encryption behavior; do not assume the trail log-file KMS setting also applies to digests.

------------------------------------------------------------------------

## Trails

- Create Trail to **archive, analyze, respond** to changes in AWS resources.

### Two types of Trail:

| Type | Description |
|----|----|
| **All Regions** (default on Console) | Record events in **all Regions**, deliver log to 1 S3 bucket |
| **One Region** (default via CLI/API) | Only record events in **specified Region** |

- **Organization trail**: records the configured event types across accounts in an AWS Organization. The source describes creation from the management account; delegated-administrator support should be checked for the intended operation.

- Default log is encrypted with **S3 SSE**; You can use **AWS KMS** instead.

- Logs are saved in S3 **as long as you want** — you can set **S3 Lifecycle rules** to archive or auto-delete.

- Can configure **SNS notifications** when log files are delivered.

- CloudTrail publishes log files approximately **every 5 minutes**.

------------------------------------------------------------------------

## Event types

### Management Events

- **Enabled by default**.

- Record **control plane operations** — resource management operations (CreateBucket, RunInstances, AttachPolicy...).

### Data Events

- **Not enabled by default** (high-volume, extra charge).

- Record **data plane operations** — direct operations on/in resources (S3 GetObject, Lambda Invoke, DynamoDB PutItem...).

- Configure the resources and event types to record. Lambda functions, DynamoDB tables, and S3 buckets are examples rather than an exhaustive list.

### Insights Events

- **Not enabled by default**.

- Detect **unusual activity** — CloudTrail detects when API usage differs significantly from the normal pattern.

- Provide information: associated API, incident time, statistics.

- Log to S3 when an anomaly is detected.

### Global Services Events

- IAM, STS, CloudFront, **Route 53** → events are logged at **US East (N. Virginia)**.

- Any trail that has "include global services" enabled will receive these events.

### Filter Events

- Can filter by: **Time range, Event name, User name, Resource name, Event source, Event ID, Resource type**.

------------------------------------------------------------------------

## Monitoring

- Use **CloudWatch Logs** to monitor log data from CloudTrail.

- CloudTrail events sent to CloudWatch Logs can **trigger alarms** according to defined metric filters.

- **Log File Integrity Validation**: detect log files that were modified or deleted after delivery. CloudTrail creates signed digest files referencing delivered log files; validation must be performed to check integrity.

------------------------------------------------------------------------

## Pricing

- **Management events**: first copy in each Region → **free**; extra copy → charged.

- **Data events**: charged according to the configured resources and events; the source examples are Lambda functions, DynamoDB tables, and S3 buckets.

- After the trail is set up → **S3 charges** apply according to usage (CloudTrail deliver log into S3 bucket).

------------------------------------------------------------------------

## Source quota reference

| Resources | Default limits | Notes |
|----|----|----|
| **Trails per region** | **5** | Trail All-Regions counts as 1 trail in each Region. **Cannot increase.** |
| Get/Describe/List APIs | 10 TPS | Unable to increase |
| LookupEvents API | 2 TPS | Unable to increase |
| All other APIs | 1 TPS | Unable to increase |
| **Event data stores** (Lake) | **5 per region** | Unable to increase |
| **Event selectors per trail** | **5** | Unable to increase |
| Advanced event selector conditions | 500 (sum of all selectors) | Unable to increase |
| **Data resources across all event selectors** | **250** | Unable to increase |

------------------------------------------------------------------------

## Notes

### Auditing and retention

- CloudTrail event history is available by default when an account is created. Configure a trail for continuing delivery and retention in S3.

- CloudTrail **does not capture** application error logs from EC2 — that's the job of CloudWatch Logs/Agent.

- **Log File Integrity Validation** → enabled on **Trail** (not S3 bucket, not Systems Manager or AWS Config) → create **digest file** → used to verify log is not tampered.
