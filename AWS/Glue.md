## Glue Data Catalog

#AWS 
Metadata repository for all your tables:
Automated schema inference
Schemas are versioned.
It can be used to index S3. Then, Glue can provide these schmeas to other services such as [[Redshift]], [[Athena]], [[EMR]].

## Glue Crawlers:
Go through your data to infer schemas and partitions.
Works with JSON, Parquet, CSV and relational stores.
It works for S3, Redshift and [[RDS]]. The crawlers can run on schedule or on demand. 

The crawlers need IAM roles to access the sources.
If you think up front about how the data will be stored, and it is fairly consistent, the crawler can find out the partition structure for you.

Depending on how you plan to query the s3 bucket, organize the keys based on that. 

## ETL
Generate code in Python or Scala, or Spark/PySpark.
The target can be S3, JDBC, or Glue Data Catalog.
It is fully managed, cost effective, and one pays only for the resources consumed.
Jobs run on a serverless Spark platform. 
The Glue Scheduler schedules the jobs
Glue Trigger triggers jobs based on events.
The ETL jobs also need IAM roles toaccess the sources/targets
### Transformations:
- DropFields, DropNullFields
- Filter
- Join
- Map (add fields, delete fields, etc), called ChangeSchema

### Machine Learning Transformations
- FindMatches ML: identifies similar records, it is not necessary for them to be equal, useful for deduplication.

### Apache Spark transformations:
- K-Means
- Others