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

### AWS Glue costs 

Billed by the second for glue crawler and ETL jobs. The first million objects which are stored and accessed are free for the glue data catalog. Development endpoints for developing ETL code are charged by the minute.

### AWS Glue Studio

Visual interface for ETL workflows. Has a visual job editor where users can create DAGs for complex workflows, transform and join data and partition. Also comes with a visual dashboard where status and run times for jobs can be seen. 

### AWS Glue data quality

DQ rules can be created manually or through auto recommendations. They can be integrated into glue jobs and use DQDL, Data quality definition language. Results can be used to fail the job, or just be reported to cloudwatch. 

### AWS Glue Databrew 

Databrew is a visual data preparation tool. There is a UI which users can use to pre-process their large datasets and also have the data come into databrew from S3. Data warehouses or databases can also be used as sources. 

There are over 250 ready made transformations for glue databrew. Users can create recipes which can be saved as jobs within a project. We cam also define DQ rules, and create datasets with SQL for redshift/snowflake. 

There is also integration with KMS for data security, SSL for data in transit and IAM/Cloudwatch/Cloudtrail to restrict and observe. 

#### Handling PII in Databrew 

There are several ways of handling PII data in glue databrew: 

1. Substituting the PII data with random values 
2. Shuffling rows 
3. Deterministic and probabilistic encryption 
4. Decryption 
5. Nulling out PII or deleting 
6. Masking PII 
7. Hashing 

### AWS Glue workflows

Glue workflow is an orchestration tool which helps to design multi job, multi crawler ETL processes and helps then to run together. Glue workflows can be created from existing blueprints from an API or from the console. 

Users can trigger glue workflows using 'workflow triggers':
- To start jobs or crawlers, workflows can be triggered and then fired to completion 
- They can be scheduled using CRON 
- They can be set on demand, so only used when needed 
- Eventbridge evemts can be used to start on a single or batch of events e.g. arrival of a new object into s3, users can set batch conditions such as size or window for events. 

# AWS Lake Formation

Lake formation was built on top of AWS Glue to make it easy to setup data lakes in days. Lake formation can help load data and monitor data flows, partitions, encryption and manage keys and also define transformations using glue/monitor them. Access control, auditing is also included. There is no cost for lake formation but the underlying services do have charges.

Process: S3/DB > Lake formation <-> S3 data lake > Athena/Redshift/EMR 

## Building a data lake using lake formation 

1. Create an IAM user for a data analyst/data person 
2. Create Glue connection to the data source of choice 
3. Create S3 bucket as data lake 
4. Register S3 data lake path in lake formation and grant permissions 
5. Create database in lake formation for a data catalog
6. Use blueprint to setup a glue workflow and run the workflow 
7. Grant SELECT permissions to whoever needs to read the data e.g. Athena, redshift spectrum

For cross-account lake formation permissions, the recipient should be setup as a data lake admin. To access lake formation, users can use AWS resource access manager. Lake formation does not support manifests in athena or redshift queries. IAM permissions are needed for KMS encryption and to create blueprints/workflows for glue. 

## Governed Tables and Security 

Lake formation has additional features which incur addtional costs

- Lake formation supports ACID transactions across multiple tables, also works with kinesis data streaming and can be queried from Athena. 
- Auto compaction can be used to optimise storage 
- Row and cell level security can be used for granular access control

## Data Filters in Lake Formation 

- Lake formation has column, row, cell level security which can be aplied when granting SELECT perms on tables 
- Users can create filters via the console using the CreateDataCellsFilter API 
- All columns + row filter = row level security 
- All rows + column filter = column level security 
- column filter + row filter = cell level security 

# Amazon Athena

Athena is a serverless service which can query S3 data interactively. It has presto under the hood and can support many data formats such as CSV/TSV/JSON/ORC/PARQUET/AVRO and also supports snappy, zlib, LZO, GZIP compression. Athena can also query unstructured, structured and semi structured data.

Example usage: ad hoc queries of web logs, analysing cloudtrail logs, integration with quicksight. 

## Athena + Glue - costs and security 

