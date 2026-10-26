# Aurora Multi-AZ Architecture

[Back to Aurora](../README.md) · [Back to index](../../../README.md)

## Distributed storage

Aurora stores six copies of cluster data across three Availability Zones within a Region. This distributed storage is built in, including for a cluster with only one DB instance; there is no separate switch to disable its multi-AZ storage architecture. Storage redundancy improves durability, but does not guarantee that data can never be lost.

## Compute availability

Storage redundancy and available database compute are separate concerns:

- **Single DB instance:** A primary failure leaves no running reader to promote. Aurora must create a replacement, which can take several minutes.
- **Instances across AZs:** Place the primary in one AZ and at least one Aurora Replica in another. Aurora can promote an existing reader after a primary failure.

The source describes replica promotion as taking less than 30 seconds. Treat this as a typical source-era timing, not a guaranteed recovery limit.

## Readers also serve as failover targets

An Aurora Replica serves read-only queries and can become the primary during failover. A cluster supports up to 15 replicas. Readers share the distributed storage volume rather than each maintaining an independent full storage copy; the source describes typical replication lag below 10 milliseconds.

Using a reader in another AZ therefore provides both read scaling and a failover target within the Region. Multi-AZ describes the availability arrangement; an Aurora Replica describes the reader instance that can participate in it.

For comparison, a traditional RDS Multi-AZ **DB instance** standby does not serve reads. A separate read replica is needed for read scaling in that architecture. This comparison does not describe the different RDS Multi-AZ DB cluster architecture.
