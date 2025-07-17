
# Distributed Logging System – System Design for Principal/Architect Role

## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements

- **Log Ingestion**: Accept structured/unstructured logs via APIs, agents, or collectors.
- **Log Storage**: Store logs durably for real-time and historical access.
- **Search & Query**: Provide fast search/filtering over large volumes (text-based, structured fields, time range).
- **Retention Management**: Archive or delete logs after a defined TTL.
- **Indexing**: Efficient full-text and structured field indexing for log search.
- **User Dashboard**: Display logs, search results, alerts.
- **Alerting**: Trigger alerts based on log patterns (e.g., error spikes).
- **Multi-Tenant Support**: Isolate logs by customer/project/environment.

**Actors**:
- Application Services (Producers)
- DevOps / SRE (Users)
- Log Collector Agents (e.g., Fluentd, Filebeat)
- Query Clients / Dashboards

**External Dependencies**:
- Authentication Provider (e.g., OAuth)
- Notification Systems (Slack, Email, PagerDuty)
- Object Storage (S3)
- Kafka / Message Broker
- Search Engine (Elasticsearch, OpenSearch)

### 🔹 Non-Functional Requirements

| Aspect           | Description |
|------------------|-------------|
| **Performance**  | <200ms search latency for 95th percentile queries |
| **Scalability**  | Horizontal scaling for ingestion, storage, search |
| **Availability** | 99.99% uptime; multi-AZ, failover-enabled |
| **Reliability**  | Retry on transient failures, DLQs, backup restore |
| **Security**     | AuthN/AuthZ (RBAC), TLS, encrypted storage, audit |
| **Maintainability** | CI/CD for services, IaC, modular components |
| **Observability** | Metrics, logs, tracing via OpenTelemetry |
| **Compliance**   | GDPR/PCI: log anonymization, data deletion APIs |

---

## 2. High-Level Design (HLD)

### 🧩 Major Components

- **Log Collector (Fluentd/Filebeat/Custom Agent)**
- **Ingestion Service (REST/gRPC + Kafka Producer)**
- **Kafka Cluster (Buffer)**
- **Log Processor (Consumer + ETL + Indexer)**
- **Storage Layer**:
  - Hot Storage: Elasticsearch/OpenSearch
  - Warm Storage: S3 + Metadata DB
- **Query API + UI**
- **Alerting Engine**
- **Authentication Gateway**
- **Admin & Control Plane**

### 🗂 Text-Based Architecture Diagram

```
[Producers] --> [Log Collector Agent] --> [Ingestion API] --> [Kafka Topic] --> [Log Processor]
                                                            ↓
                                                 [Elasticsearch / OpenSearch]
                                                            ↓
                                                       [S3 Storage]
                                                            ↓
                                                   [Query API / UI]
                                                            ↓
                                                      [Alert Engine]
```

### 📌 HLD Considerations

- **Scalability**: Kafka, log processors, and search nodes scale horizontally.
- **Availability**: Multi-AZ Kafka, Elasticsearch with replication, S3 for durability.
- **Performance**: Caching, partitioned indices, and async ingestion.
- **Fault Tolerance**: DLQs, retries, retries, replica nodes.
- **Redundancy**: N+1 ingestion and indexing services.

---

## 3. Low-Level Design (LLD)

### 🧬 Modules and Services

#### API: `/logs/ingest`

```http
POST /logs/ingest
Headers: Authorization, Content-Type: application/json
Body:
{
  "timestamp": "2025-07-17T18:45:00Z",
  "source": "auth-service",
  "level": "ERROR",
  "message": "Unauthorized access attempt",
  "tags": { "env": "prod", "region": "us-west-2" }
}
```

#### Query API: `/logs/search`

```http
GET /logs/search?source=auth-service&level=ERROR&start=...&end=...
```

### 📋 Internal Workflows

1. **Ingestion**
   - Validate, enrich metadata (host, region), write to Kafka
2. **Processing**
   - Kafka consumer parses log
   - Writes to Elasticsearch (hot tier) and S3 (archival)
   - Async: index metadata in PostgreSQL
3. **Query**
   - Hot data → Elasticsearch
   - Cold data → S3 + metadata in DB → fetch logs

### 🔁 Retry, Timeouts, Errors

- Kafka retry (at-least-once)
- Dead Letter Queue for poison messages
- 3x exponential backoff on Elasticsearch failures
- Circuit breakers on DB/Search

---

## 4. Database Design

### 📌 Database Choices

| Component | Type | Reason |
|----------|------|--------|
| Log Storage | NoSQL (OpenSearch) | Full-text, filterable search |
| Metadata / Config | SQL (PostgreSQL) | Relational config, alerting rules |
| Cold Archive | S3 | Cheap, reliable long-term storage |

### 📚 Schema Sample (PostgreSQL)

```sql
CREATE TABLE alert_rules (
  id UUID PRIMARY KEY,
  user_id UUID,
  log_pattern TEXT,
  threshold INT,
  notification_channel TEXT,
  created_at TIMESTAMP
);
```

### 🧠 CAP Theorem Justification

