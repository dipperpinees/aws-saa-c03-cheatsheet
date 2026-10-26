# AWS Lambda

[Back to index](../../README.md)

## Overview

AWS Lambda is a **serverless, event-driven** compute service. Functions run in response to events without requiring you to manage servers, and scale horizontally with demand within service quotas and scaling limits.

- Standard on-demand execution is billed by **request count and compute duration**, in **1-millisecond** increments.
- Treat functions as **stateless**; do not depend on a particular execution environment being reused.
- Deploy a **ZIP package** or a **container image**, with container images up to **10 GB**.
- Configure **128 MB to 10 GB of memory**. CPU and other compute capabilities scale with the memory allocation.
- Supported language approaches include Node.js, Java, C#, Python, Ruby, Go, and PowerShell, using managed runtimes, custom runtimes, or container images as applicable.
- The source lists compliance programs including SOC, HIPAA, PCI, and ISO; application compliance still depends on its configuration and use.

## Execution features

Lambda uses isolated execution environments based on **Firecracker microVMs**. Supported architectures include **x86** and **Arm/Graviton2**.

- **Provisioned concurrency** prepares environments in advance to reduce initialization latency.
- **SnapStart** snapshots initialized execution environments and restores them for faster startup for supported Java, Python, and .NET runtimes.
- The source describes integrations with **220+ AWS services**, including API Gateway, S3, and DynamoDB.
- Mount **EFS** for persistent shared storage.
- Use **CloudWatch** logs and metrics and **X-Ray** tracing through the relevant configuration and permissions.

## Source-described Lambda Managed Instances

The source describes a managed-instance compute mode using selected EC2 capacity, including high-memory instances, Graviton4, and GPU use cases. It gives the following model, whose feature support and prices have not been independently verified in this conversion:

- Define a **capacity provider** with VPC settings, instance types, and scaling configuration.
- Run multiple concurrent requests on an instance, unlike a standard Lambda execution environment’s one in-flight invocation.
- AWS manages instance lifecycle, operating-system patches, load balancing, and scaling.
- Keeping capacity available reduces startup latency; the source describes this as avoiding cold starts.
- The source claims EC2 commitment discounts, including Savings Plans/Reserved Instances, and charges based on instance uptime rather than function duration.
- Its quoted pricing is EC2 On-Demand or Savings Plan pricing, a management fee of approximately **15% of the On-Demand price**, and the Lambda request fee of **$0.20 per million requests**, with **no duration fee**.

These are source claims rather than a verified purchasing or feature-support guide.

## Application components

| Component | Purpose |
|---|---|
| Function | Code that handles an event and produces a result. |
| Execution environment | Isolated environment in which code runs. |
| Runtime | Language execution environment, such as Node.js or Python. |
| Environment variables | Configuration values, such as a database endpoint. Sensitive values require appropriate encryption and permissions. |
| Layers | Reusable dependencies and libraries kept separate from ZIP function code. |
| Event source | A service or client that causes invocation, such as S3 or API Gateway. |
| Downstream resources | Services called by the function, such as DynamoDB or SQS. |
| Log streams | Function execution logs in CloudWatch Logs. |

## Functions and deployment

Configure the function’s memory, **timeout up to 15 minutes**, and **IAM execution role**.

- **Versions** are immutable snapshots identified by suffixes such as `:1` and `:2`.
- **Aliases**, such as `:Prod` or `:Dev`, point to published versions.
- **Layers** share supported dependencies separately from ZIP function deployments.
- To package external dependencies, place libraries in the project directory, build the ZIP archive, and upload it directly or through S3.
- Container-image deployments use an image stored in **ECR**.

## Invocation models

### Synchronous

The invoking client waits for the function result. Integrations include **API Gateway, ALB, Cognito**, and CloudFront’s **Lambda@Edge**, according to the event type.

### Asynchronous

Lambda accepts the event and returns **202 Accepted**, then executes the function through its asynchronous queue. Sources include **S3, CloudWatch Logs, EventBridge**, and supported CodeCommit events.

The source quotes a **256 KB asynchronous payload limit**. This is retained as a source-era figure; confirm the current quota for the selected invocation model. SQS uses event source mapping rather than Lambda’s asynchronous invocation queue.

### Event source mappings

Lambda polls a queue or stream and invokes the function with batches. Supported sources include **Kinesis, DynamoDB Streams, SQS, Amazon MQ, MSK, and self-managed Kafka**.

