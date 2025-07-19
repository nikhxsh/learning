# Scalable UPI System Design for PhonePe / GPay / BHIM

## Functional & Non-Functional Requirements

### Functional Requirements (Expanded)

* **Users and Authentication**:

  * Users must register and link bank accounts via Aadhaar/Phone verification.
  * Multi-Factor Authentication (MFA) using device fingerprint and UPI PIN.
  * Binding to device using hashed device ID and secure storage of binding tokens (via Android Keystore / iOS Keychain).

* **Transaction Types**:

  * P2P: Send to individual via UPI ID, mobile number, or QR code.
  * P2M: Pay merchant, including static/dynamic QR support.
  * Collect: Request money (async, approval-based).
  * Refunds: Linked to transaction ID, tracked separately with original status.

* **NPCI API Dependencies**:

  * `/v1/initiateTransaction`: for sending collect/send messages.
  * `/v1/statusInquiry`: for polling or callback-based status checks.
  * `/v1/validateVPA`: for VPA verification.
  * NPCI callbacks need mutual TLS and replay-attack protection (nonce validation).

* **PSPs/Banks**:

  * PSP SDKs must be integrated with retry and fallback banks logic.
  * Session management with PSPs must use signed JWTs with expiry.

### Non-Functional Requirements (Expanded)

* **Performance**:

  * **P95** latency < 200ms for API response.
  * **P99** latency < 350ms for 90% of UPI requests.
  * Cold start latency for Lambda-based flows < 400ms.
  * Kafka round-trip < 100ms between producer and consumer.

* **Scalability**:

  * Stateless services auto-scale with EKS HPA via KEDA + custom metrics (Kafka lag, CPU).
  * Read replicas for PostgreSQL (Aurora) scale under read-heavy loads.
  * DynamoDB partition management for throttling limits and rate control.

* **Availability**:

  * SLA: 99.99% with SLO defined for latency & uptime.
  * Multi-AZ deployments with ALB/NLB failover.
  * UPI transactions buffered using Kafka in case of downstream outages.

* **Reliability**:

  * Distributed tracing using X-Ray/OpenTelemetry to trace across services.
  * Transaction retries with de-duplication and idempotency token.
  * Database writes wrapped with retry logic using exponential backoff and jitter.

* **Security**:

  * Tokenization of UPI IDs, virtual account masking.
  * Device-specific binding keys and ECDSA signing for transaction payloads.
  * mTLS with NPCI, PSPs with rotated certs every 30 days.
  * CSP headers and OWASP compliance for frontend security.
  * Role-based access control with fine-grained IAM roles for microservices.

* **Maintainability**:

  * Full CI/CD pipelines with unit, integration, security scan, and e2e testing.
  * Centralized logging via FluentBit -> CloudWatch -> Grafana Loki.
  * Secrets managed via AWS Secrets Manager and rotated automatically.

* **Observability & Compliance**:

  * SLI/SLO defined for UPI TXN: latency, success %, retries.
  * PCI-DSS & NPCI audit logs retained for 7 years with encryption.
  * Continuous compliance monitoring using AWS Config and Security Hub.

### Load & Traffic Spike Handling

* **Autoscaling**:

  * EKS HPA with CPU & custom KafkaLag metric.
  * Lambda concurrency limits with Provisioned Concurrency warmers.

* **Throttling & Rate Limiting**:

  * Envoy filter-based rate limiting (Redis-backed).
  * API Gateway usage plans for clients.

* **Queue-based Load Leveling**:

  * Kafka backpressure management with consumer group lag monitoring.
  * Retry queues with Kafka dead-letter topics per business flow.

* **Bulkheads & Circuit Breakers**:

  * Polly-based circuit breakers for downstream PSPs and NPCI.
  * Bulkhead isolation for compute units (e.g., PSPAdapter pods separate from TXN pods).

* **Cache Pre-warming**:

  * Pre-populate Redis cache at boot with PSP configs, limits, and bank metadata.
  * Use TTL with fallback to DynamoDB when expired.

### Disaster Recovery & Fault Tolerance

* **RTO / RPO Definitions**:

  * RTO: 5 mins for critical payment services (API + DB).
  * RPO: < 1 minute with binlog-based replication and CDC.

* **Multi-AZ / Region Failover**:

  * Aurora Global DB with primary failover and reader promotions.
  * Multi-region Kafka (MSK Replicator), S3 with CRR (Cross-Region Replication).
  * Route53 health checks and automated DNS failover.

* **Chaos Testing & Recovery**:

  * Gremlin used for scheduled fault injection.
  * Simmy chaos framework integrated into Polly for simulated faults.

* **Automated Snapshots & PITR**:

  * Aurora snapshots every 30 min, PITR enabled for 7-day rewind.
  * S3 versioning and MFA delete for backups.
  * Vault sealed backups written to cold storage.


## High-Level Design (HLD)

### Components and Responsibilities (Expanded)

| Component                    | Description                                                                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Mobile App/Web**           | Frontend interface, QR scanner, contact selector, status polling, encrypted UPI PIN input, session/cookie management.                             |
| **API Gateway**              | Authentication, rate limiting, request shaping, IP whitelisting, JWT/OAuth2.0 token validation, per-client usage plans, path-based routing.       |
| **Authentication Service**   | OTP verification, session token generation, device fingerprint validation, Cognito integration, refresh token rotation.                           |
| **Transaction Orchestrator** | Core workflow controller: validates request, calls PSP adapter, emits Kafka events, tracks timeouts/retries, generates audit logs.                |
| **Transaction Service**      | Writes transaction to database, handles idempotency token validation, applies business rules, enriches metadata.                                  |
| **PSP Adapter Layer**        | Connects to specific PSP (PhonePe, Paytm, Amazon Pay), performs retries, error mapping, handles cert pinning and API schema compliance.           |
| **NPCI Gateway Proxy**       | Manages mTLS comms with NPCI; retries, signs payloads, verifies response integrity.                                                               |
| **Event Bus (Kafka)**        | Publishes domain events like `TxnInitiated`, `TxnDebited`, `TxnCompleted`, `TxnFailed`. Used for decoupled communication, materialization, audit. |
| **Redis Cache Layer**        | Stores limits, bank configs, feature flags, throttle counters. TTL-based invalidation, backed by DynamoDB fallback.                               |
| **WebSocket Push Service**   | Listens to `txn-status` Kafka topic and sends live updates to clients subscribed via WebSockets. Uses ALB + sticky session routing with failover. |
| **Reporting/Audit Service**  | Listens to all Kafka events, writes to long-term storage (S3 + Athena), indexes logs for queries, integrates with regulatory dashboards.          |

