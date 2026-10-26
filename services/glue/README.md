# AWS Glue

[Back to index](../../README.md)

AWS Glue is a fully managed, serverless extract, transform, and load (ETL) service. It runs data-processing jobs without requiring you to provision or maintain a server cluster or its operating systems.

## Schema discovery and transformation

- **Glue crawlers** inspect sources such as S3 files, recognize formats such as CSV, infer column schemas, and store metadata in the **Glue Data Catalog**. This reduces manual schema definition.
- **Glue ETL jobs** use scripts such as Python or Scala to transform data. Jobs can write **Apache Parquet**, a columnar format that can reduce the data Athena scans and improve analytical query performance relative to CSV.
- **Glue Studio** provides a visual ETL pipeline builder for developers and data engineers.
- **Glue DataBrew** provides no-code data preparation, cleaning, and profiling for analysts and data engineers.

Managed processing reduces operational work compared with maintaining an EC2 or EMR cluster. ETL compute is billed while jobs run; catalog, crawler, and other service usage can have their own charges. Serverless service selection still depends on the actual workload.

## Notes

### Daily CSV-to-Parquet processing

For an e-commerce business receiving daily sales CSV files from partners into S3, use a crawler to discover the raw files and their schema. Run a scheduled Glue ETL job to convert them to Parquet and write the results to the destination bucket `tutorialsdojo-data-transformed`. This automates schema discovery and conversion while minimizing cluster management.

### Avoiding repeated processing with job bookmarks

For daily financial reports that keep reprocessing old S3 input, enable **Glue job bookmarks**. Bookmarks retain supported source-processing state between successful runs so subsequent runs can identify new or changed input rather than repeatedly scanning and transforming all historical data. In a growing stock-market dataset, this reduces repeated work, runtime, and cost without adding a separate state-management service or deleting previously processed source files.

Enable bookmarks through the supported job setting or parameter and use the required script contexts. Bookmark behavior depends on the source and format; it is not a general guarantee that every job processes only brand-new files.
