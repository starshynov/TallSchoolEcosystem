# TallSchoolEcosystem

### Author
### Current Doc Version
- 11-01-2025  version 1.0  Starshynov  Created and structured Spec logic.  
- 11-04/05-2025  version 1.1  Starshynov  Filled main descriptions in document subsections.

---

## 1. Introduction

### Project name
"[SchoolName] Tall Ecosystem"

### Brief description (what the system does and for whom)
A system of supporting applications that simplifies the individual process of learning the Dutch language.  
The system is primarily designed for people learning Dutch as their second language.  
The platform integrates vocabulary study, reading, video lessons, and tests into a single system with automatic progress tracking.

The project represents an educational platform for schools with accelerated learning programs. It combines various content formats — dictionaries, texts, videos, and interactive exercises — into one cohesive system with built-in progress control.  
The platform addresses the issue of fragmented learning sources (textbooks, online tasks, flashcards, video lessons) and content duplication by providing both students and teachers with a unified learning environment.

### Project goal (what problem it solves)
**Accelerated Learning Platform.**  
The system of services simplifies and automates the individual learning process and customizes the school’s unique teaching approach.

The goal is to create a unified, adaptive learning system for gifted students where all elements (vocabulary, reading, tests, videos, exercises) are integrated, and progress is automatically tracked.

**Main users:**
- Students enrolled in the accelerated program.  
- Teachers monitoring progress and adjusting assignments.  
- Educational administrators managing content and access.  
- Technical administrator responsible for infrastructure and security.

### Product History
<details>
  <summary>Initially the project was designed and implemented as an MVP by me. The potential client responded and gave the green light for full-scale development.</summary>

One of the schools launched a new program — an accelerated course for gifted students.  
The program combined several textbooks and online tasks, which made tracking progress difficult and caused duplicate content.  
Managing multiple sources was cumbersome.  

I began by merging the dictionaries. Then, I expanded the system by adding two vocabulary learning modes (thematic flashcards and spaced-repetition cards) and a personal dictionary. Both algorithms were co-developed with the language school client — not copied from existing tools like Anki.  

Next came the Reader service with free-reading and comprehension-test modes featuring automatic answer validation.  
After that, the Video service appeared — online/offline lessons with quiz questions.  

Both the Reader and Vocabulary services later received native Android versions alongside the web version.  
A new service is under development — **interactive exercises (drag-and-drop and input fields)** with automatic validation.
</details>

---

## 2. Overall Architecture

### Architectural approach
<details>
  <summary>Event-Driven Microservices Architecture with Kafka and independent REST APIs.</summary>

The system is built as a collection of independent microservices that communicate asynchronously through Kafka.  
Each service has its own REST API and internal logic and may act as both producer and consumer.  
Most services are bidirectional — they both publish and consume events.
</details>

### Core principles
<details>
  <summary>Asynchronous, scalable, independent, loosely coupled, and API/event version–driven.</summary>

**Core principles:**
- Asynchronous communication between services via Kafka.  
- Independence: each module is deployed and maintained separately.  
- Scalability: services can be independently scaled and updated.  
- Fault isolation: a failure in one service doesn’t halt the system.  
- Extensibility via versioned APIs and Kafka topics.
</details>

### List of microservices and their short responsibilities
<details>
  <summary>Profile, Lobby, Cards, Reader, Video, Admin, PII, and utility services interacting via Kafka.</summary>

- **Profile Service** — registration, login, roles, tokens, and access management.  
- **Lobby Service** — interface router and list of available “products.”  
- **Thematic Cards Service** — topic-based flashcards with a custom repetition algorithm.  
- **Learning Cards Service** — personal vocabulary flashcards with another repetition algorithm.  
- **Reader Service** — free reading and comprehension-test modes.  
- **Listening Service** — video lessons and video quizzes.  
- **Admin Service** — content editing and manual content creation.  
- **PII Service** — isolated storage and encryption of personal data.  
- **Content Utility Services** — supporting modules (analytics, mailings, cron events).
</details>

### System overview / Async flows
<details>
  <summary>Angular, Bun, AWS, and Kafka connect all clients and microservices without a central monolith.</summary>

Mobile (Cordova), desktop (Electron), and web (Angular) clients share a single REST backend built on Bun.  
Communication between microservices occurs via Kafka.  
Data is stored in PostgreSQL (authentication) and MongoDB (content).  
Media (audio, video, images) are stored in AWS S3.

**Key asynchronous flows:**
- User created in Profile Service → `user.created` event → updates in other services.  
- Cards or texts updated → `content.updated` event → client synchronization.  
- Cron events (e.g., weekly cohort updates) → global Kafka system events.
</details>

