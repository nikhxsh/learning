
# Scalable Chat System Design – Principal/Architect Role

Here's a **comprehensive, structured design** for a **Scalable Chat System** (like Slack) with **1:1 and group chat**, aligned to your requirements for a **Principal/Architect system design interview**:

## 1. Functional & Non-Functional Requirements

### 🔹 Functional Requirements

**Actors:**
- Users (send/receive messages)
- Admins (moderate users/groups)
- External Services (e.g., push notifications)

**Core Features:**
- User registration/login (OAuth)
- 1:1 chat: real-time messaging, read receipts, typing indicators
- Group chat: multiple users, mentions, message threads
- Message persistence with history
- Push notifications for offline users
- WebSocket-based real-time delivery
- Offline sync
- Search chat history
- Media sharing (images, files)
- Presence indicator (online/offline)
- Moderation tools (delete/edit messages)

**External System Dependencies:**
- Notification service (email/SMS/push)
- CDN (media delivery)
- Identity Provider (e.g., Auth0/OAuth)
- Analytics (Datadog, Amplitude)

### 🔹 Non-Functional Requirements

| Category          | Requirements |
|------------------|--------------|
| **Performance**  | ≤ 200ms message delivery latency, support 1M concurrent connections |
| **Scalability**  | Horizontally scalable WebSocket gateways, autoscaling chat workers |
| **Availability** | 99.99% uptime SLA, HA across availability zones |
| **Reliability**  | Retries on failure, persistent queues, offline sync, idempotency |
| **Security**     | OAuth 2.0, encrypted storage (AES-256), TLS 1.2+, audit logs |
| **Maintainability** | Modular microservices, CI/CD pipelines |
| **Observability** | Logs, metrics, traces |
| **Compliance**   | GDPR, CCPA (data retention policies, user data deletion) |

---

## 2. High-Level Design (HLD)

### 🧩 Components

```
+------------------+       +------------------+
|   API Gateway    | <---> | Auth Service     |
+--------+---------+       +--------+---------+
         |                          |
         v                          v
+--------+---------+       +--------+---------+       +----------------+
| WebSocket Gateway| <---> | Chat Service     | <---> | Notification   |
+--------+---------+       +--------+---------+       +----------------+
         |                          |
         v                          v
+--------+---------+       +--------+---------+       +----------------+
| Kafka Topics     | <---> | Message Service  | <---> | Media Service  |
+--------+---------+       +--------+---------+       +----------------+
         |                          |
         v                          v
+--------+---------+       +--------+---------+
| MongoDB (Messages)|      | Redis (Presence) |
+-------------------+      +------------------+
```

### Key Attributes
- **Scalability**: Stateless WebSocket gateways with Kafka for pub-sub and buffering.
- **Availability**: HA clusters with multi-AZ Kafka, MongoDB replica sets.
- **Performance**: Redis caching for presence, Kafka for real-time streams.
- **Fault Tolerance**: Kafka DLQs, retries, WebSocket reconnects.
- **Redundancy**: Multi-AZ, DB replicas, stateless services behind ALBs.

---

## 3. Low-Level Design (LLD)

### 📡 API Definitions (REST & WebSocket)

#### REST (for auth, chat history):
```
POST /messages
{
  senderId, recipientId, type: '1-1'|'group', content
}

GET /messages?conversationId=xyz&limit=50
```

#### WebSocket (JSON format):
```
{
  type: "MESSAGE",
  conversationId: "...",
  senderId: "...",
  content: "Hello world!"
}
```

### 📄 DB Schema (MongoDB - NoSQL)

**Messages Collection:**
```json
{
  _id: ObjectId,
  conversationId: String,
  senderId: String,
  timestamp: ISODate,
  content: String,
  type: "text" | "image" | "file",
  deliveryStatus: ["sent", "delivered", "read"]
}
```

**Indexes:**
- `conversationId + timestamp`
- `senderId`
- TTL index for ephemeral messages

### 🔄 Protocols & Data Flow

- **WebSocket** for live bi-directional comms
- **Kafka** for fan-out to consumers
- **REST** for fetching history, media
- **gRPC** (optional) for internal service communication

### 🔁 Retry & Failure Handling

