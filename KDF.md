---
tags:
  - AWS
---
Producers: it can read from [[KDS]], from [[CW]], and from [[AWS IoT]], although the most normal scenario is to read from KDS. Each record can be up to 1Mb. KDF executes a [[Lambda]], which transforms the data and then KDF uses batch writes to another service. 

It is not instantaneous then. It is near real time service. Classic destinations are:
- S3
- Redshift
- ElasticSearch
3rd part Partner destinations are also possible, like mongoDB.
You can also set another API using HTTP endpoints.

All or failed data only can be set to a [[S3]] backup bucket.
It allows to insert data into [[Redshift]], [[S3]], [[ElasticSearch]] and [[Splunk]].

Automatic scaling
Supports many data format, it can convert from CSV/JOSN to Parquet/ORC.
It can compress automatically
The data conversion is done through the lambda if it is from CSV to JSON, but if it is from CSV/JSON to Parquet/ORC, it does not need a Lambda. The lambda itself is optional. 

The user pays for the amount of data going through Firehose.

When creating a KDF, one specifies the buffer size, and the buffer interval. This is not so different from the time window batch and the size of the window for SQS. The buffer interval specifies how big can the batch be before it is sent to target, and the buffer interval does the same thing but time based. 