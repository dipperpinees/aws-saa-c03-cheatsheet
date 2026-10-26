# Decoupling

[Back to index](../../README.md)

| Service | Decoupling mechanism | When to use |
|----|----|----|
| **Amazon SQS** | Queue, pull-based | Separate producer and consumer, workers process jobs asynchronously |
| **Amazon SNS** | Pub/Sub, push-based | Send an event to many subscribers |
| **Amazon EventBridge** | Event bus/routing | Decouple events between multiple services/apps/accounts |
| **Kinesis Data Streams** | Streaming data | Real-time, high-throughput streams with ordering within a shard for a partition key |
| **Amazon Data Firehose** (formerly Kinesis Data Firehose) | Delivery stream | Push stream data to S3/Redshift/OpenSearch/Splunk |
| **AWS Step Functions** | Workflow orchestration | Coordinate processing steps, retries, and branching |
| **Amazon MQ** | Managed message broker | Applications requiring RabbitMQ or ActiveMQ protocols |
| **AWS Lambda async invocation** | Async event processing | Call Lambda asynchronously, retry automatically |
| **API Gateway** | Separating client and backend | Clients call a stable API backed by Lambda, ECS, or an HTTP service |