### Architecture diagram
<details>
  <summary>diagram (optional)</summary>

Overall architecture of microservices and Kafka interactions  
--- DIAGRAM ---
</details>

---

## 3. Technology Stack

### Frontend
<details>
  <summary>Angular + Material + Zustand with Cordova and Electron support.</summary>

**Frontend**
- **Angular (TypeScript)** — main SPA client (web).  
- **Angular Router (@angular/router)** — navigation and routing.  
- **Angular Material** — UI library; all interfaces use its components and styling.  
- **Angular CDK (Drag & Drop)** — interactive online exercises with drag-and-drop and input fields.  
- **Zustand** — client-side state management (role, token, and module state cache).  
- **Electron** — desktop build based on Angular.  
- **Cordova (Android)** — native Android build based on the web client.
</details>

### Backend
<details>
  <summary>Bun with REST API and Event-Driven Architecture (EDA) supporting versioned APIs and events.</summary>

The backend is implemented in **Bun** using **TypeScript**.  
Each microservice exposes its own REST API (e.g., `/api/v1`, `/api/v2`), ensuring independent evolution of versions.  
Architecture follows the **Event-Driven Architecture (EDA)** model — microservices communicate asynchronously via Kafka.  
Most services act as both producers and consumers, providing flexibility.  
Client version is verified at each login: outdated clients receive an update prompt.
</details>

### Messaging
<details>
  <summary>Apache Kafka + Schema Registry ensure event exchange and schema compatibility control.</summary>

**Kafka** is the central message broker between microservices.  
All events are published to versioned topics (`*.v1`, `*.v2`).  
A **Schema Registry** validates and stores schema versions (1, 2, 3, …), maintaining backward/forward compatibility.  
Topics are secured via **SASL_SSL** and ACL access rights.  
System events include content updates, profile changes, and scheduled cron operations.
</details>

### Databases
<details>
  <summary>PostgreSQL for auth, MongoDB for content, AWS S3 for media.</summary>

**PostgreSQL** — authentication/authorization (profile data).  
**MongoDB** — educational content and progress:
- Global dictionary; personal dictionaries;  
- Reader texts: built-in library (by topics/levels) and user-imported texts (separate collections);  
- Comprehension test texts;  
- Video quiz content.

**Caching:**
- **Client:** LocalStorage (Access/Refresh tokens, role).  
- **Server:** In-memory per service (sessions, cycles, weeks, word cohorts, “resume points”).

**Media & Files**
- **AWS S3** — isolated buckets/prefixes for each service (duplicates allowed for independence).  
- *(Optional)* **CloudFront** — CDN for videos/images.  
- **Pre-signed URLs** — temporary access to private media.  
- **Desktop videos** — encrypted and not stored openly inside the app bundle.
</details>

### Deployment, DevOps & Delivery
<details>
  <summary>Docker Compose + AWS</summary>

**GitHub** (mono-org, multi-repo) — separate repos for frontend/backend of each service.  
**CI/CD** — automatic deployment to AWS on push to `main`.  
**Docker Compose** — container build and orchestration.  
**Environments:** local, production (staging planned).
</details>

### Desktop & Mobile
<details>
  <summary>Desktop — Electron</summary>

The desktop version is built using **Electron** on top of the existing Angular frontend.  
It uses the same REST API and databases as the web client.  
Videos in the desktop build are **encrypted** and stored in binary form, excluded from the app archive for security.  
Electron supports offline mode with local progress caching, synchronizing results when back online.
</details>

<details>
  <summary>Mobile — Cordova</summary>

The mobile version is built using **Apache Cordova** based on the same Angular codebase.  
The build is generated in Android Studio, producing a native Android APK.  
Cordova clients access the same REST API and share the same tokens and roles as the web app, ensuring full data synchronization.  

Offline mode with caching is supported, with progress synchronization upon reconnecting.  
In essence, it’s a web application (Angular + TypeScript) packaged as a native Android build — keeping the codebase fully cross-platform.
</details>

### Monitoring
<details>
  <summary>ELK, Prometheus + Grafana</summary>

**Observability stack:**  
- **ELK (Elasticsearch + Logstash + Kibana)** — application/system logs.  
- **Kafka + S3** — event logs (business events, long-term archive).  
- **Prometheus + Grafana** — metrics/traces (latency, throughput, offsets, consumer lag).
</details>

### Security
**Passwords** — Argon2 (hashing).  
**PII** — AES-256-GCM encryption at application level; keys stored in AWS Secrets Manager.  
**JWT** — Access (15 min) + Refresh (7 days), stored in LocalStorage; refresh endpoints available.  
**Transport** — TLS 1.3, AWS VPC private networks, Security Groups.  
**Kafka** — SASL_SSL + certificate authentication.  
**GDPR-ready** — export/delete endpoints, access audit (Kafka events).

