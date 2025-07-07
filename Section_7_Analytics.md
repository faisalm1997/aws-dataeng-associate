# AWS Analytics Services

---

## AWS Glue

AWS Glue is a **serverless, fully managed ETL (Extract, Transform, Load) service**. It is trigger-driven (on a schedule or on demand) and is used to build table definitions, perform ETL, and discover/define tables and schemas. Glue integrates with S3 data lakes, RDS, Redshift, and other SQL databases.

**Typical Data Flow:**  
`S3 → Glue → Redshift/Athena/EMR → Quicksight`

### Glue Crawler & Data Catalog

- **Glue Crawler:** Scans data in S3 and infers schema, creating or updating tables in the Glue Data Catalog.
- **Data Catalog:** Central metadata repository for all your data assets, used by Athena, Redshift Spectrum, EMR, and more.
- Crawlers can be scheduled to keep the catalog up to date.

### Glue Partitions

- Glue crawlers can extract partitions based on S3 data organization (e.g., year/month/day).
- Proper partitioning (especially on large datasets) improves query performance and reduces cost.

### AWS Glue + Hive

- Glue Data Catalog can serve as a Hive metastore for Spark, Presto, and Hive jobs.
- You can import an existing Hive metastore into Glue for seamless migration.

### Glue ETL

- **Auto-generates code** for transformations in Python or Scala.
- Supports server-side or SSL encryption for data in transit and at rest.
- Event-driven: can provision extra DPUs (Data Processing Units) for performance.
- Job metrics and errors are available in the Glue Console and CloudWatch.

#### Dynamic Frame

- A collection of dynamic, self-describing records with a schema.
- Similar to Spark DataFrame but supports schema flexibility and both Scala and Python APIs.

#### Transformations

1. **Bundled:** Dropfields, dropnullfields, filter, join, map, etc.
2. **ML Transformations:** FindMatchesML for deduplication and entity resolution.
3. **Format Conversions:** CSV, JSON, Parquet, Avro, XML.
4. **Apache Spark Transformations:** e.g., K-means clustering, joins, aggregations.

#### Resolve Choice

- Handles ambiguities in dynamic frames (e.g., duplicate field names, type casting, nested structures).

### Modifying the Data Catalog

- ETL scripts can update schema and partitions in the Data Catalog.
- JSON/AVRO/PARQUET files can only be modified inside S3.
- Nested schemas are not supported for modification in Glue.

### Running Glue Jobs

- Jobs can be scheduled (CRON) or triggered by events.
- **Job bookmarks** persist state to avoid reprocessing data.
- CloudWatch Events and SNS can notify on job success/failure.
- Glue jobs can trigger EC2, Kinesis, or Step Functions for further processing.

### AWS Glue Costs

- Billed by the second for crawlers and ETL jobs.
- First million objects in the data catalog are free.

### AWS Glue Studio

- Visual interface for building, running, and monitoring ETL workflows.
- Create DAGs (Directed Acyclic Graphs), transform/join/partition data, and monitor job execution visually.

### AWS Glue Data Quality

- Create Data Quality (DQ) rules manually or via recommendations.
- Integrated into Glue jobs using DQDL (Data Quality Definition Language).
- Results can fail jobs or be reported to CloudWatch for monitoring and alerting.

### AWS Glue Databrew

- Visual data preparation tool for data analysts and engineers.
- 250+ ready-made transformations (cleaning, normalization, enrichment).
- Create recipes, define DQ rules, and integrate with KMS, IAM, CloudWatch, CloudTrail.

#### Handling PII in Databrew

- Substitute, shuffle, encrypt, decrypt, null, mask, or hash PII data for compliance and privacy.

### AWS Glue Workflows

- Orchestrate multi-job, multi-crawler ETL processes.
- Triggered by events, CRON, or on demand.
- EventBridge can trigger workflows on S3 events or other AWS service events.

---

## AWS Lake Formation

Lake Formation builds on Glue to **simplify data lake setup, loading, monitoring, partitioning, encryption, and access control**.

**Typical Data Lake Flow:**  
`S3/DB → Lake Formation ↔ S3 Data Lake → Athena/Redshift/EMR`

### Building a Data Lake

