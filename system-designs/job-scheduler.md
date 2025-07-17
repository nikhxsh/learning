## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements

**Actors:**

* **Users**: Define workflows, configure DAGs, monitor jobs.
* **Scheduler**: Determines when jobs should run.
* **Executor**: Runs the job tasks.
* **Worker**: Executes individual task instances.
* **External Systems**: Databases, APIs, cloud resources, etc.

**Core Features:**

* DAG-based job scheduling.
* Cron/interval-based triggers.
* Task dependencies and execution order.
* Support for retries and failure handling.
* Web UI for DAG creation, job status, logs.
* Notification on failure/success (Email/SMS/Slack).
* REST/gRPC API for DAG operations.

**Use Cases:**

* Data pipeline orchestration.
* ETL workflow scheduling.
* ML model training pipelines.
* Notification and reporting systems.

**External Dependencies:**

* Git (for DAG versioning).
* Authentication systems (e.g., OAuth, LDAP).
* Cloud services (S3, RDS, Kinesis, etc.).

### 🔹 Non-Functional Requirements

| Category            | Requirement                                                                 |
| ------------------- | --------------------------------------------------------------------------- |
| **Performance**     | Task start latency < 1s, Support 10k+ DAGs, Task throughput: 1000+/min      |
| **Scalability**     | Horizontally scalable scheduler and workers                                 |
| **Availability**    | 99.99% uptime, HA Scheduler/Executor using leader election or clustering    |
| **Reliability**     | Retry policies, failure queues, at-least-once semantics                     |
| **Security**        | Role-based access control (RBAC), encrypted secrets, audit logging          |
| **Maintainability** | Modular DAG definition, plugin system, IaC support                          |
| **Observability**   | Distributed tracing, logs, metrics via Prometheus/Grafana/CloudWatch        |
| **Compliance**      | GDPR-ready (audit trail, data anonymization), SOC2 for cloud infrastructure |

---

## 2. High-Level Design (HLD)

### 🌐 Architecture Components

```
[User/UI/API]
     |
 [Scheduler (HA)]
     |
 [Metadata DB] <------> [DAG Parser]
     |
 [Task Queue (Kafka/SQS)]
     |
 [Executor Cluster] <-----> [Worker Pool]
     |
 [Result Backend (Redis/S3)] -----> [Logs & Artifacts]
     |
 [Notification Service]
```

### 🔍 Key Concerns

* **Scalability**: Executors and Workers scale out; Kafka scales partitions.
* **Availability**: Scheduler in active-passive or leader-election model.
* **Performance**: Caching parsed DAGs, parallel workers per node.
* **Fault Tolerance**: Retry queues, heartbeat checks, DLQs.
* **Redundancy**: Multi-AZ setup, backup for metadata DB and queues.

---

## 3. Low-Level Design (LLD)

### 🔗 REST API Definitions (Sample)

**POST /dags**

```json
{
  "name": "daily_report",
  "cron": "0 9 * * *",
  "tasks": [
    {"id": "fetch_data", "type": "http", "uri": "..."},
    {"id": "transform", "depends_on": ["fetch_data"]},
    {"id": "send_report", "depends_on": ["transform"]}
  ]
}
```

**GET /dags/{dag\_id}/status** – Shows DAG run history

### 🧱 DB Schema (PostgreSQL)

```sql
TABLE dags (
  id UUID PK,
  name TEXT UNIQUE,
  cron_expression TEXT,
  created_at TIMESTAMP
);

TABLE tasks (
  id UUID PK,
  dag_id UUID FK,
  name TEXT,
  type TEXT,
  command TEXT,
  depends_on TEXT[]
);

TABLE dag_runs (
  id UUID PK,
  dag_id UUID FK,
  run_time TIMESTAMP,
  status TEXT,
  retries INT
);

TABLE task_runs (
  id UUID PK,
  dag_run_id UUID FK,
  task_id UUID FK,
  status TEXT,
  attempt INT,
  logs_uri TEXT
);
```

### 🔄 Internal Workflows

* **Scheduler Loop**: Cron evaluator → DAG Run creation → Push to Task Queue
* **Executor**: Polls for new tasks → Schedules worker threads → Updates status
* **Failure Handling**: Retry with backoff → DLQ on max attempts

---

## 4. Database Design

