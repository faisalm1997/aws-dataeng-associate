# Application Integration

---

## Amazon SQS (Simple Queue Service)

A **queue** enables applications to decouple and scale by acting as a temporary buffer for messages, allowing producers to send messages and consumers to retrieve them asynchronously.

- **Standard Queue:** Fully managed, scales from 1 message to >15,000 per second.
  - Default retention: 4 days (max 14 days).
  - No limit to the number of messages in the queue.
  - Low latency, horizontal scaling for consumers.
  - Possible duplicate and out-of-order messages.
  - Max message size: 256 KB (use SQS extended library for larger messages).
- **Use Cases:** Decouple apps, buffer writes to a database, handle large message loads, integrate with auto-scaling via CloudWatch.

### Producing Messages

- Define the message body (attributes, optional delivery delay).
- Queue returns a message identifier and MD5 hash of the body.

### Consuming Messages

- Consumer polls the SQS queue (can receive up to 10 messages at a time).
- Processes messages with a **visibility timeout** (prevents other consumers from processing the same message).
- Deletes the message using the message ID and receipt handle after processing.

### FIFO Queue

- **First-In-First-Out:** Lower throughput than standard, but preserves order.
- Messages are processed exactly once, avoids duplicates.
- Deduplication interval: 5 minutes (using duplication ID).
- **Throughput:** Up to 3,000 messages/sec with batching.

### SQS Limits

- Max 120,000 messages can be processed by consumers "in flight".
- Batch request: max 10 messages.
- Message content: XML, JSON, or unformatted text.
- Pricing is per API request.

### SQS Security

- **Encryption in flight:** HTTPS endpoints.
- **IAM policies:** Manage SQS usage.
- **SQS access policy:** Fine-grained control over requests.

### SQS Dead Letter Queue (DLQ)

- If a consumer fails to process a message within the visibility timeout, the message returns to the queue.
- Retry mechanism: after max retries, message moves to DLQ.
- DLQ for FIFO must also be FIFO; same for standard.
- Messages in DLQ must be processed before expiry.

#### DLQ - Redrive to Source

- Redrive messages from DLQ back to the source queue after fixing code, to reprocess failed messages.

---

## Amazon SNS (Simple Notification Service)

- **Pub/Sub architecture:** Event producer sends messages to an SNS topic; subscribers receive all messages.
- Up to 12.5 million subscriptions per topic; 100,000 topic limit per account.
- Supports direct publishing via SDK or to topics.

### SNS + SQS Fan-Out

- Push message once to SNS; all SQS queues subscribed receive the message.
- No data loss; services are fully decoupled.
- SQS handles delayed processing, retries, and persistence.
- Supports cross-region delivery from SNS to SQS.

#### Fan-Out Use Cases

1. **S3 Events to Multiple Queues:**  
   S3 can only have one event rule per event type/prefix. Use SNS fan-out to send the same event to multiple SQS queues.
2. **SNS to S3:**  
   SNS cannot directly send to S3; use Kinesis Data Firehose as an intermediary.
3. **Message Filtering:**  
   SNS topic receives a message/event, filter policies route to the correct SQS queue.

---

## AWS Step Functions

- Used to design and visualize workflows.
- Advanced error handling and retry mechanisms.
- Keeps audit/history of workflow executions.
- Max execution time for a state machine: 1 year.

### State Machine Concepts

- **State Machine:** The workflow itself.
- **State:** Each step in the workflow.

#### Types of States

1. **Task:** Performs a prescribed action.
2. **Choice:** Conditional logic (branching).
3. **Wait:** Delays execution for a specified time.
4. **Parallel:** Separate branches of execution.
5. **Map:** Runs a set of steps for each item in a dataset, in parallel.

- Each state can be: pass, succeed, or fail.

---

## Amazon AppFlow

A fully managed integration service to securely transfer data between SaaS apps and AWS.

- Supports sources, destinations, and scheduled frequency.
- Data transformation capabilities: filtering, validation.
- Data is encrypted in transit (over the internet or via PrivateLink).

---

## Amazon EventBridge

- Can schedule cron jobs, match event patterns, and trigger Lambda functions, SQS, or SNS.
- **Rules:** Specify which events to send to which targets; a single rule can send an event to multiple targets (parallel execution).
- **Event Bus:** Acts as a router for events, enabling communication between applications/services via publish/subscribe.
- **Schema Registry:** Analyzes events, infers schema, and generates code for applications to understand event structure. Schemas can be versioned.
- **Resource-Based Policy:** Manage permissions for a specific event bus; allow/deny events from another AWS account/region.
  - **Use Case:** Aggregate all events from an AWS Organization in a single account or region.

---