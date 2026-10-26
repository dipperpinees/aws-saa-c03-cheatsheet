# AWS CloudHSM

[Back to index](../../README.md)

CloudHSM provides dedicated hardware security modules with customer control over cryptographic users and keys. AWS operates the infrastructure but does not hold a recovery password that can unlock a customer's keys.

## Notes

### Zeroization and unrecoverable keys

Zeroization erases cryptographic material as a security response. The source describes a CloudHSM device being zeroized after **three consecutive invalid administrator login attempts**, within its discussion of **FIPS 140-2 Level 3** tamper protection. This is a source-specific, device-generation-dependent behavior; do not assume the same login response for every CloudHSM generation.

In that scenario, the cluster had just been created and there was **no other copy of the keys**. Once zeroized, those keys were permanently lost. Neither the AWS CLI nor AWS Support can recreate erased keys. Recovery requires a valid pre-existing key copy or recoverable backup; redundant HSMs across AZs and an appropriate backup strategy reduce the chance of losing the only available key material.
