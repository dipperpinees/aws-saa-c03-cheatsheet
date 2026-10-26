# CloudWatch

[Back to index](../../README.md)

The source includes historical quotas and features. Numerical limits and service availability below should be checked for the target configuration.

## Overview

- Monitoring tool for AWS resources and applications

- Acts as a **metrics repository**: AWS services push metrics in, you query statistics

- Metrics are stored in their originating Region. Cross-Region dashboards can display them together.

------------------------------------------------------------------------

## Core concepts

### Namespaces

- Container containing CloudWatch metrics

- **No default namespace**

- AWS services use convention: `AWS/service` (eg: `AWS/EC2`)

### Metrics

- Time-ordered set of data points, **only exists in created region**

- Metric data cannot be manually deleted through the metric API. Data points age out according to retention rules, with retention up to **15 months**; new data does not restart retention for old points.

- Timestamp can be **2 weeks in the past** to **2 hours in the future**

- **EC2 Note**: CloudWatch **does not automatically collect** memory utilization and disk space → must install **CloudWatch Agent**

### Dimensions

- Name/value pair to determine the metric

- Source-era limit: **10 dimensions per metric**

### Resolution

| Type | Granularity | Use when |
|---------------------|-------------|------------------------|
| **Standard** | 1 minute | Default AWS services |
| **High Resolution** | 1 second | Need sub-minute insight |

### Statistics

| Statistics | Description |
|-----------------|-----------------------------------------------------|
| **Minimum** | Lowest value in period |
| **Maximum** | Highest value in period |
| **Sum** | Sum of all values |
| **Average** | Sum / SampleCount |
| **SampleCount** | Number of data points |
| **pNN.NN** | Percentile (eg: p95.45), do not use with negative values |

- Default period: **60 seconds**

- **Metric Math**: combine multiple metrics using mathematical expressions

------------------------------------------------------------------------

## CloudWatch Alarms

### Alarm States

| State | Description |
|-----------------------------------|----------------------------|
| **OK** | Metric within the allowable threshold |
| **ALARM** | Metric exceeds the threshold |
| **INSUFFICIENT_DATA** | Newly created or not enough data |

### Alarm configuration (3 parameters)

- **Period**: length of time to calculate each data point (seconds)

- **Evaluation Period**: number of most recent periods to consider

- **Datapoints to Alarm**: number of data points breaching during the evaluation period to activate ALARM (does not need to be consecutive)

### Handling missing data

| Options | Behavior |
|------------------------|--------------------------------|
| **missing** (default) | Evaluate with the available data; entirely missing evaluation data can yield INSUFFICIENT_DATA |
| **notBreaching** | Considered within the threshold |
| **breaching** | Considered to exceed the threshold |
| **ignore** | Maintain current status |

> Most alarm actions run on **state transitions**, rather than for each breaching data point. Some actions, including Auto Scaling actions, have different repeat behavior. Can be used to monitor **estimated AWS charges** (not actual utilization).

------------------------------------------------------------------------

## CloudWatch Dashboard

- **Unlimited** number of dashboards

- **Global**, not by region → can view cross-region resources

- Share dashboard in **3 ways**: specific email, public link, or SSO (Amazon Cognito integration)

------------------------------------------------------------------------

## CloudWatch Events → Amazon EventBridge

- Stream **near real-time** events about AWS resource changes

- **EventBridge** is built on CloudWatch Events API, using the same infrastructure

- EventBridge extends: connecting **third-party SaaS apps**

- 3 main concepts: **Events** (change) → **Rules** (match) → **Targets** (process)

------------------------------------------------------------------------

## CloudWatch Logs

### Main features

- Query log data, monitor real-time, archive, log Route 53 DNS queries

- Logs **do not expire** by default (kept indefinitely)

- **Logs Insights**: query and analyze interactive log data

- **Metric Filters**: create metrics from log data (only applies to events **after** the filter is created)

- Maximum **2 subscription filters** per log group

- **Contributor Insights**: analyze structured log events

- **Metric Insights**: aggregate and group real-time metrics

- **CloudWatch RUM**: collects client-side performance data from real user sessions

- **CloudWatch Evidently**: the source describes A/B testing with a subset of users; this is historical feature coverage.

### Metric Filter Concepts

- **filter pattern**: search pattern in log

