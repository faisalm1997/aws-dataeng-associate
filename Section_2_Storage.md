# Data Storage

## S3

Known as 'infinitely scaling storage'

Use cases: backup and storage, disaster recover, archiving files, application hosting, media hosting, software delivery, datalakes and for
hybrid cloud storage

Storage is in buckets which need to have globally unique names across all regions. Buckets are the directories and files are known as objects.

Buckets are creater in a region, they are defined at the region level

### S3 - Objects

All objects/files have a key, the key is the full path to the object e.g.

```s3://faisalm-bucket/faisal_file.txt```

The key is composed of prefix + object name

There are no directories within buckets, only folders. The max object size which you can upload is 5TB, if the object is greater than 5TB then we must use multi part upload.

The metadata - each object will have metadata which will tell you about the object, they can have tags which is useful for security and lifecycle and also a versionid if versioning is enabled.

### S3 - Security

Security for S3 buckets can come in 3 types:
    1. User based - IAM policies whereby API calls can be allowed for a specific user from IAM
    2. Resource based - bucket policies can be implemented using Object ACL or bucket ACL
    3. Encryption - objects can be encrypted inside s3 buckets using encryption keys

S3 bucket policies: they are JSON based policies which can be used to grant public access to a bucket or force objects to be encrypted when uploaded to the bucket. The policy can allow/deny actions using an account or an IAM user.

A user can block all public access to s3 buckets to prevent data leaks. This can be set at the account level to make sure any s3 bucket cannot be accessed via a public url.

### S3 - Versioning

A user can version their files in s3, this can be enabled at the bucket level. It is best practice to version buckets to protect against unintended deletes and easy roll back to previous versions. Any file/object which is not versioned will have a version as NULL. Suspending versioning does not delete previous versions of the bucket. 

### S3 - Replication

Must enable versioning in source and destination buckets before replication can occur. Buckets can be in different AWS accounts and copying is asynchronous. Only new objects are replicated after replication is enabled. Old objects need to be replicated using s3 batch replication. 

1. CRR - Cross region replication - use case: for compliance and low latency access across accounts
2. SRR - Same region replication - use case: for log aggregation or live replicaton between same accounts

Objects with a version id are not deleted, a user cannot chain replications across buckets.

### S3 - Storage classes

S3 has different storage classes and users can move between the classes manually or using s3 lifecycle configurations. 

S3 has 11 9's of durability across multiple A-Zs. Has 99.99% of availability and measures how ready a service is. 

1. General purpose - used for frequent data access, has low latency and high throughput
2. Infrequent access - data which is accessed less frequently but requires rapid access when needed. 
    a. S3 Standard IA - disaster recovery and backups
    b. S3 One Zone IA - data is lost when the AZ is destroyed, can be used for secondary backups and recreated data. 
3. Glacier - low cost object storage for archiving/backup.
    a. Instant retrieval - has ms retrieval e.g. for data accessed once a quarter
    b. Flexibile retrieval - expedited (1-5mins), standard (3-5hours), bulk (5-12hours) and has a min storage duration of 90 days
    c. Glacier deep archive - for long term storage, standard (12hours), bulk (48hours) and min storage of 180 days

S3 intelligent tiering - has a monthly monitoring and auto tiering fee which can move objects automatically between access tiers based on usage. No retrieval charges for s3 intelligent tiering. 

S3 lifecyle rules - rules which move data around into storage tiers based on how often they have been accessed.

1. Transition actions - configuring objects to transition to another storage class e.g. moving to glacier for archiving after 6 months
2. Expiration actions - configuring objects to delete after some time e.g. deleting old versions of files

These rules can be set to specific prefix or a certain object tag. 

S3 analytics - helps the user decide when to transition objects to the right storage class, does not work for one zone-IA or glacier. The report is updated daily and can take 24-48 hours to see the data.

### S3 - Event notifications

Events are when objects are created, removed or replicate etc. objects can be filtered by name where possible. You can generate thumbnails of images which are uploaded to s3 and create as many 's3 events' as desired. S3 event notifications typically deliver events in seconds but sometimes can take longer.

e.g. S3 events can be sent to SQS, SNS, Lambda function but all 3 need a resource access policy to be able to send the event notifications.

A user can also send all events from an s3 bucket to eventbridge which can then send rules to over 18 aws services. A user can filter the events with JSON rules and also send events to multiple destinations.

### S3 - Performance

Baseline performance of s3 - can auto scale to high request rates which have a latency of 100-200ms. Apps can achieve 3500 PUT/COPY/POST/DELETE or 5500 GET/HEAD requests per second per prefix in a bucket. There is no limit on the number of prefixes in a bucket.

