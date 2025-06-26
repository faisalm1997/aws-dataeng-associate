# Migration services

## AWS Application Discovery Services

If you're migrating to the cloud, you can use this service to plan migration projects or gather information. 

### Agentless discovery connector

This is used for VM inventory, config and performance history such as CPU, memory and disk usage

### Application discovery agent

This is used for system config, system performance, running processes and details of the network connections between systems. AWS migration Hub can be used to view the results of this data. 

## AWS Application migration service MGN 

A 'lift and shift' rehost solution which simplifies migrating apps to AWS. It can convert your physical, virtual and cloud based servers to run natively on AWS. Supports a wide range of platform, OS and databases. There is minimal downtime when migrating becauuse of continuous replication and then adhering to a cutover when using MGN from staging to production. 

## AWS Database migration service DMS 

This service is used to quickly and securely migrate databases to AWS, the service is resilient and self healing. The source database which is being migrated always remains available during the migration, DMS can support:
    1. Homogeneous migrations: e.g. Oracle to Oracle 
    2. Heteregeneous migrations: e.g. SQL server to aurora

DMS also supports continuous migration through CDC. For DMS to work, there must always be an EC2 instance which is used to perform the replication tasks. 

On premise DBs and Azure/AWS DBs can be used as source and targets for the DMS service. 

## AWS Schema Conversion Tool SCT

SCT is a tool which can be used to convert a DBs schema from one engine to another e.g. for OLTP SQL Server > MySQL or OLAP Oracle > Redshift. 

SourceDB > SCT + DMS > TargetDB (with different engine)

You do not need to use SCT if you are migrating to the same DB engine e.g. on premise postgreSQL > RDS postgreSQL

### DMS - Multi AZ Deployment 

When multi AZ is enabled, DMS provisions and maintains a standby replica in a different AZ. This can provide data redundancy, eliminates I/O freezes and minimises latency spikes. 

## AWS DataSync

This service moves large amounts of data to andf from: 
    - On premise/ other clouds to AWS - this would need an agent
    - AWS to AWS - no agent is needed

Can sync to: 
    - S3 (any storage class)
    - EFS 
    - FSX: Lustre, Windows, NetApp 

Replication tasks on datasync can be scheduled daily, weekly and hourly. File permissions and metadata can be preserved using NFS POSIX, SMB. One datasync agent can use 10GBps of speed and user can setup a bandwidth limit. 

Datasync can also copy data and metadata between AWS storage services. 

## AWS Snow family

AWS Snowball - portable device which is used to collect and process dbata at the edge, can be used to migrate data in and out of AWS. Helps to migrate PB of data. There are two types: one which is storage optimised, the other compute optimised. 

Snowball can have limited connectivity, bandwidth and a high network cost when migrating data. The time to transfer depends on the transfer speed and the amount of data being migrated. 

### Edge computing

Edge computing processes data when it is being created on an edge location. These locations have limited internet and no access to compute. A snowball edge device can be setup to do edge computing, can be optimised, EC2 instances/lambda functions can be used on the edge e.g. for ML workloads, to pre process data. 

## AWS Transfer family

A fully managed service which is used for file transfers in and out of S3/EFS using FTP protocol. 

Support protocols: FTP (file transfer protocol), FTPS (file transfer protocol over SSL), SFTP (secure file transfer protocol)

Managed infrastructucture which is scalable, reliable and highly available over Multi A-Z. Users pay per endpoint per hour and for every GB transferred. Users can store and manage credentials in service, and can integrate with other auth services such as Azure AD, Okta etc.