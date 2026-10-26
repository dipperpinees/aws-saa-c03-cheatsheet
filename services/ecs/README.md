# Amazon Elastic Container Service

[Back to index](../../README.md)

## Overview

Amazon ECS is a managed, **regional** container orchestration service for deploying, managing, and scaling containerized applications. The source cites a **99.99% monthly uptime SLA**; the applicable SLA and eligibility depend on the deployment.

## Images, containers, and task definitions

A container packages application code, runtime, and libraries. Images are built from a **Dockerfile** and stored in a registry. Depending on the launch type and configuration, storage options include local instance storage, EBS, EFS, bind mounts, and Docker volumes.

A **task definition** is a versioned JSON description of up to **10 containers**. It includes:

- Task family and revision.
- IAM task role.
- Network mode.
- Container images, CPU, and memory.
- Volumes, placement constraints, and launch-type compatibility.

### Scheduling

| Strategy | Behavior |
|---|---|
| **REPLICA** | Maintain a desired task count, with placement across available capacity and AZs. |
| **DAEMON** | Run one task on each eligible container instance. |

Each Fargate task has isolated compute resources and its own networking environment. Task scale-in protection can protect work such as batch processing or gaming sessions from service scale-in.

## Clusters and capacity providers

A cluster is a logical grouping of compute and tasks in a Region. It can contain **Fargate**, **EC2**, and **external ECS Anywhere** capacity. Delete its services and deregister container instances before deleting a cluster.

Capacity providers can manage an EC2 Auto Scaling group’s capacity according to task requirements. A capacity-provider strategy controls placement across eligible capacity providers, such as On-Demand and Spot capacity.

## Launch types

### Fargate

- AWS manages the underlying servers.
- Requires **`awsvpc`** network mode, with an ENI for each task.
- Provides **20 GiB** ephemeral storage by default, configurable up to **200 GiB** for supported configurations.
- Supports **ARM64/Graviton** workloads; the source cites up to **40%** cost savings for its Graviton2 comparison.
- Uses supported registries, including ECR and Docker Hub.
- Supports ALB and NLB; Classic Load Balancer is not supported.
- Supports CloudWatch Logs through **`awslogs`**, along with other supported logging configurations.

### EC2

You manage the container instances, their operating systems, and capacity. ECS supports Docker volumes, bind mounts, and private image repositories. Each container instance belongs to **one cluster**.

### ECS Anywhere

Run ECS tasks on on-premises VMs or bare-metal servers. Install **SSM Agent** and **ECS Container Agent**, and provide outbound connectivity to the required AWS APIs over an appropriate internet, Direct Connect, or VPN path.

## Services and deployment

| Strategy | Behavior |
|---|---|
| Rolling update | Replace tasks gradually according to configured minimum and maximum capacity. |
| Blue/green with CodeDeploy | Deploy and validate a replacement task set before shifting traffic; uses ALB or NLB integration. |

A service replaces tasks that fail its health checks. A service on EC2 or Fargate can attach to multiple target groups.

### Load balancing

| Load balancer | Use |
|---|---|
| ALB | Layer-7 HTTP/HTTPS routing and dynamic port mapping. |
| NLB | Layer-4 TCP/UDP traffic. |
| Classic Load Balancer | Legacy use with static port mapping; not supported with `awsvpc`. |

ALB dynamic port mapping supports multiple tasks on different host ports of the same EC2 instance.

## Service communication and operations

**ECS Service Connect** simplifies service-to-service communication using logical names such as `frontend` and `db`. It provides managed proxy configuration, connection draining, retries, health checks, and telemetry such as request counts, latency, and error rates through ECS and CloudWatch.

**ECS Exec** runs an interactive shell or individual command inside a running EC2 or Fargate container.

## Monitoring

- **CloudWatch Logs** collects application and container logs when configured.
- **CloudWatch Alarms** responds to metric thresholds.
- **CloudTrail** audits supported API activity and can deliver events to CloudWatch Logs when configured.

## Pricing

| Launch type | Billing basis |
|---|---|
| Fargate | Provisioned vCPU and memory, from image download until task termination; other configured resources can add charges. |
| EC2 | Underlying resources such as EC2 and EBS, without an additional ECS control-plane fee. |
| ECS Anywhere | The source cites approximately **$0.01025 per instance-hour** for management, excluding hardware. |

## Notes

### Execution role and task role

```text
executionRoleArn = permissions for the ECS agent/Fargate to pull images, write logs, and retrieve configured secrets
taskRoleArn = permissions for application code in the container to call AWS services
```

### Automatic scaling

A service’s CPU utilization is a workload metric; CloudWatch supplies the metric and alarm evaluation. A scaling policy can increase the number of ECS tasks when service CPU exceeds its threshold. An EC2-backed deployment must also have enough cluster capacity, which can be managed through its capacity provider and Auto Scaling group.

The flow is: **higher ECS service CPU → metric/alarm evaluation → service scaling → more tasks**, with underlying compute capacity added as needed. An ALB or target-group CPU metric is not a substitute for ECS service CPU.