Athena can be used alongside a glue crawler for a unified metadata approach. Fine grained access from athena to glue data catalog can be imposed by IAM based database and table level security. At best, the user should have a policy which grants access to the database and the glue data catalog in each region. The policies in place may restrict access to some CRUD/DDL commands but would need to add/delete what operations are needed. 

### Athena workgroups 

- Users can organise users/teams/apps/workloads into workgroups 
- Workgroups helps to control query access and track costs by workgroup
- Integrates with IAM. Cloudwatch and SNS 
- Each workgroup can have its own query history, data limits, IAM policies and encryption settings

### Athena Cost Model 

- PAYG: $5 for each TB of data scanned, failed queries do not count in the data scanning, there is no charge for DDL 
- Users can save lots of money by using ORC/PARQUET columnar formats, users can save 30-90% and get better performance. 
- However, glue and athena will continue to have their own charges. 

### Athena Security

- Access control is governed by IAM, ACLs and S3 bucket policies. 
- Encryption occurs at rest in the S3 directory - either server side or client side.
- Cross account access in S3 bucket policies is possible 
- TLS encrypts data in transit between athena and s3.

Note: Athena should not be used for ETL rather glue should be used and also not for data visualisation reports - quicksight is the better option. 

### Optimising Athena Performance 

1. Use columnar file formats such as Parquet / ORC 
2. A smaller number of large files performs better than a large number of small files 
3. Using partitions can optimise performance by only dividing files into smaller more manageable chunks when reading

### Athena ACID Transactions 

1. ACID transactions in Athena are powered by Apache Iceberg, ```table_type = 'ICEBERG``` in the ```CREATE TABLE``` command. 
2. Users can safely make row level modifications 
3. Compatible with EMR, spark and anything which supports iceberg table format 
4. ACID transactions remove the needs for custom reord locking and can enable time travel - any data which has been deleted can be retrieved by a SELECT statement. 
5. Governed tables in lakeformation is another way of getting ACID features in Athena. 
6. Performance is preserved by periodic compaction.

Iceberg is a table format for data lakes which can scale to petabyte level. It is ACID compliant, involves schema and partition evolution and incorporates time travel. There is efficient metadata management and works well with spark, flink, trino, presto and hive. 

### How does iceberg connect with AWS Glue/S3?

Glue can take the place of hive as icebergs metadata storage. The iceberg API communicates with the glue catalog for metadata and can store/retrieve files from s3 usually in parquet. Spark/flink then sits on top of the iceberg API and can be used in athena to maintain ACID compliance. 

Users can migrate glue data catalog to iceberg by making sure they are compatible with the iceberg API. Once this is done, athena can then use the catalog for ACID transactions. Reasons to migrate catalogs to iceberg: 
    - Compliance, old versions of tables can expire
    - Iceberg enables historical reporting with time travel queries 

Types of migration: 
- In place migration: Leave data files where they are, users would need to create iceberg metadata, this type of migration does not allow for schema or partition changes. 
- Shadow migration: copies the data over from glue to iceberg, allows for additional validation and has easier migration and recovery than in place migration. 

### CTAS 

CREATE TABLE AS SELECT 

Can be used/seen in the context of athena, creates a new table from the query results, can be used to create a new table as a subset of another. Can also be used to convert data into a new format - can use s3 as an external location within the query. 

### Athena Federated Queries 

Athena can query data from sources other than S3. Lambda can run connections between other services such as cloudwatch, dynamodb, rds, opensearch and Athena. 

Views created from federated data sources/services can be stored in glue. These can be secured using secrets manager using a vpc endpoint. Cross account federated queries are possible with passthrough queries using the native language of the data source. 

# Apache Spark 

Apache spark is an open source distributed processing framework for big data. It supports in memory caching and has optimised query execution. Supports Java, Scala, Py and R. Can be used across batch processing, real time analytics, ML, graph processing. Spark streaming can be carried out on kinesis, kafka and EMR. Spark is not meant to be used for OLTP. 

## How does spark work?

Spark apps are run as independent processes on a cluster. The spark conext/driver program coordinates them through a cluster manager. The executors run computations/tasks to store data and spark context sends app code and tasks to executors.

