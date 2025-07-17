# 📘 Twitter-like Application – System Design (Principal/Architect Level)

This document presents a comprehensive system design for a Twitter-like application, structured to align with Principal/Architect level interviews. It includes detailed design sections from requirements to architecture, messaging, observability, and references.

---

## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements

**Core Features:**

* User signup/login (OAuth/social/email)
* Tweet: post/update/delete short messages
* Timeline feed (home feed & profile feed)
* Follow/unfollow users
* Like, retweet, reply to tweets
* Hashtags, mentions
* Notifications (likes, mentions, follows)
* Media attachments (images/videos)

**Actors:**

* End users
* Admin (moderation, abuse reports)
* Third-party clients (via APIs)

**External Dependencies:**

* Email/SMS providers
* CDN for media
* OAuth providers (Google, Apple, etc.)

---

### 🔹 Non-Functional Requirements

| Aspect              | Requirements                                 |
| ------------------- | -------------------------------------------- |
| **Performance**     | Feed latency < 200ms, tweet post < 500ms     |
| **Scalability**     | Handle 10M+ DAUs, horizontally scalable      |
| **Availability**    | 99.99% uptime, multi-AZ, failover support    |
| **Reliability**     | Message retries, replication, idempotent ops |
| **Security**        | OAuth2, JWT, encrypted PII/media, RBAC       |
| **Maintainability** | Modular codebase, microservices, CI/CD       |
| **Observability**   | Logs, metrics, distributed tracing           |
| **Compliance**      | GDPR/CCPA for data handling, audit logs      |

---

## 2. High-Level Design (HLD)

### 🧱 Components:

* **API Gateway**
* **Auth Service**
* **User Service**
* **Tweet Service**
* **Timeline Service**
* **Follow Graph Service**
* **Notification Service**
* **Media Service**
* **Search Service (ElasticSearch)**
* **Kafka/Event Bus**
* **Redis/Memcached**
* **PostgreSQL/Cassandra**
* **S3 (media), CDN (CloudFront)**

### 📀 Architecture Diagram (Text-Based)

```
[Client] → [API Gateway]
                 ↓
        ┌───────────────┬─────────────────┬─────────────────┐
        ↓             ↓                    ↓                    ↓
 [Auth Service] [Tweet Service]   [User Service]         [Media Service]
        ↓             ↓                    ↓                    ↓
   [JWT Tokens]   [Postgres/Cassandra] [User DB]           [S3 + CDN]
                                      ↓
                          [Follow Graph Service]
                                      ↓
                             [Timeline Service]
                                      ↓
                                 [Redis Cache]
                                      ↓
                             [Notification Service]
                                      ↓
                                  [Kafka Topics]
```

---

## 3. Low-Level Design (LLD)

### 🧹 Modules:

#### Tweet Service

* POST `/tweets`
* GET `/tweets/{id}`
* DELETE `/tweets/{id}`

#### Timeline Service

* GET `/timeline/home` → async fanout or pull from follow graph
* GET `/timeline/profile/{userId}`

#### Follow Service

* POST `/follow`, DELETE `/unfollow`
* DB stores follower & followee mappings

### 🔀 Communication:

* Sync: API Gateway → microservices (REST/gRPC)
* Async: Kafka → Timeline, Notification, Analytics

### 🔠 API Example: Tweet Service

```http
POST /tweets
Headers: Authorization: Bearer <token>
Body: {
  "text": "My first tweet!",
  "media": ["media_id_123"]
}
Response: {
  "tweetId": "t_987",
  "status": "created"
}
```

### 🧬 Database Schema (PostgreSQL or Cassandra)

#### `tweets` Table

```sql
CREATE TABLE tweets (
  tweet_id UUID PRIMARY KEY,
  user_id UUID,
  content TEXT,
  created_at TIMESTAMP,
  media_ids TEXT[],
  reply_to UUID,
  likes_count INT,
  retweet_count INT
);
```

### ♻ Retry/Error Handling

* HTTP: Retry with exponential backoff for 5xx
* Kafka: DLQ + retry topics
* Timeouts: 3s for inter-service REST/gRPC

---

## 4. Database Design

