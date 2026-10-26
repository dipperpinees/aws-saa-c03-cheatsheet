# AWS Systems Manager

[Back to index](../../README.md)

## Overview

- Centralize operational data and **automate tasks** on AWS resources

- Manage both **EC2, on-premises, cross-cloud** (hybrid)

- **SSM Agent** must be installed and running on managed instances. Some AMIs include it; install it separately where absent, and configure the required instance permissions and service connectivity.

---

## Main Capabilities

### 1. Automation

- Automate repetitive IT operations

- The source lists **25 concurrent Automations** with additional executions queued as `Pending`; concurrency quotas depend on the automation type and account configuration.

- Supports running each step or the entire document

- Supports **concurrency controls** and **conditional branching**

### 2. Run Command

- Manage remote instance configuration, **no need to open inbound ports**

- Target by **tags, resource groups, AWS Organizations**

### 3. Session Manager

- Browser-based shell or CLI to manage EC2

- **No need for SSH keys, bastion host, inbound ports**

- Supports **port forwarding, SSH, SCP tunneling**

- Access is controlled through IAM. Configure session logging for supported session types; SSH and port-forwarding tunnel contents are not logged by Session Manager.

### 4. Patch Manager

- Automatically scan and patch instances (EC2 + on-premises)

- Use **patch baselines**: auto-approve patches according to release date

- Supports **CVE IDs** to identify security vulnerabilities

- Supports custom patch baselines. Container host patching and container image patching are separate concerns.

- Can configure actions **before and after** when patching

### 5. Maintenance Window

- Schedule regular maintenance in planned windows to limit production disruption

- Supports **4 types of tasks**: Run Command, Automation, Lambda, Step Functions

- Supports **task prioritization** and concurrency controls

### 6. State Manager

- Maintain EC2/hybrid infrastructure in **pre-defined state**

- Use **associations** to assign configuration to managed instances

### 7. Inventory Manager

- Automatically collect **software inventory** from managed instances

- Customize metadata types, instances, and collection schedules

### 8. Parameter Store

- Store **configuration data and secrets** in a hierarchical form

- Plain text or encrypted with **SecureString**

- Integrates **AWS KMS** for fine-grained encryption

- Used with Run Command, State Manager, Automation

### 9. Distributor

- Package and distribute software to managed instances

- Install **once** (Run Command) or **scheduled** (State Manager)

- Support **versioned updates and rollback**

### 10. OpsCenter

- View, investigate, resolve **operational issues** centrally

- Aggregate data from Config, CloudTrail, CloudWatch Events

- Reduce **MTTR** (Mean Time To Resolution)

- Cross-account support via delegated administrator

### 11. Change Manager

- Change management framework: request → approval → implementation → report

- Maximum **5 levels of approvers** per change template

- Cross-account/region management via **AWS Organizations**

- Can be used for AWS and on-premises resources

### 12. Incident Manager

- Mitigation and disaster recovery for AWS-hosted applications

- Lifecycle: **Alerting → Triage → Investigation → Post-incident analysis**

- Support **automated escalation policies**

### 13. AppConfig

- Create, manage, deploy **application configurations** quickly

- Has built-in validation and monitoring when deploying

### 14. Explorer

- Customizable dashboard operations, view **multi-account/region**

- Export reports to **Amazon S3**

### 15. Fleet Manager

- Manage and monitor the entire server fleet (AWS + on-premises)

- OS-level visibility, interactive troubleshooting

- Can manage supported underlying EC2 hosts, including hosts used for ECS; Fleet Manager is not a replacement for ECS cluster orchestration.

---

## SSM Documents (SSM)

| Type | Use with | Purpose |
|----|----|----|
| **Command** | Run Command, State Manager | Execute command, apply config |
| **Policy** | State Manager | Enforce policy continuously |
| **Automation** | Automation | Maintenance, deployment tasks (AMI,...) |
| **Package** | Distributor | Install packaged software |

- Format: **JSON or YAML**

- Support **versioning**, setting default version, tagging

---

## Resource Groups

- Group AWS resources in the same region according to query criteria

- Integrate **AWS Organizations** for large-scale management

- Built-in Insights: CloudTrail, Config, patch compliance, software inventory

---

## Monitoring

- SSM Agent → logs → **CloudWatch Logs** (real-time monitoring)

- **CloudTrail**: log API calls

- **EventBridge**: event-driven automation

- **SNS**: notifications for Run Command and Maintenance Windows

---

## Security

- Integrate **IAM** for access control

- Supports **least-privilege** via IAM roles + session-based credentials

---

## Pricing

Charge according to:

- Number and type of **Automation steps**

- Number of **OpsItems, change requests, API requests**

- Number of **configuration requests**

- Number of **advanced parameters** stored + instances activated

- Distributor: according to **storage size, duration, API calls, data transfer**


## Notes

### Contractor console and private-instance access

| Access requirement | Service |
|---|---|
| AWS Management Console across organization accounts | IAM Identity Center permission sets |
| Operating system access to EC2 in private subnets | Systems Manager Session Manager |

For contractors troubleshooting private Amazon Linux instances, install SSM Agent, attach an instance profile with the required Systems Manager permissions, and provide network connectivity to Systems Manager endpoints. Assign contractors least-privilege console access centrally through Identity Center. Session Manager avoids inbound SSH ports and bastion hosts; configure supported session logs and API auditing to meet activity-recording requirements.

### Patching instances behind a load balancer

Use **Maintenance Windows** to schedule patching, with an Automation workflow that deregisters a target, waits for traffic to drain, applies patches, validates health, and registers the target again. The source names the `AWSEC2-PatchLoadBalancerInstance` runbook for this workflow.

Its example uses an ALB target group registered by **IP address**. Check that the selected runbook supports that target registration mode and the required load balancer type; scheduling a maintenance window alone does not automatically deregister targets. A custom workflow may be needed when the runbook's supported inputs do not match the target group.
