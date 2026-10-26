# Amazon Redshift

[Back to index](../../README.md)

## Querying data in S3

A data lake query workflow combines S3 storage, metadata describing the data, and a query engine:

1. **AWS Glue Data Catalog:** Stores table schemas and locations. Glue crawlers can discover schemas in JSON files; metadata can also be defined explicitly. The catalog does not itself query the data.
2. **Amazon Athena:** Runs serverless SQL queries directly over supported files in S3, including JSON. In the source's on-demand pricing model, charges depend on data scanned.
3. **Amazon Redshift Spectrum:** Extends Redshift queries to supported data stored in S3 without first loading it into Redshift tables. Queries can combine external data with warehouse tables.

## Notes

### Large BI datasets

For a dataset of **more than 500 TB**, mostly structured data with a smaller collection stored in S3, the source uses **Redshift with Redshift Spectrum** to serve standard SQL business intelligence tools. Redshift handles warehouse analytics, while Spectrum queries supported external data in S3. Dataset size alone does not determine the best service; query patterns and data formats also matter.
