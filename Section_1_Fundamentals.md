# AWS Data Engineering Fundamentals

---

## Types of Data

- **Structured:**  
  Organised in a defined manner or schema (e.g., relational databases).  
  - Easily queryable, organised into rows and columns, consistent structure.  
  - Examples: CSV files, SQL tables.

- **Semi-Structured:**  
  Not fully structured but has some organisation (e.g., tags, key-value pairs).  
  - Examples: XML, JSON, log files.

- **Unstructured:**  
  No predefined schema or structure.  
  - Not easily queried, comes in various formats.  
  - Examples: Videos, images, free text.

---

## Properties of Data

- **Volume:**  
  The amount or size of data organisations handle (GB to PB).  
  - Challenges: Storage, processing, analytics.

- **Velocity:**  
  The speed at which new data is generated, collected, and processed.  
  - High-velocity data requires real-time or near real-time processing.

- **Variety:**  
  The different types, structures, and sources of data.  
  - Data can come in multiple formats and from various sources.

---

## Data Warehouses, Data Lakes, and Lakehouse

- **Data Warehouse:**  
  Centralised repository for structured data from different sources.  
  - Designed for complex queries, uses ETL, often with star/snowflake schema.  
  - Optimised for read-heavy operations.  
  - Examples: Amazon Redshift, Google BigQuery.

  ```
  Data sources → Data warehouse → Data mart
  ```

- **Data Lake:**  
  Storage repository for raw data (structured, semi-structured, unstructured).  
  - No predefined schema, supports real-time/stream processing, uses ELT.  
  - Example: S3 as a data lake.

  ```
  S3 → Glue → Athena
  ```

- **Comparison:**  
  - Data lakes are more agile/scalable, accept various raw data types.
  - Data warehouses are optimised for complex queries but can be more expensive.
  - Data lakes are often used for advanced analytics/ML; warehouses for BI.

- **Data Lakehouse:**  
  Combines features of data lakes and warehouses.  
  - Ingests raw data into a lake, processes/refines into a warehouse.  
  - Built on distributed architecture, supports ACID transactions.  
  - Example: AWS Lake Formation (with S3 and Redshift Spectrum).

---

## Data Mesh

- **Definition:**  
  Governance and organisation of data where individual teams own "data products" within a domain.
- **Features:**  
  - Data products serve use cases and can be grouped as "data domains".
  - Emphasises self-service tooling and infrastructure.
  - Focuses on data management, not specific tools/technologies.

---

## Managing and Orchestrating ETL Pipelines

**ETL:** Extract, Transform, Load  
A process to move data from source to data warehouse.

- **Extract:**  
  Collect raw data from APIs, files, or other repositories (batch or real-time).
- **Transform:**  
  Cleanse, enrich, reformat, aggregate, and handle missing data.
- **Load:**  
  Move transformed data into the target warehouse or repository (batch or streaming).

**Pipeline Management:**  
Automate using AWS Glue, EventBridge, MWAA, Step Functions, Lambda, and Glue Workflows.

---

## Data Sources and Data Formats

- **Sources:**  
  - JDBC: Java-based, platform/language independent.
  - ODBC: Platform dependent, language independent.
  - Raw logs, APIs, Streams.

- **Formats:**  
  - **CSV:** Text-based, comma-separated, human-readable/editable.
  - **JSON:** Lightweight, key-value pairs, flexible schema.
  - **AVRO:** Binary, stores data and schema, used for big data/real-time (Kafka, Spark, Flink, Hadoop).
  - **Parquet:** Columnar, optimised for analytics and I/O (Redshift Spectrum, Spark, Hive).

---

## Data Modelling, Lineage, and Schema Evolution

- **Star Schema:**  
  Fact tables, dimensions, primary/foreign keys. ERDs illustrate relationships.

- **Data Lineage:**  
  Visual representation tracing data flow/transformation from source to reporting.  
  - Helps track errors, ensures compliance, and clarifies data movement.

  *Example:*  
  Capture lineage using a Spline agent (Spark) attached to Glue, store in Neptune via Lambda, and query with external tools.

- **Schema Evolution:**  
  Ability to adapt dataset schema over time without disrupting processes.  
  - AWS Glue Schema Registry manages schema versions and backward compatibility.

---

## Database Performance Optimisation Techniques

1. **Indexing:**  
   Build indexes to avoid full table scans.
2. **Partitioning:**  
   Partition data for parallel processing and efficient scans.
3. **Compression:**  
   Reduces I/O, speeds up data transfer (e.g., GZIP, LZOP, BZIP2, ZSTD, columnar compression in Parquet).

---

## Data Sampling

1. **Random Sampling:**  
   Every item has an equal chance.
2. **Stratified Sampling:**  
   Divide population into subgroups (strata), sample within each for representation.
3. **Other Types:**  
   Systematic, clustering, convenience, judgmental.

---

## Data Skew

Unequal distribution or imbalance of data across nodes/partitions in distributed systems.

- Even partitioning fails if traffic is uneven.
- Causes:
  - Data growth over time.
  - Inadequate partitioning strategy.
  - **Temporal skew:** Partitioning by time/date, some periods have more data.

---

## Data Validation and Profiling

1. **Completeness:**  
   All required data is present (check for null/missing values).
2. **Consistency:**  
   Data format is consistent across datasets (cross-field validation).
3. **Accuracy:**  
   Data is correct/reliable (reconcile with trusted sources, validation checks).

---

## SQL Joins

1. **INNER JOIN:**  
   Returns intersection of two tables (matching data only).
2. **LEFT OUTER JOIN / LEFT JOIN:**  
   All data from left table, matching data from right; unmatched right is NULL.
3. **RIGHT OUTER JOIN / RIGHT JOIN:**  
   All data from right table, matching data from left; unmatched left is NULL.
4. **FULL OUTER JOIN / FULL JOIN:**  
   All data from both tables, regardless of match.
5. **CROSS JOIN:**  
   Every possible combination between two tables.

---

## SQL Regular Expressions

- `LIKE` is used for pattern matching/searching in SQL.
- `~` Regular expression operator
- `~*` Case-insensitive regex
- `!~*` Not match, case-insensitive
- `^` Match at start of string
- `$` Match at end of string (e.g., `boo$` matches 'boo')
- `|` Alternation (e.g., `sit|sat` matches 'sit' or 'sat')
- `[a-z]` Any lowercase letter
- `[a-z]{4}` Any four-letter lowercase word

**Example:**
```sql
SELECT * FROM table1 WHERE name ~* '(fire|ice)'
```
Selects rows where `name` starts with 'fire' or 'ice' (case-insensitive).

---