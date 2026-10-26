# AWS Lake Formation

[Back to index](../../README.md)

AWS Lake Formation is a governance layer for data lakes backed by Amazon S3. It organizes access to cataloged data; it is not a separate repository that replaces S3 storage.

## Centralized permissions

Lake Formation supports database-style grants and revocations for data-lake access, including fine-grained row- and column-level controls. It can centralize governance and share cataloged data across AWS accounts, reducing the complexity of independently maintaining many bucket policies. The source positions it as a way to establish a secure data lake in days rather than months.

Register the relevant S3 locations with Lake Formation and configure the required service permissions. Registration alone does not revoke direct S3 access already granted through IAM or bucket policies. Restrict those access paths as needed so users cannot bypass the intended governance controls.

## Notes

### Role-based analytics across accounts

When several accounts each hold business-function-specific data in their own S3 buckets, use Lake Formation's cross-account sharing and role-based permissions to provide a central analytical view. This avoids maintaining scheduled copies solely to consolidate access. The source describes this as consolidating data into a single account; the important distinction is centralized cataloging and governed sharing, which need not physically move every source object.
