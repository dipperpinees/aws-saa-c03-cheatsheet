# System Design

[Back to index](../../README.md)

## Notes

### Scale an existing stateless EC2 application

For a stateless application running on one On-Demand EC2 instance that experiences high CPU and 5xx errors during traffic spikes:

1. Create an **AMI** of the application instance.
2. Reference it in a **launch template** for an **Auto Scaling group**.
3. Configure scaling policies based on CPU utilization.
4. Place an **Application Load Balancer** in front of the group to distribute requests.

This extends the existing deployment to scale with demand. A fixed pair of instances does not provide automatic scaling. Rewriting the application with Lambda, SQS, and DynamoDB introduces a larger redesign when the requirement only calls for cost-effective scaling of the existing application.

### Vertical and horizontal scaling

When a monolithic web application reaches **100% CPU**, **Compute Optimizer** can recommend a more appropriate instance type for vertical scaling. An **Auto Scaling group and ALB** provide horizontal scaling and improve availability. An AMI and launch template supply the instance configuration; they are parts of the solution rather than the scaling mechanism by themselves.

### Scheduled, long-running tasks

The source describes EC2 Linux tasks written by different teams, with no common programming language and durations of approximately **one hour**. Creating an AMI and running multiple instance copies in an Auto Scaling group reuses that existing runtime environment. Moving to a different job platform requires assessing the migration and scheduling changes; a one-hour task cannot run as a single standard Lambda invocation.

### Managed application deployment

The source scenario concerns a **resource-intensive Java Spring Boot application on on-premises Windows servers**. Its migration requires a containerized AWS deployment, automatic scaling based on **CloudWatch metrics**, and minimal infrastructure-management work.

For a supported application supplied as source code or a container image, **AWS App Runner** manages deployment and infrastructure scaling. However, the source conflates this managed deployment with containerization: App Runner does not automatically convert an arbitrary Windows application into a compatible container. Assess runtime and operating-system migration, prepare a supported deployment artifact, and validate the required metric-based scaling behavior before treating the source's selection as a complete solution.

### Large migrations with limited bandwidth

The source describes migrating a large on-premises MongoDB database to DynamoDB within a few weeks when internet bandwidth is insufficient. Its offline migration approach uses **SCT extraction with a Snowball Edge device**, followed by **DMS** migration into DynamoDB. Direct Connect requires physical provisioning and can have a longer setup lead time. This is a historical source workflow; confirm the availability and supported source/target combinations before planning a new migration.