S3 Performance optimisation -
    1. Multi part upload - it is recommended to use multi part upload for files > 100mb and must use for files > 5gb. Multi part upload can parallelise uploads which can speed up transfers.
    2. Transfer acceleration - a user can increase the speed of transfer to an AWS edge location which can then forward the data to an s3 bucket in the target region. Transfer acceleration is compatible with multi part upload.
    3. S3 byte range fetches - a user is able to parallelise GETs by requesting specific byte ranges. This gives the s3 bucket better resilience in case of failures. Byte range fetches can speed up downloads and can also be used to retrieve only partial data.

### S3 - Encryption

There are 4 ways to encrypt objects in s3 buckets:

    1. SSE-S3: this is server side, and is enabled by default in the bucket. The key is managed by AWS. AES-256
    2. SSE-KMS: can leverage AWS KMS to manage encryption keys. The key is managed by the user. KMS limits can impact object encryption. 
    3. SSE-C: this is encryption through customer provided keys. S3 never stores the encryption key and the key is managed by the user. 
    4. Client side encryption - user has to use a client side s3 encryption library. The client encrypts the data themselves before sending to s3 and decrypt when retrieving from s3. Customer fully manages the keys. 

Encryption in flight - SSL/TLS: S3 has two endpoints, one which is non encrypted HTTP and HTTPS which is encryption in flight. HTTPS is recommended but mandatory for SSE-C. Most clients use HTTPS endpoints by default.

Force encryption - A user can force encryption in transit by using a bucket policy and set the condition for 'secure transport' to false/true when not using/using.

Default encryption - this is switched on for all new objects in the s3 bucket by default and auto applied.

### S3 - Access Points

Users can create S3 bucket access points when different user groups want access to different sets of data within the same bucket. Users can create a policy which can grant read/write access to the s3 access point. Access points simplify security management for s3, each access point has its own DNS with an internet or vpc origin. An access point policy can be written which can manage security at scale. 

1. VPC origin - defines access to s3 bucket which is privately accessible from within the VPC. Have to create a VPC endpoint which allows the user to connect to the vpc origin s3 access point. The VPC endpoint has a policy which needs access to the vpc origin s3 endpoint and the s3 bucket itself. 
2. 

### S3 - Object Lambda

Lambda apps can be used to change objects stored in an s3 bucket before it is retrieved by a caller application. In this case, only one s3 bucket is needed where two access points are created. An S3 access point to retrieve the original object and an object lambda access point which can send the changed object to the caller application. e.g. redacting PII data or watermarking images on the fly. 

## EBS

EBS is a type of EC2 storage, Elastic Block Storage is a network drive which you can attach to instances whilst they run. It allows instances to persist data whilst they run and can only be mounted to one instance at a time, they are also bound to one AZ. 

An EBS instance can be moved across AZ if a snapshot is used. EBS needs config for provisioned capacity and IOPS. EBS volummes dont need to be attached to an instance, they can be left unattached and used 'on demand'. 

    - Delete on termination: by default, the root EBS volume is deleted, any other attached volumes are not deleted (can also be controlled by cli).
    - EBS Elastic volumes: EBS elastic volumes can be changed without needing to restart the EC2 instance, can increase volume size but not decrease. Change volume type and increase/decrease performance. 

## EFS

Managed network file storage system which can be mounted on an EC2. Works in multi AZ and is 3x more expensive than a EBS volume.

A sg (security group) needs to be setup to control access to EFS, EFS uses NFS protocol and can be used to store content, web serving and wordpress. EFS is only useable on linux and not windows (POSIX file system). The file system auto scales with pay per use. 

### Performance and storage classes

EFS can scale concurrently and have 10Gb + /s throughput. Can grow to PBs through auto scaling.

#### EFS Types

    1. Performance mode: this is set as default at the time of creation of EFS, general purpose is set as default with Max I/O as high latency throughput. 
    2. Throughput mode:  this can be of 3 types: 
        a. bursting - provideds throughput that scales with the amount of storage for the workloads with basic performance requirements. 
        b. provisioned - where throughput is set regardless of storage size
        c. elastic - where throughput auto scales based on workloads, used for unpredictable workloads.

#### EFS Storage Classes

Storage tiers - this is the lifecycle management feature 
    1. Standard tier - for frequently accessed files
    2. Infrequent access - for infrequent access, but there is a cost to retrieve files. Cheaper than standard.
    3. Archive - for rarely accessed data and is 50% cheaper than standard. 

Lifeycle policies will allow files to move between storage tiers depending on how frequently they have been accessed. 

Availability: one AZ is used for development, for production workloads is it best to use multi A-Z

## AWS Backup

Fully managed service which is centrally managed and has automated backups across AWS services. There's no need to create custom scripts and manual processes to automate or create backups.

Supports cross region and cross account backups, point in time backups for supported services. On demand and scheduled backups. Users are able to create backup policies which are called backup plans. Backup plan can contain the type of storage and duration of storage required. 

Backup vault lock policy - has WORM enforced, 'write once, read many' state for all backups. This can be stored in the backup vault, the root user cannot delete backups when vault lock is enabled.