Spark has several components: 
1. Spark streaming which is real time streaming analytics: Kafka, HFDS
2. SparkSQL: 100x faster than mapreduce - supports parquet, orc, json, hdfs, jdbc/odbc 
3. MLLib: library for ML workloads 
4. GraphX: graph processing for ETL, analysis, but is not widely used. 

Spark core: has fault recovery, in memory management, scheduling, distribute and monitor jobs with interactive storage. 

Spark structured streaming: data stream > unbounded table - new data into the stream  = new rows appended to input table 

## Spark integration with kinesis + redshift 

1. Integration with kinesis = kinesis producers > kinesis data streams > spark dataset implemented from KCL (kinesis client library)
2. Integration with redshift = spark-redshift package allows spark datasets to integrate with redshift - can be treated like any other Spark SQL data source. 

## Spark integration with Athena 

Users can run a jupyter notebook with spark within an Athena console - the notebook is auto encrypted or users can use KMS. The integration is serverless and can be used an alternate analytics engine. Uses firecraker for spinning up spark resources and can be used programmatically using aws-cli. Can adjust DPUs for coordinator and executor sizes with pricing based on compute usage and DPU per hour. 

# Amazon EMR 

EMR or Elastic Map Reduce is a managed hadoop framework on EC2 instances. It includes spark, hbase, presto, flink, hive and more. EMR can use notebooks for processing and has several integration points with AWS. 

An EMR Cluster has a master node which manages the cluster and has a core node which hosts the HDFS data, runs tasks and has a task node which also runs tasks but does not host data. 

## EMR Usage 

1. Transient: transient clusters terminate once all steps are complete - loading data, processing, storing and then shut down which saves money 
2. Long-running: long running clusters must be manually terminated which is basically a DWH with periodic processing on large datasets. Users can spin up task nodes, using spot instances for temp capacity or can use reserved instanced on long running clusters to save costs. Auto termination is off. 

Frameworks and applications can be specified at cluster launch which can connect directly to master node to run the jobs directly. Steps for data processing can be invoked via the AWS console. 

## EMR/AWS services integration 

EMR can integrate well with other AWS services:

1. EC2 instances are used for the nodes in the EMR cluster 
2. VPC is used to configure the network in which the instances will be launched. 
3. S3 is used to store input and output data 
4. Cloudwatch is used to monitor cluster performance 
5. IAM is used to configure permissions 
6. Cloudtrail is used to audit requests made by users to the clusters/service
7. Data pipeline is used to schedule and start clusters 

## EMR Storage

- HDFS is used for storage: hadoop distributed file system
- Multiple copies of the data are made across cluster instances for redundancy
- Files are stored as 128mb blocks and ephemeral (cluster is terminated if data is lost)
- EBS can also be used for HDFS (M4/C4), EBS is also deleted when the cluster is terminated but the EBS volumes can only be attached when launching a cluster. If EBS is manually detached from EMR cluster then it is replaced. 
- Another method of storage is EMRFS: Access to S3 allows for persistent storage after cluster termination. Dynamodb is used to track consistency. 

## Other EMR info 

EMR charges by the hour + plus EC2 charges. Can provision new nodes if core node fails, can add and remove task nodes on the fly and can resize a running clusters core nodes for processing and HDFS capacity. Core nodes can also be added or removed, although removing core nodes can risk data loss from EMR cluster. 

### EMR managed scaling 

EMR has auto scaling which supports instance groups only. Managed scaling was introduced in 2020 which can support instance fleets as well as instance groups and can be used alongside a savings plan. 

The scale up strategy adds core nodes, then task nodes. The scale down strategy removes task nodes then core nodes. Spot nodes are always removed before on demand instances. 

## EMR Serverless (EMR on EKS)

EMR can run serverless. Users can choose an EMR release and runtime. The serverless version of EMR allows users to submit queries and scripts via job run requests. EMR can manage the underlying capacity while the user can specify the default worker size and capacity. 

EMR allows submission of jobs on EKS WITHOUT the need to provisiom clusters. 

Serverless app lifecycle: creating > created > starting > started > stopping > stopped > terminated -- all these steps need to call the API and incur costs. 

Note: When setting up an EMR cluster for spark - make sure to add 10% more capacity than requested by the job. 

EMR serverless uses similar security protocols to EMR. S3 encryption at rest, TLS in transit between EMR nodes. 