## 4. Users and Roles

The role system is centralized and managed by the **Profile Service**.  
After authentication, a user receives a **JWT token** containing their role.  
All services read this role and determine the level of access accordingly.

### Roles
<details>
  <summary>List of Roles (Technical Admin, Educational Admin, Teacher, Student)</summary>

**1. Technical Admin**
- Full control over infrastructure (Kafka, databases, deployment, configurations).  
- Has access to logs, monitoring, and security settings.  
- Can create and delete users.

**2. Educational Admin (Admin-Teacher)**
- Responsible for educational content and user roles.  
- Uploads built-in texts and tests (shared for all).  
- Assigns teachers and students, manages their access.  
- Has access to the administrative interface of the **Admin Service**.

**3. Teacher**
- Has access to their assigned students (teacher ↔ students linkage).  
- Views analytics of their students’ progress, reading and test results.  
- Read-only permissions, cannot edit content.

**4. Student**
- Primary user of the system.  
- Works with cards, reads texts, completes tests.  
- Progress is saved and synchronized across devices.
</details>

### Description of permissions and access zones
<details>
  <summary>Summary</summary>
  (Content to be defined)
</details>

### Authentication and authorization service
<details>
  <summary>(Profile Service, JWT, Argon2, Refresh Tokens)</summary>

**Authentication & Authorization**
- Registration and login handled by **Profile Service**.  
- Passwords are hashed using **Argon2** and stored in PostgreSQL.  
- After successful login, the user receives:
  > Access Token (15 minutes of activity)  
  > Refresh Token (7 days)

- Tokens are stored in LocalStorage and refreshed through dedicated endpoints.  
- Refresh tokens are encrypted using AES-256.  
- Tokens are device- and session-specific.
</details>

**Additionally – Lobby Service**
- The **Lobby Service** is available to all roles after logging in via Profile Service.  
- It acts as an interface router — showing the user which microservices (“products”) are available according to their role.  
- It does not manage roles, only reads them from the JWT.  
- Provides consistent navigation between modules of the system.

---

## 5. Microservice Descriptions

The project uses a **microservice event-driven architecture** with **Kafka** as the message broker, where:
- Each service exchanges events through Kafka;  
- Each service exposes its own API endpoints;  
- Some services act only as producers (sending events);  
- Most are both producers and consumers (sending and processing events);  
- There are user-facing services (Reader, Vocabulary, Video) and utility ones (logging, analytics, mailings, synchronization);  
- Each service stores its own media (images, audio, video, texts) on AWS S3 — independent and isolated.  
  Services do not see each other’s data, allowing for duplication but complete independence.

Each microservice communicates via **publish/subscribe**, ensuring **asynchronicity**, **scalability**, and **loose coupling**.

### Microservice schema
<details>
  <summary>(Examples: Profile, Lobby, Thematic Cards, Learning Cards, Reader, Video Tests, Admin, PII)</summary>

--- DIAGRAM HERE: microservice interaction (events, API, Kafka topics) ---
</details>

#### Profile microservice
<details>
  <summary>Profile – Registration, login, role and token management, and client version control. Central service for authentication and authorization. Stores data in PostgreSQL and publishes `user.created`, `user.updated`, and `user.roleChanged` events to Kafka.</summary>

##### Purpose
Central service for registration, login, role, and token management.

**Functions:**
- Register new users and hash passwords via **Argon2**.  
- Authenticate users and issue Access/Refresh tokens.  
- Store users and roles in PostgreSQL.  
- Provide endpoints for token refresh and client version validation.  
- Publish Kafka events: `user.created`, `user.updated`, `user.roleChanged`.

##### Main API endpoints
*(To be filled)*

##### Kafka events (topics, schemas, version)
**Kafka topics:**
- `user.events.v2`  
- `user.security.v1`

##### Databases and collections used
PostgreSQL (`users`, `roles`, `tokens`).
</details>

#### Lobby microservice
<details>
  <summary>Lobby – Entry point to the system UI that displays available products per user role. After authentication, the user lands here and sees accessible modules. It only routes and caches navigation data.</summary>

##### Purpose
Navigation hub and interface router — single entry point for users after login.

**Functions:**
- Displays available microservices depending on user role.  
- Stores user selection state and navigation context.  
- Fetches available service data via REST from Profile Service.

**Features:**
- Does not manage roles — only visualizes accessible modules.  
- Future plan: evolve into a cross-service route manager.

