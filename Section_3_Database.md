# AWS Database Services

---

## Types of Databases

- **Relational Databases (SQL):**
  - Structured data, tables with rows and columns.
  - Support ACID transactions, strong consistency.
  - Use cases: OLTP, reporting, analytics.
  - Examples: Amazon RDS (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server), Aurora.

- **NoSQL Databases:**
  - Flexible schema, designed for scalability and performance.
  - Types:
    - **Key-Value:** DynamoDB, Redis.
    - **Document:** MongoDB, DocumentDB.
    - **Wide Column:** Cassandra, Keyspaces.
    - **Graph:** Neptune.
  - Use cases: IoT, mobile, real-time analytics, social networks.

---

## Amazon RDS (Relational Database Service)

- Managed relational database service (supports multiple engines).
- Handles backups, patching, replication, scaling, and failover.
- **Multi-AZ:** Synchronous standby for high availability.
- **Read Replicas:** Asynchronous replication for scaling reads.
- **Automated Backups:** Point-in-time recovery.
- **Snapshots:** Manual backups.
- **Encryption:** At rest (KMS) and in transit (SSL).
- **Monitoring:** CloudWatch metrics, Enhanced Monitoring.

---

## Amazon Aurora

- MySQL and PostgreSQL-compatible relational database.
- Up to 5x faster than standard MySQL, 3x faster than PostgreSQL.
- Distributed, fault-tolerant, self-healing storage (6 copies across 3 AZs).
- **Aurora Serverless:** Auto-scales compute based on demand.
- **Global Databases:** Cross-region replication for disaster recovery.

---

## Amazon DynamoDB

- Fully managed, serverless, key-value and document NoSQL database.
- Single-digit millisecond performance at any scale.
- **On-Demand or Provisioned Capacity:** Auto scaling.
- **Global Tables:** Multi-region, multi-master replication.
- **DAX:** In-memory caching for microsecond response times.
- **Streams:** Change data capture for event-driven architectures.
- **Backup & Restore:** Point-in-time recovery.

---

## Amazon ElastiCache

- Managed in-memory caching (Redis or Memcached).
- Use cases: Caching, session management, real-time analytics.
- **Redis:** Supports replication, persistence, pub/sub, Lua scripting.
- **Memcached:** Simple, multi-threaded, no persistence.

---

## Amazon Neptune

- Fully managed graph database (supports Property Graph and RDF).
- Use cases: Social networks, fraud detection, knowledge graphs.
- Supports Gremlin and SPARQL query languages.

---

## Amazon DocumentDB

- Managed document database, MongoDB-compatible.
- Scalable, highly available, fully managed.
- Use cases: Content management, catalogs, user profiles.

---

## Database Migration Service (DMS)

- Migrate databases to AWS with minimal downtime.
- Supports homogeneous (e.g., Oracle to Oracle) and heterogeneous (e.g., Oracle to Aurora) migrations.
- Continuous data replication.

---

## Key Concepts

- **ACID:** Atomicity, Consistency, Isolation, Durability (relational DBs).
- **BASE:** Basically Available, Soft state, Eventually consistent (NoSQL).
- **OLTP:** Online Transaction Processing (frequent, small transactions).
- **OLAP:** Online Analytical Processing (complex queries, analytics).

---

## Choosing the Right Database

- **Relational:** Structured data, complex queries, transactions.
- **NoSQL:** Flexible schema, high throughput, low latency, scalability.
- **In-memory:** Fastest access, caching, session storage.
- **Graph:** Relationships and connections between data.

---