- Prioritize **Availability** + **Partition Tolerance**
- Logs are eventually consistent (delay in index/write is acceptable)

### 🔍 Indexing Strategy

- Elasticsearch: `timestamp`, `source`, `level`, `tags`
- Sharded by `tenant` or `day` for scaling

### 🗄 Storage Optimization

- S3 Lifecycle rules for retention
- Compression (gzip), batching
- Redis/Memcached for caching recent queries

---

## 5. Architecture Patterns

| Pattern | Use |
|--------|-----|
| **Microservices** | Ingestion, Query, Alerting, Indexer are decoupled |
| **Event-Driven** | Kafka between ingestion and processing |
| **CQRS** | Separate query/search from ingestion |
| **Hexagonal** | Ports/Adapters isolate core log processing logic |
| **Serverless (optional)** | Alerts or archival tasks (Lambda) |

---

## 6. Design Patterns

| Pattern | Use |
|--------|-----|
| **Builder** (Creational) | Construct complex alert rules |
| **Adapter** (Structural) | Integrate different log formats from sources |
| **Observer** (Behavioral) | Alert engine observes log patterns |
| **Strategy** | Different parsing strategies for JSON, syslog, etc. |

---

## 7. Event-Driven Messaging & Kafka Integration

### 📌 Use Cases

- Async ingestion
- Decoupled log producers/consumers
- Retry + DLQ
- Alerts / anomaly pipelines
- CDC → Stream logs from DBs

### 🧱 Kafka Topics

- `logs.raw`
- `logs.parsed`
- `alerts.triggered`

**Partitions**: Based on source/service  
**Consumer Groups**: Ingestion processor, indexer, alert engine

**Delivery Semantics**:  
- **At-least-once** (retry + idempotency)
- **DLQ** for poison logs

**Backpressure**: Kafka buffering + autoscale consumers  
**Schema**: Avro via Confluent Schema Registry  
**Kafka Connect**: For MySQL/PostgreSQL CDC into log stream

---

## 8. Cloud Architecture & Services

| Layer | AWS Services |
|-------|--------------|
| Compute | EKS or ECS Fargate for ingestion/indexing |
| Storage | S3 (archive), EBS (OpenSearch), EFS (backup scripts) |
| DB | Amazon Aurora (metadata), MSK (Kafka), OpenSearch |
| Networking | VPC, ALB/NLB, API Gateway, CloudFront |
| Messaging | MSK, SQS (fallback DLQ), EventBridge (alert triggers) |
| Security | IAM Roles, Secrets Manager, KMS, Shield, WAF |
| IaC | Terraform (EKS, Kafka, OpenSearch provisioning) |

---

## 9. Monitoring, Logging & Observability

| Layer | Tools |
|-------|------|
| **Metrics** | Prometheus + Grafana (Ingestion latency, QPS) |
| **Logs** | Fluent Bit → CloudWatch / OpenSearch |
| **Tracing** | OpenTelemetry + AWS X-Ray |
| **Dashboards** | Grafana, OpenSearch Dashboards |
| **Alerts** | CloudWatch Alarms → SNS → PagerDuty/Slack |

---

## 10. Testing Strategy

| Type | Tools |
|------|------|
| Unit | JUnit, xUnit, pytest |
| Integration | TestContainers for Kafka, PostgreSQL |
| Contract | Pact.io |
| Load/Stress | k6, Locust, Gatling |
| Security | OWASP ZAP, AWS Inspector |
| CI/CD | GitHub Actions + Quality Gates (SonarQube) |

---

## 11. References & Best Practices

### 📘 Tech Interview Handbook
- [Tech Interview Handbook Portal](https://app.techinterviewhandbook.org/)
- [GitHub Repository – yangshun/tech-interview-handbook](https://github.com/yangshun/tech-interview-handbook)

### 📘 Grokking the System Design Interview
- [Educative Course – Grokking the System Design Interview](https://www.educative.io/courses/grokking-the-system-design-interview)
- [Design Gurus Site](https://www.designgurus.io/course/grokking-the-system-design-interview)

### ☁️ AWS Well-Architected Framework
- [AWS Documentation](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)
- [AWS Architecture Center](https://aws.amazon.com/architecture/well-architected/)

### 🧠 Martin Fowler – Event-Driven & CQRS Patterns
- [What do you mean by “Event-Driven”?](https://martinfowler.com/articles/201701-event-driven.html)
- [CQRS – Command Query Responsibility Segregation](https://martinfowler.com/bliki/CQRS.html)

### 🔍 OpenSearch & Kafka Best Practices
- [Kafka Source in OpenSearch Data Prepper](https://docs.opensearch.org/docs/latest/data-prepper/pipelines/configuration/sources/kafka/)
- [AWS OpenSearch Best Practices](https://docs.aws.amazon.com/opensearch-service/latest/developerguide/bp.html)

### 📊 Datadog & New Relic Dashboards
- [Datadog – Maintain Relevant Dashboards](https://docs.datadoghq.com/dashboards/guide/maintain-relevant-dashboards/)
- [New Relic – Introduction to Dashboards](https://docs.newrelic.com/docs/query-your-data/explore-query-data/dashboards/introduction-dashboards/)
