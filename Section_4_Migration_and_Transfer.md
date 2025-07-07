# Migration Services

---

## AWS Application Discovery Services

Used to plan migration projects or gather information when migrating to the cloud.

- **Agentless Discovery Connector:**  
  - For VM inventory, configuration, and performance history (CPU, memory, disk usage).
- **Application Discovery Agent:**  
  - For system configuration, system performance, running processes, and network connection details.
  - Results can be viewed in AWS Migration Hub.

---

## AWS Application Migration Service (MGN)

- "Lift and shift" (rehost) solution to simplify migrating apps to AWS.
- Converts physical, virtual, and cloud-based servers to run natively on AWS.
- Supports a wide range of platforms, OS, and databases.
- Minimal downtime due to continuous replication and cutover from staging to production.

---

## AWS Database Migration Service (DMS)

- Quickly and securely migrate databases to AWS.
- Resilient and self-healing; source database remains available during migration.
- Supports:
  1. **Homogeneous migrations:** (e.g., Oracle → Oracle)
  2. **Heterogeneous migrations:** (e.g., SQL Server → Aurora)
- Supports continuous migration via CDC (Change Data Capture).
- Requires an EC2 instance to perform replication tasks.
- Source and target can be on-premises, Azure, or AWS databases.

---

## AWS Schema Conversion Tool (SCT)

- Converts a database schema from one engine to another (e.g., SQL Server → MySQL, Oracle → Redshift).
- Typical workflow:  
  `SourceDB → SCT + DMS → TargetDB (with different engine)`
- Not needed for same-engine migrations (e.g., on-premises PostgreSQL → RDS PostgreSQL).

---

### DMS - Multi-AZ Deployment

- Enables a standby replica in a different AZ for redundancy.
- Provides data redundancy, eliminates I/O freezes, and minimises latency spikes.

---

## AWS DataSync

- Moves large amounts of data to and from AWS.
  - **On-premises/other clouds to AWS:** Requires an agent.
  - **AWS to AWS:** No agent needed.
- Can sync to:
  - S3 (any storage class)
  - EFS
  - FSx (Lustre, Windows, NetApp)
- Replication tasks can be scheduled (daily, weekly, hourly).
- Preserves file permissions and metadata (NFS POSIX, SMB).
- One DataSync agent supports up to 10 Gbps; bandwidth limits can be set.
- Can copy data and metadata between AWS storage services.

---

## AWS Snow Family

- **AWS Snowball:**  
  - Portable device for collecting and processing data at the edge.
  - Used to migrate petabytes of data in/out of AWS.
  - Two types: Storage-optimised and compute-optimised.
  - Useful when connectivity is limited, bandwidth is low, or network costs are high.
  - Transfer time depends on speed and data volume.

### Edge Computing

- Processes data at the edge location as it is created.
- Edge locations may have limited internet and no compute access.
- Snowball Edge devices can run EC2 instances/Lambda functions for ML workloads or pre-processing.

---

## AWS Transfer Family

- Fully managed service for file transfers in/out of S3/EFS using FTP protocols.
- **Supported protocols:** FTP, FTPS (FTP over SSL), SFTP (Secure FTP).
- Managed, scalable, reliable, and highly available (multi-AZ).
- Pricing: Per endpoint per hour + per GB transferred.
- Credentials can be managed in the service or integrated with external auth (Azure AD, Okta, etc.).

---