### Text-Based Architecture Diagram (Advanced)

```
                         +----------------------------+
                         |     Mobile App / Web      |
                         +------------+--------------+
                                      |
                            +---------v----------+
                            |    API Gateway     |
                            +---------+----------+
                                      |
                      +---------------+---------------+
                      |                               |
           +----------v---------+           +---------v----------+
           | Authentication Svc |           |  Transaction Svc   |
           +----------+---------+           +----------+---------+
                      |                               |
            +---------v----------+         +----------v-----------+
            | Session & Token DB |         | PostgreSQL (Aurora)  |
            +--------------------+         +----------+-----------+
                                                     |
                                      +--------------v--------------+
                                      |   Transaction Orchestrator  |
                                      +--------------+--------------+
                                                     |
                          +--------------------------+-------------------------+
                          |                                                    |
           +--------------v---------------+              +---------------------v------------------+
           |     PSP Adapter (Strategy)   |              |          NPCI API Gateway Proxy        |
           +--------------+---------------+              +---------------------+------------------+
                          |                                                     |
       +------------------v-----------+                            +------------v-------------+
       |  External PSP APIs (e.g.     |                            |       NPCI API           |
       |  PhonePe, Paytm, Razorpay)  |                            | (mTLS, Signature, Replay) |
       +------------------------------+                            +---------------------------+

                          +----------------------------------+
                          |       Kafka Event Bus (MSK)      |
                          +---------+-----------+------------+
                                    |           |
                      +-------------v--+     +--v-----------------+
                      | Audit & Logs   |     | WebSocket Push Svc |
                      | (S3 + Athena)  |     +---------------------+
                      +----------------+

                          +----------------------------------+
                          |       Redis (Limits, Flags)      |
                          +----------------------------------+
```

### Design Trade-offs

| Trade-off                      | Justification                                                                                              |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| **Kafka vs SQS**               | Kafka chosen for partitioned ordering, replay, and high throughput for UPI audit and analytics workflows.  |
| **PostgreSQL vs DynamoDB**     | Strong consistency and complex joins in transactional flow favor PostgreSQL for ledger and account tables. |
| **WebSockets vs Long Polling** | Real-time feedback for UPI payments using WebSockets; fallbacks with polling for high-latency clients.     |
| **EKS vs Lambda**              | EKS preferred for orchestrated flows with long-lived transactions, retries, and control.                   |
| **API Gateway + ALB mix**      | API Gateway for public traffic and IAM auth, ALB for internal routing with WebSocket and service mesh.     |


## Low-Level Design (LLD)

### Service Decomposition & Responsibilities

1. **TransactionService**

   * Validates idempotency token.
   * Persists request to PostgreSQL with PENDING status.
   * Emits `TransactionInitiated` event to Kafka.

2. **TransactionOrchestrator**

   * Listens to Kafka `TransactionInitiated`.
   * Routes to appropriate PSPAdapter.
   * Manages state machine and retries.
   * Publishes `TransactionDebited`, `TransactionFailed`, etc.

3. **PSPAdapterService**

   * Each PSP has an adapter implementing `IPspAdapter` interface.
   * Normalizes PSP-specific APIs.
   * Adds resilience: Polly circuit breaker, retries, logging.

4. **NPCIProxyService**

   * Common interface for communicating with NPCI.
   * Handles:

     * mTLS with client certificates.
     * Nonce/timestamp generation.
     * HMAC signature encoding/validation.

5. **NotificationService**

   * Subscribes to `TransactionStatus` Kafka topic.
   * Uses SignalR/WebSocket push to update frontend.

6. **AuditLogService**

   * Stores every domain event.
   * Writes to S3 in partitioned format: `/txn/YYYY/MM/DD/txnId.json`

### API Contracts with C# Samples

#### `POST /v1/transactions/send`

```json
Request:
{
  "fromVPA": "alice@upi",
  "toVPA": "bob@upi",
  "amount": 150.75,
  "txnNote": "Subscription",
  "deviceId": "e7f7-22cb-47aa",
  "idempotencyKey": "TXN2024071901"
}

Response:
{
  "txnId": "TXN2024071901",
  "status": "PENDING",
  "pollUri": "/v1/transactions/TXN2024071901"
}
```

#### C# DTOs

```csharp
public class UpiTransactionRequest {
    public string FromVPA { get; set; }
    public string ToVPA { get; set; }
    public decimal Amount { get; set; }
    public string TxnNote { get; set; }
    public string DeviceId { get; set; }
    public string IdempotencyKey { get; set; }
}

public class UpiTransactionResponse {
    public string TxnId { get; set; }
    public string Status { get; set; }
    public string PollUri { get; set; }
}
```

#### Controller

```csharp
[HttpPost("/v1/transactions/send")]
public async Task<IActionResult> Send([FromBody] UpiTransactionRequest req) {
    if (await _store.IsDuplicateAsync(req.IdempotencyKey))
        return Conflict("Duplicate Transaction");

    var txnId = await _txnService.CreateTransactionAsync(req);
    return Ok(new UpiTransactionResponse {
        TxnId = txnId,
        Status = "PENDING",
        PollUri = $"/v1/transactions/{txnId}"
    });
}
```

### Internal Workflow Diagram (Text)

```
Client
  |
  |-- POST /v1/transactions/send --> [TransactionService]
  |                                     |
  |                                     |-- Insert PENDING txn in PostgreSQL
  |                                     |-- Emit TransactionInitiated to Kafka
  |
Kafka --[TransactionInitiated]--> [TransactionOrchestrator]
                                     |
                                     |-- Resolve PSP Adapter
                                     |-- Call PSPAdapter.SendRequest()
                                     |-- Success --> Emit TransactionDebited
                                     |-- Failure --> Retry or emit TransactionFailed

Kafka --[TransactionStatus]--> [NotificationService]
                                 |
                                 |-- Push to frontend via WebSocket

Kafka --[All Events]--> [AuditLogService] --> S3 (Daily folders)
```

### Retry, Error Handling, Consistency

| Concern             | Implementation                                                              |
| ------------------- | --------------------------------------------------------------------------- |
| **Retry**           | Polly + exponential backoff (2s, 4s, 8s) with jitter                        |
| **Circuit Breaker** | Triggers after 3 failures, 10s cool-down, fallback to alternate PSP         |
| **Outbox Pattern**  | Transaction table + OutboxEvent table → relayed by KafkaConnector           |
| **Idempotency**     | `IdempotencyKey` stored in DB with unique constraint                        |
| **Compensation**    | Failed transactions pushed to `txn-deadletter` topic, monitored by ops tool |