##### Main API endpoints
*(To be filled)*

##### Kafka events (topics, schemas, version)
**Kafka topics:**
- `ui.navigation.v1` (listens to access-change events)

##### Databases and collections used
*(None)*

##### External integrations (AWS S3, Secrets Manager, etc.)
*(None)*
</details>

#### Thematic Cards microservice
<details>
  <summary>Thematic Cards – Topic-based vocabulary cards with a unique repetition algorithm and learning statistics. Integrates thematic dictionaries and tracks user progress. Synchronizes via Kafka.</summary>

##### Purpose
A vocabulary-learning service organized by topic and difficulty level.  
One of two independent card modules with distinct repetition algorithms.

**Functions:**
- Display cards grouped by topics and difficulty.  
- Use a custom repetition algorithm developed with the language school client.  
- Track topic-level statistics and user progress.  
- Sync with the personal dictionary service.

**In microservice (Bun):**
- Maintains an in-memory cache for temporary data (sessions, cycles, word cohorts).  
- Cache updates automatically via Kafka events or scheduled cron tasks.

##### Main API endpoints
*(To be filled)*

##### Kafka events (topics, schemas, version)
**Kafka topics:**
- `vocab.cards.v1`  
- `vocab.progress.v1`

##### Databases and collections used
MongoDB (`topics`, `words`, `userProgress`).
</details>

#### Learning Cards microservice
<details>
  <summary>Learning Cards – Personal flashcards for word repetition and vocabulary building. Each week a cron job `updateLearningCohorts()` refreshes active words for all users. Publishes progress and schedule events to Kafka.</summary>

##### Purpose
The second vocabulary module — personalized flashcards for repetition and progress tracking.  
Users build personal word lists grouped into weekly cohorts.

**Functions:**
- Create and store personal dictionaries.  
- Assign words to weekly learning cohorts.  
- Update weekly via cron job `updateLearningCohorts()` (every Monday).  
- Support offline caching and later sync.

**In microservice (Bun):**
- Maintains an in-memory cache (cycles, active sessions, cohorts).  
- Auto-updates cache via Kafka or cron tasks.

##### Main API endpoints
*(To be filled)*

##### Kafka events (topics, schemas, version)
**Kafka topics:**
- `learning.cards.v1`  
- `learning.schedule.v1`

**Schemas:** TBD  
**Versioning:** TBD

##### Cron jobs
- `updateLearningCohorts()` — runs weekly (Monday).

##### Databases and collections used
MongoDB (`userWords`, `schedule`).

</details>

#### Reader microservice
<details>
  <summary>Reader – “Reader” service with free-reading and comprehension-test modes. Stores three types of texts: built-in, user-imported, and test-based. Performs automatic answer validation and progress saving.</summary>

##### Purpose
Text reading and comprehension testing module.  
Supports free-reading and quiz modes.

**Functions:**
- Two main modes: free reading and comprehension testing.  
- Stores three types of texts:
  - **Built-in library:** shared texts grouped by topics and levels.  
  - **User-imported texts:** each user can import their own; stored in separate MongoDB databases; cannot be edited or shared, only permanently deleted.  
  - **Test texts:** shared test materials with editable answers.

- Automatic validation by comparing user responses with stored correct answers.

##### Main API endpoints
*(To be filled)*

##### Kafka events (topics, schemas, version)
**Kafka topics:**
- `reader.events.v1`  
- `reader.content.v2`

**Schemas:** TBD  
**Versioning:** TBD

##### Databases and collections used
Two independent MongoDB databases:
- Built-in texts  
- User-imported texts

</details>

#### Listening microservice
<details>
  <summary>Listening (Video Tests) – Interactive videos with quiz questions and automatic result validation. Works with encrypted videos stored on AWS S3. Desktop version uses binary encrypted streams. Publishes results via Kafka.</summary>

##### Purpose
Interactive videos with questions and automated checking.  
Supports encrypted offline playback on desktop.

**Functions:**
- Built-in video player (web) and binary encrypted streams (desktop).  
- Videos hosted on AWS S3 with pre-signed URL access.  
- Desktop version stores encrypted binaries, not open files.  
- Publishes test results and progress events to Kafka.

##### Main API endpoints
*(To be filled)*

##### Kafka events (topics, schemas, version)
**Kafka topics:**
- `video.events.v1`  
- `video.results.v1`

**Schemas:** TBD  
**Versioning:** TBD

##### Databases and collections used
MongoDB (`videos`, `questions`, `results`).

##### External integrations
*(AWS S3, encryption management — TBD)*
</details>

