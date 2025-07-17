# System Design: Instagram / Facebook-style Social Media Platform

Comprehensive, production-ready architecture for a large-scale social networking application — targeting Principal / Architect level interviews.

---

## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements

**Actors:**

* Users (logged-in, anonymous)
* Admins
* Background workers
* External CDNs & analytics services

**User Flows / Use Cases:**

* Signup/Login (Email, OAuth)
* View/post/edit/delete photo or video
* Like/comment/share posts
* Follow/unfollow users
* Feed timeline generation
* Real-time notifications (likes, comments, followers)
* Search (users, hashtags, posts)
* Messaging/chat (for FB-like apps)
* Admin moderation and analytics

**External Systems:**

* CDN (CloudFront)
* Notification Services (SNS, Firebase)
* Email/SMS Gateway
* Payment gateway (if monetization exists)
* External Auth (Google, FB)

---

### 🔹 Non-Functional Requirements

| **Property**        | **Requirement**                                                       |
| ------------------- | --------------------------------------------------------------------- |
| **Performance**     | <200ms for feed load, <50ms for likes/comments                        |
| **Scalability**     | Horizontal scaling (millions of users, posts/day), autoscaling groups |
| **Availability**    | 99.99% SLA, multi-AZ, disaster recovery                               |
| **Reliability**     | Idempotent APIs, circuit breakers, retries, fallbacks                 |
| **Security**        | OAuth2, JWT, HTTPS, data encryption (at rest/in transit), RBAC, KMS   |
| **Maintainability** | Microservices, CI/CD, IaC, schema versioning                          |
| **Observability**   | Metrics, logging, traces, health checks, alerting                     |
| **Compliance**      | GDPR, COPPA (if minors involved), audit trails                        |

---

## 2. High-Level Design (HLD)

### 🧹 Major Components

* **API Gateway**
* **Auth Service**
* **User Service**
* **Post Service**
* **Feed Service**
* **Like/Comment Service**
* **Notification Service**
* **Search Service**
* **Media Service**
* **Kafka/EventBridge**
* **Redis**
* **Databases**
* **CDN**
* **Analytics**

### 🖼 Architecture Diagram (Text-Based)

```
Clients → API Gateway → Microservices (Auth, User, Post, Feed, Comment)
                                ↓
                            Kafka/MSK → Notification, Analytics, Feed Fanout
                                ↓
                DBs (PostgreSQL, DynamoDB, Cassandra), Redis, Elasticsearch
                                ↓
                          S3 + CloudFront (Media Storage & Delivery)
```

---

## 3. Low-Level Design (LLD)

### 🔗 API Definitions (REST/JSON)

* `POST /users`
* `GET /feed`
* `POST /post`
* `POST /post/{id}/like`
* `POST /follow/{userId}`

### 🔢 DB Schema (Sample)

**Users**

```sql
id UUID PRIMARY KEY, name, username, email, password_hash, created_at
```

**Posts**

```sql
id UUID, user_id FK, image_url, caption, hashtags[], created_at
```

**Likes**

```sql
user_id, post_id, created_at (composite PK)
```

**Followers**

```sql
follower_id, followee_id, followed_at
```

### 🔁 Data Flow & Error Handling

* Timeout: 3s APIs, 1s cache, 5s DB
* Retry: 3x exponential backoff
* Circuit breakers
* Idempotency via hash/token

---

## 4. Database Design

### 📌 Polyglot Persistence

* PostgreSQL → User, Comments (CA in CAP)
* Cassandra → Feed (AP in CAP)
* DynamoDB → Sessions

### 📂 Schema Modeling

* Denormalized feed store
* B-tree indexes
* Sharding via user ID / tokens
* Redis caching for feeds/sessions

---

## 5. Architecture Patterns

* **Microservices**: Domain-based services
* **Event-Driven**: Kafka fanout/async
* **CQRS**: Separate feed read/write
* **Layered**: Presentation → Logic → Data
* **Hexagonal**: Ports & Adapters
* **Serverless**: Media/analytics with Lambda

---

## 6. Design Patterns

| Pattern         | Use Case                     | Component            |
| --------------- | ---------------------------- | -------------------- |
| Singleton       | Kafka/Redis client           | Feed Service         |
| Factory         | Media upload handling        | Media Service        |
| Strategy        | Feed generation algorithms   | Feed Service         |
| Observer        | Notify on posts              | Notification Service |
| Circuit Breaker | Fail fast                    | All services         |
| Adapter         | DB ↔ domain model            | Repository Layer     |
| Command         | Post/comment/like operations | API Handlers         |

---

## 7. Event-Driven Messaging & Kafka

### 🔄 Use Cases

* Feed fanout
* Analytics, notifications
* Audit logs, media pipeline

### Kafka Setup

* Topics: `post_events`, `like_events`, `notify_events`
* Partitioning: user ID
* Consumers: feed\_fanout, notifier
* Idempotency: event ID
* DLQ: poison records

---

## 8. Cloud Architecture (AWS)

| Layer      | Services                                 |
| ---------- | ---------------------------------------- |
| Compute    | ECS Fargate, Lambda                      |
| Storage    | S3, EFS                                  |
| Databases  | RDS (Postgres), DynamoDB, MSK, Cassandra |
| Networking | VPC, ALB, API Gateway, CloudFront        |
| Messaging  | MSK, SQS, EventBridge                    |
| Security   | IAM, KMS, WAF, Secrets Manager           |
| IaC        | Terraform / CDK                          |

---

## 9. Monitoring & Observability

* Metrics: Prometheus + Grafana
* Logs: CloudWatch / ELK
* Tracing: OpenTelemetry, AWS X-Ray
* Dashboards: Grafana
* Alerts: SNS + CloudWatch Alarms

---

## 10. Testing Strategy

* Unit Testing: xUnit, mocks
* Integration: TestContainers
* Contract: Pact
* Load: k6, Locust
* Security: OWASP ZAP, Snyk
* CI/CD: GitHub Actions, CodePipeline

---

## 11. References & Best Practices

* [Tech Interview Handbook – System Design](https://www.techinterviewhandbook.org/system-design/)
* [Grokking Modern System Design](https://www.designgurus.io/)
* [AWS Well-Architected Framework](https://docs.aws.amazon.com/wellarchitected/latest/framework/)
* [Martin Fowler – Architectural Styles](https://martinfowler.com/architecture/)
* [Google SRE Book](https://sre.google/books/)
* [Uber Engineering Blog](https://eng.uber.com/)
* [ElasticSearch Scaling Guide](https://www.elastic.co/guide/en/elasticsearch/reference/current/scalability.html)
