# EC2 Auto Scaling

[Back to index](../../README.md)

## Notes

### Capacity during an Availability Zone failure

Provision enough capacity in the surviving Availability Zones (AZs) to meet the workload’s minimum requirement without waiting for replacement instances.

For evenly distributed instances:

`(number of AZs − number of failed AZs) × instances per AZ ≥ minimum required capacity`

For a workload requiring at least **2 instances**, tolerance of **one AZ failure**, and peak capacity of **6 instances**:

- Across **2 AZs**, a minimum of **4 instances** provides 2 per AZ. Losing one AZ leaves 2 running instances. Set maximum capacity to 6 for the peak load.
- At 6 instances evenly distributed across 2 AZs, losing one AZ leaves 3.
- Across **3 AZs**, 3 instances, one per AZ, leave 2 after a single AZ failure.
- A minimum of 2 and maximum of 6 across 2 AZs can reduce normal operating cost when the application can tolerate reduced capacity while Auto Scaling replaces failed instances.

High availability emphasizes continued service and recovery. Fault tolerance requires sufficient redundancy to sustain the required service level during the specified failure. The latter generally needs more provisioned capacity.

### Scaling policies and stabilization

| Policy | Behavior | Example |
|---|---|---|
| Step scaling | Applies different adjustments according to the size of an alarm breach. | Add 1 instance above 70% CPU and 3 above 90%. |
| Target tracking | Adjusts capacity to maintain a target metric value. | Maintain average CPU utilization near 50%. |
| Simple scaling | Makes a single adjustment and uses a cooldown before further simple scaling actions. | Add a fixed number of instances after an alarm. |
| Scheduled scaling | Changes capacity at known times. | Increase capacity at 08:00 every Monday. |

A cooldown gives a scaling action time to affect the workload and metrics. For example, if an instance takes 2–3 minutes to become ready, reacting repeatedly to high CPU during that interval can overprovision capacity. The default Auto Scaling cooldown described in the source is **300 seconds**. The source recommends step scaling or target tracking over simple scaling.

### Launch settings

An Auto Scaling group launches instances using a launch template, or a legacy launch configuration. This defines settings such as the AMI, instance type, and security groups. Instances launched with the same security-group configuration receive those groups.

### EC2 and ECS metrics

EC2 instance metrics include CPU utilization, disk reads and writes, disk read and write operations, network in and out, and status-check failures for the instance, system, or either category. Memory utilization is not among these standard instance metrics.

ECS service target-tracking metrics include:

- `ECSServiceAverageCPUUtilization`: average service CPU utilization.
- `ECSServiceAverageMemoryUtilization`: average service memory utilization.
- `ALBRequestCountPerTarget`: completed requests per target in an ALB target group.

Scale ECS service task capacity using service CPU or memory utilization. Account for the underlying cluster’s compute capacity when using EC2-backed ECS. An ALB target group does not provide a target-group CPU utilization metric.

### Least-privilege database access from an Auto Scaling group

For SQL Server used only by the application tier, replace an inbound **TCP 1433** source of **`0.0.0.0/0`** (and **`::/0`**) with the **application tier’s security-group ID**. This permits the required application instances as the group scales, without opening the database port to all addresses. Instance IDs and NACL IDs are not valid substitutes for a source security group in this rule.

The source console warns that editing an existing rule deletes it and creates a replacement with the new details. Traffic depending on that rule can briefly drop until the replacement exists. Account for that interruption when using the source-described editing workflow.
