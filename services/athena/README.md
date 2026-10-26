# Amazon Athena

[Back to index](../../README.md)

## Notes

### Improving queries over JSON data in S3

When an application loads hundreds of JSON documents into S3 every hour and Athena queries become slow as data volume grows, transform the data into **Apache Parquet**. Its columnar layout lets analytical queries read the columns they need instead of scanning complete JSON documents.

For data registered and governed through AWS Lake Formation, the querying principal needs the `lakeformation:GetDataAccess` IAM permission for underlying data access, along with the applicable Lake Formation permissions. Retain these access controls when optimizing the storage format.