#### Admin microservice
<details>
  <summary>Admin – Content creation and editing interface for administrators. Accessible only to Educational Admins. Publishes `content.updated` events to refresh caches across services.</summary>

##### Purpose
Admin interface for Educational Admins to create and edit educational content.  
Secure access controlled via JWT and “Educational Admin” role.  
Does not access user data — operates solely on content.

**Functions:**
- Create and edit tests, cards, texts.  
- Manage content status and metadata.  
- Provide visual UI for MongoDB modification without direct DB access.  
- Publish updates via Kafka (`content.updated`).

##### Main API endpoints
*(To be filled)*

##### Databases and collections used
MongoDB (`content`, `metadata`).

</details>

#### PII microservice
<details>
  <summary>PII – Isolated storage and encryption of personal data. Stores encrypted fields in PostgreSQL (AES-256-GCM). Manages key rotation via AWS Secrets Manager and publishes access logs to Kafka.</summary>

##### Purpose
Secure storage and encryption of Personal Identifiable Information (PII).  
Handles access logging and GDPR compliance.

**Functions:**
- Isolated storage of name, email, DOB, and other PII fields.  
- AES-256-GCM encryption at Bun application level.  
- Keys stored and rotated every 90 days in AWS Secrets Manager.  
- Access audit logged via Kafka (`pii.access.log`).

##### Main API endpoints
*(To be filled)*

##### Kafka events (topics, schemas, version)
*(To be filled)*

##### Databases and collections used
PostgreSQL (encrypted fields).

##### External integrations
AWS Secrets Manager, Kafka audit logs.
</details>

---

## 6. Integration and Communication
<details>
  <summary>Event-Driven Communication</summary>

All microservices interact through **Apache Kafka** using the **publish/subscribe** pattern.  
Each service has its own topic set and can both publish and consume messages.  
This ensures asynchronous, decoupled communication between components.

**Core rules:**
- All events are structured and validated via **Schema Registry**.  
- Kafka topics are versioned in their names (`user.events.v2`, `reader.content.v1`).  
- Message schemas have internal version history (1, 2, 3 …) in Schema Registry.  
- Compatibility checks are enforced (backward/forward).  
- Kafka operates in **SASL_SSL** mode with certificate-based authentication.

--- DIAGRAM HERE: Kafka event and API flows between services ---
</details>

### Versioning principles
<details>
  <summary>Summary</summary>
  (Content to be defined)
</details>

### API 
<details>
  <summary>/api/v1, /api/v2</summary>

**API versioning**
- Versions defined in URL paths:
/api/v1/...
/api/v2/...

- Old versions remain supported until migration is complete.  
- Version transitions on production occur without downtime.
</details>

### Kafka
<details>
  <summary>Kafka</summary>

- Topic names include version numbers (`user.events.v2`).  
- **Schema Registry** stores historical schemas and ensures compatibility.  
- During transition, a service may publish to both old and new topics simultaneously until clients migrate.
</details>

### Schema Registry 
<details>
  <summary>Schema versions</summary>
  Schema versions
</details>

### Client version checks
<details>
  <summary>Client version checks</summary>

- Each client (web, mobile, desktop) sends its version with API requests.  
- If outdated and incompatible, the user is prompted to update.  
- When content updates occur centrally, users are notified that progress might be affected.
</details>

### Scheduled tasks and cron jobs (e.g., weekly cohort updates)
<details>
  <summary>Summary</summary>

- Backend cron jobs handle global updates:
  > Example — weekly vocabulary cohort refresh (`updateLearningCohorts()`), executed on Mondays.  
  > Cron jobs publish system events to Kafka (`system.cron.weekly`).

- Utility system events (errors, schema updates, broker states) are published to system topics (`system.events.v1`).
</details>

---

## 7. Data and Storage

**Overall data structure**
The system uses a hybrid model:
- **PostgreSQL** for structured and sensitive data (profiles, tokens, roles, PII).  
- **MongoDB** for flexible educational data (dictionaries, texts, tests, progress).  
- **AWS S3** for media storage (audio, video, images, files).  
- **Kafka** for event logs and inter-service communication.

### General data model
<details>
  <summary>Data storage structure</summary>

--- DIAGRAM HERE: data structure (PostgreSQL, MongoDB, AWS S3, Kafka) ---
</details>

### PostgreSQL / MongoDB
<details>
  <summary>Division between PostgreSQL / MongoDB</summary>

**PostgreSQL**
- Used for authentication and personal data.  
- Tables: `users`, `roles`, `tokens`, `pii_encrypted`.  
- Passwords hashed with **Argon2**.  
- PII encrypted with **AES-256-GCM** (app-level).  
- Keys stored and rotated every 90 days in AWS Secrets Manager.

