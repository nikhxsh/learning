# 📘 System Design: URL Shortening Service (Bit.ly Clone)

## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements

**Actors:**

* Users (Anonymous or Authenticated)
* Admin (for monitoring/analytics)
* Redirect Service
* Analytics Processor

**Core Features:**

* Accept long URLs and generate a unique short alias
* Redirect from short alias to original URL
* Support optional custom aliases and expiry times
* Maintain metadata like creation time, click count, expiry
* Support QR code generation for URLs
* Analytics dashboard (click stats, geographical data, etc.)

**System Use Cases:**

1. `POST /shorten`: Accepts a long URL and returns a short one.
2. `GET /{shortCode}`: Redirects to the original long URL.
3. `GET /analytics/{shortCode}`: Returns click statistics.
4. Optional: `POST /custom`: Create user-defined alias.

**External Dependencies:**

* Email/Notification service (optional)
* CDN for QR codes or static page delivery
* Analytics/Observability Stack (e.g., ELK, CloudWatch)

---

### 🔹 Non-Functional Requirements

| Category            | Description                                          |
| ------------------- | ---------------------------------------------------- |
| **Performance**     | Sub-10ms redirection latency; 10K+ RPS throughput    |
| **Scalability**     | Horizontally scalable services, autoscaling          |
| **Availability**    | ≥99.99% uptime, global HA                            |
| **Reliability**     | Retry mechanisms, circuit breakers, idempotent APIs  |
| **Security**        | HTTPS, JWT/OAuth, KMS-encrypted secrets              |
| **Maintainability** | Modular services, well-defined contracts, IaC        |
| **Observability**   | Logs, metrics, traces; central monitoring stack      |
| **Compliance**      | GDPR-compliant URL retention & deletion, access logs |

---

## 2. High-Level Design (HLD)

### 🔹 Major Components

* **API Gateway / Load Balancer**
* **Shortening Service**
* **ID Generator**
* **Redirect Service**
* **Metadata Store (SQL/NoSQL)**
* **Analytics Processor**
* **Redis Cache**
* **Kafka (or SQS/SNS)**
* **S3 for logs and exports**
* **Admin UI (SPA)**
* **Monitoring Stack**

---

### 🔹 Text-Based Architecture Diagram

```
User
 │
▼
[API Gateway / ALB]
 │
├──► [Shortening Service] ──► [ID Generator] ──► [DB (Aurora/DynamoDB)]
│
├──► [Redirect Service] ──► [Redis Cache] ─► [DB]
│                              │
│                              └──► [Kafka] ──► [Analytics Processor] ──► [Analytics DB]
│
├──► [Admin UI] ──► [Analytics DB]
│
└──► [Monitoring / Tracing Stack]
```

---

### 🔹 Key Design Qualities

* **Scalability**: Stateless components, cache, sharding DBs
* **Fault Tolerance**: Retries, fallback from cache to DB
* **High Availability**: Region failover, DNS-based routing
* **Performance**: Redis lookup first, async analytics write
* **Redundancy**: Multi-AZ DB, replicated brokers, load-balanced services

---

## 3. Low-Level Design (LLD)

### 🔹 API Definitions (REST)

#### 🔸 Shorten URL

```http
POST /shorten
{
  "longUrl": "https://example.com/some/long/path",
  "customAlias": "myalias", // optional
  "expiry": "2025-12-31T00:00:00Z"
}
Response:
{
  "shortUrl": "https://sho.rt/myalias"
}
```

#### 🔸 Redirect URL

```http
GET /{shortCode}
→ 301/302 Redirect to long URL
```

#### 🔸 Analytics API

```http
GET /analytics/{shortCode}
Response:
{
  "clicks": 1204,
  "createdAt": "2024-06-01",
  "referrers": {"facebook.com": 124},
  "locations": {"IN": 300, "US": 500}
}
```

---

### 🔹 Database Schema (SQL Example)

```sql
CREATE TABLE urls (
  id BIGINT PRIMARY KEY,
  short_code VARCHAR(10) UNIQUE NOT NULL,
  long_url TEXT NOT NULL,
  user_id UUID,
  created_at TIMESTAMP,
  expiry TIMESTAMP,
  click_count INT DEFAULT 0
);

CREATE INDEX idx_short_code ON urls(short_code);
```

---

### 🔹 Protocols & Communication

* REST APIs over HTTPS
* Kafka (or SQS) for async messaging
* gRPC (optional) between internal services
* Redis for fast reads (LRU eviction, TTL)

---

### 🔹 Retry, Timeout & Error Handling

* Retry on failed Kafka events
* Circuit breaker for DB/network failures
* 60s expiry cache fallback
* Dead-letter queues for poisoned messages
* Timeouts: 5s for DB, 1s for cache

---

## 4. Database Design

### 🔹 SQL vs NoSQL Justification

| Use Case             | DB Type             | Reason                            |
| -------------------- | ------------------- | --------------------------------- |
| URL metadata         | SQL (Aurora)        | Strong consistency, transactional |
| Redirection (lookup) | NoSQL (DynamoDB)    | High throughput, low latency      |
| Click analytics      | Columnar (Redshift) | Aggregation queries               |

**CAP Theorem:**

