# TallSchoolEcosystem

### Author
### Current Doc Version
- 11-01-2025  version 1.0  Starshynov  Created and structured Spec logic.
- 11-04/05-2025  version 1.1  Starshynov  Filled main descripiton in subsections of the document.

## 1. Introduction

### Project name
"[SchoolName] Tall Ecosystem"
### Brief description (what the system does and for whom)
Система вспомогательных приложений, упрощающая индивидуальный процесс обучения Голландскому языку. Система предназначеня в первую очередь для людей, кто изучает Голландский как свой второй язык. 

### Project goal (what problem it solves)
Система сервисов упрощает, автоматизирует индивидуальный процесс обучения и кастомизирует уникальный школьный подход к процессу передачи знаний.

### Product Hystory
<details>
  <summary>Изначально проект был придумал и оформлен как MVP с моей стороны. Потенциальный заказчик откликнулся и дал зеленый свет полноценной разработке</summary>
Одна из школ создала новую программу – ускоренный курс изучения для способных учеников. Программа состояла из комбинации нескольких учебников и он-лайн заданий, что вызывало сложность контроля и содержало повторы. Было сложно и следить-хранить несколько источников. Я объединил сначала словари. Потом расширил систему добавив два способа заучивания слов(карточки по темам и карточки повторения), добавил индивидуальный словарь. Далее появился сервис Читалки с режимом свободного чтения и Теста на понимание с автопроверкой ответов. Следующий сервис это он-лайн/оффлайн видео с вопросами. Сервиса Читалки и Изучения слов получили в дополнение к веб-версии и нативную Андроид версию. В процессе разработки сервис он-лайн упражнений (драг-н-дроп и инпут поля) с автопроверкой.   
</details>

---

## 2. Overall Architecture

### Architectural approach: 
<details>
  <summary>Event-Driven Microservices Architecture</summary>
  Здесь описание первой темы.
</details>

### Core principles
<details>
  <summary> asynchronous, independent, scalable</summary>
  Здесь описание первой темы.
</details>

### List of microservices and their short responsibilities
<details>
  <summary>Саммари. Больше о каждом микросервисе в соответствующем разделе</summary>
  Здесь описание первой темы.
</details>

### Architecture diagram
<details>
  <summary>diagram (optional)</summary>
  Здесь описание первой темы.
</details>

---

## 3. Technology Stack

### Frontend
<details>
  <summary>Angular, Angular Material, Zustand</summary>
  Здесь описание первой темы.
</details>

### Backend
<details>
  <summary>Bun, REST API</summary>
  Здесь описание первой темы.
</details>

### Messaging
<details>
  <summary>Kafka + Schema Registry</summary>
  Здесь описание первой темы.
</details>

### Databases
<details>
  <summary>PostgreSQL (auth), MongoDB (content)</summary>
  Здесь описание первой темы.
</details>

### Deployment 
<details>
  <summary>Docker Compose + AWS</summary>
  Здесь описание первой темы.
</details>

### Desktop & Mobile: 
<details>
  <summary>Desktop - Electron</summary>
  Здесь описание первой темы.
</details>
<details>
  <summary>Mobile - Cordova</summary>
  Здесь описание первой темы.
</details>

### Monitoring 
<details>
  <summary>ELK, Prometheus + Grafana</summary>
  Здесь описание первой темы.
</details>

---

## 4. Users and Roles
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Roles 
<details>
  <summary>List of Roles(Technical Admin, Educational Admin, Teacher, Student)</summary>
  Здесь описание первой темы.
</details>

### Description of permissions and access zones
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Authentication and authorization service 
<details>
  <summary>(Profile Service, JWT, Argon2, Refresh Tokens)</summary>
  Здесь описание первой темы.
</details>

---

## 5. Microservice Descriptions
  Здесь описание первой темы.

### microservice schema
<details>
  <summary>(Examples: Profile, Lobby, Thematic Cards, Learning Cards, Reader, Video Tests, Admin, PII)</summary>
  Здесь описание первой темы.
</details>

#### Profile microservice.
<details>
  <summary>Profile Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>

#### Lobby microservice.
<details>
  <summary>Lobby Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>

#### Thematic Cards microservice.
<details>
  <summary>Thematic Cards Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>

#### Learning Cards microservice.
<details>
  <summary>Learning Cards Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>