- Exponential backoff on retries
- Dead Letter Queues in Kafka
- Idempotent message writes using UUIDs

---

## 4. Database Design

### Choice: **MongoDB** (NoSQL)

#### Justification (CAP Theorem):
- **Partition Tolerant & Available** over strict consistency
- Uses eventual consistency for chat history retrieval
- Real-time updates cached and later persisted

#### Key Features:
- **Schema**: Flexible for rich message formats
- **Indexes**: `conversationId`, `timestamp`
- **Partitioning**: By `conversationId`
- **Replication**: Replica sets for HA
- **Backups**: Periodic snapshots
- **Caching**: Redis for latest messages/presence

---

## 5. Architecture Patterns

| Pattern        | Justification |
|----------------|----------------|
| **Microservices** | Clear separation of Auth, Chat, Media, Notification |
| **Event-Driven** | Kafka for loose coupling and async delivery |
| **CQRS**        | Separation of write (WebSocket) vs read (REST for history) |
| **Hexagonal**   | Adapters for WebSocket, Kafka, REST, clean domain logic |
| **Serverless**  | Push notification fan-out, Lambda functions for audits |

---

## 6. Design Patterns

| Pattern            | Use Case |
|-------------------|----------|
| **Factory**       | Creating message objects (text/image/file) |
| **Observer**      | Notify presence updates to subscribed users |
| **Strategy**      | Different message delivery strategies |
| **Command**       | Encapsulate operations like `SendMessage`, `MarkAsRead` |
| **Circuit Breaker** | Prevent failure cascades on dependent services |
| **Builder**       | Build complex message payloads with metadata |

---

## 7. Event-Driven Messaging & Kafka Integration

### 📌 Use Cases
- Decouple WebSocket producers & DB consumers
- Message delivery, notifications, analytics
- Retry failures via DLQ
- CDC for audit logging

### 🧱 Kafka Design
- **Topics**: `messages`, `read_receipts`, `notifications`
- **Partitions**: by `conversationId` for order
- **Consumers**: Message service, notification service
- **Guarantees**: At-least-once, idempotent writes
- **DLQ**: For messages that fail processing after N attempts

### 🛠 Integration
- **Kafka Connect**: Stream changes to Elasticsearch
- **Kafka Streams**: Real-time analytics (active users)
- **Schema**: Avro with Schema Registry for compatibility

---

## 8. Cloud Architecture & Services (AWS)

| Service        | Use |
|----------------|-----|
| **EKS/ECS**    | Host microservices |
| **API Gateway**| Entry point for REST |
| **MSK (Kafka)**| Messaging backbone |
| **ElastiCache (Redis)** | Presence, caching |
| **MongoDB Atlas** | Chat persistence |
| **S3**         | Media storage |
| **CloudFront** | CDN for media |
| **IAM**        | Access control |
| **Secrets Manager** | DB/queue credentials |
| **WAF + Shield** | Protect APIs |
| **Terraform/CDK** | Infrastructure as Code |

---

## 9. Monitoring, Logging & Observability

| Component     | Tool |
|---------------|------|
| **Metrics**   | Prometheus + Grafana, CloudWatch |
| **Logs**      | FluentBit + ELK, CloudWatch Logs |
| **Tracing**   | OpenTelemetry, AWS X-Ray |
| **Dashboards**| Grafana |
| **Alerts**    | CloudWatch Alarms, PagerDuty |
| **Health Checks** | Liveness + Readiness probes on services |

---

## 10. Testing Strategy

| Type              | Tool |
|-------------------|------|
| **Unit Tests**     | xUnit, Jest |
| **Integration**    | Postman/Newman, TestContainers |
| **Contract Tests** | Pact (Producer/Consumer) |
| **Load Testing**   | k6, Locust |
| **Security**       | OWASP ZAP, Burp Suite |
| **CI/CD**          | GitLab/GitHub Actions, SonarQube, Quality Gates |

---

## 11. WebSocket Flow – 1:1 and Group Chat

### 1:1 Chat WebSocket Flow

