## Online Notepad with Code Execution Platform (Like LeetCode) – System Design

## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements

**Core Features:**

* User Authentication: Signup/Login via OAuth (Google, GitHub)
* Create/Edit/Delete Notes (Markdown or Rich Text)
* Syntax highlighting and code execution for supported languages (e.g., Python, C++, JavaScript)
* Save & Retrieve code history/versioning
* Collaboration (real-time editing, like Google Docs)
* Public/private sharing links
* Commenting on notes
* Auto-save, undo/redo
* Tags & Search functionality

**Actors:**

* End User
* Authentication Provider (e.g., Google OAuth)
* Code Execution Engine (e.g., custom or third-party)
* Notification Service (for comments, collaboration invites)

**External Dependencies:**

* Auth Providers (OAuth)
* Code execution sandbox (e.g., Docker/Kubernetes based)
* Email/SMS APIs for notifications
* GitHub API for importing/exporting

---

### 🔹 Non-Functional Requirements

| Aspect              | Requirement                                                                       |
| ------------------- | --------------------------------------------------------------------------------- |
| **Performance**     | <300ms for note read/write, <2s for code execution                                |
| **Scalability**     | Horizontal scaling via stateless services, container orchestration via Kubernetes |
| **Availability**    | 99.99% with auto-failover, multi-AZ deployments                                   |
| **Reliability**     | Retry with exponential backoff, circuit breakers                                  |
| **Security**        | OAuth2, Role-based access, encrypted storage (at-rest & in-transit), audit logs   |
| **Maintainability** | Clean APIs, modular architecture, IaC                                             |
| **Observability**   | Centralized logging, distributed tracing, real-time monitoring                    |
| **Compliance**      | GDPR (data deletion/export), SOC2 for data handling                               |

---

## 2. High-Level Design (HLD)

### 🔧 Major Components:

* **Frontend**: React (Monaco Editor / TipTap for notepad)
* **Backend API**: Node.js/Python (REST/gRPC)
* **Auth Service**: OAuth, JWT validation
* **Note Service**: CRUD APIs, Markdown support
* **Code Execution Service**: Sandboxed Docker runtime (Kubernetes Jobs)
* **Collab Service**: WebSocket Server (Quill/OT/CRDT support)
* **Notification Service**: Email/Webhooks
* **DB**: PostgreSQL for metadata, S3 for document storage
* **Cache**: Redis for session & real-time presence
* **Queue**: Kafka/SQS for execution jobs & events
* **Monitoring**: Prometheus, Grafana, CloudWatch

### 📊 Architecture Diagram (text)

```
[Client App]
   ↓
[API Gateway] ──→ [Auth Service]
        ↓
    [Note Service] ──→ [PostgreSQL]
        ↓
    [Code Exec Service] ──→ [Job Queue] ──→ [K8s Executor Pods]
        ↓
    [Notification Service] ──→ [SNS/SQS]
        ↓
    [Collab Service] ── WebSocket ──→ [Redis Pub/Sub]
        ↓
  [Logging & Metrics] ─→ [CloudWatch/Grafana]
```

---

## 3. Low-Level Design (LLD)

### 📌 APIs (REST/gRPC)

**POST /notes**

```json
Request: { "title": "Example", "content": "# Markdown here", "tags": ["interview"] }
Response: { "id": "note123", "createdAt": "..." }
```

**POST /execute**

```json
Request: { "language": "python", "code": "print('Hello')" }
Response: { "status": "Success", "stdout": "Hello", "execTime": "0.3s" }
```

### 🧹 Internal Modules

* **Notes Module**: Text diffing, auto-save engine
* **Execution Module**: Language runner, output sanitizer
* **Collaboration Module**: Operational Transform / CRDT engine
* **Security Module**: Token validator, RBAC

### 📋 Retry/Timeout Policies

* Code execution job timeout: 5s soft, 10s hard
* DLQ for failed jobs
* Retry with exponential backoff (max 3)

---

## 4. Database Design

### 📀 SQL or NoSQL?

* **SQL (PostgreSQL)** for strong consistency (note updates, user access)
* **CAP Theorem**: CP → consistency over partition tolerance

### 📁 Schema Snippets

```sql
users(id, name, email, auth_provider)
notes(id, owner_id, content, version, created_at, updated_at)
executions(id, user_id, language, status, output, created_at)
comments(id, note_id, user_id, message, created_at)
```

### Optimization

* **Indexing**: `notes(owner_id)`, `executions(user_id)`
* **Partitioning**: Large `executions` table by `month`
* **Caching**: Redis for active notes, hot documents
* **Backups**: Daily RDS snapshots, S3 versioning

---

## 5. Architecture Patterns