## Domain-Driven Design (DDD)

### Strategic Design – Bounded Contexts

| Context Name            | Responsibilities                                                       |
| ----------------------- | ---------------------------------------------------------------------- |
| `UPITransactionContext` | Manages end-to-end fund transfer, collect, refund workflows            |
| `PSPIntegrationContext` | Manages PSP APIs, retry logic, circuit breakers, fallback handling     |
| `NPCIContext`           | Interacts with NPCI gateway, signature validation, status polling      |
| `BankMetadataContext`   | Bank limits, cutoff times, holiday calendars, cached in Redis/DynamoDB |
| `NotificationContext`   | Handles push via WebSocket, SMS, email, and mobile notifications       |

* **Ubiquitous Language**: Every context uses domain-specific language. E.g., `CollectRequest`, `TxnRetryPolicy`, `UpiVPA`, `DebitedState`.
* **Integration Patterns**:

  * Contexts communicate via Kafka events (event-driven integration).
  * REST for read-side aggregations or dashboard APIs.

### Tactical Design

#### Aggregate: `UPITransaction`

```csharp
public class UPITransaction {
    public Guid Id { get; private set; }
    public string FromVPA { get; private set; }
    public string ToVPA { get; private set; }
    public decimal Amount { get; private set; }
    public UPIStatus Status { get; private set; }
    public List<DomainEvent> Events { get; private set; }

    public void Initiate() {
        if (Status != UPIStatus.NotStarted)
            throw new InvalidOperationException("Transaction already initiated");
        Status = UPIStatus.Pending;
        Events.Add(new TransactionInitiatedEvent(this));
    }

    public void Complete() {
        Status = UPIStatus.Completed;
        Events.Add(new TransactionCompletedEvent(this));
    }
}
```

#### Repository: `ITransactionRepository`

```csharp
public interface ITransactionRepository {
    Task<UPITransaction> GetAsync(Guid txnId);
    Task SaveAsync(UPITransaction txn);
}
```

#### Domain Events

```csharp
public class TransactionInitiatedEvent : DomainEvent {
    public UPITransaction Transaction { get; set; }
    public TransactionInitiatedEvent(UPITransaction txn) => Transaction = txn;
}

public class TransactionCompletedEvent : DomainEvent {
    public UPITransaction Transaction { get; set; }
    public TransactionCompletedEvent(UPITransaction txn) => Transaction = txn;
}
```

#### Event Dispatcher (Outbox pattern)

```csharp
public class DomainEventDispatcher {
    public async Task DispatchEventsAsync(UPITransaction txn) {
        foreach (var evt in txn.Events) {
            await _kafkaProducer.ProduceAsync(evt);
        }
        txn.ClearEvents();
    }
}
```

### Mapping to Services

| Service              | Aggregate Used            | Key Domain Events                              |
| -------------------- | ------------------------- | ---------------------------------------------- |
| `TransactionService` | `UPITransaction`          | `TransactionInitiated`, `TransactionCompleted` |
| `PSPAdapterService`  | `PSPTransaction`          | `PspTimeout`, `PspSuccess`, `PspFallbackUsed`  |
| `NPCIProxyService`   | `NpciTransaction`         | `NpciRequestSent`, `NpciStatusReceived`        |
| `BankConfigService`  | Stateless (Value Objects) | `LimitsUpdated`, `ConfigInvalidated`           |


## Design Patterns with C\#

### 1. Factory Pattern – PSP Adapter Instantiation

```csharp
public class PSPFactory {
    public static IPspAdapter Create(string pspName) => pspName switch {
        "Razorpay" => new RazorpayAdapter(),
        "PhonePe" => new PhonePeAdapter(),
        _ => throw new NotSupportedException("Unknown PSP")
    };
}
```

### 2. Strategy Pattern – PSP Retry Policies

```csharp
public interface IRetryStrategy { Task<bool> ExecuteAsync(Func<Task> action); }
public class ExponentialRetryStrategy : IRetryStrategy {
    public async Task<bool> ExecuteAsync(Func<Task> action) {
        for (int i = 1; i <= 3; i++) {
            try { await action(); return true; } 
            catch { await Task.Delay(i * 1000); }
        }
        return false;
    }
}
```

### 3. Circuit Breaker – Resilience to PSP Failure

```csharp
Policy.Handle<HttpRequestException>()
      .CircuitBreakerAsync(2, TimeSpan.FromMinutes(1));
```

### 4. Outbox Pattern – Eventual Consistency

```csharp
public class OutboxEvent {
    public Guid Id { get; set; }
    public string AggregateId { get; set; }
    public string EventType { get; set; }
    public string Payload { get; set; }
    public DateTime CreatedAt { get; set; }
    public bool Published { get; set; }
}
```

### 5. Saga Pattern – Transaction Compensation

* Split debit and credit flows into multiple services.
* Rollback via `TxnReversalRequested` if credit fails.

### 6. Singleton Pattern – Kafka Producers

```csharp
public class KafkaProducerSingleton {
    private static readonly Lazy<IProducer<string, string>> _instance =
        new(() => new ProducerBuilder<string, string>(config).Build());
    public static IProducer<string, string> Instance => _instance.Value;
}
```

## Database Design

### Hybrid Model: SQL (Aurora PostgreSQL) + NoSQL (DynamoDB)

| Data Type                     | Storage                 | Justification                                                                |
| ----------------------------- | ----------------------- | ---------------------------------------------------------------------------- |
| UPI Transactions, audit trail | Aurora PostgreSQL       | Strong consistency, transactional guarantees, ACID compliance                |
| Bank configs, PSP limits      | DynamoDB (NoSQL)        | High-speed read, low latency, partitionable, eventual consistency acceptable |
| Cached per-user limits        | Redis (in front of DDB) | In-memory cache for rate limiting and feature flags                          |

### CAP Theorem Justification

| Data        | Storage    | CAP Alignment | Rationale                                                 |
| ----------- | ---------- | ------------- | --------------------------------------------------------- |
| Txn         | PostgreSQL | **CA**        | Must be consistent and available (RDBMS, no partitioning) |
| PSP Configs | DynamoDB   | **AP**        | Must stay available even if slightly stale                |

### Core Schema: PostgreSQL

#### `transactions` Table

