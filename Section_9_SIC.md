# Security Identity and Compliance 

## Princple of Least Privilege

- Grant only the permissions required to perform a task 
- Start with broad permissions while developing and then lock it down once you have a better idea of the services and operations a workload requires
- Can use IAM access analyser to generate least privilege policies based on access activity

## Data Masking and Anonymisation 

Dealing with PII and other sensitive data requires data masking, masking obfuscates the data, e.g. masking all but the last 4 digits of a credit card. Masking is supported in redshift and databrew. 

Anonymisation allows for replacing values with random values, shuffling data and encryption with hashing. 

## Key Salting 

Salting involves appending or prepending a random valye known as 'salt' to a piece of data. This prevents pre computed attacks where adversaries use pre generated hashes of commonly used passwords to find matches. Ensures the same piece of data doesnt produce the same hash across different instances. Salts should be rotated periodically, with each user having a unique salt. 

Note: Always salt and hash passwords before storing them. 

## Keeping Data where it belongs 

Compliance may prohibit data from being transferred into certain regions. It's all too easy for backups or replication to send data to a region where it is not allowed. AWS organisations service control policies are one way to enforce this, restrict IAM policies and S3 bucket policies to allowed regions only and take care when configuring backups. 

An example is an IAM policy to restrict RDS backups in us-east-1 and us-west-1

# IAM 

IAM = identity and access management which is a global service on AWS. The root account is created by default and shouldnt be used or shared, users are people within the organisation and can be grouped. Groups can only contain users and not other groups. 

## IAM Permissions 

Users or groups can be assigned JSON documents called policies. These policies can define the permissions of the users. In AWS, you can apply the principle of least privilege via these policies to only give permissions to a user that they need and nothing more. 

## IAM Policies 

Policies can be attached to multiple users, and can be inherited from the same IAM group. An inline policy is a policy which is attached to one user. The structure of an IAM policy consists of the policy version, an id for the policy and the statement. The statement consists of: sid (identifier), effect, principal, action and resource (list of resource we want to apply the action to). There is also a condition for when the policy is in effect but this is optional.

## IAM MFA 

### Password Policy 

Stronger passwords = higher security for the account. In AWS, users can setup a password policy which has a minimum password length, specific characters and prevent password reuse. 

### MFA 

Multi factor authentication is another level of auth whcih is beneficial if the password is stolen or the account is hacked. You want to protect the root user accounts and the IAM users. 

MFA can be enrolled on a virtual MFA device  or through a U2F universal 2nd factor security key. 

## IAM Roles

Some AWS services will need to perform actions on your behalf, thus we need to assign permissions to AWS services with IAM roles. Common roles include: EC2, Lambda and cloudformation. 

# AWS KMS 

AWS manages encryption keys for us which is fully integrated with IAM for authorisation. It is an easy way to control access to the data and KMS can audit key usage using cloudtrail. Never store secrets in plaintext or in code. Keys and passwords can be stored in env variables instead. 

## KMS Key Types: 

- AES-256 Keys which are symmetric, a single encryption key which is used to encrypt and decrypt.
- RSA & ECC Key pairs whcih are assymetric, have a public key which encrypts and a private key which decrypts. These key types are used by users who cant call the KMS API. 
- AWS Owned keys: Free
- AWS Managed keys: Free, keys are rotated automatically once per year
- Customer managed keys; $1 a month + $0.03 per 10,000 API calls, key rotation must be enabled 
- Imported KMS keys: rotation is done manually 

## KMS Key Policies 

KMS Key policies control access to KMS keys and users cannot control access to keys without key policies. A default key policy is created if you dont provide a specific key policy. A custom key policy can define users, roles who can access the KMS key - this is useful for cross account access of the KMS key in question. 

# Amazon Macie

Macie is a fully managed data security and data privacy service which uses ML and pattern matching to discover and protect sensitive data in AWS. Macie helps identify and alert you to sensitive data such as PII. Example: data in s3 bucket is analyse using Macie for PII data which then notifies eventbridge and its subsequent integrations. 

# AWS Secrets Manager 

Uses to store secrets and has the capability to force rotation of secrets every few days. Can also generate secrets automatically and rotate using lambda. Secrets are encrypted in KMS. Users can replicate secrets across multiple AWS regions with secrets manager keeping the read replicas in sync. Read replicas can be promoted to standalone secrets. 

# AWS WAF 

Web applicatiom firewall - protects web apps from common web exploits (layer 7 of OSI model). Layer 7 is HTTP and WAF can be deployed on ALB, API Gateway, cloudfront, appsync, cognito. 