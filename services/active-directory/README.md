# Active Directory

[Back to index](../../README.md)

## Notes

### Federating existing corporate identities

Identity federation lets users access AWS with identities managed in an external system, such as an existing on-premises Active Directory (AD). Access can follow the organization’s AD group assignments rather than requiring separate IAM users.

**AD Connector** is a directory gateway that forwards authentication requests to the existing AD. It does not copy directory users into a separate directory or require directory synchronization. Configure IAM role access for the relevant directory users and groups.

### SAML 2.0 and AD FS

AWS supports SAML 2.0 federation. Active Directory Federation Services (AD FS) acts as the identity provider: it authenticates a user against AD and issues a SAML assertion. The user then assumes an IAM role with the permissions assigned for that role.

Employees can reuse their corporate credentials. Disabling the AD account prevents subsequent authentication through that identity provider; already issued AWS sessions remain subject to their session lifetime.