**MongoDB**
- Used for dynamic and content data.  
- Each service owns its collections and does not access others’.

Examples:
- Thematic Cards Service — `topics`, `words`, `userProgress`.  
- Learning Cards Service — `userWords`, `schedule`.  
- Reader Service — built-in texts, user-imported texts, test texts.  
- Video Tests Service — `videos`, `questions`, `results`.  
- Admin Service — `content`, `metadata`.

Mongo stores texts, test results, and links to S3 media.
</details>

### AWS S3 storage per service
<details>
  <summary>Each microservice has its isolated prefix or bucket:</summary>

```
app-media/
├── cards/
├── reader/
├── video-tests/
├── profile/
```


**Main rules:**
- Services cannot access other services’ media.  
- Duplication allowed if shared resources are needed.  
- **Audio:** .mp3 or .ogg, lossless compression, access via pre-signed URLs.  
- **Video:** .mp4 (H.264/AAC), CDN via CloudFront, archived copies stored in Glacier.  
- **Images:** .webp / .avif, auto-generated thumbnails via AWS Lambda.  
- **Texts:** JSON / Markdown files in S3 (if not stored in Mongo).  
- **Desktop videos:** encrypted binary form, not open files.
</details>

### Caching 
<details>
  <summary>LocalStorage, per-service in-memory cache</summary>

**Client-side:**
- LocalStorage keeps Access/Refresh tokens, role, and last session data.

**Server-side (per service):**
- In-memory cache stores active sessions, cycles, word cohorts, progress.  
- Allows the user to resume from their last saved state.  
- Cache updates via Kafka events or cron jobs.
</details>

### Session state and progress restoration
<details>
  <summary>Session state and progress restoration</summary>

- After login (Profile Service), user lands in **Lobby**.  
- Upon entering a microservice, system restores saved state (learning context, progress).  
- Cache updates on logout or when switching between services.
</details>

---

## 8. Security and PII

### Isolation of personal data
<details>
  <summary>PII Service</summary>
  PII Service
</details>

### Encryption
<details>
  <summary>AES-256-GCM, keys stored in AWS Secrets Manager</summary>
  
  - AES-256-GCM
  - Keys stored in AWS Secrets Manager
</details>

### Password hashing: 
<details>
  <summary>Argon2</summary>
  Argon2
</details>

### Token-based authorization
<details>
  <summary>Access + Refresh tokens</summary>
  Access + Refresh tokens
</details>

### Key rotation and access audit
<details>
  <summary>Kafka pii.access.log</summary>
  Kafka pii.access.log
</details>

### GDPR compliance
<details>
  <summary>Export/delete endpoints, user.pii.updated</summary>

**GDPR Compliance**
- The **PII Service** provides full GDPR-compliant data management:
  - `/export` — returns a downloadable, encrypted JSON package with all user data.  
  - `/delete` — performs hard deletion of all user-related PII from PostgreSQL and cached stores.  
- Each action generates corresponding Kafka events:
  - `user.pii.exported` — when the export is completed.  
  - `user.pii.deleted` — when personal data has been permanently removed.  
- All events are archived in Kafka for compliance audits (`pii.audit.v1`).  
- Users receive confirmation through the client application once the process completes.
</details>

## 9. Logging and Monitoring

**Goals and Principles**
- Complete traceability of events and errors across all services.  
- Unified JSON log format with required fields:  
  `timestamp`, `service`, `env`, `version`, `level`, `correlationId`, `userId?`  
- Exclude PII from logs (mask emails/phones).  
  For access to PII, a separate audit topic is used.

### Application logs 
<details>
  <summary>ELK</summary>

**Content:** errors, `console.error`, timings, stack traces, key business steps.  
**Collection and delivery:** Filebeat/Fluent Bit from containers → Logstash → Elasticsearch.  
**Indexing:** `app-logs-{service}-{env}-{yyyy.MM.dd}` (rotation by date).  
**Visualization:** Kibana dashboards — error trends, endpoint latency, retry frequency.  
**Retention:** 30–90 days depending on service criticality.
</details>

### Event logs 
<details>
  <summary>→ Kafka + S3 archive</summary>

**Content:** business events (`user.created`, `content.updated`, `test.completed`).  
**Flow:** producers write to versioned topics (*.v1/2), archiver-consumer writes to S3.  
**Schemas:** Schema Registry ensures backward/forward compatibility.  
**Archival:** S3 with lifecycle policy (cold storage/Glacier).  
**Retention:** active topics — Kafka policy; S3 archive — 180–365 days.
</details>

### System logs
<details>
  <summary>→ ELK</summary>

