# CloudFormation

[Back to index](../../README.md)

## Notes

### Wait for application initialization

An EC2 instance reaching `running` does not mean its software is ready. For a deployment such as SharePoint with AD and SQL Server dependencies, use a **CreationPolicy** and **cfn-signal** to wait for successful initialization.

- A CreationPolicy prevents a supported resource, such as `AWS::EC2::Instance` or `AWS::AutoScaling::AutoScalingGroup`, from reaching `CREATE_COMPLETE` until the required success signals arrive. A timeout or failed initialization causes creation to fail.
- Run `cfn-signal` after the `UserData` installation and configuration commands finish, and report their result to CloudFormation. AWS provides this helper in its CloudFormation helper-script tools for supported Amazon Linux and Windows environments.
- `DependsOn` controls resource creation order; it does not by itself verify that software inside an instance is ready.
- `cfn-init` applies resource configuration; `cfn-signal` reports the result.
- `UpdateReplacePolicy` controls retention, deletion, or snapshots of resources replaced during a stack update.
- `UpdatePolicy` controls supported update behavior, such as rolling updates for an Auto Scaling group.

### Template sections

| Section | Purpose |
|---|---|
| `AWSTemplateFormatVersion` | Declares the template format version, usually `2010-09-09`. |
| `Description` | Describes the template’s purpose. |
| `Metadata` | Supplies additional template or resource information, such as console UI configuration. |
| `Parameters` | Defines deployment inputs, such as VPC ID, subnet ID, and instance type. |
| `Mappings` | Defines static lookup tables, such as AMIs by Region. |
| `Conditions` | Controls conditional resource creation and outputs. |
| `Transform` | Applies macros or transforms, including the SAM `AWS::Serverless` transform. |
| `Resources` | Declares resources to create; this section is required. |
| `Outputs` | Exposes information such as instance IDs, load balancer DNS names, and VPC IDs after deployment. |