#### Reader microservice.
<details>
  <summary>Reader Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>

#### Listening microservice.
<details>
  <summary>Listening (Video Tests) Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>

#### Admin microservice.
<details>
  <summary>Admin Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>

#### PII microservice.
<details>
  <summary>PII Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>

#### For microservice.
<details>
  <summary>Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>

#### For each microservice:
<details>
  <summary>Саммари</summary>
##### Name and purpose
 Здесь описание первой темы.
##### Main API endpoints
 Здесь описание первой темы.
##### Kafka events (topics, schemas, version)
 Здесь описание первой темы.
##### Databases and collections used
 Здесь описание первой темы.
##### External integrations (AWS S3, Secrets Manager, etc.)
</details>
---

## 6. Integration and Communication
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Data exchange mechanisms (Kafka topics)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Versioning principles:
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### API (/api/v1, /api/v2)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Kafka topics (user.events.v2)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Schema Registry (schema versions)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Client version checks
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Scheduled tasks and cron jobs (e.g., weekly cohort updates)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

---

## 7. Data and Storage
  Здесь описание первой темы.

### General data model
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Division between PostgreSQL / MongoDB
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### AWS S3 storage per service
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Media storage (audio, video, images, texts)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Caching (LocalStorage, per-service in-memory cache)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Session state and progress restoration
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

---

## 8. Security and PII
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Isolation of personal data (PII Service)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Encryption: AES-256-GCM, keys stored in AWS Secrets Manager
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Password hashing: Argon2
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Token-based authorization 
<details>
  <summary>(Access + Refresh tokens)</summary>
  Здесь описание первой темы.
</details>

### Key rotation and access audit 
<details>
  <summary>(Kafka pii.access.log)</summary>
  Здесь описание первой темы.
</details>

### GDPR compliance
<details>
  <summary>export/delete endpoints, user.pii.updated event</summary>
  Здесь описание первой темы.
</details>

---

## 9. Logging and Monitoring

### Application logs 
<details>
  <summary>→ ELK</summary>
  Здесь описание первой темы.
</details>

### Event logs 
<details>
  <summary>→ Kafka + S3 archive</summary>
  Здесь описание первой темы.
</details>

### System logs
<details>
  <summary> → ELK</summary>
  Здесь описание первой темы.
</details>

### Metrics / traces 
<details>
  <summary>→ Prometheus + Grafana</summary>
  Здесь описание первой темы.
</details>

### Log retention and storage policies
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

---

## 10. DevOps and CI/CD

### GitHub repositories (separate for each service, frontend/backend)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Auto-deployment on push to main
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Docker Compose and AWS deployment
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Environments: local / production
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Planned addition of dev / staging environments
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

---

## 11. Testing and Quality

### Unit tests 
<details>
  <summary>(Vitest, Jest)</summary>
  Здесь описание первой темы.
</details>

### Integration tests 
<details>
  <summary>(Testcontainers, Supertest)</summary>
  Здесь описание первой темы.
</details>

### End-to-End tests 
<details>
  <summary>(Playwright)</summary>
  Здесь описание первой темы.
</details>

### Contract tests 
<details>
  <summary>(Pact + Schema Registry)</summary>
  Здесь описание первой темы.
</details>

### Load testing 
<details>
  <summary>(k6, Kafka performance tools)</summary>
  Здесь описание первой темы.
</details>

### CI test integration 
<details>
  <summary>(GitHub Actions)</summary>
  Здесь описание первой темы.
</details>

---

## 12. Monitoring and Incident Response

### Metrics tracked 
<details>
  <summary>(latency, throughput, consumer lag)</summary>
  Здесь описание первой темы.
</details>

### Alerting 
<details>
  <summary>(Grafana Alerts / Slack / Email)</summary>
  Здесь описание первой темы.
</details>

### Failure response scenarios 
<details>
  <summary>(rollback, redeploy)</summary>
  Здесь описание первой темы.
</details>

---

## 13. Roadmap and Future Development
Здесь описание первой темы.

### Transition to staging environment
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Introduction of a centralized PII service
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Automated progress analytics
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Auto-scaling 
<details>
  <summary>(AWS ECS / Kubernetes)</summary>
  Здесь описание первой темы.
</details>

### Offline-first support
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

