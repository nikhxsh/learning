
# 📊 Real-Time Metrics Dashboard – System Design (Principal/Architect Level)

Designing a scalable, resilient, real-time metrics dashboard with stream processing and time-windowed analytics.

---

## 🧭 Table of Contents

1. [High-Level Design (HLD)](#1-high-level-design-hld)
2. [Low-Level Design (LLD)](#2-low-level-design-lld)
3. [Database Design](#3-database-design)
4. [Design Principles & Patterns](#4-design-principles--patterns)
5. [Cloud Architecture & Services](#5-cloud-architecture--services)
6. [Monitoring, Logging, and Observability](#6-monitoring-logging-and-observability)
7. [Testing Strategy](#7-testing-strategy)
8. [References](#8-references)

---

## 1. High-Level Design (HLD)

### 🧱 Major Components

- **API Gateway**: Entry point for clients to send/fetch metrics.
- **Ingestion Service**: Validates and routes data to streaming platform.
- **Streaming Platform**: (Kafka/Kinesis) Handles high-throughput metric events.
- **Stream Processor**: (Flink/Kinesis Analytics) Aggregates metrics in real time.
- **Time-Series Database**: (TimescaleDB/InfluxDB) Stores raw and aggregated metrics.
- **Dashboard Service**: Serves dashboard APIs.
- **Cache**: (Redis) Accelerates dashboard queries.
- **Auth Service**: Validates client tokens and permissions.

### 🔧 Architecture Diagram (Text Format)

```
[Client Apps]
     |
     v
[API Gateway]
     |
     v
[Ingestion Service] ---> [Auth Service]
     |
     v
[Kafka/Kinesis Stream] ---> [Stream Processor]
                                 |
                                 v
               +----------------+----------------+
               |                                 |
         [Time-Series DB]                 [Redis Cache]
               |                                 |
               v                                 v
        [Dashboard Service] <---------------- [Client]
```

### ✅ NFR Coverage

| Concern         | Strategy                                                                 |
|----------------|--------------------------------------------------------------------------|
| Scalability     | Kafka partitioning, horizontally scalable services                      |
| Availability    | Load-balanced endpoints, replicated services and DBs                    |
| Performance     | Redis caching, async ingestion, batched writes                          |
| Fault Tolerance | Kafka retention, Flink checkpointing, retries                           |
| Redundancy      | Multi-AZ deployment, DB replicas                                        |

---

## 2. Low-Level Design (LLD)

### 📦 Key Modules

- `MetricValidator`, `KafkaProducerClient`, `RetryHandler`
- `WindowAggregator`, `AnomalyDetector`, `SinkClient`
- `MetricsQueryHandler`, `CacheClient`, `AuthClient`

### 🔌 API Contracts

**POST /metrics**

```json
{
  "metric_name": "cpu_usage",
  "value": 92.5,
  "timestamp": 1721199945,
  "tags": {
    "host": "web-01",
    "env": "prod"
  }
}
```

**GET /dashboard?metric=cpu_usage&window=5m**

```json
{
  "metric_name": "cpu_usage",
  "window": "5m",
  "aggregations": {
    "avg": 87.3,
    "max": 95.0,
    "p95": 93.2
  }
}
```

### 🧬 Database Schema (TimescaleDB)

```sql
CREATE TABLE metrics (
  id SERIAL PRIMARY KEY,
  metric_name TEXT NOT NULL,
  value DOUBLE PRECISION,
  ts TIMESTAMP,
  tags JSONB
);
CREATE INDEX idx_metric_time ON metrics(metric_name, ts DESC);
SELECT create_hypertable('metrics', 'ts');
```

### 🔄 Service Communication

- **Sync**: API Gateway → Ingestion → Auth
- **Async**: Ingestion → Kafka → Stream Processor → DB

### ⚙️ Algorithms

- **Windowed Aggregation**: Tumbling/Sliding windows (1m, 5m, 15m)
- **Anomaly Detection**: Optional moving average + threshold check

### ❗ Error Handling

- Ingestion retry with exponential backoff
- Flink checkpoint-based retry
- Circuit breakers + timeouts in API/Dashboard

---

## 3. Database Design

### 🗃 SQL vs NoSQL

- **Choice**: Time-Series SQL DB (TimescaleDB)
- **Justification (CAP Theorem)**:
  - **Consistency**: Yes (important for real-time insights)
  - **Availability**: De-prioritized for ingestion, dashboard should fail gracefully
  - **Partition Tolerance**: Yes (handled by Kafka/Flink)

### 📐 Schema Modeling

- Hypertables by time and metric_name
- JSONB tags for flexible dimensions

### 🧲 Indexing

- Composite index: `(metric_name, ts DESC)`
- GIN index on tags if needed

### ⚡ Optimizations

- Pre-aggregation in Flink
- Redis cache for hot metrics
- Batched writes

### 🔄 Replication & Backup

- Streaming read replicas
- Nightly S3 backups via pg_dump

---

## 4. Design Principles & Patterns

### 🔑 SOLID Principles

- **S**: MetricValidator does one thing
- **O**: Aggregator open for new window logic
- **L**: Interface-based processors
- **I**: Separate interfaces for cache and DB
- **D**: DI used for producer/service clients

### 🏗 Architecture Patterns

- **Microservices**
- **Event-Driven Architecture**
- **CQRS (for ingest vs query paths)**

### 🧰 Design Patterns

| Pattern     | Usage                                     |
|-------------|--------------------------------------------|
| Factory     | Dynamic Kafka client creation              |
| Strategy    | Aggregation types (avg, p95, etc.)         |
| Observer    | Alert system or anomaly notifier           |
| Singleton   | Cache, config, DB pools                    |
| Adapter     | Common DB/Cache interface                  |

---

## 5. Cloud Architecture & Services

| Concern       | AWS Service              | Alternatives                         |
|---------------|--------------------------|--------------------------------------|
| Compute       | ECS Fargate, Lambda      | GKE, Azure Functions                 |
| Streaming     | Kinesis Data Streams     | MSK (Kafka), Pub/Sub                 |
| DB            | Timestream, RDS + TS     | InfluxDB, Azure Time Series Insights |
| Caching       | ElastiCache (Redis)      | Memcached                            |
| API Gateway   | Amazon API Gateway       | Kong, NGINX                          |
| Auth          | Cognito, IAM             | Auth0, Okta                          |
| Storage       | S3 for backups           | Azure Blob, GCS                      |
| Messaging     | SNS, EventBridge         | SQS, Azure Event Grid                |
| IaC           | Terraform                | CDK, CloudFormation                  |

---

## 6. Monitoring, Logging and Observability

### 📈 Metrics

- CPU, memory, latency, throughput, error rate
- Prometheus + Grafana dashboards

### 🪵 Logging

- JSON structured logs → CloudWatch or ELK stack
- Fluent Bit/Logstash for aggregation

### 📡 Tracing

- AWS X-Ray or OpenTelemetry

### 🚨 Alerting

- CloudWatch Alarms → SNS → Slack/PagerDuty

---

## 7. Testing Strategy

| Layer             | Tools/Approach                         |
|------------------|-----------------------------------------|
| Unit Tests        | JUnit, NUnit, pytest                    |
| Integration Tests | Testcontainers for Kafka, PostgreSQL   |
| Contract Tests    | Pact for REST APIs                     |
| Load Testing      | k6, Locust, JMeter                      |
| Pen Testing       | OWASP ZAP, Burp Suite                  |
| CI/CD Automation  | GitHub Actions, CodePipeline            |

---

## 8. References

- [Tech Interview Handbook – System Design](https://www.techinterviewhandbook.org/system-design/)
- [Design Gurus – Grokking the System Design Interview](https://www.designgurus.io/course/system-design-interview)