```sql
CREATE TABLE transactions (
    txn_id UUID PRIMARY KEY,
    from_vpa VARCHAR(255) NOT NULL,
    to_vpa VARCHAR(255) NOT NULL,
    amount NUMERIC(12, 2) NOT NULL,
    status VARCHAR(30) NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ
);

CREATE INDEX idx_txn_status_created_at ON transactions (status, created_at DESC);
```

#### `outbox_events` Table

```sql
CREATE TABLE outbox_events (
    event_id UUID PRIMARY KEY,
    aggregate_id UUID NOT NULL,
    event_type VARCHAR(50) NOT NULL,
    payload JSONB NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    published BOOLEAN DEFAULT FALSE
);
```

### C# Entity Example

```csharp
public class Transaction {
    [Key] public Guid TxnId { get; set; }
    [Required] public string FromVPA { get; set; }
    [Required] public string ToVPA { get; set; }
    [Required] public decimal Amount { get; set; }
    [Required] public string Status { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime? UpdatedAt { get; set; }
}
```

### DynamoDB Schema (Bank Configs)

* **PK**: `bank_code#ICICI`
* **SK**: `config#v1`
* **Attributes**: `limits`, `timeout_policy`, `cutoff_times`

#### Read Path:

```csharp
var response = await ddbClient.GetItemAsync(new GetItemRequest {
    TableName = "BankConfigs",
    Key = new Dictionary<string, AttributeValue> {
        { "PK", new AttributeValue { S = "bank_code#ICICI" } },
        { "SK", new AttributeValue { S = "config#v1" } }
    }
});
```

### Sharding Strategy

* PostgreSQL:

  * Partitioned tables by `created_at` (monthly).
  * Read replicas for read-intensive endpoints.
* DynamoDB:

  * Provisioned throughput with partition key design for hot key avoidance.
  * Use GSIs for reverse lookups (e.g., limit history).

### Indexing

* PostgreSQL:

  * Composite index on `(status, created_at)` for dashboard and reconciliation.
  * B-tree on `txn_id`, `from_vpa`, `to_vpa`.
* DynamoDB:

  * GSI on `bank_code`, `limits_updated_at`.

### Backup and Recovery

| Storage Type | Backup Strategy                                                       |
| ------------ | --------------------------------------------------------------------- |
| PostgreSQL   | Aurora automated backups (7-day PITR), manual snapshots via Terraform |
| DynamoDB     | Point-in-time recovery (PITR), daily AWS Backup snapshots             |
| Redis        | No backups — data sourced from DDB — cold warmers restore on boot     |

### Read/Write Optimization

* Write Path:

  * Transaction insert + OutboxEvent write in a single transaction.
  * Batching enabled for Kafka connectors.

## Messaging & Event-Driven Design

### Kafka Topics & Design Principles

| Topic Name                  | Key     | Purpose                                     | Partitions |
| --------------------------- | ------- | ------------------------------------------- | ---------- |
| `upi.transaction.initiated` | `txnId` | Transaction created; triggers orchestration | 30         |
| `upi.transaction.debited`   | `txnId` | Debit confirmed from payer                  | 30         |
| `upi.transaction.completed` | `txnId` | Final status (success/fail/timeout)         | 30         |
| `upi.audit.log`             | `txnId` | Full audit trail of domain events           | 10         |
| `upi.notification.status`   | `txnId` | For WebSocket-based push to clients         | 15         |
| `upi.transaction.dlq`       | `txnId` | Failed events for dead-letter processing    | 5          |

### Schema Evolution with Avro & Schema Registry

* Use **Apache Avro** for serialization for schema enforcement.
* Employ **Confluent Schema Registry** (or AWS Glue Schema Registry) to manage schema evolution.
* **Forward and backward compatibility** required.
* Example schema:

```json
{
  "type": "record",
  "name": "TransactionInitiated",
  "namespace": "com.avalara.upi",
  "fields": [
    { "name": "txnId", "type": "string" },
    { "name": "amount", "type": "double" },
    { "name": "fromVPA", "type": "string" },
    { "name": "toVPA", "type": "string" },
    { "name": "timestamp", "type": "long" }
  ]
}
```

### Producer Logic in C\#

```csharp
var config = new ProducerConfig { BootstrapServers = "broker:9092" };
using var producer = new ProducerBuilder<string, TransactionInitiated>(config)
    .SetValueSerializer(new AvroSerializer<TransactionInitiated>(schemaRegistry))
    .Build();

await producer.ProduceAsync("upi.transaction.initiated",
    new Message<string, TransactionInitiated> {
        Key = txn.Id,
        Value = txn
    });
```

### Consumer with Idempotency

```csharp
public class TransactionConsumer : IHostedService {
    public async Task StartAsync(CancellationToken cancellationToken) {
        var consumer = new ConsumerBuilder<string, TransactionInitiated>(config).Build();
        consumer.Subscribe("upi.transaction.initiated");

        while (!cancellationToken.IsCancellationRequested) {
            var msg = consumer.Consume();
            if (!_repository.IsProcessed(msg.Message.Key)) {
                _repository.MarkProcessed(msg.Message.Key);
                await _orchestrator.HandleAsync(msg.Message.Value);
            }
        }
    }
}
```

### DLQ Strategy

* Each critical topic (e.g., `transaction.completed`) has a DLQ (`transaction.dlq`).
* Failed consumers publish original message + exception to DLQ.
* DLQ messages are retained for 7 days with exponential retry.
* Metrics exported:

  * `DLQMessageCount`
  * `AverageRetryDuration`

### Retry Mechanism

| Strategy              | Description                                                           |
| --------------------- | --------------------------------------------------------------------- |
| **Short Retry**       | Retry immediately (3 attempts max) with exponential backoff via Polly |
| **Long Retry**        | Push to `upi.transaction.retry.delayed` topic; consumed with delay    |
| **Dead Letter Queue** | After final failure, message goes to DLQ and alert is triggered       |

### Task Offloading and Audit Logging Use Cases

* **Async Workflows**:

  * Reconciliation pipelines
  * Transaction timeout management
  * Bank response polling

* **Audit Logs**:

  * All domain events (initiated, completed, failed) written to S3 via Kafka consumer.
  * Partitioning: `s3://upi-audit-logs/YYYY/MM/DD/txnId.json`

## Cloud-Native Architecture (AWS Preferred)

### Compute Services

