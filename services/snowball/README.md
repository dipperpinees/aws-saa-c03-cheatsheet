# AWS Snowball

[Back to index](../../README.md)

## Local storage and compute

The source describes **Snowball Edge** as a physical AWS device with local storage and compute. Supported devices can run EC2-compatible instances and supported edge-processing workloads where connectivity is limited or unavailable. Device and service availability depend on the AWS offer and customer eligibility.

A surveying team collecting drone images at construction sites can store and process images locally instead of relying on laptops with insufficient capacity or an intermittent connection to EC2 and S3. The device can later be shipped to AWS for data import:

```text
Drone images → Snowball Edge at the site → Local storage and processing
            → Ship device to AWS → Import into Amazon S3
```

## Notes

### Large offline archive migration

For the source example of **750 TB** on a branch-office NAS with a poor internet connection, the selected design uses **10 Snowball appliances**, imports into an S3 bucket, then applies an S3 lifecycle policy to transition the objects to Glacier. The device count reflects the source's appliance-capacity assumptions and must be sized for the actual model and usable capacity.

Snowball imports into S3 rather than directly into a Glacier vault. The offline transfer avoids saturating the branch connection or interfering with normal business traffic.
