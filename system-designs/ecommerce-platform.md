# Microservices-Based E-Commerce Platform Design (Online & Offline)

## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements

#### Core Features:

* User Registration/Login with authentication
* Product Catalog (browse, search, filter)
* Shopping Cart and Wishlist
* Order Management (place, cancel, track)
* Inventory & Warehouse Integration
* Payment Gateway Integration
* Offline Purchase Sync (POS → central system)
* Notifications (email/SMS/push)
* Review and Ratings

#### Actors:

* Customer (Online/Offline)
* Admin
* Warehouse Staff
* POS Agents
* Third-party Integrations (Payment, Shipping)

#### External Dependencies:

* Payment Gateways (Stripe, Razorpay)
* Notification Providers (Twilio, SES, Firebase)
* POS Systems
* Shipping Providers

### 🔹 Non-Functional Requirements

| Category        | Requirement                                                   |
| --------------- | ------------------------------------------------------------- |
| Performance     | API < 300ms, Cart ops < 100ms, Payment < 1s                   |
| Scalability     | Autoscaling, handle Black Friday/Big Billion Day traffic      |
| Availability    | 99.99% uptime, failover setup, HA for core services           |
| Reliability     | Retry on failure, distributed transactions, circuit breakers  |
| Security        | OAuth2, JWT, data encryption (at-rest/in-transit), audit logs |
| Maintainability | Modular code, domain-driven, CI/CD pipelines                  |
| Observability   | Traces, metrics, logs → centralized (CloudWatch/Grafana/ELK)  |
| Compliance      | PCI-DSS (payments), GDPR (user data), SOC2                    |

---

## 2. High-Level Design (HLD)

### 🧱 Major Components:

* API Gateway
* Auth Service
* User Service
* Product Catalog Service
* Order Service
* Cart Service
* Inventory Service
* Payment Service
* Notification Service
* Review Service
* POS Sync Service
* Kafka/EventBus

### ⚓ Architecture Diagram (Textual)

```
Client
  |
[API Gateway]
  |         ↘
[Auth]     [User Service]
              |
         [Cart Service] <---> [Product Catalog]
              |
         [Order Service] ----> [Payment Service]
              |
         [Inventory Service] <--> [POS Sync Service]
              |
         [Notification Service]
              |
         [Review Service]
              |
         [Kafka/EventBus]
              |
         [Analytics / Data Lake / DLQ]
```

### System Characteristics:

* Scalability: Horizontally scalable microservices; product catalog via CDN/cache
* Availability: HA enabled with active-active design
* Performance: Caching (Redis), indexing, async processing for notifications/payments
* Fault Tolerance: Circuit breakers, retries, fallback patterns
* Redundancy: Active-active DB replication; DLQs for failed Kafka messages

---

## 3. Low-Level Design (LLD)

### 🔌 API Definitions

#### POST /order

```json
Request: {
  "userId": "u123",
  "items": [{ "productId": "p456", "qty": 2 }],
  "paymentMethod": "CARD"
}
Response: {
  "orderId": "o789",
  "status": "PENDING"
}
```

### 🛠 Internal Workflows:

1. Validate inventory
2. Create order → send Kafka event
3. Process payment asynchronously
4. Update inventory
5. Notify user

### ⛓ Communication:

* REST: Gateway ↔ Auth/User/Product
* gRPC: Order ↔ Inventory/Payment
* Async: Order → Notification via Kafka

### 🔄 Retry & Error Handling:

* Retry (3x exponential backoff) for payment
* Timeouts: gRPC (2s), REST (5s)
* Circuit Breakers on downstream services
* DLQ for failed Kafka events

---

## 4. Database Design

### 📃 Choice:

* SQL (PostgreSQL, MySQL) for Order, Payment, User
* NoSQL (DynamoDB, MongoDB) for Catalog, Inventory, Cart

### 🔠 Schema Modeling:

#### Order Table (SQL)

```sql
order_id PK | user_id FK | status | total | created_at
```

#### Product Document (MongoDB)

```json
{
  "productId": "p123",
  "name": "Nike Shoes",
  "category": "Footwear",
  "inventory": 100,
  "price": 3999
}
```

### 🔍 Indexing:

* Composite indexes on `user_id, order_id`, `category, price`

