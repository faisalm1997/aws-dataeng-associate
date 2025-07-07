# Data Storage

---

## S3 (Simple Storage Service)

Known as **"infinitely scaling storage"**.

**Use Cases:**  
- Backup and storage  
- Disaster recovery  
- Archiving files  
- Application hosting  
- Media hosting  
- Software delivery  
- Data lakes  
- Hybrid cloud storage

**Key Concepts:**  
- Storage is organised in **buckets** (directories) with globally unique names across all regions.
- **Objects** are the files stored in buckets.
- Buckets are created in a specific region and are defined at the region level.

---

### S3 - Objects

- All objects/files have a **key** (the full path to the object), e.g.:
  ```
  s3://faisalm-bucket/faisal_file.txt
  ```
- The key is composed of a prefix + object name.
- There are no true directories, only folders (prefixes).
- **Max object size:** 5TB. For larger files, use multi-part upload.
- **Metadata:** Each object has metadata (tags, version ID if versioning is enabled).

---

### S3 - Security

**Three types of security:**
1. **User-based:** IAM policies (API calls allowed for specific IAM users).
2. **Resource-based:** Bucket policies using Object ACL or Bucket ACL.
3. **Encryption:** Objects can be encrypted using encryption keys.

**S3 Bucket Policies:**  
- JSON-based, can grant public access or enforce encryption.
- Allow/deny actions for accounts or IAM users.
- Can block all public access at the account level to prevent data leaks.

---

### S3 - Versioning

- Enable at the bucket level.
- Best practice for protecting against accidental deletes and easy rollback.
- Unversioned files have a version of `NULL`.
- Suspending versioning does not delete previous versions.

---

### S3 - Replication

- **Versioning** must be enabled on both source and destination buckets.
- Buckets can be in different AWS accounts.
- Replication is asynchronous; only new objects are replicated.
- Old objects: Use S3 batch replication.
- **Types:**
  1. **CRR (Cross-Region Replication):** Compliance, low-latency access across accounts.
  2. **SRR (Same-Region Replication):** Log aggregation, live replication within the same account.
- Objects with a version ID are not deleted; replication cannot be chained across buckets.

---

### S3 - Storage Classes

- **General Purpose:** Frequent access, low latency, high throughput.
- **Infrequent Access:** Less frequent access, rapid retrieval.
  - **S3 Standard-IA:** Disaster recovery, backups.
  - **S3 One Zone-IA:** Data lost if AZ is destroyed; for secondary backups.
- **Glacier:** Low-cost archiving/backup.
  - **Instant Retrieval:** ms retrieval (e.g., quarterly access).
  - **Flexible Retrieval:** Expedited (1–5 min), Standard (3–5 hr), Bulk (5–12 hr), min 90 days.
  - **Deep Archive:** Long-term storage, Standard (12 hr), Bulk (48 hr), min 180 days.
- **Intelligent Tiering:** Auto-moves objects between tiers based on usage (monthly monitoring fee, no retrieval charges).
- **Lifecycle Rules:** Move/delete data based on access patterns.
  - **Transition Actions:** Move to another class (e.g., Glacier after 6 months).
  - **Expiration Actions:** Delete after a set time (e.g., old versions).
- **S3 Analytics:** Helps decide when to transition objects (not for One Zone-IA or Glacier).

---

### S3 - Event Notifications

- Triggered when objects are created, removed, replicated, etc.
- Can filter events by object name.
- Example: Generate thumbnails on upload.
- Events can be sent to **SQS**, **SNS**, or **Lambda** (requires resource access policy).
- Can send all events to **EventBridge** for routing to other AWS services.

---

### S3 - Performance

- **Baseline:** Auto-scales to high request rates (100–200 ms latency).
- **Request Rates:** 3500 PUT/COPY/POST/DELETE or 5500 GET/HEAD per second per prefix.
- **No limit** on number of prefixes.

**Performance Optimisation:**
1. **Multi-part Upload:** Recommended for files >100 MB, required for >5 GB. Enables parallel uploads.
2. **Transfer Acceleration:** Upload to AWS edge location, then to S3 bucket in target region. Compatible with multi-part upload.
3. **Byte Range Fetches:** Parallelise GETs by requesting specific byte ranges (faster downloads, partial data retrieval).

---

### S3 - Encryption

**Four ways to encrypt objects:**
1. **SSE-S3:** Server-side, default, key managed by AWS (AES-256).
2. **SSE-KMS:** Uses AWS KMS, key managed by user (KMS limits may apply).
3. **SSE-C:** Customer-provided keys, S3 never stores the key.
4. **Client-side Encryption:** User encrypts data before upload (manages keys).

**Encryption in flight:**  
- **SSL/TLS:** Use HTTPS endpoints (mandatory for SSE-C).
- **Force encryption:** Use bucket policy to require secure transport.
- **Default encryption:** Enabled for all new objects by default.

---

### S3 - Access Points

- Create access points for different user groups to access different data sets in the same bucket.
- Each access point has its own DNS, can be internet or VPC origin.
- **VPC Origin:** Private access from within a VPC (requires VPC endpoint and policy).

---

### S3 - Object Lambda

- Use Lambda to modify objects before retrieval (e.g., redact PII, watermark images).
- Requires two access points: one for original, one for Lambda-modified object.

---

## EBS (Elastic Block Store)

- Network drive attached to EC2 instances.
- Persists data while instance runs.
- Can only be mounted to one instance at a time, bound to one AZ.
- Can move across AZs using snapshots.
- **Elastic Volumes:** Change size/type/performance without restart (can increase, not decrease).
- **Delete on Termination:** Root volume deleted by default; others can be retained.

---

## EFS (Elastic File System)

- Managed network file storage for EC2.
- Works across multiple AZs.
- 3x more expensive than EBS.
- Uses NFS protocol (Linux only, POSIX-compliant).
- Auto-scales, pay-per-use.
- **Security Group** required for access control.

### EFS Performance & Storage Classes

- **Performance:** Scales to 10+ GB/s throughput, PBs of storage.
- **Performance Modes:**
  1. **General Purpose:** Default, for most workloads.
  2. **Max I/O:** High latency, high throughput.
- **Throughput Modes:**
  - **Bursting:** Throughput scales with storage size.
  - **Provisioned:** Set throughput regardless of storage.
  - **Elastic:** Auto-scales based on workload.
- **Storage Tiers:**
  - **Standard:** Frequently accessed files.
  - **Infrequent Access:** Cheaper, retrieval cost.
  - **Archive:** Rarely accessed, 50% cheaper than standard.
- **Lifecycle Policies:** Move files between tiers based on access frequency.
- **Availability:** Use multi-AZ for production.

---

## AWS Backup

- Fully managed, centralised, automated backups across AWS services.
- No need for custom scripts/manual processes.
- Supports cross-region and cross-account backups.
- Point-in-time, on-demand, and scheduled backups.
- **Backup Plans:** Define storage type and retention.
- **Backup Vault Lock:** Enforces WORM (Write Once, Read Many); root user cannot delete backups when enabled.

---
