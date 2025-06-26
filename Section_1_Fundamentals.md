# AWS Data Engineering Fundamentals

## Types of data 

- Structured: data which is organised in a defined manner or schema, found in relational databases. Easily queriable, organised into rows and columns and has a consistent structure e.g. csvs 

- Semi structured: Data which is not organised like structured data but has some level of structure in the form of tags and other patterns. Might be tagged, or categorised. e.g. XML/JSON files or log files. 

- Unstructured: Data that doesnt have a predefined schema or structure. Cannot be queried easily, comes in various formats e.g. videos and images. 

## Properties of data

- Volume: Refers to the amount or size of data which organisations are dealing with at any given time. Can range from GB to PB and comes with storage, processing and analytical challenges. 

- Velocity: Refers to the speed at which new data is generated, collected and processed. High velocity data requires real time or near real time processing capabilities. Rapid ingestion is critical. 

- Variety: Refers to the different types, structures and sources of data. Data can come in various formats and from multiple sources. 

## Data warehouses/Data Lakes/Lakehouse

- Data warehouse: A centralised repo which has data from different soruces stored in a structured format. Designed for complex queries, has an ETL process and has a snowflake/star schema. Data is optimised for read heavy operations. e.g. Redshift, bigquery

```Data sources > Data warehouse > Data mart```

- Data Lake: A storage repo which holds raw data in structured, semi structured and unstructured formats. Can store large amounts of raw data without a pre defined schema. Data is loaded without pre processing and can support real-time, stream processing. Follows an ELT process. e.g. S3 can be used as a data lake 

```S3 > Glue > Athena```

- Data lakes are more agile/scalable than warehouses as they can accept different varities of raw data without a pre defined schema. DWHs are more expensive because of optimisation required for complex queries. The costs can rise in a data lake when processing large amounts of data. 

- Data lakes are more often used for advanced analytics/ML and DWHs more for BI

- Data Lakehouse: Organisations can also use a combination of both, can ingest raw data into a data lake and then process and refine the data into a DWH. They are typically build on top of distributed architecture and can benefits from delta lake, ACID transactions. e.g. AWS Lake formation (with s3 and redshift spectrum)

## Data Mesh 

- Data mesh is about governance and organisation of data. Individual teams own 'data products' within a given domain. The data products can serve use cases. Data products can be grouped or serve individually as 'data domains' which is called 'domain based data management'.

- Data mesh also brings about self service tooling and infrastructure. Data lakes, DWH can be part of a data mesh but data mesh is more about the data management side and not the specific tools/technologies.

## Managing and orchestrating ETL pipelines

Definition: Extract, Transform, Load
 
ETL is a PROCESS used to move data from source to a data warehouse

- Extract: this is where you collect the raw data either from APIs, files, other data repos. Can be done in batches or in real time.

- Transform: extract the format suitable into a target data warehouse, data cleansing, enrichment, format changes, aggregations and handling missing data occurs here. 

- Load: move the transformed data into the target DWH or data repo. Can be done in batches or through streaming as data becomes available. We have to make sure of data integrity throughout this phase. 

Managment of pipelines: Has to be automated through glue, and using orchestration services such as eventbridge, MWAA, step functions, lambda and glue workflows. 

## Data sources and data formats 

- Sources: 
    - JDBC - java based connectivity which is platform and language independent 
    - ODBC - open database connectivity which is platform dependent and language independent. 
    - Raw logs 
    - APIs 
    - Streams 

- Formats: 
    - CSV - text based format, comma separated. For small to medium datasets, where data needs to be human readable and editable. Importing, exporting data from databses or spreadsheets. 
    - JSON - lightweight, text based format which represents structured or semi structured data based on key-value pairs. Can be used for settings, config and when a flexible schema is needed. 
    - AVRO - binary formnat data which stores both data and schema. Can allow files to be processed later without needing the systems context. Used for big data and real time processing, for data transport between systems. e.g. kafka, spark, flink and hadoop
    - Parquet - columnar storage format optimised for analytics. Used for analysing large datasets and when I/O operations need to be optimised. e.g. redshift spectrum, apache spark, hive. 

## Review of data modelling/lineage/schema evolution

- Star schema: Has fact tables, dimensions and primary/foreign keys. ERDs are drawn to show case a star schema. 

- Data lineage: this is a visual representation which traces the flow/transformation of data from source to reporting. It helps tracking errors back to source, ensures compliance and provides an understanding of how data is moved, transformed and consumed in systems.

e.g. we can capture data lineage using a spline agent/API (spark) which is attached to glue (for data transformations) and dump the lineage data in neptune via a lambda. Can then query neptune for the lineage using external tools. 

- Schema evolution: the ability to adapt the schema of a dataset over time without disrupting existing processes or systems. AWS glue schema registry can manage different versions of schemas over time and ensure backward compatibility with old schema versions. 

## Database performance optimisation techniques 

1. Indexing - build indexes to avoid full table scans, query only the indexes. 

2. Paritioning - partition the data to avoid scanning all the rows, enables parallel processing if the data is partitioned logically. 

3. Compression - speeding up data transfer, compression can reduce I/O reads. GZIP/LZOP/BZIP2/ZTSD are redshift compression examples, columnar compression is also an option in parquet format. 

## Data Sampling 

1. Random sampling - everything has an equal chance 

2. Statified sampling - divides the population into sub groups (strata). There is a random sample within each strata and ensures representation of each subgroup

others: systematic, clustering, convenience, judgmental. 

## Data skew 

Refers to the unequal distribution or imbalance of data across various nodes or partitions in distributed computing systems. 

- Even partitioning doesn't work if the traffic is uneven
- This can be caused by: 
    - When data is increasing over time
    - No adequate partitioning strategy 
    - Temporal skew: occurs when data is partitioned based on time or date, and some time periods have more data than others

## Data validation and profiling 

1. Completeness: ensures all required data is present and no data is missing. Checking for null values, missing values.
2. Consistency: is the data in a consistent format across datasets. Cross field validation is when you can compare across different sources and periods. 
3. Accuracy: ensuring the data is correct and reliable, reconciliation with trusted sources and validation checks. Invalid data can lead to poor insights. 

## SQL Joins

1. INNER JOIN - results only returned for the intersection of two tables, where there is common data across the two tables e.g. customer id common across table a and table b 

2. LEFT OUTER JOIN/LEFT JOIN  - will get results for all the data in the left table and the data which is matching in the right table from the left table, if no data is matching then we get NULL values. 

3. RIGHT OUTER JOIN/RIGHT JOIN - will get results for all the data in the right table and the data which is matching in the left table from the right table, if no data is matching then we get NULL values. 

4. FULL OUTER JOIN/FULL JOIN - will get results of everything from both tables regardless of match.

5. CROSS OUTER JOIN/CROSS JOIN - will give every possible combination of data between the two tables.

## SQL Regular expressions

```LIKE``` is used for pattern matching or searching in SQL

- ```~``` Regular expression operator
- ```~*``` to make the operator case insensitive
- ```!~*``` would mean to not match the expression and make it case insensitive
- ```^``` match a pattern at the start of the string 
- ```$``` match a patter at the end of the string - ```boo$``` would look for 'boo'
- ```|``` matches any alternate characters e.g. ```sit|sat``` would match both 'sit and sat'
- ```[a-z]``` would match any lowercase letter 
- ```[a-z]{4}``` would match any four letter lowercase word

e.g. ```SELECT * FROM table1 where name ~*'(fire|ice)'```
will select any rows where the name starts with fire or ice (case insensitive)