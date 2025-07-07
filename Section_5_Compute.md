# AWS Compute Services

---

## EC2 (Elastic Compute Cloud)

An Amazon EC2 instance is a virtual server in the cloud that provides resizable compute capacity for running applications.

### Use of EC2 in Data Engineering

1. **Instance Types:**
    - **Spot:** Low cost, can tolerate interruptions (e.g., ML checkpointing).
    - **Reserved:** For long-running clusters or databases; discounts for reservations.
    - **On Demand:** For unpredictable or instant compute needs.

2. **Auto Scaling:**  
   Used for EMR, DynamoDB, and auto scaling groups to manage compute resources automatically.

> **Note:**  
> EC2 is the underlying infrastructure for EMR. Pay attention to:
> - **Master nodes:** Manage the cluster.
> - **Core nodes:** Store data and run tasks.
> - **Task nodes:** Run tasks but do not store data.

### Graviton-Based Instances

- Amazon's own family of processors powering several EC2 types (general purpose, compute/memory/storage optimized, accelerated computing).
- Offers best price/performance for many data engineering services (e.g., MSK, RDS, EMR, Lambda, Fargate).

---

## AWS Lambda

Lambda lets you run code snippets in the cloud—serverless and auto-scaling. Commonly used to process data as it moves between services.

### Why Use Lambda Instead of Servers?

- No server management required.
- Scaling is automatic and cost-effective.
- Pay only for what you use.
- Easier integration between frontend and backend.

### Main Uses of Lambda in Data Engineering

- Real-time file processing
- Real-time stream processing
- ETL
- CRON replacement
- Processing AWS events

Lambda supports a wide range of languages.

### Examples

1. **S3 → Lambda → Elasticsearch/OpenSearch Service**
2. **Lambda with Data Pipelines:**  
   Scheduled to detect new files in S3; triggers pipelines after data arrives.
3. **Lambda and Redshift:**  
   - Best practice: Use `COPY INTO` command for loading data.
   - Lambda responds to new data ingestion but is stateless, so use DynamoDB to track loaded data.
   - Lambda batches new data and loads into Redshift.
4. **Lambda and Kinesis:**  
   - Lambda receives a batch of stream records.
   - Batch size is configurable; too large can cause timeouts.
   - Lambda retries until success or data expires.
   - Solution: Add more shards for larger batch processing.

---

## Lambda - File System Mounting

- Lambda functions can access EFS file systems if running inside a VPC.
- Mount EFS during initialization using EFS access points.
- Too many EFS mounts can max out connection limits.

**Storage Options:**
- **Ephemeral storage (`/tmp`):** Fastest, but data is only available within a single invocation/session.
- **Lambda Layers:** Up to 5 layers per function (250 MB each); data can be shared across invocations using IAM permissions.
- **S3:** Elastic object storage, governed by IAM; data shared across invocations (not as fast as `/tmp` or layers).
- **EFS:** Elastic, not included in Lambda pricing; permissions via NFS/IAM; data shared across invocations.

---

## AWS SAM (Serverless Application Model)

A framework to develop and deploy serverless apps using YAML. Simplifies building complex CloudFormation stacks.

- Supports all CloudFormation resources.
- Integrates with CodeDeploy for Lambda deployments.
- Can run locally to test AWS services (Lambda, API Gateway, DynamoDB).

### Recipe

- Write a SAM template (YAML) describing the serverless app.
- Deploy with `sam deploy`.
- Use `sam sync --watch` to quickly sync local changes to AWS Lambda.

**Process:**
1. Build app locally with SAM YAML template + app code.
2. Transform into CloudFormation template + app code.
3. Zip and upload to S3.
4. Create and execute change set in CloudFormation to deploy the stack.

### SAM Accelerate

- Deploy code changes as fast as possible to AWS.
- `sam sync` synchronizes declared resources in SAM templates.
- `sam sync --code` syncs code changes only.
- `sam sync --code --resource <resource_name>` syncs code for a specific resource.
- `sam sync --watch` monitors for file changes and auto-syncs.

---

## AWS Batch

Run batch jobs as Docker images with dynamic provisioning of EC2 and spot instances. Fully serverless; pay for underlying EC2 instances used.

- Jobs can be scheduled with CloudWatch Events.
- Orchestrate jobs using Step Functions.

### AWS Batch vs Glue

- **Glue:** Runs Apache Spark code for ETL, includes a data catalog, and is fully managed.
- **Batch:** For any compute jobs (ETL or not); resources are created and managed in your AWS account. Better for non-ETL workloads.

---