### 📌 Choice: **Cassandra** for Tweets, **PostgreSQL** for User/Auth

### 🔺 CAP Theorem Justification:

* **Tweets DB (Cassandra):** AP – prioritizes availability and partition tolerance
* **User DB (PostgreSQL):** CP – strong consistency for authentication/user integrity

### 🗂 Indexing:

* `user_id`, `created_at` for tweet fetch
* Inverted index for hashtags (ElasticSearch)

### 📦 Partitioning:

* Tweet shards by user\_id hash
* Cassandra partition keys to spread load

### 🔄 Replication & Backup:

* Multi-region Cassandra replicas
* Daily backups + WAL + TTL for tweets

### ⚡ Optimization:

* Redis cache for home/profile feeds
* Read replicas for PostgreSQL

---

## 5. Architecture Patterns

| Pattern           | Application                           |
| ----------------- | ------------------------------------- |
| **Microservices** | Each domain is an independent service |
| **Event-Driven**  | Kafka: feed fanout, notifications     |
| **CQRS**          | Write: Tweet DB, Read: Timeline cache |
| **Hexagonal**     | Adapters for DB, Kafka, APIs          |
| **Layered**       | REST API → Service → DAO layers       |
| **Serverless**    | Media thumbnail generation (Lambda)   |

---

## 6. Design Patterns

| Pattern             | Use Case                                     |
| ------------------- | -------------------------------------------- |
| **Factory**         | Auth tokens based on provider (Google/Apple) |
| **Builder**         | Tweet creation pipeline with optional media  |
| **Observer**        | Notification fanout on tweet/like            |
| **Strategy**        | Feed fanout: async vs on-read                |
| **Circuit Breaker** | External API fallbacks (email/media)         |

---

## 7. Event-Driven Messaging & Kafka

### 🧰 Topics

* `tweet-posted`, `tweet-liked`, `user-followed`, `media-uploaded`

### ↺ Flow

```
Tweet Service → [Kafka Topic: tweet-posted]
                         ↓
           [Timeline Service, Notification Service]
```

### ⚙️ Features

* **Partitions:** By `user_id`
* **Consumer groups:** Timeline, Notification
* **DLQ:** For retrying failed events
* **Schema Registry:** Avro schemas for validation

---

## 8. Cloud Architecture (AWS)

| Domain    | Services                                 |
| --------- | ---------------------------------------- |
| Compute   | EKS (microservices), Lambda (media jobs) |
| Storage   | S3 (media), EBS for DB volumes           |
| DBs       | RDS Postgres (User), MSK Kafka, DynamoDB |
| Network   | VPC, ALB, Route53                        |
| Messaging | MSK, SQS (notifications), EventBridge    |
| Security  | IAM, Secrets Manager, KMS                |
| IaC       | Terraform, Helm charts for EKS           |

---

## 9. Monitoring, Logging & Observability

* **Metrics:** Prometheus + Grafana (CPU, latency)
* **Logging:** FluentBit → CloudWatch Logs
* **Tracing:** OpenTelemetry → AWS X-Ray
* **Alerts:** CloudWatch Alarms, PagerDuty integration
* **Dashboards:** Grafana for API latency, Kafka lag

---

## 10. Testing Strategy

| Type             | Tool                                            |
| ---------------- | ----------------------------------------------- |
| **Unit Tests**   | xUnit/NUnit + Moq                               |
| **Integration**  | TestContainers for Kafka/Postgres               |
| **Contract**     | Pact for microservices                          |
| **Load Testing** | Locust, k6 for API and timeline                 |
| **Security**     | OWASP ZAP, BurpSuite                            |
| **CI/CD**        | GitHub Actions + quality gates + security scans |

---

## 11. References & Best Practices

* [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
* [Tech Interview Handbook – System Design](https://www.techinterviewhandbook.org/)
* [Grokking the System Design Interview](https://www.designgurus.io/product/system-design-interview-course)
* [Martin Fowler: Architectural Patterns](https://martinfowler.com/architecture/)
* [Twitter Engineering – Fan-out vs Pull](https://blog.twitter.com/engineering/en_us/topics/infrastructure/2013/new-twitter-search)
