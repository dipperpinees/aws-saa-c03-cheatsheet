# Amazon Simple Queue Service

[Back to index](../../README.md)

## Overview

Amazon SQS provides message queues for integration and decoupling of distributed applications. Consumers **poll** for messages: SQS is pull-based rather than a push-delivery service.

- Use **AWS PrivateLink interface endpoints** for private VPC access without public IP addresses.
- Use **resource-based queue policies** for supported cross-account access.

## Standard and FIFO queues

| Characteristic | Standard | FIFO |
|---|---|---|
| Throughput | Very high, nearly unlimited throughput. | Throughput depends on mode, batching, and Region; the source’s baseline is **300 messages/s without batching** or **3,000 messages/s with batching**. |
| Ordering | Best effort. | Ordered within each message group. |
| Delivery and deduplication | At least once; duplicates can occur. | Deduplication prevents duplicate sends within its deduplication interval; application processing still needs appropriate failure handling. |
| Typical use | High-throughput decoupling when consumers tolerate ordering differences. | Work requiring ordered delivery and send deduplication. |

FIFO queue names end in **`.fifo`**. The source describes FIFO as available in selected Regions; check availability for the deployment Region rather than treating the historical comparison as a current region list.

## Visibility timeout

After receiving a message, SQS hides it from other consumers for the visibility timeout.

- Default: **30 seconds**.
- Range: **0 seconds to 12 hours**.
- Receiving does not delete the message. The consumer must delete it after successful processing.
- Set or extend the timeout to cover actual processing time so the message does not reappear while work is still in progress.

A longer visibility timeout reduces duplicate processing caused by premature redelivery; it does not remove Standard queues’ at-least-once delivery semantics.

## Delayed delivery and polling

A **delay queue** delays newly added messages at queue level. A **message timer** delays a particular message where supported. The delay range is **0 to 15 minutes**.

| Polling type | Behavior |
|---|---|
| Short polling | Returns immediately, possibly empty; **`WaitTimeSeconds = 0`**. |
| Long polling | Waits for messages or a timeout, reducing empty responses and request costs. |

## Dead-letter queues

A **DLQ** holds messages that repeatedly fail processing. It supports debugging, error analysis, and isolation of poison messages.

- Choose a maximum receive count that allows reasonable retries; avoid **`maxReceiveCount = 1`** for a Standard queue.
- A DLQ is unsuitable when work must retry indefinitely.
- Moving a failed FIFO message to a DLQ can break the application’s required end-to-end processing order. Assess that tradeoff before enabling it.

## Limits and billing units recorded in the source

These figures preserve the source’s version of the quotas; some depend on queue mode, workload, or service changes and should be verified before sizing a new system.

| Item | Source figure |
|---|---|
| Standard in-flight messages | Approximately **120,000**. |
| FIFO in-flight messages | **20,000**. |
| Batch | Up to **10 messages**, with a **256 KB** source-era aggregate payload limit. |
| Message attributes | Up to **10**. |
| Queue name | Up to **80 characters**, using letters, digits, hyphens, and underscores; FIFO also uses the required `.fifo` suffix. |
| Request billing unit | Each **64 KB** payload chunk counts as a request. |

## Integrations

- **Lambda** polls SQS through an event source mapping; the queue and function must be in the same Region. A queue can have multiple consumers, and functions can have separate mappings to multiple queues.
- **SNS** can deliver topic messages to subscribed SQS queues.
- **Step Functions** integrates with SQS operations.
- **EventBridge** can target SQS; EventBridge Pipes can use an SQS queue as a source.

## Security and monitoring

- **IAM** policies and queue resource policies control access to operations such as sending and receiving.
- **Server-side encryption**, including KMS-based encryption, protects stored messages.
- **VPC endpoints** provide private connectivity.
- Policy conditions can restrict access using applicable IP, VPC, or time conditions.
- **CloudWatch** monitors queue metrics; **CloudTrail** audits supported API activity; **EventBridge**, formerly CloudWatch Events, supports event-driven automation.

## Pricing and operational guidance

SQS is billed by requests, commonly quoted per **million requests**, with **64-KB payload chunks** counted separately. Batch message actions to reduce API calls. S3 and KMS integrations can add charges. The source cites a historical data-transfer allowance of the **first 1 GB per month**; current transfer pricing and allowances require verification.

Use long polling to reduce empty receives. For request/reply patterns, create a **reply queue per producer**, rather than a queue per message.

## Notes

### Buffer traffic while workers scale

When an EC2 Auto Scaling group takes time to add workers, place incoming work in SQS so requests are buffered rather than dropped. Use CloudWatch backlog metrics to guide worker scale-out. Consider both the backlog and worker processing capacity when selecting the scaling target.

### Hybrid and container applications

SQS lets on-premises servers and EC2 workers exchange decoupled work. The source also identifies **SWF** for coordinating work across those environments. VPC peering is connectivity between VPCs, rather than a message queue or hybrid workflow coordinator.

For front-end and back-end ECS tasks, have the front end **send** messages and the back end **poll** the queue. This reduces direct dependencies and helps isolate component failures.

### Duplicate order processing

Standard queues can redeliver messages and can return them out of order, such as **1–3–2** after sends in order **1–2–3**. FIFO adds ordered delivery within a message group and send deduplication. Deletion, retries, visibility timeout, and idempotent processing still matter; FIFO does not make arbitrary external side effects exactly once.

### Streaming to EMR

The source’s messaging workload receives thousands of messages per day and needs durable, ordered processing in **Amazon EMR**. It selects **Kinesis Data Streams** for the streaming integration rather than a default Standard SQS queue.

A **partition key** routes related records to a shard, providing ordering within that shard. EMR applications such as Spark Streaming can track their read position through checkpointing. The producer and consumer must still handle retry duplicates and ensure idempotency; Kinesis alone does not guarantee global ordering or exactly-once external processing.

### Asynchronous fan-out to Lambda

An EC2 application can publish to **SNS** to invoke subscribed Lambda functions asynchronously while keeping the producer decoupled. Use SQS for queued work and Kinesis for a streaming-data model according to the required consumption pattern. Both SQS and Kinesis use pull-based consumption.
