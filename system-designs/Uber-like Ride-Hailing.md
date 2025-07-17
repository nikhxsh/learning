## Uber-like Application System Design

---

### 1. Functional & Non-Functional Requirements

#### 🔹 Functional Requirements

##### Actors

* **Rider**: Search for rides, book rides, pay, rate drivers
* **Driver**: Accept/reject rides, navigate, complete trips, view earnings
* **Admin**: Monitor system health, resolve disputes, manage users

##### User Flows

* Rider signs up/logs in
* Rider requests a ride with pickup and drop locations
* Nearby drivers are notified
* A driver accepts; rider gets ETA and tracking
* Trip begins, completes, and payment is processed
* Both rider and driver rate each other

##### External Dependencies

* **Payment Gateway**: Stripe, Razorpay
* **Maps API**: Google Maps or Mapbox
* **SMS/Email Provider**: Twilio, SendGrid
* **Push Notification Service**: Firebase Cloud Messaging

#### 🔹 Non-Functional Requirements

| Category        | Requirements                                                |
| --------------- | ----------------------------------------------------------- |
| Performance     | <200ms for API response; updates every 1-2 sec              |
| Scalability     | Handle 10M+ daily active users; autoscaling with traffic    |
| Availability    | 99.99% uptime; multi-region failover                        |
| Reliability     | Retry logic for APIs and messaging; Idempotent operations   |
| Security        | OAuth2/JWT auth; encrypted data (TLS, AES); audit logs      |
| Maintainability | Modular microservices; clean separation of concerns         |
| Observability   | Logs, traces, metrics using OpenTelemetry                   |
| Compliance      | GDPR (data rights), PCI DSS (payment), SOC2 (data handling) |

---

### 2. High-Level Design (HLD)

#### 📦 Major Components

```
[Client Apps (iOS/Android/Web)]
        |
    [API Gateway / BFF Layer]
        |
    +-----------------------------+
    |       Microservices        |
    +-----------------------------+
    | - User Service             |
    | - Ride Matching Service    |
    | - Driver Location Service  |
    | - Payment Service          |
    | - Notification Service     |
    | - Trip Management Service  |
    | - Rating & Review Service  |
    +-----------------------------+
        |        |         |
      [DBs]   [Kafka]   [Redis]
        |        |
    [Geo-Spatial Store]  [Cache]
```

#### 🧱 Considerations

* **Scalability**: Stateless microservices, autoscaling on EKS, partitioned Kafka
* **Availability**: Redundant services across zones; RDS Multi-AZ, MSK replication
* **Performance**: Redis for caching, Kafka for async processing
* **Fault Tolerance**: Retry + DLQ for failed tasks; fallback APIs
* **Redundancy**: Backup DBs, multiple Kafka brokers, active-active services

---

### 3. Low-Level Design (LLD)

#### 🔗 API Examples

##### Create Ride Request (REST)

```
POST /rides
Body:
{
  "riderId": "u123",
  "pickup": { "lat": 19.12, "lng": 73.24 },
  "drop": { "lat": 19.20, "lng": 73.45 }
}
Response: 202 Accepted
```

#### 📋 DB Schema: Ride Table (SQL)

```sql
CREATE TABLE rides (
  id UUID PRIMARY KEY,
  rider_id UUID,
  driver_id UUID,
  status ENUM('requested', 'ongoing', 'completed', 'cancelled'),
  pickup_location GEOGRAPHY,
  drop_location GEOGRAPHY,
  start_time TIMESTAMP,
  end_time TIMESTAMP
);
CREATE INDEX idx_location ON rides USING GIST(pickup_location);
```

#### 🧫 Protocols & Workflow

* Rider triggers `/rides`, Ride Matching publishes event to Kafka
* Driver Location Service streams real-time GPS updates
* Matched driver receives notification (via Firebase)
* Async confirmation → Trip Service creates trip record

#### ⚙️ Error Handling

* Retries: Exponential backoff on transient failures
* Timeouts: 2s for user APIs, 5s internal services
* Circuit breakers using Resilience4j

---

### 4. Database Design

#### 🎯 Type: Hybrid (SQL + NoSQL)

