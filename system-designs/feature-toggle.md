# Feature Toggle System Design

## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements

* **Actors**:

  * Admin (creates, updates toggles, defines rules, rollouts)
  * Application Services (consume toggles at runtime)
  * Feature Toggle System (evaluation engine, config store, rollout executor)

* **Core Features**:

  * Create, update, delete feature toggles
  * Enable/disable features globally or per user/group
  * Targeting: by user ID, region, version, roles, etc.
  * Gradual rollouts (percentile-based)
  * Toggle versioning and history
  * SDKs/REST APIs for toggle evaluation
  * Audit logs of changes

* **External Dependencies**:

  * Auth system (SSO/OAuth for admin)
  * Central config store (DB/Redis)
  * CDN/cache layer for low-latency reads

### 🔹 Non-Functional Requirements

| Category            | Requirements                                                    |
| ------------------- | --------------------------------------------------------------- |
| **Performance**     | SDK lookup latency < 5ms, > 10K toggle reads/sec                |
| **Scalability**     | Horizontally scalable toggle server & CDN                       |
| **Availability**    | 99.99% uptime via HA (Multi-AZ, failover)                       |
| **Reliability**     | Retry logic, fallback flags (cached defaults), circuit breakers |
| **Security**        | Role-based access, audit trails, encrypted toggle values (KMS)  |
| **Maintainability** | Modular design, versioned APIs, easy-to-extend targeting rules  |
| **Observability**   | Metrics: toggle hit/miss, cache hit ratio; Logging; Tracing     |
| **Compliance**      | GDPR: anonymized user data; SOC2: audit logs                    |

---

## 2. High-Level Design (HLD)

### 🔧 Major Components

* **Admin Portal** – Manage feature flags
* **Toggle API Service** – CRUD for toggles
* **Evaluation SDK / REST Gateway** – Evaluate toggles per context
* **Config Store** – PostgreSQL for metadata + Redis for fast reads
* **Rule Engine** – Evaluate targeting rules
* **Rollout Manager** – Handles gradual rollouts
* **Audit Logger** – Stores change history
* **Monitoring & Metrics** – Exposes metrics to Prometheus, CloudWatch

### 📊 Architecture Diagram (Text)

```
 [Admin UI] ──> [API Gateway] ──> [Toggle API Service] ──> [PostgreSQL]
                                                   │
                                                [Rule Engine]
                                                   │
                                           [Redis | CDN Cache]
                                                   ▲
   [Mobile App / Backend] ──> [SDK / REST] ────────┘
```

### 💡 Design Considerations

* **Scalability**: CDN + Redis for global fast reads; API services scale on K8s/ECS
* **Availability**: Multi-AZ Redis + RDS failover; fallback config on client
* **Performance**: Toggle resolution happens in < 5ms using precompiled rules
* **Fault Tolerance**: Local SDK fallback + circuit breaker
* **Redundancy**: Redis master-slave; PostgreSQL HA or Aurora

---

## 3. Low-Level Design (LLD)

### 📘 Modules

* **AdminService**: Toggle CRUD, ACL, rollout setup
* **EvaluationService**: Evaluates toggles per user context
* **RuleEngine**: Evaluates Boolean expressions
* **AuditService**: Logs changes and access
* **SDK**: Lightweight resolver with fallback support

### 🔌 API Definitions

#### `GET /v1/toggles/:toggleKey`

```json
Request: {
  "userId": "abc123",
  "appVersion": "1.2.3",
  "region": "IN"
}
Response: {
  "enabled": true,
  "variant": "blue"
}
```

#### `POST /v1/toggles`

```json
{
  "key": "new_checkout_ui",
  "default": false,
  "rules": [
    { "condition": "region == 'IN'", "value": true },
    { "condition": "userId in ['abc123']", "value": true }
  ],
  "rollout": {
    "type": "percentage",
    "value": 10
  }
}
```

### 🗄 DB Schema (PostgreSQL)

**Toggles Table**:

```sql
id (UUID) PK
key TEXT UNIQUE
default_value BOOLEAN
rules JSONB
rollout JSONB
version INT
created_by TEXT
updated_at TIMESTAMP
```

**Audit Logs**:

```sql
id, toggle_id, action, actor, timestamp
```

### 🔁 Retry, Timeouts, Errors

* Redis/DB retry with exponential backoff
* SDK uses fallback values if cache fails
* Timeout: 100ms for toggle resolution

