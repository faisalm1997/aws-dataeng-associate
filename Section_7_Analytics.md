# AWS Glue 

Building table definitions and ETL. AWS Glue is a serverless fully managed service which is trigger driven, on a schedule and on demand - used in ETL pipelines. It also has serverless discovery and definition of tables and schemas, can be used alongside s3 data lakes/rds/redshift and other SQL databases. 

Process: S3 > Glue > Redshift/athena/EMR > quicksight 

## Glue crawler/data catalog 

AWS Glue crawler scans data in S3 and then creates a schema. This crawler can be run periodically, and populate the glue data catalog which stores the table definition and the source data stays in S3. Once the data is cataloged, users can treat unstructured data like its structured and be used in other AWS services. 

## Glue partitions 

The glue crawler can extract partitions based on how S3 has organised the data. This will affect how glue will work and where the data is read from. If the data is queried by date or by any other field, the data will need to be organised in such partitions if not already or glue performance will degrade. 

## AWS Glue + Hive 

Hive lets you run SQL like queries from EMR. The glue data catalog can serve as a hive metastore and can also import a hive metastore into glue. 

## Glue ETL 

AWS glue can auto generate code by dragging and dropping what transformations to apply to the data. The code will be written in python or scala, and has server side or SSL encryption. Glue ETL is event driben and users can provision extra DPUs to increase performnace of the underlying spark jobs. 

Job metrics can be viewed to understand the max capacity and whether further DPUs are needed, this can be found in the glue console but errors are reported in cloudwatch and be configured using SNS. 

The targets for Glue ETL can be S3, RDS/Redshift via JDBC or glue data catalog. Glue is fully managed and PAYG with jobs being run on a serverless spark platform. Glue triggers run jobs based on events and scheduler will schedule jobs. 

### Dynamic Frame 

A dynamic frame is a collection of dynamic records which are self describing and have a schema. A spark data frame is similar but the dynamic data frame can work with scala and py APIs. 

### Transformations 

1. Bundled  - Dropfields, dropnullfields, filter, join, map 
2. ML transformations - FindmatchesML can identify dupes or matching records in the dataset, even when records dont have a common unique identifier and have no fields matching 
3. Format conversions - CSV, JSON, Parquet, Avro, XML 
4. Apache spark transformations - K-means 

#### Resolve Choice

Deals with ambiguities in a dynamic frame and returns a new one e.g. two fields with the same name can be rectified. Users can create new columns, cast values to a specific type, create a structure that contains each data type and project each type to a given type 

### Modifying the data catalog 

ETL scripts can update your schema and partitions if necessary. Users can add new partitions, update the table schema, create new tables, and re run the crawler. There are some restrictions, JSON/AVRO/PARQUET files can only be modified inside S3 with parquet requiring special code. Nested schemas as not supported for modifying. 

### Running glue jobs 

Glue jobs can be run using a CRON schedule or a job bookmark. A job bookmark can persist the state from the old job run ie, the job will know where you left off and whats been processed, allows you to only process new data. Works with S3 and relational databases via JDBC. 

Cloudwatch events can also be configured to notify when the job succeeds or fails using SNS. Things like invoking an EC2 run, sending an event to kinesis or activating a step function can also be done using cloudwatch events when running glue jobs. 

## AWS Glue costs 

Billed by the second for glue crawler and ETL jobs. The first million objects which are stored and accessed are free for the glue data catalog. Development endpoints for developing ETL code are charged by the minute.