| Component        | AWS Service         | Justification                                                                |
| ---------------- | ------------------- | ---------------------------------------------------------------------------- |
| Core APIs        | EKS (Fargate + EC2) | Containerized workloads; mix of EC2 for baseline and Fargate for spikes      |
| Async Tasks      | AWS Lambda          | Lightweight stateless workers for callbacks, audit pushes, cold transactions |
| PSP Integrations | ECS Fargate         | Isolated compute for PSP retry engines and SLA-bound orchestrators           |

### Storage & Caching

| Use Case                    | Service             | Details                                                             |
| --------------------------- | ------------------- | ------------------------------------------------------------------- |
| Transaction DB              | Aurora PostgreSQL   | Multi-AZ, Global DB for HA, reader endpoints for analytics          |
| Bank config/cache           | DynamoDB + DAX      | Low-latency config lookups with in-memory cache                     |
| Temporary request/session   | Redis (Elasticache) | TTL-based caching, multi-AZ cluster mode, secured via VPC endpoints |
| Document logs / audit trail | S3 + Glacier        | Partitioned audit logs, lifecycle to Glacier after 30 days          |

### Messaging

| Purpose             | Service     | Implementation                                                               |
| ------------------- | ----------- | ---------------------------------------------------------------------------- |
| Main Event Bus      | MSK (Kafka) | Brokered event-driven flow for transaction states, audit logs, notifications |
| Retry / DLQs        | SQS         | Dead-letter queues for retry logic and fallback buffers                      |
| Notification fanout | EventBridge | Invokes webhook handlers, retry policies, circuit breakers                   |

### Networking & Security

| Concern             | Service                            | Details                                                                                 |
| ------------------- | ---------------------------------- | --------------------------------------------------------------------------------------- |
| Public access       | API Gateway + ALB                  | API Gateway for REST (JWT, IAM), ALB for gRPC/WebSockets                                |
| Private networking  | VPC + Private Subnet               | All services are in isolated subnets with NAT/GW-controlled egress                      |
| Identity & Secrets  | Cognito, IAM, KMS, Secrets Manager | Fine-grained IAM roles for services, KMS envelope encryption, automatic secret rotation |
| DDoS & Web Security | WAF + Shield                       | Rate limiting, IP blacklisting, bot mitigation                                          |

### Infrastructure as Code (IaC)

| Use Case        | Tool             | Details                                                                                    |
| --------------- | ---------------- | ------------------------------------------------------------------------------------------ |
| Cloud Resources | Terraform        | Modular structure with environments (dev/stage/prod) split by workspaces                   |
| Lambda & Config | AWS CDK (.NET)   | Programmatic IaC for Lambda, SNS, SQS, and API Gateway                                     |
| EKS Management  | Helm + Terraform | Helm charts for service deployments; EKS cluster via Terraform with node groups autoscaled |
| GitOps          | ArgoCD           | Declarative K8s manifests with sync policies, rollback support, health checks              |

### Sample Terraform Snippet (EKS Module)

```hcl
module "eks" {
  source = "terraform-aws-modules/eks/aws"
  cluster_name = "upi-eks-cluster"
  subnets = module.vpc.private_subnets
  node_groups = {
    app_nodes = {
      desired_capacity = 3
      instance_type = "t3.medium"
      capacity_type = "ON_DEMAND"
    }
    batch_nodes = {
      desired_capacity = 5
      instance_type = "t3.large"
      capacity_type = "SPOT"
    }
  }
}
```

### Example CDK (Lambda)

```csharp
var lambda = new Function(this, "TxnStatusLambda", new FunctionProps {
    Runtime = Runtime.DOTNET_6,
    Code = Code.FromAsset("../src/TxnStatusLambda/bin/Release/net6.0"),
    Handler = "TxnStatusLambda::Handler::FunctionHandler",
    MemorySize = 512,
    Timeout = Duration.Seconds(10)
});
```

## Observability & Monitoring

### Metrics Collection

| Metric Type      | Example                                   | Collection Mechanism               |
| ---------------- | ----------------------------------------- | ---------------------------------- |
| Business Metrics | `UPI.TransactionSuccessRate`              | Custom CloudWatch metrics via SDK  |
| System Metrics   | `CPUUtilization`, `MemoryUsage`           | CloudWatch Agent on EC2/EKS nodes  |
| Kafka Metrics    | `consumer_lag`, `records_in/sec`          | JMX Exporter + Prometheus scraping |
| DB Metrics       | `AuroraCPUUtilization`, `ConnectionCount` | CloudWatch + Enhanced Monitoring   |

### Tracing: Distributed Tracing with OpenTelemetry

| Component         | Integration Method                                    |
| ----------------- | ----------------------------------------------------- |
| ASP.NET Core APIs | OpenTelemetry SDK + OTLP Exporter to AWS X-Ray        |
| Kafka Consumers   | Trace context propagation via headers (B3/W3C)        |
| Lambdas           | Auto-instrumentation via AWS Distro for OpenTelemetry |

#### C# OpenTelemetry Sample (ASP.NET Core)

```csharp
services.AddOpenTelemetryTracing(builder =>
{
    builder
        .AddAspNetCoreInstrumentation()
        .AddHttpClientInstrumentation()
        .AddSource("TransactionService")
        .SetSampler(new AlwaysOnSampler())
        .AddOtlpExporter(otlp =>
        {
            otlp.Endpoint = new Uri("https://otel-collector.avalara.local:4317");
        });
});
```

### Centralized Logging

| Log Source       | Format            | Aggregation & Querying           |
| ---------------- | ----------------- | -------------------------------- |
| API Services     | JSON (Serilog)    | FluentBit → CloudWatch Logs      |
| Lambda Functions | Native log groups | CloudWatch Log Insights          |
| Kafka Connectors | Filebeat + ECS    | Shipped to OpenSearch            |
| Audit Logs       | Structured JSON   | Persisted in S3 (Athena-enabled) |

### Dashboards & Visualization

| Tool       | Purpose                                  | Example Panels                              |
| ---------- | ---------------------------------------- | ------------------------------------------- |
| Grafana    | Unified dashboard from Prometheus & Loki | TPS, P99 latency, PSP status, circuit trips |
| CloudWatch | System metrics (CPU, Mem, Lambda)        | EKS node health, queue depth, SNS retries   |
| Athena     | S3-based log queries                     | Audit trail by txnId, VPA, status           |

### Alerting & SLIs/SLOs