### ⚙ Partitioning & Replication:

* Partition by region (geo-partitioned tables)
* Read replicas for load balancing
* Backups via RDS snapshots / MongoDB Atlas

### 🧐 Caching:

* Redis for product catalog, session tokens, cart state

---

## 5. Architecture Patterns

| Pattern       | Usage                                                        |
| ------------- | ------------------------------------------------------------ |
| Microservices | Independent deployability, bounded context                   |
| Event-Driven  | Order → Inventory/Notification async updates                 |
| CQRS          | Product Search = Read Optimized (Elastic), Write via Catalog |
| Serverless    | Notification and POS sync as Lambda functions                |
| Hexagonal     | Domain logic isolated via adapters                           |
| Layered       | Presentation ↔ Logic ↔ Persistence separation                |

---

## 6. Design Patterns

| Pattern         | Context                  | Problem Solved              | Example                 |
| --------------- | ------------------------ | --------------------------- | ----------------------- |
| Factory         | Create orders/payments   | Object creation abstraction | PaymentFactory          |
| Strategy        | Payment mode (UPI, Card) | Runtime logic switch        | PaymentStrategy         |
| Observer        | Order status notify      | Event listener decoupling   | Kafka Subscribers       |
| Builder         | Complex order creation   | Object construction         | OrderBuilder            |
| Adapter         | Gateway integration      | Interface translation       | StripeAdapter, Razorpay |
| Circuit Breaker | Downstream reliability   | Failure prevention          | Hystrix/Resilience4j    |

---

## 7. Event-Driven Messaging & Kafka Integration

### 📌 Use Cases:

* Order placed → inventory deduction
* Order completed → email notification
* POS sale → sync to central system
* Analytics → stream order data
* Audit logs → store event trail

### 🧱 Design Elements:

* Topics: `order.events`, `inventory.updated`, `notifications`
* Partitions: 6 (based on orderId hash)
* Consumer Groups: Inventory, Email Service, Audit Logger

### 🛠 Integration Strategy:

* Kafka Connect for MySQL CDC
* Kafka Streams for real-time metrics
* Avro schema with Schema Registry

### 💡 Message Delivery:

* At-least-once for inventory
* Exactly-once for payment (Kafka transactions)
* DLQ for poison messages
* Idempotent consumers with `orderId` deduplication

---

## 8. Cloud Architecture & Services (AWS)

| Layer      | AWS Services                                  |
| ---------- | --------------------------------------------- |
| Compute    | ECS Fargate, Lambda (POS sync, notifications) |
| Storage    | S3 (product images, invoices), EBS            |
| DB         | Aurora MySQL, DynamoDB                        |
| Networking | VPC, ALB, API Gateway                         |
| Messaging  | MSK (Kafka), SQS (DLQs), EventBridge          |
| Security   | IAM, Cognito, KMS, Secrets Manager            |
| IaC        | Terraform for provisioning                    |

---

## 9. Monitoring, Logging & Observability

* **Metrics**: CPU, memory, DB connections → CloudWatch
* **Structured Logs**: JSON logs shipped to ELK or CloudWatch Logs
* **Tracing**: AWS X-Ray for distributed tracing
* **Dashboards**: Grafana/CloudWatch
* **Alerts**: Latency > 1s, DB errors → PagerDuty via SNS

---

## 10. Testing Strategy

| Type             | Tools/Methods                                        |
| ---------------- | ---------------------------------------------------- |
| Unit Testing     | xUnit, Mocha, JUnit                                  |
| Integration      | TestContainers for Kafka/DB                          |
| Contract Testing | Pact for service contracts                           |
| Load Testing     | k6 or JMeter                                         |
| Security Testing | OWASP ZAP, SonarQube, Pen Testing                    |
| CI/CD            | GitHub Actions, Jenkins pipelines with quality gates |

---

## 11. References & Best Practices

* [Tech Interview Handbook](https://techinterviewhandbook.org)
* [Grokking System Design](https://designgurus.io)
* [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
* [Martin Fowler - Microservices](https://martinfowler.com/articles/microservices.html)
* [Google Cloud Reference Architecture](https://cloud.google.com/architecture)
* [Azure Reference Architecture](https://learn.microsoft.com/en-us/azure/architecture)