1. **Create IAM user** with appropriate permissions.
2. **Create Glue connection** to your data source (e.g., RDS, on-prem DB).
3. **Create S3 bucket** for your data lake.
4. **Register S3 path** in Lake Formation and grant permissions.
5. **Create database** in Lake Formation for the data catalog.
6. **Use blueprint** to set up Glue workflow for ingestion and transformation.
7. **Grant SELECT permissions** to consumers (e.g., Athena, Redshift Spectrum).

### Governed Tables & Security

- Supports ACID transactions, Kinesis streaming, Athena queries.
- Auto compaction for storage optimization.
- Row/cell-level security for granular access control.

### Data Filters

- Column, row, and cell-level security via console or API for fine-grained access.

---

## Amazon Athena

Athena is a **serverless, interactive query service** for S3 data (supports CSV, TSV, JSON, ORC, PARQUET, AVRO, and compression).

**Use Cases:**  
- Ad hoc queries on S3 data
- Log analysis
- Integration with Quicksight for BI

### Athena + Glue: Costs & Security

- Fine-grained access via IAM.
- Policies can restrict CRUD/DDL operations for security and compliance.

### Athena Workgroups

- Organize users/teams/apps.
- Control query access, track costs, and manage encryption settings.

### Athena Cost Model

- Pay-as-you-go: $5 per TB scanned.
- Save costs by using columnar formats (ORC/PARQUET) and partitioning.

### Athena Security

- Access control via IAM, ACLs, S3 bucket policies.
- Encryption at rest (S3) and in transit (TLS).
- Cross-account access is possible with proper permissions.

### Athena Performance Optimization

1. Use columnar formats (Parquet/ORC) for efficient storage and querying.
2. Fewer, larger files are better than many small files (reduces overhead).
3. Use partitions for efficient querying and cost savings.

### Athena ACID Transactions

- Powered by Apache Iceberg (`table_type = 'ICEBERG'`).
- Supports row-level modifications, time travel, schema/partition evolution.
- Compatible with EMR, Spark, Flink, Trino, Presto, Hive.

#### Iceberg Integration

- Glue can serve as Iceberg's metadata store.
- Spark/Flink can use Iceberg API for ACID compliance.

#### Migration Types

- **In-place:** Create Iceberg metadata for existing data.
- **Shadow:** Copy data to Iceberg, allowing validation and easier recovery.

### CTAS (Create Table As Select)

- Create new tables from query results.
- Convert data formats, use S3 as external location.

### Athena Federated Queries

- Query data from sources other than S3 via Lambda connectors.
- Store views in Glue; secure with Secrets Manager and VPC endpoints.
- Supports cross-account federated queries.

---

## Apache Spark

Open-source distributed processing for big data (batch, real-time, ML, graph).

### How Spark Works

- Apps run as independent processes on a cluster.
- Spark context/driver coordinates via cluster manager.
- Executors run computations and store data.

### Spark Components

1. **Spark Streaming:** Real-time analytics (Kafka, HDFS).
2. **SparkSQL:** Fast SQL queries (supports Parquet, ORC, JSON, JDBC/ODBC).
3. **MLLib:** Machine learning library.
4. **GraphX:** Graph processing.

### Spark Structured Streaming

- Data stream → unbounded table (new data = new rows).

### Spark Integration

- **Kinesis:** Use KCL for streaming data.
- **Redshift:** Use spark-redshift package.
- **Athena:** Run Spark notebooks in Athena console (serverless, encrypted).

---

## Amazon EMR

Managed Hadoop framework on EC2 (includes Spark, HBase, Presto, Flink, Hive, etc.).

### EMR Cluster Architecture

- **Master Node:** Manages cluster.
- **Core Node:** Hosts HDFS data, runs tasks.
- **Task Node:** Runs tasks, does not host data.

### EMR Usage

- **Transient:** Terminates after processing (cost-saving).
- **Long-running:** Manual termination, suitable for DWH.

### EMR Integration

- Integrates with EC2, VPC, S3, CloudWatch, IAM, CloudTrail, Data Pipeline.

### EMR Storage

- **HDFS:** Distributed file system, ephemeral.
- **EBS:** Used for HDFS, deleted on cluster termination.
- **EMRFS:** Persistent S3 storage, DynamoDB for consistency.

### EMR Scaling

- Managed scaling supports instance groups and fleets.
- Scale up: add core, then task nodes.
- Scale down: remove task, then core nodes (spot nodes removed first).

