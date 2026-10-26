# AWS Security Token Service

[Back to index](../../README.md)

## Temporary credentials

STS issues an **access key ID**, **secret access key**, and **session token** with a limited lifetime. It does not create a permanent IAM user for each session. Temporary credentials expire, reducing the lifetime of exposure, but must still be protected while valid.

STS is a global service with Regional endpoints that can reduce latency. Session duration depends on the API and configuration: the source gives a general **15-minute to 36-hour** range, a common **one-hour default**, and up to **12 hours** for configured role sessions. These ranges are not interchangeable across all STS APIs.

## APIs

| API | Purpose |
|---|---|
| `AssumeRole` | Obtain a role session, including same-account or cross-account access. AWS services such as EC2 and Lambda use roles to access resources. |
| `AssumeRoleWithWebIdentity` | Exchange a supported web identity or OIDC token for temporary credentials, including provider sign-ins such as Google, Facebook, or Amazon through appropriate integrations. |
| `AssumeRoleWithSAML` | Exchange an enterprise SAML 2.0 assertion, such as one issued by an AD-backed identity provider, for role credentials. |
| `GetSessionToken` | Obtain a temporary session for an existing IAM user, commonly with MFA for CLI/API access. |
| `GetFederationToken` | Let a trusted custom broker request temporary credentials for federated users. |

## Federation

Federation allows employees or application users authenticated outside AWS to access AWS resources without a separate IAM user for every person. SAML 2.0 is commonly used for workforce federation; OIDC/web identity is common for mobile and web applications.

Cognito provides application-user identity management and AWS credential integrations. IAM defines identities, roles, and permission policies; STS issues the temporary credentials used by role sessions and federated users.

## Cross-account access

For a developer in **Account A** who needs an S3 resource in **Account B**:

1. Create a role in Account B with the required S3 permissions and a trust policy allowing the appropriate Account A principal.
2. Give that principal permission to call `sts:AssumeRole` on the role.
3. Call `AssumeRole` and use the returned temporary credentials for the authorized S3 operations.

Both the role's trust relationship and the caller's applicable permissions matter. A trust policy identifies the principals allowed to assume the role.