**Sources:** Kafka brokers, ZooKeeper (if used), Docker/Container Runtime, OS, reverse proxy/ingress.  
**Purpose:** infrastructure diagnostics, capacity, broker/disk/network failures.  
**Retention:** 30–90 days (depending on SLA).
</details>

### Metrics / traces 
<details>
  <summary>→ Prometheus + Grafana</summary>

**Application metrics:** latency, RPS/throughput, error rate, DB pool connections, cron queues.  
**Kafka metrics:** offsets, consumer lag, ISR, under-replicated partitions, broker disk usage.

**Exporters:**
- HTTP metrics from Bun (`/metrics` in Prometheus format)  
- JMX Exporter for Kafka  
- Node Exporter for hosts/nodes  
- Custom metrics for cron jobs (`job.last_run_time`, `job.status`)

**Visualization:** Grafana dashboards per service and a platform overview.  
**Alerts:** latency, error rate, consumer lag, CPU/memory usage (details in section 12).
</details>

---

## 10. DevOps and CI/CD

**General strategy**  
The project follows Infrastructure as Code (IaC) and full CI/CD principles via GitHub and Docker Compose.  
Each microservice is fully isolated — with separate frontend/backend repos, pipelines, and independent AWS deployments.

### GitHub repositories 
<details>
  <summary>Separate for each service, frontend/backend</summary>

- **Organization:** GitHub (mono-org, multi-repo).  
- Each microservice has 2 repositories:
  > `<service>-frontend`  
  > `<service>-backend`
- Repos share the same folder structure and CI workflow templates (GitHub Actions).  
- Main branch: `main` → triggers production deployment.
</details>

### CI 
<details>
  <summary>Continuous Integration</summary>

- On every push or pull request:
  - Run unit and integration tests (**Vitest**, **Jest**, **Testcontainers**).  
  - Linting and formatting checks (**ESLint**, **Prettier**).  
  - Kafka schema validation via **Schema Registry**.  
  - Docker container build and smoke tests.  
- PR stage produces build artifacts and GitHub Checks summary report.  
- Merge is blocked if tests fail.
</details>

### CD 
<details>
  <summary>Continuous Deployment</summary>

- Automatic deployment after merge to `main`.  
- **Docker Compose** and **AWS ECS/EC2** manage container execution.  
- Each microservice deploys independently without dependency on others.  
- Secrets and environment variables are loaded from **AWS Secrets Manager**.  
- Rolling container updates — zero downtime.  
- Deployment logs available in CI Dashboard (GitHub Actions + AWS CLI logs).
</details>

### Environments
<details>
  <summary>local / production</summary>

- **Local** — full development environment (Docker Compose launches Kafka, Mongo, Postgres, and services).  
- **Production** — main AWS deployment.  
- **Staging** (planned) — for pre-release testing and QA.
</details>

### Docker Compose and AWS deployment
<details>
  <summary>Kafka, MongoDB, and PostgreSQL spin up locally during testing</summary>

- Each service has its own `docker-compose.yml` and a shared CI template.  
- Kafka, MongoDB, and PostgreSQL spin up locally during testing.  
- Multi-stage builds optimize Bun and Angular image sizes.  
- Network aliases ensure inter-service communication in dev mode.
</details>

### Artifacts and versioning
<details>
  <summary>Version updates synchronized via Kafka topic</summary>

- Each deployment is tagged (`vX.Y.Z`) and logged in **Changelog**.  
- Version updates synchronized via Kafka topic `system.version.updated`.  
- Backward compatibility maintained for minor versions.
</details>

---

## 11. Testing and Quality

**General principles**  
Testing covers all layers — from unit to end-to-end scenarios.  
Goal: ensure microservice stability and prevent breaking event or API contracts.  
All automated tests run via GitHub Actions during commits and before deployment.

### Unit tests 
<details>
  <summary>Verify individual functions, components, and classes. (Vitest, Jest)</summary>

**Frontend (Angular)**
- Framework: **Jest** (replaces Karma).  
- Coverage: components, services, pipes, Zustand stores.  
- Mocking: `HttpClientTestingModule`.

**Backend (Bun)**
- Framework: **Vitest** (native Bun support).  
- Mocking via `vi.fn()` or `sinon`.  
- Tests business logic, validation, cron jobs, Kafka event generation.

✅ Runs on every commit and PR.
</details>

### Integration tests 
<details>
  <summary>Validate inter-service interaction and API correctness. (Testcontainers, Supertest)</summary>