| Alert Type            | Criteria                       | Destination                |
| --------------------- | ------------------------------ | -------------------------- |
| Latency SLO Violation | P99 latency > 500ms for 5 min  | PagerDuty + Slack          |
| Kafka Consumer Lag    | Lag > 1000 for 10 min          | SNS → Lambda alert handler |
| PSP Failure Rate      | Failure rate > 2% over 15 min  | Opsgenie + Jira ticket     |
| DLQ Spike             | DLQ count increase > threshold | CloudWatch Alarm + Email   |

### Synthetic & Real User Monitoring

* Synthetic probes for `/health`, `/readiness` using Route53 health checks.
* RUM tools like AWS CloudWatch RUM or Datadog for frontend error tracking.
* Canary UPI requests in prod using special VPA pairs and synthetic users.

## Testing Strategy & CI/CD Pipeline

### Layered Testing Strategy

| Layer                | Tooling / Frameworks                         | Purpose                                                            |
| -------------------- | -------------------------------------------- | ------------------------------------------------------------------ |
| **Unit Tests**       | xUnit, NUnit, Moq                            | Verify isolated business logic, service methods                    |
| **Integration**      | Testcontainers + PostgreSQL + DynamoDB Local | Validate service-to-DB + Redis interactions                        |
| **Contract Testing** | Pact (Consumer/Provider)                     | Validate API and Kafka event schemas between services              |
| **Load Testing**     | k6, JMeter                                   | Simulate 50K+ TPS UPI transactions; stress test PSP/NPCI endpoints |
| **Security Testing** | OWASP ZAP, Snyk, Trivy, Checkmarx            | Identify OWASP Top 10 issues, dependency vulnerabilities           |
| **E2E Tests**        | Playwright, Cypress, REST-assured            | Full UPI transaction simulation; verify collect/pay/refund paths   |

#### Example Moq-based Unit Test (C#)

```csharp
var mockRepo = new Mock<ITransactionRepository>();
mockRepo.Setup(r => r.Save(It.IsAny<Transaction>())).ReturnsAsync(true);

var service = new TransactionService(mockRepo.Object);
var result = await service.CreateAsync(new TransactionRequest { ... });
Assert.Equal("PENDING", result.Status);
```

#### Pact Contract Test (Consumer)

```javascript
const { Pact } = require('@pact-foundation/pact');
const provider = new Pact({ ... });
provider.setup().then(() => {
  provider.addInteraction({
    uponReceiving: 'a valid txn initiation',
    withRequest: { method: 'POST', path: '/v1/transactions/send', ... },
    willRespondWith: { status: 200, body: { txnId: 'txn-123', status: 'PENDING' } }
  });
});
```

#### k6 Load Test Example

```javascript
import http from 'k6/http';
import { check } from 'k6';

export let options = { vus: 500, duration: '2m' };

export default function () {
  const res = http.post('https://upi.avalara.com/v1/transactions/send', JSON.stringify({ ... }));
  check(res, { 'is status 200': (r) => r.status === 200 });
}
```

## CI/CD Pipeline

### Tools Used

| Stage     | Tool                       | Description                                                   |
| --------- | -------------------------- | ------------------------------------------------------------- |
| Build     | GitHub Actions, Jenkins    | Compile .NET services, run unit tests, publish artifacts      |
| Test      | GitHub Actions             | Pact tests, security scans, test container-based integrations |
| Deploy    | ArgoCD, Helm               | Sync K8s manifests to EKS; rollout health checks              |
| Promotion | Argo Rollouts, Manual Gate | Promote across environments; auto-rollback if SLO fails       |
| Security  | Trivy, Snyk, Checkmarx     | Static scanning on containers, open source libraries          |

### CI Workflow YAML (GitHub Actions)

```yaml
jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: '6.0.x'
    - run: dotnet restore
    - run: dotnet test --logger trx

  docker:
    needs: build-and-test
    steps:
    - name: Build and push Docker
      run: docker build -t upi-txn-service:latest .

  deploy:
    needs: docker
    uses: argoproj/argocd-action@v2
    with:
      command: sync
      app: upi-transaction-app
```

### Promotion & Rollback

| Strategy           | Detail                                                                                  |
| ------------------ | --------------------------------------------------------------------------------------- |
| **Blue/Green**     | Fully provisioned staging environment, switch over DNS after full validation            |
| **Canary**         | Argo Rollouts release 5%, 25%, 50%, 100%; monitored via Prometheus + alertmanager       |
| **Rollback**       | Automated rollback via Argo if health checks fail or error rate exceeds threshold       |
| **Shadow Traffic** | Use Envoy to duplicate traffic to new versions in background for observability analysis |

## Feature Flags & Experimentation

### Why Feature Flags Matter

* Enable controlled rollout of risky features.
* Support A/B testing and progressive exposure.
* Allow fast toggling during incidents without redeploying.
* Separate deploy from release.

### Frameworks & Tools

| Technology                  | Usage                                                                 |
| --------------------------- | --------------------------------------------------------------------- |
| Microsoft.FeatureManagement | Built-in .NET support for flags, time-based and context-based toggles |
| LaunchDarkly, Unleash       | Advanced SaaS solutions for experimentation, analytics                |
| GitOps-driven Flags         | Use ConfigMaps or Parameter Store for environment-based toggles       |

### Example: Microsoft.FeatureManagement in ASP.NET Core

**Install NuGet Package:**

```
dotnet add package Microsoft.FeatureManagement.AspNetCore
```

**appsettings.json**

```json
{
  "FeatureManagement": {
    "EnableNewPSPIntegration": true,
    "TxnRetryExperiment": {
      "EnabledFor": ["pilot_users"]
    }
  }
}
```

**Startup.cs**

```csharp
services.AddFeatureManagement();
```

**Controller Usage**

```csharp
public class PSPController : ControllerBase {
  private readonly IFeatureManager _features;
  public PSPController(IFeatureManager features) => _features = features;

  public async Task<IActionResult> Pay() {
    if (await _features.IsEnabledAsync("EnableNewPSPIntegration")) {
      return Ok("New PSP Path");
    }
    return Ok("Fallback PSP Path");
  }
}
```

### GitOps / ConfigMap Driven Flags

* Flags defined in Kubernetes `ConfigMap`, mounted into services.
* Read at startup or injected via DI abstraction.
* Separate flags per environment (e.g., `dev`, `stage`, `prod`).

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: feature-flags
  namespace: upi-app

data:
  EnableBetaBankFlow: "true"
  EnableNewRateLimitEngine: "false"
