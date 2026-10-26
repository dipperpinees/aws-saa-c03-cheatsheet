# Control Tower

[Back to index](../../README.md)

## Overview

- Configure and manage an **AWS multi-account environment**

- Provide a **landing zone** that complies with security and compliance best practices

------------------------------------------------------------------------

## Landing Zone

- **well-architected** multi-account environment

- A Control Tower deployment establishes **one landing zone per organization**.

### Structure

    Root
    ├── Management Account
    ├── Security OU (shared accounts)
    │ ├── Log Archive Account
    │ └── Audit Account
    └── Sandbox OU (member accounts - user workloads)

    IAM Identity Center (permissions & identities management)

### Shared Accounts

| Account | Role |
|----|----|
| **Management** | Billing, provisioning accounts, managing OUs/guardrails |
| **Log Archive** | Central repository for API activity logs and resource configurations |
| **Audit** | Restricted account for security & compliance teams |

------------------------------------------------------------------------

## Guardrails

AWS environment governance rules/policies, applicable to both **OU and accounts within the OU**

### Classification by Behavior

| Type | Description | Deploy using | Status |
|----|----|----|----|
| **Preventive** | Block actions that violate policy | **AWS Organizations SCPs** | Enforced / Not enabled |
| **Detective** | Detect non-compliance, warn via dashboard | **AWS Config rules** | Clear / In violation / Not enabled |
| **Proactive** | Scan resources **before** provision | **AWS CloudFormation hooks** | PASS / FAIL / SKIP |

### Classification by Guidance

| Type | Description |
|--------------------------|----------------------------------------|
| **Mandatory** | Always enforced |
| **Strongly Recommended** | Enforce best practices |
| **Elective** | Track actions that are often restricted |

> Default: **Mandatory guardrails** applies to top-level OUs. Exception only for **root/management accounts**

------------------------------------------------------------------------

## Account Factory

- **Automate** provisioning new accounts according to pre-approved configuration

- Provisioning support via **AWS Control Tower Account Factory for Terraform**

- **Account customizations**: the source groups CI/CD pipelines, CodePipeline, and Terraform under “Account Factory Customizations (AFC).” Check the specific customization mechanism: Account Factory Customization and Account Factory for Terraform are distinct offerings.

------------------------------------------------------------------------

## Dashboard

Continuous monitoring:

- All accounts in the enterprise

- Guardrails are being enforced

- Controls detecting noncompliance

- Noncompliant resources grouped by account and OU

------------------------------------------------------------------------

## Networking

- AWS account creation includes default VPCs in enabled Regions, including Regions not governed by Control Tower.

- Default VPC ≠ VPC created by Control Tower for provisioned accounts

- The source describes a provisioned VPC layout of **3 AZs**, with **1 public and 2 private subnets per AZ**. Treat this as the source configuration rather than an invariant layout for every landing zone.

- Support **VPC-to-VPC peering**

- Default VPCs in non-governed regions can be deleted

### Region Deny Guardrail

- Block API calls to services in **non-governed regions**

- The existence of a default VPC outside governed Regions does not by itself permit resource deployment; access depends on the applicable controls and network configuration.

- If guardrail is enabled → **cannot deploy resources** in denied regions

------------------------------------------------------------------------

## Additional features

- **Custom guardrails**: self-define preventive/detective guardrails using SCPs and Config rules

- **Drift detection**: detects configuration drift, provides guided updates

- **Service-managed StackSets**: more reliable deployment and lifecycle management

- **Proactive guardrails GA**: block non-compliant resources before creating

- Expanded support for registering existing accounts/OUs into landing zones

------------------------------------------------------------------------

## Monitoring

- **Log Archive account**: collect all logs centrally

- **CloudTrail**: capture Control Tower actions/events

- **CloudWatch Logs + Logs Insights**: view and query **lifecycle events**

- **Lifecycle event**: only recorded after **completion of entire chain of actions**
  - Record success/failure of action

  - Automatically recorded by CloudTrail as **non-API AWS service event**

  - Send to **Amazon EventBridge**

## Notes

### Standardized departmental accounts

For a financial institution moving multiple departments to AWS, establish a Control Tower landing zone and use Account Factory to provision member accounts with preconfigured networking and approved controls. Give department administrators the permissions needed to manage their own accounts while restricting service access according to least privilege. Control Tower supplies a common governance baseline across the organization rather than requiring departments to establish separate organizations.

A landing zone, account factory, preventive/detective/proactive controls, centralized logging, and a compliance baseline are complementary parts of this multi-account approach.
