# TallSchoolEcosystem

### Author
### Current Doc Version

## 1. Introduction

### Project name

### Brief description (what the system does and for whom)

### Project goal (what problem it solves)

### Product Hystory
<details>
  <summary>Изначально проект был придумал и оформлен как MVP с моей стороны. Потенциаьлный заказчик откликнулся и дал зеленый свет полноценной разработке</summary>
  Здесь описание первой темы.
</details>
---

## 2. Overall Architecture

### Architectural approach: Event-Driven Microservices Architecture

### Core principles (asynchronous, independent, scalable)

### List of microservices and their responsibilities

### Architecture diagram (optional)

---

## 3. Technology Stack

### Frontend: Angular, Angular Material, Zustand

### Backend: Bun, REST API

### Messaging: Kafka + Schema Registry

### Databases: PostgreSQL (auth), MongoDB (content)

### Deployment: Docker Compose + AWS

### Desktop & Mobile: Electron / Cordova

### Monitoring: ELK, Prometheus + Grafana

---

## 4. Users and Roles

### List of roles (Technical Admin, Educational Admin, Teacher, Student)

### Description of permissions and access zones

### Authentication and authorization service (Profile Service, JWT, Argon2, Refresh Tokens)

---

## 5. Microservice Descriptions

### For each microservice:

### Name and purpose

### Main API endpoints

### Kafka events (topics, schemas, version)

### Databases and collections used

### External integrations (AWS S3, Secrets Manager, etc.)

(Examples: Profile, Lobby, Thematic Cards, Learning Cards, Reader, Video Tests, Admin, PII)

---

## 6. Integration and Communication

### Data exchange mechanisms (Kafka topics)

### Versioning principles:

### API (/api/v1, /api/v2)

### Kafka topics (user.events.v2)

### Schema Registry (schema versions)

### Client version checks

### Scheduled tasks and cron jobs (e.g., weekly cohort updates)

---

## 7. Data and Storage

### General data model

### Division between PostgreSQL / MongoDB

### AWS S3 storage per service

### Media storage (audio, video, images, texts)

### Caching (LocalStorage, per-service in-memory cache)

### Session state and progress restoration

---

## 8. Security and PII

### Isolation of personal data (PII Service)

### Encryption: AES-256-GCM, keys stored in AWS Secrets Manager

### Password hashing: Argon2

### Token-based authorization (Access + Refresh tokens)

### Key rotation and access audit (Kafka pii.access.log)

### GDPR compliance: export/delete endpoints, user.pii.updated event

---

## 9. Logging and Monitoring

### Application logs → ELK

### Event logs → Kafka + S3 archive

### System logs → ELK

### Metrics / traces → Prometheus + Grafana

### Log retention and storage policies

---

## 10. DevOps and CI/CD

### GitHub repositories (separate for each service, frontend/backend)

### Auto-deployment on push to main

### Docker Compose and AWS deployment

### Environments: local / production

### Planned addition of dev / staging environments

---

## 11. Testing and Quality

### Unit tests (Vitest, Jest)

### Integration tests (Testcontainers, Supertest)

### End-to-End tests (Playwright)

### Contract tests (Pact + Schema Registry)

### Load testing (k6, Kafka performance tools)

### CI test integration (GitHub Actions)

---

## 12. Monitoring and Incident Response

### Metrics tracked (latency, throughput, consumer lag)

### Alerting (Grafana Alerts / Slack / Email)

### Failure response scenarios (rollback, redeploy)

---

## 13. Roadmap and Future Development

### Transition to staging environment

### Introduction of a centralized PII service

### Automated progress analytics

### Auto-scaling (AWS ECS / Kubernetes)

### Offline-first support
