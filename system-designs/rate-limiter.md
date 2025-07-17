
# System Design – Rate Limiter

## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements
- Enforce request limits (e.g., 1000 requests per user per hour).
- Support multiple algorithms: **Token Bucket**, **Leaky Bucket**.
- Granularity: **per user**, **per IP**, **per API key**, **per endpoint**.
- Configurable policies (global, regional, customer-tier).
- Real-time decisioning: **Allow / Reject / Queue**.
- Admin APIs for viewing/editing policies.
- Metrics: count of allowed, throttled, and dropped requests.

#### Actors & Interactions

| Actor           | Action                                 |
|----------------|-----------------------------------------|
| API Gateway     | Calls Rate Limiter before routing      |
| Service Clients | Send API requests                      |
| Admins          | Define throttling rules via dashboard  |

#### External Dependencies
- Redis / Memcached for counters
- Kafka / SQS (optional) for async logging
- Metrics system (Prometheus/Grafana)
- Persistent store (PostgreSQL/DynamoDB) for policy config

### 🔹 Non-Functional Requirements

| Attribute      | Description                                                                 |
|----------------|------------------------------------------------------------------------------|
| Performance    | < 5ms decision latency                                                      |
| Scalability    | Millions of RPS, use horizontal scaling, sharded counter keys               |
| Availability   | ≥ 99.99%, redundant nodes, fallback policies                                |
| Reliability    | Retry strategies, circuit breakers                                          |
| Security       | Auth tokens for config APIs, TLS, audit logs                                |
| Maintainability| Config driven, modular design, separation of concerns                       |
| Observability  | Logs, dashboards, traces, alarms                                            |
| Compliance     | GDPR-compliant user data handling, API access logging                      |

## 2. High-Level Design (HLD)

### 🧱 Major Components
- **Rate Limiter Service** – Enforces rate limits
- **Config Store** – Holds limit rules
- **Token Store** – High-speed store (Redis) for tokens
- **Admin API** – Rule management
- **Monitoring Layer** – Logs/metrics exporters

### 📊 Text-Based Architecture Diagram

```
                +-------------------+
                |  Admin Dashboard  |
                +--------+----------+
                         |
                         v
                +--------+----------+
                |   Admin Service   |
                +--------+----------+
                         |
                         v
                +--------+----------+
                |   Config Store    |
                +------------------+
                         ^
                         |
+-------------+    +-----+------+    +---------------------+
| API Gateway +--->| Rate Limiter|<--+ Redis Token Store   |
+-------------+    +-----+------+    +---------------------+
                         |
                         v
                +--------+----------+
                | Monitoring System |
                +------------------+
```

## 3. Low-Level Design (LLD)

### 🔌 API Definitions

#### Rate Check API (gRPC / REST)
```http
POST /checkRateLimit
{
  "userId": "abc123",
  "endpoint": "/v1/payments",
  "method": "POST"
}
→
{
  "allowed": true,
  "remaining": 57,
  "resetIn": 180
}
```

#### Admin API
```http
POST /limits
{
  "userTier": "free",
  "limit": 1000,
  "interval": "hour",
  "algorithm": "token_bucket"
}
```

### 💾 Database Schema (SQL)
```sql
Table: rate_limit_rules
- id (UUID)
- user_tier (VARCHAR)
- limit (INT)
- interval (VARCHAR)
- algorithm (VARCHAR)
- created_at
```

### 🔁 Internal Workflow
- Fetch policy for user from Config Store (cached).
- Retrieve counter/token from Redis.
- Apply algorithm logic.
- Store updated state with TTLs.

### ⏱ Retry & Timeout
- Redis: 100ms timeout, exponential backoff retry 3 times.
- Circuit breaker fallback: return “allow” after N failures.

## 4. Database Design

### Chosen DBs:
- **SQL** (PostgreSQL) for config rules
- **NoSQL (Redis)** for counters & TTLs

### 📚 CAP Theorem Justification
- **Redis (AP)** – Prioritize availability & partition tolerance.
- **PostgreSQL (CA)** – Strong consistency for rule config.

### Optimizations
- **Sharding** Redis keys by `userId % N`.
- **Indexes**: On user_tier, endpoint
- **Backup**: Snapshot config nightly
- **Replication**: Redis Sentinel or AWS ElastiCache Multi-AZ

## 5. Architecture Patterns

| Pattern         | Justification |
|----------------|---------------|
| Microservices   | Rate Limiter is isolated and stateless |
| Event-Driven    | Logs, telemetry via Kafka              |
| Layered         | Clear split between API, logic, store  |
| Serverless (optional) | AWS Lambda for config ingestion |

## 6. Design Patterns

| Pattern      | Use Case                             | Description                                                                 |
|--------------|--------------------------------------|-----------------------------------------------------------------------------|
| Singleton    | Redis connection pool                | Ensure one connection manager per instance                                 |
| Strategy     | Token Bucket vs Leaky Bucket         | Swap rate-limiting algorithms at runtime                                   |
| Factory      | Create limiter instances dynamically | Abstract creation of limiter types based on config                         |
| Chain of Responsibility | Multi-policy enforcement      | Apply org-wide, endpoint-specific, and user-level limits in sequence       |
| Observer     | Config refresh subscribers           | Dynamically update in-memory rules on change                               |

## 7. Event-Driven Messaging & Kafka Integration

### 📌 Use Cases
- Asynchronous audit logs
- Real-time analytics (per-user traffic patterns)
- DLQs for policy ingestion errors

### 🧱 Kafka Topics
- `rate_limiter.events`
- `rate_limiter.audit_logs`

```text
Producer → Kafka Broker → Consumer (Analytics Engine)

- Topics: 10 partitions
- Consumer Group: stream processors
- Format: Avro (schema registered)
```

### Handling
- **Ordering**: Partition by userId
- **Retries**: Exponential backoff
- **DLQ**: Send malformed logs to DLQ for triage

## 8. Cloud Architecture & Services

| Area        | AWS Service            |
|-------------|------------------------|
| Compute     | ECS Fargate / Lambda   |
| Storage     | S3 for logs            |
| DB          | Aurora (Postgres), ElastiCache Redis |
| Messaging   | MSK (Kafka), SQS       |
| Networking  | VPC, NLB/API Gateway   |
| Security    | IAM, KMS, SecretsMgr   |
| IaC         | Terraform               |

## 9. Monitoring, Logging & Observability

| Metric         | Tool                      |
|----------------|---------------------------|
| RPS, Throttled | Prometheus + Grafana      |
| Logs           | CloudWatch Logs / ELK     |
| Tracing        | AWS X-Ray / OpenTelemetry |
| Alerts         | CloudWatch Alarms, PagerDuty |

## 10. Testing Strategy

| Type              | Tools/Approach                     |
|-------------------|------------------------------------|
| Unit              | JUnit, xUnit, edge case checks     |
| Integration       | Localstack + TestContainers        |
| Contract          | Pact for policy APIs               |
| Load & Stress     | k6, Locust, simulate 10M RPS       |
| Security          | ZAP, Snyk, static scanning         |
| CI/CD             | GitHub Actions + SonarQube         |

## 11. References & Best Practices

- [Grokking Modern System Design](https://designgurus.io)
- AWS Well-Architected Framework
- Martin Fowler’s Event-Driven Architectures
- Redis Rate Limiting Recipes
- Kafka Streams & Schema Registry – Confluent.io
