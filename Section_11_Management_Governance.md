# Management and Governance

---

## Amazon CloudWatch

### CloudWatch Metrics

- Provides metrics for every AWS service (e.g., CPU, network, disk).
- Metrics belong to namespaces and have dimensions (attributes).
- Up to 30 dimensions per metric; each metric has a timestamp.
- Users can create custom dashboards and custom metrics.

### CloudWatch Metric Streams

- Stream CloudWatch metrics in near real time to destinations like Kinesis Data Firehose and third-party services (e.g., Datadog).
- Option to filter and stream only a subset of metrics.

### CloudWatch Logs

- Users can define log expiration policies (never expire, 1 day to 10 years).
- **Log Groups:** Each app/service has a log group.
- **Log Streams:** Instances within the app containing log files.
- Logs can be sent to S3, Kinesis Data Streams, Kinesis Data Firehose, Lambda, or OpenSearch.
- Logs are encrypted by default; KMS encryption with custom keys is supported.

#### CloudWatch Logs Insights

- Search and analyze log data with a purpose-built query language.
- Save queries and add them to dashboards.
- Query multiple log groups across AWS accounts (not real time).

#### CloudWatch Logs S3 Export

- Export logs to S3 (can take up to 12 hours; not real time).
- Use `CreateExportTask` for export.
- For real-time/near real-time, use log subscriptions.

#### CloudWatch Logs Subscriptions

- Real-time log events can be sent to Kinesis Data Streams, Firehose, or Lambda.
- Supports filtering to deliver only relevant logs.

#### CloudWatch Logs Aggregation

- Aggregate logs to a single S3 bucket in near real time from different accounts/regions.

#### CloudWatch Logs for EC2

- By default, EC2 logs are not sent to CloudWatch.
- Run an agent inside EC2 to push logs (IAM permissions required).
    - **CloudWatch Logs Agent:** Old version, only sends logs.
    - **CloudWatch Unified Agent:** Collects additional metrics and logs; config managed via SSM Parameter Store.

#### CloudWatch Alarms

- Trigger notifications for any metric.
- Alarm states: OK, INSUFFICIENT_DATA, ALARM.
- Period: Length of time (seconds) to evaluate the metric.
- Can be created based on log metric filters.
    - **Targets:** Stop, terminate, reboot, or recover EC2; trigger auto scaling; send notifications to SNS.
    - **Composite Alarms:** Monitor multiple alarms using AND/OR logic to reduce noise.

---

## AWS CloudTrail

- Provides governance, compliance, and audit for AWS accounts.
- Enabled by default; records history of events and API calls.
- Logs can be sent to CloudWatch Logs or S3.
- Trails can be applied to all regions or a specific region.

### CloudTrail Events

- **Management Events:** Operations on resources (e.g., security, routing, logging). Logged by default. Can separate read/write events.
- **Data Events:** Not logged by default; must be configured (e.g., S3 object-level activity).

### CloudTrail Insights

- Detects unusual activity (e.g., hitting service limits, resource misprovisioning).
- Analyzes normal management events to create a baseline and flags anomalies.

### CloudTrail Events Retention

- Events stored for 90 days by default.
- To retain longer, log to S3 or analyze with Athena.

### CloudTrail Lake

- Managed data lake for CloudTrail events.
- Integrates collection, storage, preparation, and optimization for analysis/query.
- Events converted to ORC format; query with SQL.
- Data retained for up to 7 years.
- Visualize events with dashboards; sample queries available.
- Bound queries by eventTime to control costs.

---

## AWS Config

- Audits and records compliance of AWS resources.
- Records configuration and changes over time.
- Alerts for resource changes.
- Per-region service; can aggregate across regions/accounts.
- Config data can be stored in S3 and analyzed with Athena.

### Config Rules

- Users can create >75 rules (managed or custom via Lambda).
- Rules evaluated at intervals; do not prevent actions, only monitor.
- Pricing: $0.0003 per config item recorded/region; $0.001 per rule evaluation/region.

#### Remediations

- Automate remediation of non-compliant resources using SSM Automation documents (AWS-managed or custom).
- Configure remediation retries.

#### Notifications

- Use EventBridge to trigger notifications for non-compliance or config changes.
- Send notifications to SNS.

---

## AWS CloudFormation

- Declarative way to manage and outline AWS infrastructure as code.
- Stacks create resources in the correct order as specified.
- No manual resource creation; changes are reviewed through code.
- Costs can be estimated through the CloudFormation template.

### Infrastructure Composer

- Visual tool to build infrastructure and see resource relationships.

---

## SSM Parameter Store

- Secure storage for configuration and secrets.
- Supports seamless KMS encryption.
- Serverless, scalable, with version tracking.
- Security via IAM; integrates with CloudFormation and EventBridge.

### Tiers

- **Standard:** 10,000 parameters, 4KB max size, free.
- **Advanced:** 100,000 parameters, 8KB max size, parameter policies, $0.05 per advanced parameter/month.
    - Assign TTL to parameters for automatic expiration (e.g., passwords).
    - Multiple policies per parameter.

---

## AWS Well-Architected Framework

Six pillars for best practices in cloud architecture:

1. Operational Excellence
2. Security
3. Reliability
4. Performance Efficiency
5. Cost Optimization
6. Sustainability

- **Well-Architected Tool:** Free tool to review architecture against the six pillars and adopt best practices.

---

## Amazon Managed Grafana

- Fully managed, auto-scaling Grafana service.
- Monitor, visualize, and alert on metrics/logs.
- Integrated with IAM Identity Center for user management and permissions.
- Compatible with Grafana plugins and alerts.
- Encryption at rest and in transit.

---

## Amazon DataZone

- Data management service for cataloging, discovering, sharing, and governing data.
- Supports AWS, on-premises, and third-party data.
- Provides secure, governed access and transparency.

### Uses

- Catalog and discover data.
- Govern access with fine-grained controls.
- Collaborate across teams.
- Automate workflows to share data between producers and consumers.

### Key Components

1. **Domains:** Organizational entities to group users, data, and projects.
2. **Data Portal:** Controls web apps, IAM, catalog, and discovery.
3. **Business Data Catalog:** Centralized metadata repository.
4. **Data Projects:** Groups people and datasets.
5. **Data Environments:** Provides infrastructure within projects.
6. **Governance and Access Control:** Built-in workflows for requesting and approving data access; manage permissions via Lake Formation and Redshift.

---