# Security, Identity, and Compliance (SIC)

---

## Principle of Least Privilege

- Grant only the permissions required to perform a task.
- Start with broad permissions during development, then lock down as requirements become clear.
- Use IAM Access Analyzer to generate least privilege policies based on access activity.

---

## Data Masking and Anonymisation

- **Data Masking:** Obfuscates sensitive data (e.g., mask all but last 4 digits of a credit card). Supported in Redshift and Databrew.
- **Anonymisation:** Replace values with random values, shuffle data, or encrypt with hashing.

---

## Key Salting

- **Salting:** Appending or prepending a random value ("salt") to data before hashing.
- Prevents precomputed attacks (e.g., rainbow tables) by ensuring the same data does not produce the same hash across instances.
- Salts should be unique per user and rotated periodically.
- **Best Practice:** Always salt and hash passwords before storing.

---

## Keeping Data Where It Belongs

- Compliance may prohibit data transfer to certain regions.
- Backups or replication can inadvertently send data to restricted regions.
- Use AWS Organizations Service Control Policies (SCPs), restrict IAM and S3 bucket policies to allowed regions, and carefully configure backups.
- **Example:** IAM policy to restrict RDS backups to `us-east-1` and `us-west-1`.

---

# IAM (Identity and Access Management)

- **IAM** is a global AWS service for managing identities and permissions.
- The root account is created by default—should not be used or shared.
- **Users:** People within the organization.
- **Groups:** Collections of users (groups cannot contain other groups).

## IAM Permissions

- Users or groups are assigned JSON documents called **policies**.
- Policies define user permissions and enforce least privilege.
- Only grant permissions necessary for the user's tasks.

## IAM Policies

- Policies can be attached to multiple users and inherited from groups.
- **Inline Policy:** Attached to a single user.
- **Policy Structure:**
  - `Version`
  - `Id`
  - `Statement` (contains `Sid`, `Effect`, `Principal`, `Action`, `Resource`, and optional `Condition`)

## IAM MFA (Multi-Factor Authentication)

### Password Policy

- Stronger passwords = higher security.
- Enforce minimum length, character requirements, and prevent password reuse.

### MFA

- Adds a second authentication factor (virtual MFA device or U2F security key).
- Protects root and IAM user accounts.

## IAM Roles

- Assign permissions to AWS services to perform actions on your behalf.
- Common roles: EC2, Lambda, CloudFormation.

---

# AWS KMS (Key Management Service)

- AWS manages encryption keys, fully integrated with IAM for authorization.
- Controls access to data and audits key usage via CloudTrail.
- **Best Practice:** Never store secrets in plaintext or code; use environment variables for keys/passwords.

## KMS Key Types

- **AES-256 (Symmetric):** Single key for encrypt/decrypt.
- **RSA & ECC (Asymmetric):** Public key encrypts, private key decrypts (for users who can't call KMS API).
- **AWS Owned Keys:** Free.
- **AWS Managed Keys:** Free, auto-rotated yearly.
- **Customer Managed Keys:** $1/month + $0.03 per 10,000 API calls; rotation must be enabled.
- **Imported KMS Keys:** Manual rotation.

## KMS Key Policies

- Control access to KMS keys; users cannot access keys without policies.
- Default key policy is created if none provided.
- Custom key policies define users/roles with access (useful for cross-account access).

---

# Amazon Macie

- Fully managed data security and privacy service.
- Uses ML and pattern matching to discover and protect sensitive data (e.g., PII) in AWS.
- Example: Analyze S3 buckets for PII, notify EventBridge for further action.

---

# AWS Secrets Manager

- Securely stores secrets (API keys, passwords, etc.).
- Supports automatic secret rotation (can use Lambda for custom rotation).
- Secrets are encrypted with KMS.
- Supports replication across AWS regions; read replicas stay in sync and can be promoted to standalone secrets.

---

# AWS WAF (Web Application Firewall)

- Protects web apps from common exploits (Layer 7 of OSI model—HTTP).
- Deploy on ALB, API Gateway, CloudFront, AppSync, Cognito.
- **Web ACLs (Access Control Lists):** Define rules for:
  - IP sets
  - HTTP headers/body (prevent SQL injection, XSS)
  - Geo-matching (block by country)
  - Rate-based rules (DDoS protection)
- Web ACLs are regional except for CloudFront.
- **Rule Groups:** Reusable sets of rules for Web ACLs.
- **Note:** WAF does not support Network Load Balancer (Layer 4).

---

# AWS Shield

- Protects AWS services from DDoS (Distributed Denial of Service) attacks.

1. **AWS Shield Standard:**  
   - Free, enabled for all AWS customers.
   - Protects against Layer 3/4 attacks.

2. **AWS Shield Advanced:**  
   - Paid ($3k/month), 24/7 access to DDoS response team.
   - Auto-creates and deploys WAF rules to mitigate Layer 7 attacks.
   - Enhanced DDoS protection and reporting.

---