| Aspect          | Design Choice                                                      |
| --------------- | ------------------------------------------------------------------ |
| **Type**        | **SQL (PostgreSQL)** for DAG metadata, **Redis** for state caching |
| **CAP**         | CP system — DAG/task consistency over availability                 |
| **Sharding**    | DAG-based horizontal partitioning                                  |
| **Indexes**     | `cron_expression`, `dag_id`, `status`                              |
| **Replication** | PostgreSQL streaming replica for HA                                |
| **Backups**     | Daily S3 dumps, WAL archiving                                      |
| **Caching**     | Redis for DAG definitions, task status                             |

---

## 5. Architecture Patterns

* **Microservices**: Scheduler, Executor, Worker, UI decoupled for independent deployment.
* **Event-Driven**: Kafka/SQS pipeline between scheduler → executor → worker.
* **Layered**: UI → API → Scheduler Logic → Persistence.
* **CQRS**: Separate query APIs for DAG/job status; write APIs for triggering.
* **Hexagonal**: Executors use ports/adapters to execute shell/HTTP/SQL jobs.

---

## 6. Design Patterns

| Pattern      | Use Case                                                     |
| ------------ | ------------------------------------------------------------ |
| **Builder**  | DAG creation via fluent API (e.g., `.addTask().dependsOn()`) |
| **Observer** | Workers notify status → scheduler via event bus              |
| **Factory**  | TaskFactory generates task executors based on type           |
| **Retry**    | Implemented with exponential backoff for failed jobs         |
| **Command**  | Encapsulates execution logic for jobs uniformly              |

**Example – Retry Pattern (Behavioral):**

```python
def retry(fn, attempts=3):
    for i in range(attempts):
        try:
            return fn()
        except Exception:
            if i == attempts - 1:
                raise
            sleep(2 ** i)
```

---

## 7. Event-Driven Messaging & Kafka Integration

### 📌 Use Cases

* Decoupled task execution
* DLQ for task failures
* Notifications
* Backpressure handling

### 🧱 Kafka Design

* **Topics**: `dag-triggered`, `task-ready`, `task-result`, `notifications`
* **Partitions**: Per DAG ID (consistent hashing)
* **Consumer Groups**: Executor clusters, worker nodes

### 🚰 Integration Strategy

* **Producers**: Scheduler emits DAG trigger → Executor emits task-ready
* **Consumers**: Workers poll task-ready → Emit result to task-result topic
* **Kafka Connect**: Capture DB change streams for audit trail
* **Payloads**: JSON with schema registry for validation & evolution

---

## 8. Cloud Architecture & Services (AWS)

| Layer      | AWS Service                                      |
| ---------- | ------------------------------------------------ |
| Compute    | EKS (Scheduler/Executor), Lambda (Notifications) |
| Storage    | S3 (logs/artifacts), EBS (DB snapshots)          |
| DB         | Aurora PostgreSQL, ElastiCache Redis             |
| Networking | VPC, NLB/ALB, API Gateway                        |
| Messaging  | MSK (Kafka), SQS (retry queue)                   |
| Security   | IAM Roles, Secrets Manager, KMS                  |
| IaC        | Terraform with CI/CD pipeline in CodePipeline    |

---

## 9. Monitoring, Logging & Observability

* **Metrics**: Prometheus → Grafana dashboards

  * DAG success/fail count
  * Task execution time
  * Retry counts, executor lag
* **Logs**: Fluent Bit to CloudWatch Logs / ELK
* **Tracing**: OpenTelemetry + Jaeger/X-Ray
* **Alerting**: CloudWatch Alarms → SNS → PagerDuty

---

## 10. Testing Strategy

* **Unit Testing**: DAG parser, cron engine, retry/backoff logic
* **Integration Testing**: Scheduler ↔ DB ↔ Executor
* **Contract Testing**: Pact for API endpoints
* **Load Testing**: Simulate 1000 DAGs via k6
* **Security Testing**: OWASP ZAP, static scans in CI
* **CI/CD**: GitHub Actions + SonarQube + E2E in isolated namespace

---

## 11. References & Best Practices

* [Tech Interview Handbook – System Design](https://www.techinterviewhandbook.org/)
* [Grokking the System Design Interview – DesignGurus.io](https://www.designgurus.io/)
* [AWS Well-Architected Framework](https://wa.aws.amazon.com/)
* [Martin Fowler – Event-Driven Architecture](https://martinfowler.com/articles/201701-event-driven.html)
* [Airflow Architecture Guide](https://airflow.apache.org/docs/)