---

## 4. Database Design

### 📌 SQL Justification (PostgreSQL)

* Strong **Consistency** (CAP)
* Joins for ACLs, rollouts, auditing
* Preferable for structured, relational toggle data

### 🔍 Optimizations

* **Indexing**: `key`, `updated_at`, `created_by`
* **Partitioning**: By tenant/project for multi-tenancy
* **Sharding**: Horizontally partition across toggle key ranges if needed
* **Backups**: RDS snapshot + WAL backups
* **Read Optimization**: Redis cache + CDN
* **Caching Strategy**:

  * Redis TTL = 1 min
  * Client SDK L1 cache with TTL and ETag checks

---

## 5. Architecture Patterns

* **Microservices**: Admin, Evaluation, Audit
* **Event-Driven**: Changes publish updates to Kafka/SNS for cache invalidation
* **Layered Architecture**: UI → API → Service → Repo → DB
* **Hexagonal**: SDKs as ports; toggle resolver is the domain
* **CQRS**: Write (Admin API) is separate from Read (SDK/Edge APIs)
* **Serverless** (optional): Admin API + Rollout Scheduler on Lambda

---

## 6. Design Patterns

| Pattern                    | Use Case                                                                |
| -------------------------- | ----------------------------------------------------------------------- |
| **Factory** (Creational)   | Create toggle evaluation contexts dynamically                           |
| **Strategy** (Behavioral)  | Evaluate toggles via different strategies (percentage, region, AB test) |
| **Observer** (Behavioral)  | Cache invalidation when toggles change                                  |
| **Builder** (Creational)   | Create complex rollout definitions                                      |
| **Decorator** (Structural) | Add logging or fallback to evaluation pipeline                          |

**Example**: Strategy pattern for `PercentageRolloutStrategy` vs `TargetGroupStrategy`.

---

## 7. Event-Driven Messaging & Kafka Integration

### 🔹 Use Cases

* Toggle changes published to Kafka
* Clients subscribe via WebSockets/SSE for live updates
* Audit logging
* CDC from PostgreSQL → Kafka → analytics

### 🧱 Design

* **Topics**: `toggle.updated`, `toggle.created`, `audit.log`
* **Partitions**: By toggle key hash
* **Consumer Groups**:

  * SDK update processors
  * Analytics
* **Delivery**: At-least-once
* **DLQ**: `toggle.failed.events`

### 🚰 Kafka Integration

* Kafka Connect for DB changelogs
* Kafka Streams for real-time toggle usage dashboards
* Schema registry with Avro payloads

---

## 8. Cloud Architecture & Services (AWS)

| Layer      | AWS Service                                          |
| ---------- | ---------------------------------------------------- |
| Compute    | ECS Fargate / Lambda (Admin, Rule Engine)            |
| Storage    | S3 (backups), EBS (Redis), RDS                       |
| Database   | Amazon Aurora PostgreSQL                             |
| Cache/CDN  | ElastiCache Redis + CloudFront                       |
| Networking | VPC, API Gateway, NLB                                |
| Messaging  | MSK (Kafka), SNS, SQS                                |
| Security   | IAM (RBAC), KMS (toggle encryption), Secrets Manager |
| IaC        | Terraform / CDK                                      |

---

## 9. Monitoring, Logging & Observability

* **Metrics**: Redis hit rate, toggle latency, rollout errors
* **Logs**: CloudWatch Logs, JSON structured
* **Tracing**: AWS X-Ray for evaluation path
* **Dashboards**: Grafana (Prometheus, CloudWatch)
* **Alerts**: CloudWatch Alarms, PagerDuty for errors

---

## 10. Testing Strategy

* **Unit Tests**: Rule engine, SDK fallback, toggle resolution
* **Integration Tests**: API → Redis → DB
* **Contract Tests**: Using Pact for SDK ↔ API
* **Load Testing**: k6 → Evaluate 50K toggles/sec
* **Security**: OWASP, input validation, RBAC tests
* **CI/CD**: GitHub Actions / GitLab CI with SonarQube, coverage gates

---

## 11. References & Best Practices

* [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
* Martin Fowler – [Feature Toggles](https://martinfowler.com/articles/feature-toggles.html)
* LaunchDarkly & Unleash OSS
* Tech Interview Handbook
* Grokking System Design
* Principles of Event-Driven Architecture