- Event filtering can reduce unnecessary invocation work.
- Invocation depends on batch-size and batching-window settings, or reaching the synchronous invocation payload limit of **6 MB**, subject to source-specific behavior.

## Concurrency

- **Concurrency** is the number of simultaneous in-flight invocations.
- **Reserved concurrency** reserves part of the account’s concurrency allocation for the function and also caps that function’s concurrency. It does not keep execution environments warm.
- **Provisioned concurrency** keeps initialized environments available to reduce latency.

## Function URLs

A function URL supplies an HTTPS endpoint without requiring API Gateway:

`https://<url-id>.lambda-url.<region>.on.aws`

- The URL remains stable and supports **IPv4 and IPv6**.
- **`AWS_IAM`** authentication requires authorized IAM credentials.
- **`NONE`** allows unauthenticated invocation subject to the function’s access policy.
- Supports **CORS**.
- Can be attached to an alias or **`$LATEST`**, rather than an arbitrary published version directly.

## VPC connectivity

Lambda runs in an AWS-managed network by default. To access private resources such as EC2 or RDS in your VPC, configure **subnet IDs and security groups**. Lambda manages network interfaces for connectivity.

## Lambda@Edge and SnapStart

Lambda@Edge runs request or response logic at CloudFront edge locations, at **viewer request, origin request, origin response, or viewer response** events.

SnapStart captures the initialized execution environment and reuses that snapshot to reduce cold-start overhead for supported runtimes.

## Pricing

The source lists a monthly free tier of **1 million requests** and **400,000 GB-seconds**. Standard compute charges depend on requests and allocated memory multiplied by duration, billed in 1-ms increments.

Other charges can include **provisioned concurrency**, applicable **SnapStart** charges, **ECR image storage**, and networking/data-transfer resources used by a VPC-connected function. Prices and free-tier eligibility depend on the applicable terms.

## Notes

### Protecting sensitive environment variables

Lambda encrypts environment variables at rest by default. To prevent developers with function-configuration access from seeing sensitive database and API credentials in plaintext, the source uses a **customer-managed KMS key** and **encryption helpers** for client-side encryption. Restrict decryption permissions and give the function only the access it needs; default at-rest encryption alone does not restrict all authorized configuration viewers.

### APIs with occasional bursts

For a low-activity application whose traffic rises within seconds after product announcements, **API Gateway with Lambda** provides an API without continuously running an idle server fleet. It can scale rapidly, subject to concurrency and scaling limits. EC2-based scale-out needs time to start and register instances; account for database connection capacity when accessing RDS.

### Container images and temporary storage

For an ECR-packaged function requiring **5 GB of temporary storage**, configure **5 GB of `/tmp` ephemeral storage**. The source describes a range from **512 MB to 10 GB**, so this requirement fits without adding EFS. Use EFS when data must persist or be shared, rather than merely to supply temporary scratch space. The function must still fit Lambda’s runtime and resource limits.

### Network capacity and invocation errors

A **`/27` subnet** contains **32 addresses**: `2^(32 − 27) = 32`. After AWS’s **5 reserved addresses**, **27** remain assignable. Limited subnet address space or an ENI quota can constrain VPC connectivity.

The source associates `EC2ThrottledException` with EC2/ENI management throttling during bursts and recommends checking ENI quotas and available subnet IP addresses. Modern Lambda networking shares Hyperplane ENIs; do not assume a separate ENI or IP address is allocated per concurrent invocation.

The source labels **`10.31.0.0/27`** as a VPC CIDR, but the usable-address calculation applies to a subnet. That VPC label is inconsistent with VPC CIDR-size constraints.

### Managed access to DynamoDB

Two managed API architectures can expose DynamoDB data:

- API Gateway REST API with a direct AWS service integration to DynamoDB.
- API Gateway REST API invoking Lambda, which reads DynamoDB.

Route 53 provides DNS routing and does not itself invoke function code or turn a function ARN into a web API. A callable HTTPS endpoint, such as API Gateway or a function URL, is needed.

### Transform objects when reading from S3

The source describes **S3 Object Lambda** for transforming images on **GET object** requests, with **CloudFront** providing global delivery. This is a source-described architecture whose service availability should be checked before new adoption.

S3 Event Notifications respond to supported events such as object creation, deletion, restoration, and replication. They do not provide a GET-response transformation trigger.
