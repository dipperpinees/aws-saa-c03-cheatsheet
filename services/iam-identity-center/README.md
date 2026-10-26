# AWS IAM Identity Center

[Back to index](../../README.md)

IAM Identity Center, formerly AWS Single Sign-On (SSO), centralizes employee access to AWS accounts and enterprise applications such as Slack, Salesforce, and Microsoft 365 using one set of credentials.

## Core functions

### Single sign-on

Employees sign in to the **AWS access portal** to see the accounts and applications assigned to them.

### Central administration

Manage workforce users and groups in one place and assign access to organization member accounts. Administration can be performed centrally through the organization management account or an appropriately configured delegated administrator.

### Identity sources

Use the Identity Center directory, connect Microsoft Active Directory, or integrate a supported external identity provider such as Okta or Google Workspace. Employees can then use existing corporate credentials. An arbitrary LDAP store requires a compatible integration rather than direct LDAP connectivity alone.

### Permission sets

Create a permission set such as `ReadOnlyAccess`, assign it to a group such as `Finance-Team`, and select target accounts such as a payments account and an audit account. Identity Center provisions the corresponding account roles and permissions.

## Comparison with individual IAM users

| Feature | Individual IAM users | IAM Identity Center |
|---|---|---|
| Scope | User exists in one AWS account | Central access assignments across accounts |
| Sign-in | Separate account credentials and sign-in contexts | One access portal |
| Administration | Per-account identity management | Central user/group assignments |
| MFA | Configured per IAM user | Central identity-source controls |
| Directory integration | Requires a separate federation design | Supported corporate identity integrations |

## Uses and cost

Useful for organizations with **two or more accounts**, department-based access, existing corporate identities, and centralized MFA requirements. IAM Identity Center has no additional service charge; AWS resources and connected services can have their own charges.

## Notes

### Least-privilege team access

In an application using Aurora, EFS, EventBridge, and Step Functions, map data science, engineering, and compliance teams to groups and permission sets appropriate to their responsibilities. Central group assignments reduce onboarding/offboarding work and avoid maintaining separate IAM roles manually in every account.

SCPs constrain account permissions but do not grant a team's resource permissions. Cognito manages application-user identities and is generally a different fit from workforce account access. Separate AWS accounts can provide useful isolation, but creating one solely for every team is not required for group-based access in an existing Identity Center deployment.