- Uses **Testcontainers (NodeJS SDK)** to spin up Kafka, MongoDB, PostgreSQL dynamically.  
- Each service runs inside a container with test configuration.  
- Scenarios:
  - Create user → `user.created` → processed by Reader/Card Service.  
  - Update content → `content.updated` → client cache synchronization.  
- Tools: **Supertest**, **Vitest**, **Docker Compose (dev)**.
</details>

### End-to-End tests 
<details>
  <summary>Simulate real user behavior. (Playwright)</summary>

- Framework: **Playwright** (Angular-friendly, cross-browser).  
- Tests core flows:
  - Registration/login  
  - Flashcard and reading usage  
  - Video test completion  
  - State restoration after logout  
- Supports headless and CI modes.  
- Artifacts (videos, screenshots) saved in CI pipeline.
</details>

### Contract tests 
<details>
  <summary>Ensure compatibility of Kafka events and APIs. (Pact + Schema Registry)</summary>

- Tools: **Pact**, **Schema Registry**.  
- Producers publish JSON/AVSC contracts; consumers validate them.  
- Schema mismatches block merges or deployments.  
- Applied to topics: `user.events`, `content.updated`, `video.results`.
</details>

### Load & Stress Tests
<details>
  <summary>Check performance and stability. (k6, Kafka performance tools)</summary>

- Tools:
  - **k6** — REST API load testing for Bun.  
  - **Kafka Performance Tool** — throughput, consumer lag, latency.  
  - **Prometheus + Grafana** — visualization.  
- Executed weekly via CI cron job.
</details>

### Static Analysis & Quality Gates
<details>
  <summary>GitHub Actions</summary>

- **ESLint**, **Prettier** — syntax and formatting checks.  
- **TypeScript strict mode** enforced.  
- **SonarQube / CodeQL** — detect vulnerabilities, duplicates, dead code.  
- **Coverage reports** collected in CI (min. 85% threshold).
</details>

### CI test integration 
<details>
  <summary>GitHub Actions</summary>

- GitHub Actions triggers:
  - Unit + integration → on PR  
  - E2E → on merge to main  
  - Contract + load → nightly builds  
- Failed tests block deployment.  
- Reports sent to GitHub Checks and Slack.
</details>

### Manual QA (in development)
<details>
  <summary>GitHub Actions</summary>

- Staging environment planned for visual regression testing.  
- **BackstopJS** will compare UI snapshots.
</details>

---

## 12. Monitoring and Incident Response

**Monitoring Overview**  
Monitoring is powered by **Prometheus + Grafana + ELK**, with alert delivery to Slack and Email.  
It tracks infrastructure (Kafka, DBs, containers), app performance, and user activity.  
All data is aggregated into centralized dashboards accessible to the Technical Admin.

### Metrics tracked 
<details>
  <summary>Prometheus (latency, throughput, consumer lag)</summary>

- Each Bun microservice exposes `/metrics` in Prometheus format (latency, error rate, RPS, queue size).  
- Additional exporters:
  - **Kafka JMX Exporter** — broker metrics, consumer lag, ISR, offset drift.  
  - **Node Exporter** — CPU, RAM, Disk I/O, Network.  
  - **Postgres/Mongo Exporters** — connections, query rates, replication.  
- Metrics scraped every 15–30s.  
- Cron job metrics: `job.last_run_time`, `job.status`.
</details>

### Alerting 
<details>
  <summary>(Grafana Alerts / Slack / Email)</summary>

**Dashboard categories:**
- **System** — Kafka, brokers, disks, CPU, Docker.  
- **Application** — API latency, throughput, error rate, consumer lag.  
- **User Activity** — requests per microservice, engagement.

**Visualizations:** Kafka flow graphs, consumer lag timelines.  
**Alerts:**
- CPU/RAM > 80%  
- Latency increase > 200%  
- Consumer lag > 1000 messages  
- Deployment or container failure  

**Notifications:** Slack and Email (Technical Admin).
</details>

### ELK Stack
<details>
  <summary>Application and system logs</summary>

**Purpose:** Track application/infrastructure errors.  
**Kibana Dashboards:**
- Error heatmaps per service  
- Endpoint latency charts  
- Kafka broker error logs  
Periodic analysis identifies spikes and causes (stack traces, correlationId).
</details>

---

## 13. Roadmap and Future Development

- **11-04-2025** — MVP Version, Demo and Approval  
- **11-04-2025** — Version 1.0.0  
- **(Future)** — Version 1.0.0: online exercises service (drag-and-drop and input fields) with auto-validation.  

The exercise module is built using **Angular CDK**, the official Angular team toolkit for drag-and-drop.  
CDK provides built-in directives and APIs for DnD functionality without third-party libraries, maintaining full integration with Angular Material and overall project architecture.
