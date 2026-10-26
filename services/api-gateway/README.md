# Amazon API Gateway

[Back to index](../../README.md)

## Overview

- Fully managed service to create, publish, monitor, secure and scale **REST, HTTP, WebSocket APIs**

- Only supports **HTTPS** (no plain HTTP)

- HIPAA eligible, pay-per-use

- Tight integration with **Lambda** and other AWS services

---

## Key concepts

| Concept | Description |
|----|----|
| **API Deployment** | Snapshot at a point in time must be attached to a Stage to be used |
| **API Stage** | Lifecycle environment (dev/test/prod), identified by API ID + stage name |
| **API Key** | Client identification, used with Usage Plan to control quota |
| **Model** | Define schema to validate request/response |
| **Private API** | Only accessible via VPC endpoint, completely isolated from the internet |

---

## Endpoint Types

| Type | Description | Use when |
|----|----|----|
| **Edge-Optimized** | Uses an API Gateway-managed CloudFront distribution | Globally distributed clients |
| **Regional** | Direct to Region, supports Route 53 latency routing | Clients in the same region |
| **Private** | Accessible through a VPC endpoint without traversing the public internet | Internal, high security |

---

## Integration Types

| Type | Description |
|----|----|
| **Lambda Proxy** | All requests are sent to Lambda, the function returns the required proxy response format |
| **HTTP Proxy** | Forward the entire request/response between the client and the HTTP backend |
| **Private Integration** | Access supported VPC resources through a VPC link; supported load balancers depend on API type |

---

## REST APIs vs HTTP APIs

|                    | **REST APIs** | **HTTP APIs** |
|--------------------|---------------|---------------------|
| Cost | Higher | Up to **71%** cheaper |
| Latency | Higher | Lower |
| Caching | ✅ | ❌ |
| WAF | ✅ | ❌ |
| Streaming response | ✅ | ❌ |
| Canary deployment | ✅ | ❌ |
| Usage Plans | ✅ | ❌ |
| Wildcard domains | Supported | Supported |

---

## Usage Plans

- Associated with **API keys** to identify clients and apply usage limits; API keys are not an authentication mechanism

- Enforce: **throttling** (RPS + burst) and **quota** (limit number of requests)

---

## Security and authentication

- **IAM** (SigV4 / SigV4a)

- **Lambda Authorizers** (custom auth logic)

- **Amazon Cognito** user pools

- **Mutual TLS (mTLS)**

- **AWS WAF** integration (anti-DDoS, anti-bot)

- **Resource policies** + VPC endpoint policies

- TLS **1.2 and 1.3**

---

## Features

- Run **multiple API versions** simultaneously

- **Canary deployment**: test new version before full release

- **Caching**: configure key cache + TTL, reduce backend load

- **Method-level throttling**: control each endpoint

- Supports **IPv4/IPv6 dual-stack**

- Monetize API via **AWS Marketplace**

---

## Invocation Targets

Lambda, Step Functions, EventBridge, SQS, Kinesis, Elastic Beanstalk, EC2, public HTTP endpoints

---

## Monitoring

- **CloudWatch**: API calls, latency, 4XX/5XX errors

- **Execution logs + Access logs**

- **AWS X-Ray**: trace the entire request end-to-end

---

## Pricing

- **Pay-per-call** + data transfer out

- Caching is charged hourly based on cache size

- **Source-era free tier:** 1 million API calls/month for the first **12 months**; eligibility and pricing depend on the applicable AWS offer.

- HTTP APIs are up to **71%** cheaper than REST APIs

## Notes

### Canary releases

A REST API stage can send a small percentage of traffic to a new deployment while the remaining traffic uses the stable deployment. For example, start with 5% canary traffic and 95% stable traffic, then increase the canary share to 20% and eventually 100%. If the deployment fails, return traffic to the stable deployment. The custom domain stays the same, avoiding a DNS cutover.

| Deployment approach | Traffic rollout | Infrastructure and operations |
|---|---|---|
| Canary | Gradually increase the share sent to the new deployment | Reuses an API stage; limits initial exposure |
| Blue/green | Switch traffic between separate environments when ready | Requires parallel environments and additional management; cost depends on their resources |
| Direct overwrite or merge | Changes the active API configuration | Provides no traffic split by itself; a faulty release can affect all clients |

### Custom HTTPS domains

1. Request or import an ACM certificate in the **same Region** as a Regional API. For an API in `us-east-2`, use an ACM certificate in `us-east-2`.
2. Create a Regional custom domain in API Gateway and attach the certificate.
3. Create a Route 53 alias record pointing to the generated API Gateway domain, such as `d-xxxx.execute-api.us-east-2.amazonaws.com`. Alias records support zone apex names and qualifying aliases to AWS resources have no DNS query charge.

An edge-optimized API uses CloudFront and requires its ACM certificate in **us-east-1**. Edge-optimized endpoints serve geographically distributed clients through edge locations; Regional endpoints connect directly to the selected Region and are useful for nearby clients or explicit regional routing.

### Managed endpoints and related services

API Gateway runs as a managed service rather than inside an EC2 subnet. Use an API resource policy to allow or deny source IP ranges at the API boundary.

WebSocket APIs provide persistent, two-way communication for chat, game status, or live rankings. Global Accelerator provides static anycast IP addresses; AppSync provides managed GraphQL APIs; EFA provides low-latency communication for HPC workloads. These are separate capabilities from API Gateway.
