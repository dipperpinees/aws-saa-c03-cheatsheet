# Cost Management Services

[Back to index](../../README.md)

## Analysis and visualization

- **AWS Cost Explorer** displays historical and current cost data and forecasts costs for up to **12 months**. Filter by service, Region, or cost-allocation tag.
- **AWS Cost and Usage Report (CUR)** provides detailed cost and usage records in formats such as CSV or Parquet. Analyze the data with tools such as Amazon Athena and QuickSight.

## Planning and alerts

- **AWS Budgets** tracks a budget, such as **$100 per month**, and sends email or SNS alerts when actual or forecasted costs cross configured thresholds.
- **AWS Cost Anomaly Detection** uses machine learning to identify unexpected spending increases, such as an accidentally provisioned resource or an unusual traffic surge.

## Optimization and savings

- **AWS Trusted Advisor** recommends improvements, including cost savings from removing unused resources such as unattached EBS volumes.
- **AWS Compute Optimizer** analyzes resource utilization and recommends appropriate sizing for resources including EC2 instances, Auto Scaling groups, EBS volumes, and Lambda functions.
- **Compute Savings Plans and Reserved Instances** are commitment-based pricing options. The source cites **1- or 3-year** commitments and savings of **up to 72%** over On-Demand pricing; the applicable discount depends on the purchase option and workload.

## Governance and billing

- **AWS Organizations consolidated billing** combines member-account charges into the management/payer account and can aggregate eligible usage for volume discounts.
- **AWS Billing Conductor** supports customized billing views and pricing rules for departments or downstream customers.

## Notes

### Programmatic financial reporting

The **Cost Explorer API** lets applications query cost and usage data and request forecasts based on historical spending. Code written in Python, Node.js, or another supported environment can consume the returned data to build custom reports.

Handle pagination when an operation returns multiple pages so the report includes all results without loading an unbounded response into memory.