* Redirect Service → AP (availability preferred)
* Analytics/Admin → CP (consistency preferred)

---

### 🔹 Schema Design

**NoSQL (DynamoDB Example):**

```json
{
  "short_code": "abc123",
  "long_url": "https://very-long-url.com",
  "expiry": "2025-12-31T00:00:00Z",
  "click_count": 1234
}
```

---

### 🔹 Optimization Techniques

* **Caching:** Redis for short\_code → long\_url mapping
* **Sharding:** User-based or region-based sharding for large tables
* **Replication:** Multi-AZ RDS replicas
* **Backups:** Automated snapshots + PITR
* **Indexes:** Short code, user ID
* **Write Optimization:** Append-only logs for analytics
* **Partitioning:** TTL-based archival (e.g., expired URLs)

---

## 5. Architecture Patterns

| Pattern           | Usage                                                   |
| ----------------- | ------------------------------------------------------- |
| **Microservices** | Shortening, Redirect, Analytics as independent services |
| **Event-Driven**  | Kafka decouples redirect flow from analytics            |
| **Layered**       | Controller → Service → Repository                       |
| **CQRS**          | Separate read and write logic for scale                 |
| **Hexagonal**     | Core shortening logic wrapped with adapters             |

---

## 6. Design Patterns

| Pattern                    | Use Case                                       |
| -------------------------- | ---------------------------------------------- |
| **Factory** (Creational)   | ID generation logic (random, hash, sequential) |
| **Singleton** (Creational) | Redis/Kafka connection pools                   |
| **Adapter** (Structural)   | Wrap storage engines (SQL/NoSQL)               |
| **Observer** (Behavioral)  | Emit events after redirection                  |
| **Strategy** (Behavioral)  | Pluggable ID generation algorithm              |

### 🔸 Example: Strategy Pattern

```java
interface IdGenerator {
  String generate(String url);
}

class RandomIdGenerator implements IdGenerator {...}
class HashIdGenerator implements IdGenerator {...}
class CustomAliasValidator implements IdGenerator {...}
```

---

## 7. Event-Driven Messaging & Kafka Integration

### 📌 Use Cases

* Log click events
* Audit trail
* Analytics processing
* Streaming reports (CTR, referrer trends)
* Resilient decoupling of redirector from analytics

---

### 🧱 Kafka Design

* **Topic:** `click_events`
* **Partitions:** Based on `short_code`
* **Consumer Groups:**

  * `analytics-service`
  * `real-time-dashboard`

**Message Flow:**

```
Redirect Service ──► Kafka ──► Analytics Processor ──► Redshift/S3
```

**Other Design Elements:**

* **DLQ:** Invalid messages
* **Retry:** Commit retry logic + backoff
* **Ordering:** Preserve order per short\_code
* **Idempotency:** Track event hashes or offsets

---

### 🛠 Integration Strategy

* **Schema Registry:** Avro schemas with versioning
* **Kafka Streams:** CTR aggregation pipelines
* **Kafka Connect:** DynamoDB stream → Kafka
* **Log Format:** JSON/Avro

---

## 8. Cloud Architecture & Services

| Concern                    | AWS Services                          |
| -------------------------- | ------------------------------------- |
| **Compute**                | ECS Fargate, Lambda, EKS              |
| **Storage**                | S3, EBS, Aurora, DynamoDB             |
| **Network**                | VPC, NLB/ALB, API Gateway, CloudFront |
| **Database**               | RDS/Aurora (SQL), DynamoDB (NoSQL)    |
| **Messaging**              | MSK (Kafka), SQS/SNS, EventBridge     |
| **Security**               | IAM, KMS, Cognito, Secrets Manager    |
| **Observability**          | CloudWatch, X-Ray, OpenTelemetry      |
| **Infrastructure as Code** | Terraform, CDK, CloudFormation        |

---

## 9. Monitoring, Logging & Observability

* **Metrics:** Response latency, QPS, cache hit rate
* **Logs:** Structured JSON logs → CloudWatch, ELK
* **Tracing:** X-Ray for end-to-end request path
* **Dashboards:** CloudWatch, Grafana, DataDog
* **Alerts:** Latency/error threshold → SNS → PagerDuty

---

## 10. Testing Strategy

| Layer                 | Tests                           |
| --------------------- | ------------------------------- |
| **Unit Tests**        | Encode/decode, alias validation |
| **Integration Tests** | API ↔ DB ↔ Kafka                |
| **Contract Tests**    | Pact for inter-service APIs     |
| **Load Tests**        | k6, Locust: 10K+ RPS            |
| **Security Tests**    | OWASP ZAP, Snyk                 |
| **CI/CD**             | GitHub Actions → CodePipeline   |
| **E2E Tests**         | Selenium/Cypress for UI         |

---

## 11. References & Best Practices

* [Tech Interview Handbook – System Design](https://www.techinterviewhandbook.org/system-design/)
* [Grokking the System Design Interview](https://designgurus.io/)
* [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
* [Martin Fowler – Architectural Principles](https://martinfowler.com/architecture/)
* [Bit.ly Engineering Blog](https://bitly.com/blog/)
* [Google Cloud & Azure Architecture Center](https://cloud.google.com/architecture)
