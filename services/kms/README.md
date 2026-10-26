# AWS KMS and S3 Encryption

[Back to index](../../README.md)

## Encryption options

| Criterion | SSE-S3 | SSE-KMS | SSE-C | Client-side encryption |
| --- | --- | --- | --- | --- |
| Where encryption occurs | S3 server side | S3 server side | S3 server side | Client, before upload |
| Key control | S3 manages keys | KMS manages AWS-managed or customer-managed keys | Customer supplies and retains the encryption key | Customer/application manages encryption material |
| Data-key handling | S3 manages it | KMS supplies a data key for S3's encryption workflow | Customer supplies the required encryption key on the request | Client performs the encryption |
| Encryption-specific cost in source comparison | No separate encryption charge | KMS key and request charges can apply | No separate S3 encryption charge | No separate S3 encryption charge; application costs apply |
| Rotation | Managed by S3 | Depends on KMS key type and configuration | Customer responsibility | Customer responsibility |
| Access control | S3 bucket policies and IAM | S3 permissions plus KMS authorization | S3 bucket policies and IAM, plus possession of the key | S3 permissions plus application/key access |
| Key-usage audit | No customer-visible KMS key-usage trail | Detailed KMS CloudTrail records | Customer must audit its own key handling | Customer must audit its own key handling |
| Request handling | S3 performs encryption/decryption | S3 performs encryption/decryption with authorized KMS use | Supply required encryption headers over HTTPS | Encrypt before uploading; decrypt after downloading |
| Operational effort | Low | Configure key access and policies | Manage and supply keys safely | Implement or integrate client encryption and key management |

S3 object API activity can be audited separately through configured CloudTrail data events. The source's “untrackable” labels for non-KMS options concern key use, not a claim that all S3 access auditing is unavailable.

## Notes

### Encryption request headers

For **SSE-S3** with **AES-256**, specify:

```text
x-amz-server-side-encryption: AES256
```

For **SSE-KMS**, specify `x-amz-server-side-encryption` with value `aws:kms`. Add `x-amz-server-side-encryption-aws-kms-key-id` when selecting a particular KMS key.

For **SSE-C**, supply the customer-provided key information using these headers on the applicable upload/download requests:

```text
x-amz-server-side-encryption-customer-algorithm
x-amz-server-side-encryption-customer-key
x-amz-server-side-encryption-customer-key-MD5
```

Use HTTPS to protect the request carrying the customer-provided key. SSE-C keys are not retained by S3 for future requests, so the customer must preserve and supply them as required.

### Protecting healthcare backups

Two encryption approaches for backups in S3 are server-side AES-256 encryption and client-side encryption with customer-controlled keys before uploading over HTTPS. S3 server-side encryption uses **AES-256**, not AES-128. Encryption is one component of the source's healthcare compliance scenario; it does not establish compliance on its own.

### Enforcing an explicit encryption request

When a policy requires uploads to explicitly request SSE-S3, use an **S3 bucket policy** that denies `PutObject` unless `s3:x-amz-server-side-encryption` has the required `AES256` value. This enforces the request-header requirement rather than relying solely on a bucket default. Existing objects require separate consideration.

SSE-S3 provides AWS-managed key rotation without customer scripts. The source describes rotation as required every **365 days** or annually, but does not establish a customer-configurable annual rotation guarantee for SSE-S3. If an exact auditable rotation period is a requirement, verify the chosen key-management mechanism against it. S3 Object Lock controls object retention; it does not rotate encryption keys.