```
Client A                WebSocket Gateway         Chat Service             Kafka             DB (MongoDB)
   |                           |                       |                     |                    |
1. |--- CONNECT -------------> |                       |                     |                    |
2. |<-- ACK (CONNECTED) ------|                       |                     |                    |
3. |--- AUTH (JWT) ---------->|                       |                     |                    |
4. |<-- AUTH_OK --------------|                       |                     |                    |
5. |--- MESSAGE {to: B} ----->|                       |                     |                    |
6. |                           |-- Validate + Enrich ->|                     |                    |
7. |                           |                       |-- Write to topic -->|                    |
8. |                           |                       |                     |-- Persist msg ---->|
9. |                           |                       |                     |                    |
10.|<-- ACK (msg_id) ---------|                       |                     |                    |
11.|                           |--- Push msg to B ---->|                     |                    |
12.|                           |                       |-- WebSocket to B -->|                    |
```

### Group Chat Flow

```
Client A                WebSocket Gateway         Chat Service           Kafka         DB (MongoDB)     Clients B, C
   |                           |                       |                   |               |               |
1. |--- CONNECT -------------> |                       |                   |               |               |
2. |<-- ACK (CONNECTED) ------|                       |                   |               |               |
3. |--- AUTH (JWT) ---------->|                       |                   |               |               |
4. |<-- AUTH_OK --------------|                       |                   |               |               |
5. |--- MSG {grp_id, msg} --->|                       |                   |               |               |
6. |                           |-- Validate group ID ->|                   |               |               |
7. |                           |                       |-- Pub to topic -->|               |               |
8. |                           |                       |                   |-- Persist --->|               |
9. |                           |                       |                   |               |               |
10.|<-- ACK (msg_id) ---------|                       |                   |               |               |
11.|                           |--- Push to B, C ----->|                   |               |-- WebSocket -->|
```

---

## 12. WebSocket Reconnection + Offline Sync Flow

```
Client A         WebSocket Gateway     Chat Service       Redis     MongoDB     Kafka
  |                    |                   |                |           |          |
1.|--- RECONNECT ----->|                   |                |           |          |
2.|--- AUTH (JWT) ---->|                   |                |           |          |
3.|<-- AUTH_OK --------|                   |                |           |          |
4.|--- SYNC(last_msg) ->|                  |                |           |          |
5.|                    |--- Validate ID -->|                |           |          |
6.|                    |                   |--- Get from -> |           |          |
7.|                    |                   |                |-- Missed msgs ------>|
8.|                    |                   |<-- Messages ---|           |          |
9.|<-- SYNC_RESULT ----|                   |                |           |          |
```

---

## 13. WebSocket Encryption & Security

| Concern         | Strategy |
|-----------------|----------|
| **Transport**   | Use **WSS (WebSocket over TLS)** for all clients |
| **Authentication** | JWT tokens issued at login and passed on connection |
| **Authorization** | Validate if user can send to group/conversation |
| **Rate Limiting** | IP/user-based limits at gateway level |
| **DDoS Protection** | AWS WAF, throttling per user/socket |
| **Replay Attacks** | Use expiring tokens (5-15 min TTL) + signature validation |
| **At Rest**      | AES-256 encryption in MongoDB, Redis, S3 |
| **In Transit**   | TLS 1.2+ for WebSocket and internal services |
| **End-to-End (E2EE)** | Optional: Use **libsodium/NaCl** to encrypt messages client-side |
| **Message Signing** | Optional: HMAC to validate integrity |
| **Audit Logs**   | Kafka topic `audit.logs`, access logs at API gateway |

---

## 📚 References & Best Practices

1. [Tech Interview Handbook – System Design](https://www.techinterviewhandbook.org/system-design/overview/)
2. [Grokking the System Design Interview – Educative.io](https://www.educative.io/courses/grokking-the-system-design-interview)
3. [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
4. [Martin Fowler – Event-Driven Architecture](https://martinfowler.com/articles/201701-event-driven.html)
5. [Martin Fowler – CQRS](https://martinfowler.com/bliki/CQRS.html)
6. [Kafka: The Definitive Guide – Confluent](https://developer.confluent.io/learn/kafka-definitive-guide/)
7. [Slack Engineering Blog – WebSocket at Scale](https://slack.engineering/realtime-messaging-architecture/)
8. [MongoDB Schema Design Best Practices](https://www.mongodb.com/basics/schema-design)