```

### Audit and Observability

* Every toggle state change logs an audit entry (`FeatureFlagChanged` event).
* Feature usage is tracked as a custom CloudWatch metric:

  * `FeatureToggle.Usage.EnableNewPSPIntegration = 1`
* Dashboards: toggle usage vs error correlation, opt-out trends.

### A/B Testing Strategy

* Routing layer assigns users to experiments using consistent hashing.
* Toggle value fetched per session/user.
* Result metrics (success rate, latency, errors) stored per variant.
* Analyzed in Athena/Grafana for conversion uplift.

## Data Migration & Schema Evolution

### Why It Matters

* UPI schema needs evolve: PSP APIs change, NPCI adds new status fields, business logic shifts.
* Downtime is not acceptable; migrations must be **online, backward-compatible, observable, and rollback-safe**.

### Migration Techniques

| Technique                     | Tool / Approach                | Use Case                                               |
| ----------------------------- | ------------------------------ | ------------------------------------------------------ |
| **Online Schema Change**      | Liquibase, Flyway, pt-osc      | Add columns, indexes, modify nullable fields           |
| **Blue/Green Schema Deploy**  | Dual write to old/new schemas  | Major schema overhaul without downtime                 |
| **Versioned Entities**        | C# side-by-side models         | API contract preservation across releases              |
| **Entity View Compatibility** | Postgres views for versioning  | Support legacy views using SELECT + JOIN abstraction   |
| **NoSQL Event Rehydration**   | Kafka replay, DynamoDB restore | Recompute read models (e.g., txn aggregates or limits) |

---

### Liquibase Best Practices

* Track all schema changes in versioned changelog files (`changelog-v1.xml`, `changelog-v2.xml`...)
* Annotate changesets with authorship and rollback SQL.
* Enforce CI checks: no destructive changes unless version-bumped.
* Store changelogs in Git + validate in pipeline.

**Example Liquibase Changeset:**

```xml
<changeSet id="20250719-add-txn-type" author="nshinde">
    <addColumn tableName="transactions">
        <column name="txn_type" type="VARCHAR(20)" defaultValue="SEND"/>
    </addColumn>
    <rollback>
        <dropColumn tableName="transactions" columnName="txn_type"/>
    </rollback>
</changeSet>
```

---

### C# Versioned Entity Handling

```csharp
public class V1Transaction {
    public Guid TxnId { get; set; }
    public string Status { get; set; }
}

public class V2Transaction : V1Transaction {
    public string TxnType { get; set; }
}

// Controller can deserialize based on API version header
```

### Blue/Green Schema Rollout (Safe Deployment)

| Step       | Action                                                       |
| ---------- | ------------------------------------------------------------ |
| **Step 1** | Deploy new schema (e.g., new table/column) with default/null |
| **Step 2** | Update services to write to both schemas (dual writes)       |
| **Step 3** | Start reading from new schema in shadow traffic              |
| **Step 4** | Flip read path in production with observability              |
| **Step 5** | Remove old schema once metrics stabilize                     |

### Event Rehydration (NoSQL Read Models)

* Used when denormalized views in DynamoDB need to be rebuilt.
* Replay Kafka topic `upi.transaction.completed` for last 30 days.
* Consumer rebuilds materialized state:

```csharp
foreach (var evt in eventStream) {
    var readModel = _mapper.Map<ReadModel>(evt);
    await _ddb.PutItemAsync(readModel);
}
```

## Multi-Tenancy & SaaS Readiness

### Why It Matters

* If building UPI as a platform (e.g., enabling third-party PSPs or banks), tenant isolation, metering, throttling, and tenant-aware access control become essential.

### Multi-Tenancy Models

| Strategy                  | Description                             | Pros                                           | Cons                                 |
| ------------------------- | --------------------------------------- | ---------------------------------------------- | ------------------------------------ |
| Shared DB + Discriminator | Single schema with `tenant_id` field    | Simple, cost-efficient, good for small tenants | Risk of data leakage via bad queries |
| Schema-per-Tenant         | Each tenant gets its own schema         | Strong isolation, independent indexing         | Complex migrations and operations    |
| DB-per-Tenant             | Separate RDS instance or Aurora cluster | Maximum isolation and throttling               | High cost, operational complexity    |

### C# Middleware for Tenant Resolution

```csharp
public class TenantMiddleware {
    private readonly RequestDelegate _next;
    public TenantMiddleware(RequestDelegate next) => _next = next;

    public async Task Invoke(HttpContext context, ITenantResolver resolver) {
        var tenantId = context.Request.Headers["X-Tenant-Id"].ToString();
        if (string.IsNullOrEmpty(tenantId)) {
            context.Response.StatusCode = 400;
            await context.Response.WriteAsync("Missing Tenant ID");
            return;
        }
        context.Items["TenantId"] = tenantId;
        await _next(context);
    }
}
```

### IAM & Policy Isolation per Tenant

* **Per-tenant IAM roles**:

  * `Role/TenantXYZ-Access-UPI`
  * Attach policies scoped to `dynamodb:Query` or `s3:PutObject` for tenant-owned resources.

* **Per-tenant S3 bucket prefixes**:

  * `s3://upi-audit-logs/TenantA/YYYY/MM/txnId.json`

* **Per-tenant CloudWatch metrics** with dimensions:

  * `tenantId: TenantA`, `metric: TransactionSuccess`

### Throttling & Quotas

| Metric                      | Enforced At            | Implementation                                             |
| --------------------------- | ---------------------- | ---------------------------------------------------------- |
| API Rate                    | API Gateway            | Usage plans per tenant                                     |
| Concurrent UPI Transactions | Redis Lua + RedisGears | Atomic token bucket per tenantId                           |
| Monthly Transaction Volume  | CloudWatch + Lambda    | Monitors metrics, disables tenants crossing limits via SNS |

### Billing & Metering

* **Cost Attribution**: Use `tenant_id` tags on CloudWatch metrics, S3 logs, and Kafka partitions.
* **Quotas Enforcement**: Lambda listener on CloudWatch `TransactionVolumeExceeded` events triggers PSP suspension.
* **Tenant Invoicing**: Athena queries over S3 logs partitioned by `tenant_id` + `txn_status`

### Observability Per Tenant

* Dashboards:

  * Grafana templated by `tenant_id`
  * UPI success rate, retries, and PSP fallback usage per tenant
* Alerts:

  * Error spike alerts scoped to `tenant_id`
  * Isolated tenant SLA monitoring (e.g., p95 < 300ms)

## Cost Optimization Strategies

### Compute Optimization