* **Microservices**: Independent deployable services (Auth, Notes, Execution)
* **Event-Driven**: Code execution via async job queues
* **CQRS**: Notes write (CRUD) separated from read (optimized queries)
* **Hexagonal**: Adapters for code runners, editors, DBs
* **Serverless**: Execution could be Lambda-based for short bursts

---

## 6. Design Patterns

| Pattern         | Context / Usage                                       |
| --------------- | ----------------------------------------------------- |
| Factory         | Language Runner Factory → PythonRunner, JavaRunner    |
| Strategy        | Syntax highlighters, Markdown renderers               |
| Observer        | Real-time updates to collaborators                    |
| Singleton       | Redis connection manager                              |
| Adapter         | External Auth / Execution APIs (e.g., GitHub, Lambda) |
| Circuit Breaker | Execution service resilience                          |

---

## 7. Event-Driven Messaging & Kafka Integration

### 🧱 Topics

* `execution-requests`: From API to execution service
* `execution-results`: Result to DB and frontend
* `note-shared`: For analytics or activity tracking

### 🚰 Flow

```
Client → API → Kafka(topic: exec-req) → Execution Worker → Kafka(topic: exec-res) → DB + Notify
```

* **Ordering**: Keyed by user or note ID
* **Idempotency**: Execution hash stored to avoid re-runs
* **DLQ**: For failed executions after 3 retries
* **Schema**: Avro (via Schema Registry)

---

## 8. Cloud Architecture & Services (AWS)

* **Compute**: ECS Fargate for APIs, EKS Jobs for execution
* **Storage**: S3 for notes, EFS for temp code execution files
* **DB**: Aurora PostgreSQL
* **Networking**: VPC, ALB, API Gateway
* **Messaging**: MSK (Kafka), SQS
* **Security**: IAM, KMS, Cognito/Secrets Manager
* **IaC**: Terraform

---

## 9. Monitoring, Logging & Observability

* **Metrics**: Prometheus + CloudWatch (API latency, execution time)
* **Logs**: FluentBit → ELK Stack
* **Tracing**: OpenTelemetry + AWS X-Ray
* **Dashboards**: Grafana
* **Alerts**: CloudWatch Alarms, PagerDuty integration

---

## 10. Testing Strategy

* **Unit**: NoteService, ExecutionAdapter
* **Integration**: Note CRUD + Execution + Websockets
* **Contract**: Pact between frontend & backend
* **Load Testing**: Locust simulating 1000 concurrent code runs
* **Security**: OWASP ZAP for pen testing
* **CI/CD**: GitHub Actions + CodeBuild + Quality gates

---

## 11. References & Best Practices

- **Tech Interview Handbook**: [https://techinterviewhandbook.org/](https://techinterviewhandbook.org/)  
  A comprehensive resource for algorithm questions, system design prep, interview strategy, resume review, and more.
- **Grokking System Design**  
  An interactive, LeetCode-style walkthrough of real-world system design scenarios—builds intuition through practical examples and guided exercises.
- **AWS Well‑Architected Framework**  
  Covers cloud best practices including high availability, fault tolerance, cost optimization, performance efficiency, security, and operational excellence. A must-read for scalable and resilient architecture.
- **Martin Fowler – Hexagonal Architecture**  
  A deep dive into the Hexagonal (Ports & Adapters) pattern for designing maintainable, decoupled software. See Martin's explanations in his writings or blog for guidance.


## 🔧 Best Practices (with Links)

### ✅ Idempotent APIs
- [Idempotent REST APIs – restfulapi.net](https://restfulapi.net/idempotent-rest-apis/)
- [Understanding Idempotency in APIs – JSON Server](https://json-server.dev/idempotency-in-apis/)
- [Designing Idempotent APIs in Spring Boot – dev.to](https://dev.to/devcorner/designing-idempotent-apis-in-spring-boot-2fhi)

### 🔐 Secure Secrets Storage
- [Azure Key Vault Secrets Best Practices – Microsoft](https://learn.microsoft.com/en-us/azure/key-vault/secrets/secrets-best-practices)
- [Secrets Management Cheat Sheet – OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
- [Secrets Management in CI/CD Pipelines – Devtron](https://devtron.ai/blog/secrets-management-in-ci-cd-pipeline/)

### 🐤 Canary Deployments
- [Canary Deployments for Secret Rotation – UmaTechnology](https://umatechnology.org/stateful-upgrade-strategies-for-in-cluster-secrets-rotation-integrated-in-canary-rollout-policies/)
- [Canary Deployments in IaC and CI/CD – UmaTechnology](https://umatechnology.org/how-canary-deployments-work-in-infrastructure-as-code-for-secure-ci-cd/)
