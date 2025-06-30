# EC2

An Amazon EC2 (Elastic Compute Cloud) instance is a virtual server in the cloud that provides resizable compute capacity for running applications.

## Use of EC2 in Data 

1. On demand, spot and reserved instances:
    a. Spot - can tolerate losses and are low cost which can be used for ML checkpointing 
    b. Reserved - used for longer running clusters or databases, users can get discounts when reserving their instances
    c. On demand - these instances are used when we dont know when we are going to use or may need compute instantly. 

2. Auto scaling - Can be used as leverage for EMR, auto scaling groups are automated for dynamodb, auto scaling groups 

Note: EC2 is behind EMR so we need to pay attention to master nodesl compute nodes which contain data and task nodes which do not contain data. 

### Graviton based instances

Graviton is amazons own family of processors which powers several EC2 types including general purpose, compute/memory/storage optimised and accelerated computing. The graviton based instances can be used as an option for many data engineering services and offers the best price performance. e.g MSK, RDS, EMR, Lamda, Fargate

# AWS Lambda 

Lambda is a way to run 'snippets of code in the cloud' - it is serverless and scales continually often used to process data as it moves around. Lambda is often used as a glue between services.

Why not just run the code on a server? 

- Server management is tedious 
- Servers can be cheap but scaling them is expensive 
- When running lambda, you only pay for what you use 
- By using lambda and serverless, its easier to develop between frontend and backend.

Main uses of lambda in data eng: real time file processing, real time stream processing, ETL, CRON replacement, process AWS events. Lambda also supports a wide range of languages. 

Examples: 
1. Working example: S3 > Lambda > elasticsearch/opensearch service
2. Lambda with data pipelines: can be scheduled to see when files are coming into S3, based on pre conditions lambda can then kick off a data pipeline after the files or data has been received into S3. 
3. Lambda and Redshift: The best practice for loading data into redshift is using COPY INTO command, lambda can be used to respond to new data ingestion into redshift but is stateless so cannot keep track, hence needing dynamodb to keep track of what has been loaded. Lambda can then batch new data and load that into redshift. 
4. Lambda and Kinesis: Lambda code receives an event with a batch of stream records, users can specify the batch size and too large a batch can cause lambda to timeout. Lambda will retry until it succeeds or the data expires, solution is to add more shards to ensure larger batches are processed

## Lambda - File system mounting 

- Lambda functions can access EFS file systems if they are running inside a VPC. Lambda can be mounted to EFS file systems during initialisation and must leverage EFS access points. Lambda can max out connection limits if too many EFS mounts are connected. 
- Storage options: 
    a. Ephemeral storage/tmp - a file system which is the fastest option but data is shared only in one invocation/session. 
    b. Lambda layers - 5 layers per function can hold up to 250mb each, IAM permissions are used to share data and data can be shared across invocations. 
    c. S3 - Elastic, object storage type, governed by IAM and is fast but not as fast as tmp/layers, data is shared across invocations. 
    4. EFS - Elastic, storage/throughput/data_transfer is not included in lambda pricing. Permissions are through NFS/IAM and data is shared across invocations.

# AWS SAM 

SAM is the serverless application model. A framework to develop and deploy serverless apps where all code is in YAML, users can build or design complex cloudformations from simple SAM YAML files. 

SAM supports anything from cloudformation and users can use codedeploy to deploy lambdas. SAM can also be run locally to test AWS services such as Lamnbda, API Gateway, DynamoDB. 

## Recipe 

SAM has recipes which users can write to indicate what serverless app needs to be deployed. The recipe has a transform header which indicates the SAM template and each app is then written and deployed ```sam deploy```

Can quickly sync local changes to AWS Lambda using SAM accelerate ```sam sync --watch```

Process: User builds the serverless app locally with SAM YAML template + app code > transformed into cloudformation template YAML + app code > zip + upload to s3 bucket > create and execute change set in cloudformation to deploy serverless application stack. 

## SAM Accelerate 

Deploying as fast as possible to reduce latency while deploying to AWS. ```sam sync``` synchronises the project which has been declared in SAM templates to AWS. Sam sync enabled code changes to AWS without updating infra.

- ```sam sync --code``` sync code changes without updating infra 
- ```sam sync --code --resource <resource_name>``` sync code for specific resource 
- ```sam sync --watch``` monitor for file changes and auto sync when changes are detected inside SAM template

# AWS Batch 

Run batch jobs as docker images. With dynamic provisioning of EC2 and spot instances when needed for underlying compute. Batch is fully serverless and you pay for the underlying EC2 instances which are used. Jobs can be scheduled using cloudwatch events and orchestrated using step functions. 

## AWS Batch vs Glue 

- Glue: runs on apache spark code with a focus on ETL, has a data catalog and users dont need to worry about configuring or managing resources. 

- Batch: For any computing jobs whether ETL/or not. Resources are created in the AWS account and managed by batch, batch is said to be better than glue for non ETL related work. 