| Optimization                    | Description                                                           |
| ------------------------------- | --------------------------------------------------------------------- |
| **Graviton2/3 EC2 & EKS Nodes** | Use ARM-based compute for better price/performance                    |
| **Spot Instances**              | Non-critical workloads (e.g., batch recon) use EC2 spot capacity      |
| **Fargate Auto-Pausing**        | Lambda/Fargate services scale to zero after inactivity                |
| **Provisioned Concurrency**     | Used selectively for only latency-critical Lambdas                    |
| **Node Groups Tuning**          | Use separate node pools for CPU/GPU-intensive and burstable workloads |

### Storage Optimization

| Area                   | Action                                                                    |
| ---------------------- | ------------------------------------------------------------------------- |
| **S3**                 | Lifecycle policies: `STANDARD → GLACIER → DELETE` after 90/180/365 days   |
| **EBS**                | Use `gp3` volumes with performance tuning instead of `gp2`                |
| **Snapshots**          | Schedule cleanup for unused AMIs and old snapshots using AWS Backup       |
| **Athena Query Costs** | Compress S3 logs as GZIP + partition by tenant/date for scan minimization |

### Observability Spend

| Source                 | Optimization Strategy                                                     |
| ---------------------- | ------------------------------------------------------------------------- |
| **CloudWatch**         | Set high-resolution metrics only for production; aggregate others         |
| **Logs**               | Use retention filters, log level control (debug only on demand), compress |
| **X-Ray Traces**       | Enable 5–10% sampling for normal traffic; increase under error conditions |
| **Grafana Dashboards** | Alert only on threshold breaches; reduce cardinality of metric labels     |

### Networking and API Gateway

| Optimization     | Strategy                                                               |
| ---------------- | ---------------------------------------------------------------------- |
| API Gateway      | Prefer HTTP APIs over REST APIs (cheaper + faster startup)             |
| Data Transfer    | Use private VPC endpoints for S3, DynamoDB, reducing NAT gateway usage |
| ALB Idle Timeout | Reduce from 300s to 60s unless long polling needed                     |

### Tagging and Budgeting

* Use AWS Cost Allocation Tags:

  * `ServiceName`, `Environment`, `TenantId`, `CostCenter`
* Setup **Budgets with SNS alerts** for monthly spending thresholds.
* Track per-team, per-feature, per-tenant costs via Cost Explorer + Athena (S3 CUR).

### CI/CD Pipeline Optimization

| Area                 | Strategy                                                   |
| -------------------- | ---------------------------------------------------------- |
| Docker Builds        | Use multi-stage builds, minimal base images (e.g., Alpine) |
| Artifact Caching     | GitHub Actions and Jenkins layer caching                   |
| Test Environment TTL | Auto-destroy test envs after PR merge                      |
| Preview Deployments  | Ephemeral ArgoCD previews with TTL labels                  |

---

This concludes the full enterprise-ready system design covering all 18 sections.
Let me know if you'd like:

* An export-ready markdown/pdf compilation.
* Architecture diagrams or sequence charts.
* Tailored mock interview Q\&A for Principal Engineer roles.

## Versioning & Backward Compatibility

### API Versioning (REST)

* **URI-Based**: `/v1/transactions`, `/v2/transactions`
* **Header-Based**: `X-API-Version: 1.0`
* **Media Type**: `Accept: application/vnd.upi.v1+json`

#### ASP.NET Core Versioned Controller

```csharp
[ApiVersion("1.0")]
[Route("v{version:apiVersion}/transactions")]
public class TransactionV1Controller : ControllerBase { ... }
```

### Kafka Message Compatibility

* **Avro + Schema Registry**
* Enable **backward compatibility** in schema evolution.
* Use optional fields with defaults.

### Canary Testing for Version Changes

* Deploy new version to subset of nodes.
* Shadow Kafka consumers log delta.
* Compare metric deltas (latency, errors, retries) per version.


## References & Best Practices

### Official Docs & Frameworks

* AWS Well-Architected: [https://docs.aws.amazon.com/wellarchitected/latest/framework](https://docs.aws.amazon.com/wellarchitected/latest/framework)
* Polly Resilience: [https://github.com/App-vNext/Polly](https://github.com/App-vNext/Polly)
* Microsoft.FeatureManagement: [https://learn.microsoft.com/en-us/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core](https://learn.microsoft.com/en-us/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* Liquibase Migrations: [https://www.liquibase.org/](https://www.liquibase.org/)
* Pact Contract Testing: [https://docs.pact.io/](https://docs.pact.io/)
* OpenTelemetry for .NET: [https://opentelemetry.io/docs/instrumentation/net/](https://opentelemetry.io/docs/instrumentation/net/)
* Argo Rollouts: [https://argo-rollouts.readthedocs.io/en/stable/](https://argo-rollouts.readthedocs.io/en/stable/)
* Terraform EKS Module: [https://github.com/terraform-aws-modules/terraform-aws-eks](https://github.com/terraform-aws-modules/terraform-aws-eks)
* AWS CDK for .NET: [https://docs.aws.amazon.com/cdk/api/v2/dotnet/index.html](https://docs.aws.amazon.com/cdk/api/v2/dotnet/index.html)
* DDD in ASP.NET: [https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures](https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures)

### Best Practices

* **Event-Driven Architecture**: Loosely coupled services communicate via events for resilience and scalability. [Event-Driven Design – Microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/event-driven-architecture)
* **Feature Toggles**: Use toggles to decouple deploy from release and enable safe experimentation. [Feature Management – Microsoft Docs](https://learn.microsoft.com/en-us/azure/azure-app-configuration/overview-feature-management)
* **Tenant Cost Tracking**: Use resource tagging and AWS Cost Explorer for granular billing. [AWS Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html)
* **Observability with OpenTelemetry**: Standardize tracing and metrics across all services. [OpenTelemetry .NET Docs](https://opentelemetry.io/docs/instrumentation/net/)
* **Kafka DLQ Management**: Design DLQs per critical topic with retry and alerting. [Kafka Dead Letter Queues – Confluent](https://docs.confluent.io/platform/current/kafka-dlt/design.html)
* **Security with AWS IAM and KMS**: Enforce least-privilege policies and encrypt all sensitive data. [IAM Best Practices – AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
* **S3 Lifecycle Policies**: Set up tiered storage for cost efficiency. [S3 Lifecycle Rules](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-configuration-examples.html)
* **CI/CD Resilience**: Use GitOps, health checks, and progressive delivery. [Argo Rollouts Docs](https://argo-rollouts.readthedocs.io/en/stable/)