| Use Case           | DB          | Reason                   |
| ------------------ | ----------- | ------------------------ |
| Core transactional | PostgreSQL  | ACID, strong consistency |
| Real-time location | Redis + Geo | Low latency              |
| Trip history/logs  | DynamoDB    | Fast read/write, scale   |
| Events             | Kafka       | Partition-tolerant       |

#### CAP Theorem Justification

* For trips/payments: CP (Consistency + Partition)
* For geo/tracking: AP (Availability + Partition)

#### 🛠️ Extras

* Index: Rides by `rider_id`, `status`, `start_time`
* Sharding: User ID based for trip history
* Backups: Daily full, hourly incremental
* Caching: Redis for driver ETA, pricing

---

### 5. Architecture Patterns

* **Microservices**: Each domain is independently scalable
* **Event-Driven**: Async matching, notifications
* **Layered**: Controllers → Services → Repos
* **Hexagonal**: Business logic isolated from tech (e.g., maps SDK)
* **CQRS**: Separate write model for booking and read model for dashboard
* **Serverless**: Notification, billing events on Lambda

---

### 6. Design Patterns

| Pattern         | Use Case                                          |
| --------------- | ------------------------------------------------- |
| Factory         | Instantiate trip/payment handlers based on type   |
| Singleton       | Connection pools, Redis/Kafka clients             |
| Strategy        | Surge pricing algorithms                          |
| Observer        | Notify user/driver on status change               |
| Circuit Breaker | Fallback for downstream failures (e.g., Payments) |
| Adapter         | Integrate different map/payment APIs              |

---

### 7. Event-Driven Messaging & Kafka Integration

#### 🧠 Use Cases

* Ride Matching
* Driver Status Updates
* Notifications
* Trip Completion → Billing → Rating

#### 🔧 Kafka Topics

| Topic                  | Partitions | Consumers       |
| ---------------------- | ---------- | --------------- |
| ride.requested         | 100        | Match Service   |
| trip.completed         | 100        | Payment, Rating |
| driver.location.update | 500        | ETA Service     |

#### 🛡 Delivery & Reliability

* **Ordering**: Key by `riderId` or `driverId`
* **Exactly-once**: Kafka Transactions
* **Retry**: Retry queue with backoff
* **DLQ**: For failed ride requests/notifications

#### 📦 Kafka Integration

* Kafka Connect for CDC from PostgreSQL
* Kafka Streams for ETA estimation, surge analysis
* Avro schemas with Schema Registry for compatibility

---

### 8. Cloud Architecture & Services

| Concern    | AWS Service                             |
| ---------- | --------------------------------------- |
| Compute    | EKS for core services, Lambda for async |
| Storage    | S3 (logs, receipts), EFS (shared state) |
| DB         | RDS (PostgreSQL), DynamoDB, ElastiCache |
| Networking | VPC, ALB, API Gateway, CloudFront       |
| Messaging  | MSK, SNS, SQS, EventBridge              |
| Security   | IAM, KMS, WAF, Secrets Manager          |
| IaC        | Terraform (EKS, MSK), CDK (Lambda APIs) |

---

### 9. Monitoring, Logging & Observability

| Tool           | Usage                                           |
| -------------- | ----------------------------------------------- |
| **Metrics**    | Prometheus + Grafana (latency, QPS, error rate) |
| **Logs**       | FluentBit → CloudWatch / ELK                    |
| **Tracing**    | OpenTelemetry → AWS X-Ray                       |
| **Dashboards** | CloudWatch + Grafana                            |
| **Alerts**     | CloudWatch Alarms, PagerDuty                    |

---

### 10. Testing Strategy

* **Unit**: For pricing, trip workflows, surge engine
* **Integration**: Rider ↔ Driver ↔ Trip flow
* **Contract**: Pact for APIs across services
* **Load Testing**: Locust simulating 10K RPS
* **Security**: OWASP ZAP, IAM misconfig checks
* **CI/CD**: GitHub Actions + quality gates (coverage, lint, vulnerability scan)

---

### 11. References & Best Practices

* [Tech Interview Handbook – System Design](https://www.techinterviewhandbook.org/system-design/)
* [Grokking Modern System Design](https://designgurus.io/)
* [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
* [Martin Fowler’s Microservices Principles](https://martinfowler.com/microservices/)
* [Uber Engineering Blog](https://eng.uber.com/)
* [Google SRE Book](https://sre.google/books/)