- **metric name/namespace**: place to publish metrics

- **metric value**: published value when matching

- **default value**: value when there is no match (should be set = 0 to not lose data)

### CloudWatch Metric Streams

- Stream metrics **near real-time** to destinations: Datadog, New Relic, Splunk, Dynatrace, Sumo Logic, **S3**

------------------------------------------------------------------------

## CloudWatch Agent

- Requires **manual installation** on EC2 and on-premises servers

- Default namespace: **CWAgent**

- Collect custom metrics via **StatsD** and **collectd**

- Collect **memory, disk space** and system-level metrics

------------------------------------------------------------------------

## CloudWatch Application Insights

- Automatically detect application issues, create dashboards

- Analysis: **metric anomalies + log errors**

- Integrate **AWS SSM OpsCenter** to resolve issues

- Especially good for **.NET and SQL** application stacks

- **Data Retention**: problems **55 days**, observations **60 days**

------------------------------------------------------------------------

## CloudWatch Container Insights

- Collect metrics and logs from **ECS, EKS, Kubernetes on EC2**

- **AWS Fargate** support for both ECS and EKS

- Use **embedded metric format** (performance log events)

- Create your own **log group** and **automatic dashboards**

- Encryption with **AWS KMS** (symmetric keys only)

- EKS uses **containerized CloudWatch Agent** to discover containers

------------------------------------------------------------------------

## Security & Access Control

- Use **IAM users/roles** to authenticate

- Use resource ARNs where the operation supports resource-level permissions; actions without such support require `Resource: "*"`.

- Identity-based policies + Resource-based policies

------------------------------------------------------------------------

## Pricing

| Component | Charge by |
|---------------|------------------------------------------------------------|
| Metrics | Number of metrics/month |
| API calls | Per 1,000 metrics requested |
| Dashboards | Per dashboard/month |
| Alarms | Per alarm metric (Standard/High Resolution) |
| Logs | Per GB collected/archived/analyzed |
| Custom events | Per 1 million events |
| Logs Insights | Per query (according to GB data scanned) |
| Evidently | Per 1 million events |
| RUM | Per 100k events |

> Source pricing note: no data transfer in fee; applicable data transfer out charges can apply.

## Notes

### EC2 host metrics and operating-system metrics

Default EC2 metrics include CPU utilization, network traffic and packets, disk I/O activity where applicable, and instance status checks. They do not include guest memory usage, swap usage, file-system space utilization, Windows page-file usage, or application log files. Install and configure the CloudWatch Agent to publish these OS-level metrics and collect logs. Disk I/O and free disk space are different measurements.

EC2 **basic monitoring** reports at **five-minute** intervals. Enable **detailed monitoring** for **one-minute** EC2 metrics, for example when monitoring a retail application's web tier during a large sales event. Analyze those metrics alongside the database's CloudWatch metrics; logs alone do not enable one-minute EC2 metric publication.

### Starting Fargate tasks on object uploads

For a batch workload that needs a new ECS Fargate task whenever an object is uploaded to S3, configure EventBridge to match object-created/PUT events and use the ECS cluster as a target to run a task. This supplies an event-driven task launch without an intermediary Lambda function. The source starts with a minimum task count of one; running a task for each upload is distinct from changing an ECS service's desired count.

### Database alarms and email

CloudWatch collects database metrics, such as CPU utilization and available storage. An alarm can detect a condition such as **CPU above 80% for five minutes** and notify an SNS topic. Subscribe the operations team's email addresses to that topic. SNS can deliver these alarm emails directly; SES is not required for this notification path.

### RDS Enhanced Monitoring

Standard RDS CloudWatch metrics include CPU utilization, freeable memory, and database connections. **Enhanced Monitoring** uses an agent inside the RDS operating system to report more detailed process information, including **OS processes**, **RDS child processes**, and RDS management processes. It provides a different view from the aggregated standard metrics.

### Sharing a dashboard for incident investigation

For a product manager who needs to inspect incident information over a week and return to it later, share the relevant CloudWatch dashboard with that person's email address and provide its shareable link. This can scope access more narrowly than issuing broad AWS read-only credentials. Configure the dashboard and its supported sharing permissions for the intended information; dashboard sharing is not general administrative access to CloudWatch Logs.