### EMR Serverless (EMR on EKS)

- Submit jobs without provisioning clusters.
- Specify worker size/capacity; pay per use.
- Security: S3 encryption at rest, TLS in transit.

---

## Kinesis Data Streams

Fully managed real-time data streaming.

### Process

1. **Producers:** Apps, SDK, Kinesis Agent.
2. **Records:** Partitioned by key, sent to shards.
3. **Consumers:** Lambda, Apps, Firehose, Analytics.

### Retention

- 1–365 days; immutable records.

### Capacity Modes

1. **Provisioned:** Manual shard management.
2. **On Demand:** Auto-scales, pay per usage.

### Security

- IAM policies, encryption in flight (HTTPS), at rest (KMS), VPC endpoints.

### Producers

- **SDK:** PutRecord/PutRecords (batching).
- **KPL:** High performance, auto-retry, batching.
- **Agent:** Monitors log files, pre-processes data.

### Consumers

- **SDK:** Polls shards.
- **KCL:** Java library, checkpointing with DynamoDB.
- **Lambda:** Configurable batch size.

### Enhanced Fan-Out

- Each consumer gets 2MB/s per shard, low latency, higher cost.

### Scaling

- **Shard Splitting:** Increase capacity.
- **Merging:** Decrease capacity.
- **Resharding:** Read from parent shards until data is consumed.

---

## Amazon Data Firehose

Fully managed, near real-time streaming delivery to S3, Redshift, OpenSearch, Splunk.

- Auto-scaling, supports many formats.
- Data transformations via Lambda.
- Buffering based on size/time.

### Firehose vs Data Streams

- **Streams:** Real-time, managed by user, data retention.
- **Firehose:** Fully managed, serverless, no data storage.

---

## Amazon Managed Service for Apache Flink (MSAF) / Kinesis Data Analytics

- Managed Flink for stream processing.
- Supports Java, Python, Scala.
- Integrates with S3, table API for SQL access.

---

## Amazon MSK (Managed Streaming for Kafka)

- Fully managed Kafka clusters on AWS.
- Deploys brokers/zookeepers in VPC.
- Data encrypted at rest (KMS) and in flight (TLS).
- IAM for access control.
- Monitoring via CloudWatch, Prometheus, S3 logs.

### MSK Connect

- Manage Kafka Connect workers for data integration.
- Auto-scaling, plugin support.

### MSK Serverless

- No capacity management; auto-provisions resources.
- IAM access control, pay per usage.

### Kinesis vs MSK

- **Kinesis:** Shards, 1MB message limit, IAM security.
- **MSK:** Kafka topics/partitions, configurable limits, IAM security.

---

## Amazon OpenSearch Service

Managed search and analytics engine (fork of Elasticsearch/Kibana).

### Key Concepts

- **Documents:** JSON, unique ID, type.
- **Types:** Schema/mapping.
- **Indices:** Collections of documents, split into shards.

### Storage Types

1. **Hot Storage:** Fastest, EBS-backed.
2. **Ultra Warm:** S3 + caching, lower cost.
3. **Cold Storage:** S3, periodic access.

### Index Management

- Automate policies for deletion, read-only, storage migration.
- Rollups and transformations for summarization and alternate views.

### Cross-Cluster Replication

- Replicates indices, mappings, metadata across domains.

### Security

- Resource, identity, IP-based policies, request signing.
- VPC deployment, Cognito integration for dashboards.

---

## Amazon Quicksight

Fast, easy analytics service for visualizations, reports, and business insights.

### Data Sources

- Redshift, Aurora, Athena, IoT Analytics, EC2-hosted DBs, S3.

### Use Cases

- Interactive/ad-hoc exploration, dashboards, KPIs.

### SPICE

- In-memory engine for fast queries (columnar format).
- 10GB per user, scalable to thousands of users.
- 30-minute import timeout.

### Quicksight Security

- MFA, VPC connectivity, row/column-level security.
- Private VPC access via Direct Connect.
- IAM policies for data access.
- By default, can only access data in the same region.
- For cross-region: use security groups or VPC peering.

### User Management

- IAM or Active Directory (Enterprise Edition).

### Quicksight Pricing

- Annual subscription: $9/month (Standard), $18/month (Enterprise), $28/month (Quicksight Q).

---