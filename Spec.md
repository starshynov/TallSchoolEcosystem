# TallSchoolEcosystem

### Author
### Current Doc Version
- 11-01-2025  version 1.0  Starshynov  Created and structured Spec logic.
- 11-04/05-2025  version 1.1  Starshynov  Filled main descripiton in subsections of the document.

## 1. Introduction

### Project name
"[SchoolName] Tall Ecosystem"
### Brief description (what the system does and for whom)
Система вспомогательных приложений, упрощающая индивидуальный процесс обучения Голландскому языку. Система предназначеня в первую очередь для людей, кто изучает Голландский как свой второй язык. Платформа объединяет обучение по словарям, чтению, видео и тестам в единую систему с автоматическим контролем прогресса.
Проект представляет собой образовательную платформу для школ с ускоренной программой обучения. Она объединяет различные форматы контента — словари, тексты, видео и интерактивные упражнения — в единую систему с контролем прогресса. Платформа решает проблему раздробленности источников (учебники, онлайн-задания, карточки, видеоуроки) и дублирования материалов, предоставляя ученикам и преподавателям единое пространство для обучения.

### Project goal (what problem it solves)
Accelerated Learning Platform. Система сервисов упрощает, автоматизирует индивидуальный процесс обучения и кастомизирует уникальный школьный подход к процессу передачи знаний.
Создать единую адаптивную систему обучения для способных учеников, где все элементы (лексика, чтение, тесты, видео, упражнения) интегрированы, а прогресс отслеживается автоматически.
Main users:
- Ученики, проходящие программу ускоренного обучения.
- Учителя, отслеживающие результаты и корректирующие задания.
- Учебные администраторы, управляющие контентом и доступами.
- Технический администратор, отвечающий за инфраструктуру и безопасность.
- 
### Product Hystory
<details>
  <summary>Изначально проект был придумал и оформлен как MVP с моей стороны. Потенциальный заказчик откликнулся и дал зеленый свет полноценной разработке</summary>
Одна из школ создала новую программу – ускоренный курс изучения для способных учеников. Программа состояла из комбинации нескольких учебников и он-лайн заданий, что вызывало сложность контроля и содержало повторы. Было сложно и следить-хранить несколько источников. Я объединил сначала словари. Потом расширил систему добавив два способа заучивания слов(карточки по темам и карточки повторения), добавил индивидуальный словарь. Далее появился сервис Читалки с режимом свободного чтения и Теста на понимание с автопроверкой ответов. Следующий сервис это он-лайн/оффлайн видео с вопросами. Сервиса Читалки и Изучения слов получили в дополнение к веб-версии и нативную Андроид версию. В процессе разработки сервис он-лайн упражнений (драг-н-дроп и инпут поля) с автопроверкой.   
</details>

---

## 2. Overall Architecture

### Architectural approach: 
<details>
  <summary>Микросервисная событийно-ориентированная архитектура с Kafka и независимыми REST API.</summary>
Event-Driven Microservices Architecture (событийно-ориентированная микросервисная архитектура). Система построена как набор независимых микросервисов, обменивающихся событиями через Kafka. Каждый сервис имеет собственные API-эндпоинты и внутреннюю логику, может быть как producer, так и consumer. Большинство сервисов двунаправленные: они публикуют и обрабатывают события.
</details>

### Core principles
<details>
  <summary> Асинхронность, масштабируемость, независимость сервисов, слабая связанность и расширяемость через версии API и событий. (asynchronous, independent, scalable)</summary>
  **Core principles:**
- Асинхронное взаимодействие между сервисами через Kafka.
- Независимость модулей: каждый деплоится отдельно и имеет собственный код.
- Масштабируемость: можно разворачивать и обновлять сервисы независимо.
- Fault isolation: сбой одного микросервиса не останавливает систему.
- Расширяемость через версии API и Kafka topics.
</details>

### List of microservices and their short responsibilities
<details>
  <summary>Профиль, Лобби, Карточки, Читалка, Видео, Админ, PII и служебные сервисы, взаимодействующие через Kafka. Больше о каждом микросервисе в соответствующем разделе</summary>
- Profile Service — регистрация, логин, управление ролями, токенами и доступом.
- Lobby Service — интерфейсная маршрутизация и список доступных “продуктов”.
- Thematic Cards Service — карточки по темам с собственным алгоритмом повторения.
- Learning Cards Service — карточки для изучения со вторым алгоритмом повторения.
- Reader Service — режимы свободного чтения и тестов на понимание.
- Listening Service — видеоуроки и видео-тесты с вопросами.
- Admin Service — редактирование и ручное создание контента.
- PII Service — отдельный сервис для хранения и шифрования персональных данных.
- Content Utility Services — служебные модули (аналитика, рассылки, cron-события).
</details>

### System overview / Async flows
<details>
  <summary>Angular, Bun, AWS и Kafka обеспечивают связность между клиентами и микросервисами без центрального монолита.</summary>
Мобильное (Cordova), десктопное (Electron) и веб-приложение (Angular) используют единый REST-бэк на Bun./
Коммуникация между микросервисами — через Kafka./
Все данные хранятся в PostgreSQL (аутентификация) и MongoDB (контент)./
Мультимедиа (аудио, видео, изображения) — на AWS S3./

**Key asynchronous flows:**
- Пользователь создаётся в Profile Service → событие user.created → обновление других сервисов.
- Обновление карточек или текстов → событие content.updated → синхронизация у всех клиентов.
- Cron-события (например, еженедельное обновление когорт) → глобальные Kafka-ивенты.
</details>

### Architecture diagram
<details>
  <summary>diagram (optional)</summary>
Общая архитектура микросервисов и Kafka-взаимодействий
  ``` 
  --- СХЕМА ---
  ```
</details>

---

## 3. Technology Stack

### Frontend
<details>
  <summary>Angular + Material + Zustand с поддержкой Cordova и Electron.</summary>
**Frontend**
- Angular (TypeScript) — основной SPA-клиент (web).
- Angular Router (@angular/router) — маршрутизация.
- Angular Material — UI-компоненты.
- Angular CDK (Drag & Drop) — онлайн-упражнения с перетаскиванием и input-полями.
- Zustand — стейт-менеджмент на клиенте (кеш ролей, токенов, состояний модулей).
- Electron — десктопная сборка на базе Angular.
- Cordova (Android) — нативный Android-билд на базе веб-клиента.
</details>

### Backend
<details> 
<summary>Bun с REST API и событийной архитектурой (EDA) с поддержкой версионирования API и событий.</summary> 
Бэкенд реализован на <b>Bun</b> с использованием TypeScript. Каждый микросервис имеет собственный REST API с маршрутами вида <code>/api/v1</code>, <code>/api/v2</code>, что обеспечивает независимую эволюцию версий. Архитектура построена по принципу <b>Event-Driven Architecture (EDA)</b> — микросервисы взаимодействуют асинхронно через события, публикуемые и обрабатываемые в Kafka. Большинство сервисов являются одновременно producer и consumer, что повышает гибкость системы. Проверка версии клиента выполняется при каждой авторизации: если клиент не поддерживает новые функции, ему показывается сообщение о необходимости обновления. 
</details>

### Messaging
<details> 
<summary>Apache Kafka + Schema Registry обеспечивают обмен событиями и контроль совместимости схем.</summary> 
<b>Kafka</b> выступает центральным брокером сообщений между микросервисами. Все события публикуются в версионированные топики (<code>*.v1</code>, <code>*.v2</code>). Для проверки структур сообщений используется <b>Schema Registry</b>, где хранится история версий (1, 2, 3 …) и обеспечивается совместимость <i>backward/forward</i>. Топики защищены через <b>SASL_SSL</b> и ACL-права доступа. В системе также используются служебные события — обновления контента, изменения профиля и плановые cron-события. 
</details>

### Databases
<details>
  <summary>PostgreSQL для авторизации, MongoDB для контента, AWS S3 для мультимедиа.</summary>
**PostgreSQL** — аутентификация/авторизация (данные профиля).
**MongoDB** — учебный контент и прогресс:
- Глобальный словарь; Индивидуальные словари;
- Тексты читалки: встроенная библиотека (по темам/уровням) и пользовательские тексты (отдельные базы/коллекции);
- Тексты для тестового режима;
- Тесты к видео.
**Кэширование:**
- Клиент: LocalStorage (Access/Refresh токены, роль).
- Сервер: in-memory per service (сессии, циклы, недели, когорты слов, «точка продолжения» для быстрого возврата в сохранённое состояние).

**Media & Files**
**AWS S3** — хранение медиа по сервисам (изолированные бакеты/префиксы; допускаются дубли для независимости).
(Опционально) **CloudFront** — CDN для видео/изображений.
**Pre-signed URLs** — временный доступ к приватным медиа.
**Видео в десктоп-версии** — зашифрованы и не лежат «в открытую» в архиве приложения.
</details>

### Deployment, DevOps & Delivery
<details>
  <summary>Docker Compose + AWS</summary>
**GitHub** (mono-org, multi-repo) — отдельные репозитории фронта/бэка на каждый сервис.
**CI/CD** — автодеплой на AWS при пуше в main (каждый сервис независимо).
**Docker Compose** — сборка и запуск контейнеров.
**Среды:** local, production (staging в планах).
</details>

### Desktop & Mobile: 
<details>
<summary>Desktop — Electron</summary>
Десктопная версия создаётся с помощью <b>Electron</b> на базе существующего Angular-фронтенда. Приложение использует общий REST API Bun-сервера и синхронизируется с той же базой данных, что и веб-клиент. Видео-контент в десктоп-версии зашифрован и хранится в бинарном виде — исходные файлы не входят в сборку. Electron-клиент поддерживает офлайн-режим и локальное кэширование прогресса, а при подключении к сети синхронизирует результаты с сервером. 
</details>

<details>
<summary>Mobile — Cordova</summary>
  Мобильная версия собирается через <b>Apache Cordova</b> на основе того же Angular-кода. Сборка осуществляется в Android Studio, в результате чего получается нативный Android-билд. Cordova-клиент обращается к общему REST API и использует единый набор токенов и ролей, что обеспечивает полную синхронизацию данных с веб-версией. Поддерживается кеширование и частичная работа офлайн с последующей синхронизацией при появлении соединения.
</details>

### Monitoring 
<details>
  <summary>ELK, Prometheus + Grafana</summary>
Observability
**ELK (Elasticsearch + Logstash + Kibana)** — application/system logs.
**Kafka + S3** — event logs (бизнес-события, долговременный архив).
**Prometheus + Grafana** — метрики/трейсы (latency, throughput, offsets, consumer lag).

</details>

### Security
**Пароли** — Argon2 (хеширование).
**PII** — шифрование AES-256-GCM на уровне приложения; ключи в AWS Secrets Manager.
**JWT** — Access (15 мин) + Refresh (7 дней), хранение в LocalStorage; эндпоинты обновления.
**Транспорт** — TLS 1.3, приватные сети в AWS VPC, Security Groups.
**Kafka** — SASL_SSL + аутентификация по сертификатам.
**GDPR-ready** — экспорт/удаление данных, аудит доступа (события в Kafka).

---

## 4. Users and Roles
Система ролей централизована и управляется сервисом Profile. После авторизации пользователь получает JWT-токен, в котором зашифрована его роль. Все сервисы считывают эту роль и на её основе определяют уровень доступа.

### Roles 
<details>
  <summary>List of Roles(Technical Admin, Educational Admin, Teacher, Student)</summary>
**1. Technical Admin**
- Полный контроль над инфраструктурой (Kafka, базы, деплой, конфигурации).
- Имеет доступ к логам, мониторингу и настройкам безопасности.
- Может создавать и удалять пользователей.

**2. Educational Admin (админ-преподаватель)**
- Отвечает за учебный контент и роли пользователей.
- Загружает встроенные тексты и тесты (общие для всех).
- Назначает учителей и учеников, управляет их доступами.
- Имеет доступ к административному интерфейсу Admin Service.

**3. Teacher**
- Имеет доступ к своим ученикам (связка “учитель ↔ ученики”).
- Просматривает аналитику их прогресса, результаты тестов и чтения.
- Имеет права только на чтение, не редактирует контент.

**4. Student**
- Основной пользователь системы.
- Работает с карточками, читает тексты, выполняет тесты.
- Прогресс сохраняется и синхронизируется между устройствами.
</details>

### Description of permissions and access zones
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Authentication and authorization service 
<details>
  <summary>(Profile Service, JWT, Argon2, Refresh Tokens)</summary>
**Authentication & Authorization**
- Регистрация и логин через Profile Service.
- Пароли хешируются через Argon2, данные хранятся в PostgreSQL.
- После успешного входа выдается:
> Access Token (15 минут активности)
> Refresh Token (7 дней)
- Токены хранятся в LocalStorage и обновляются через выделенные эндпоинты.
- Refresh токены зашифрованы с помощью AES-256.
- Привязка осуществляется к конкретному устройству и сессии.
</details>

**Дополнитеьлно - Lobby Service**
- Сервис “Лобби” доступен всем ролям после входа через Profile Service.
- Является интерфейсным маршрутизатором — показывает пользователю доступные микросервисы (“продукты”) согласно его роли.
- Не управляет ролями, а только читает их из JWT.
- Обеспечивает согласованный пользовательский сценарий навигации между модулями системы.

---

## 5. Microservice Descriptions
  Здесь описание первой темы.


### microservice schema
<details>
  <summary>(Examples: Profile, Lobby, Thematic Cards, Learning Cards, Reader, Video Tests, Admin, PII)</summary>
--- ТУТ БУДЕТ СХЕМА: взаимодействие микросервисов (события, API, Kafka topics) ---
</details>

#### Profile microservice.
<details>
  <summary>Profile - Регистрация, логин, управление ролями, токенами и версиями клиентов. Центр аутентификации и авторизации. Отвечает за создание пользователей, хеширование паролей (Argon2), выдачу Access/Refresh токенов и проверку версии клиента. Хранит данные в PostgreSQL и публикует события `user.created`, `user.updated`, `user.roleChanged` в Kafka.</summary>
##### Name and purpose
Центральный сервис для регистрации, логина, управления ролями и токенами. Регистрация, авторизация, управление ролями и токенами.

**Функции:**
- Регистрация нового пользователя и хэширование пароля через Argon2.
- Авторизация, выдача Access/Refresh токенов.
- Хранение пользователей и ролей в PostgreSQL.
- Эндпоинты для обновления токенов и проверки актуальности версии клиента.
- Отправка событий в Kafka (user.created, user.updated, user.roleChanged).

##### Main API endpoints
Здесь описание первой темы.
 
##### Kafka events (topics, schemas, version)
**Kafka topics:**
- ser.events.v2
- user.security.v1

**schemas**
- ЧТО?
- ЧТО?

**version**
- ЧТО?
- ЧТО?

##### Databases and collections used
PostgreSQL (таблицы users, roles, tokens).

##### External integrations (AWS S3, Secrets Manager, etc.)
НЕТ ОПИСАНИЯ
</details>

#### Lobby microservice.
<details>
  <summary>Lobby - Интерфейсная точка входа в систему, отображающая пользователю доступные продукты по роли. После входа через Profile Service пользователь попадает в Лобби, где видит все доступные модули. Lobby не управляет ролями, а только маршрутизирует и кэширует навигацию.</summary>
##### Name and purpose
 Панель навигации по продуктам и интерфейсный маршрутизатор. Единая точка входа для пользователей после входа в систему.

**Функции:**
- Отображает список доступных микросервисов в зависимости от роли.
- Хранит состояние выбора пользователя и контекст навигации.
- Получает данные о доступных сервисах по REST API от Profile Service.

**Особенности:**
- Не управляет ролями — только визуализирует доступные модули.
- В будущем может быть расширен как менеджер маршрутов между микросервисами.

##### Main API endpoints
НЕТ ОПИСАНИЯ

##### Kafka events (topics, schemas, version)
**Kafka topics:**
- ui.navigation.v1 (прослушивает системные изменения доступов).

**schemas**
- ЧТО?
- ЧТО?

**version**
- ЧТО?
- ЧТО?

##### Databases and collections used
НЕТ ОПИСАНИЯ

##### External integrations (AWS S3, Secrets Manager, etc.)
НЕТ ОПИСАНИЯ
</details>

#### Thematic Cards microservice.
<details>
  <summary>Thematic Cards - Карточки по темам с уникальным алгоритмом повторения и статистикой обучения. Сервис объединяет тематические словари и позволяет учить слова по уровням. Алгоритм повторения разработан совместно с языковой школой. Прогресс сохраняется в MongoDB, обновления отправляются через Kafka.</summary>
##### Name and purpose
Сервис изучения слов по темам с уникальным алгоритмом повторения. Один их двух независимых модулей карточек с разными алгоритмами повторения и статистикой.

**Функции:**
- Отображение карточек, группированных по темам и уровням сложности.
- Алгоритм повторения, разработанный совместно с языковой школой-заказчиком.
- Отслеживание статистики по темам и прогрессу пользователя.
- Возможность синхронизации индивидуального словаря.

##### Main API endpoints
НЕТ ОПИСАНИЯ
 
##### Kafka events (topics, schemas, version)
**Kafka topics:**
- vocab.cards.v1
- vocab.progress.v1

**schemas**
- ЧТО?
- ЧТО?

**version**
- ЧТО?
- ЧТО?

##### Databases and collections used
MongoDB (коллекции topics, words, userProgress).
 
##### External integrations (AWS S3, Secrets Manager, etc.)
НЕТ ОПИСАНИЯ
</details>

#### Learning Cards microservice.
<details>
  <summary>Learning Cards - Индивидуальные карточки для повторения слов и формирования персонального словаря. Пользователь создаёт собственные списки слов, сервис формирует когорты и циклы обучения. Каждую неделю запускается cron-задача `updateLearningCohorts()`, обновляющая словари всех пользователей.</summary>
##### Name and purpose
Один их двух независимых модулей карточек с разными алгоритмами повторения и статистикой. Индивидуальные карточки для повторения слов и формирования персонального словаря, с собственным алгоритмом и недельным обновлением. Этот сервис отвечает за персонализированное обучение словарю. Пользователь формирует индивидуальный набор слов, а система группирует их по когортам и неделям. Каждую неделю по понедельникам запускается cron-задача <code>updateLearningCohorts()</code>, обновляющая активные слова для всех пользователей. Сервис использует MongoDB (коллекции <code>userWords</code> и <code>schedule</code>) и обменивается событиями через Kafka-топики <code>learning.cards.v1</code> и <code>learning.schedule.v1</code>.

**Функции:**
- Создание и хранение персональных словарей.
- Подбор слов по когортам и неделям обучения.
- Еженедельное обновление словарей (через cron-задачу).
- Возможность офлайн-доступа (через кэш и синхронизацию).

##### Main API endpoints
НЕТ ОПИСАНИЯ
 
##### Kafka events (topics, schemas, version)
**Kafka topics:**
- learning.cards.v1
- learning.schedule.v1
 
**schemas**
- ЧТО?
- ЧТО?

**version**
- ЧТО?
- ЧТО?

##### Cron-задачи:
- updateLearningCohorts() — выполняется по понедельникам для всех пользователей.

##### Databases and collections used
- MongoDB (коллекции userWords, schedule).

##### External integrations (AWS S3, Secrets Manager, etc.)
НЕТ ОПИСАНИЯ
</details>

#### Reader microservice.
<details>
  <summary>Reader - "Читалка" текстов с режимами свободного чтения и тестирования понимания. Хранит три типа текстов: встроенные тематические, пользовательские (импортируемые) и тестовые. Автоматически проверяет ответы и сохраняет результаты. Использует отдельные Mongo-базы для разных типов контента.</summary>
##### Name and purpose
Режим чтения текстов и тестов на понимание прочитанного. Свободное чтение и тесты на понимание; три типа текстов (дефолтные, пользовательские, тестовые).

**Функции:**
- Два режима: свободное чтение и тестирование.
- Хранение трёх типов текстов:
- Встроенная библиотека (по темам/уровням)
- Пользовательские тексты (импортируемые пользователем)
- Тексты для тестового режима (общие, встроенные)
- Автоматическая проверка тестов.

##### Main API endpoints
НЕТ ОПИСАНИЯ
  
##### Kafka events (topics, schemas, version)
**Kafka topics:**
- reader.events.v1
- reader.content.v2
  
**schemas**
- ЧТО?
- ЧТО?

**version**
- ЧТО?
- ЧТО?

##### Databases and collections used
**Две независимые базы MongoDB:**
- для дефолтных текстов;
- для пользовательских текстов.

##### External integrations (AWS S3, Secrets Manager, etc.)
  НЕТ ОПИСАНИЯ
</details>

#### Listening microservice.
<details>
  <summary>Listening (Video Tests) - Видео с вопросами, автопроверкой ответов и синхронизацией прогресса. Работает с зашифрованными видео на AWS S3. В десктоп-версии видео хранятся в бинарном виде, в вебе — по pre-signed URL. Результаты тестов и события (`video.results.v1`) публикуются в Kafka.</summary>
##### Name and purpose
Интерактивные видео с вопросами и автоматической проверкой. Видео с вопросами и автопроверкой, поддержка шифрования и офлайн-доступа.

**Функции:**
- Встроенный видеоплеер (web) и шифрованные бинарные потоки (desktop).
- Видео хранятся на AWS S3, доступ — через pre-signed URLs.
- В десктоп-версии (Electron) видео зашифрованы и не лежат в исходном архиве.
- Генерация результатов тестов и отправка событий прогресса.

##### Main API endpoints
НЕТ ОПИСАНИЯ
  
##### Kafka events (topics, schemas, version)
**Kafka topics:**
- video.events.v1
- video.results.v1
   
**schemas**
- ЧТО?
- ЧТО?

**version**
- ЧТО?
- ЧТО?

##### Databases and collections used
- MongoDB (коллекции videos, questions, results).
 
##### External integrations (AWS S3, Secrets Manager, etc.)
НЕТ ОПИСАНИЯ
</details>

#### Admin microservice.
<details>
  <summary>Admin - Создание и редактирование учебного контента через визуальный интерфейс. Доступен только администраторам-преподавателям. Позволяет добавлять тексты, карточки, тесты и метаданные. Публикует события `content.updated`, которые обновляют кэш в других сервисах.</summary>
##### Name and purpose
Редактирование и ручное создание контента преподавателями (Educational Admin). Редактирование и создание контента админом-преподавателем через UI.

**Функции:**
- Создание и редактирование тестов, карточек, текстов.
- Управление статусами контента и метаданными.
- Визуальный UI для модификации MongoDB-записей без прямого доступа к базе.
- Публикация обновлений контента через Kafka (content.updated).

##### Main API endpoints
НЕТ ОПИСАНИЯ
  
##### Kafka events (topics, schemas, version)
НЕТ ОПИСАНИЯ
 
##### Databases and collections used
- MongoDB (коллекции content, metadata).

##### External integrations (AWS S3, Secrets Manager, etc.)
НЕТ ОПИСАНИЯ
</details>

#### PII microservice.
<details>
  <summary>PII - Изолированное хранение и шифрование персональных данных пользователей. Хранит PII в PostgreSQL, шифрует данные AES-256-GCM, ключи — в AWS Secrets Manager. Ведёт журнал обращений в Kafka (`pii.access.log`) и реализует GDPR-эндпоинты `/export` и `/delete`.</summary>
##### Name and purpose
Безопасное хранение и шифрование персональных данных (PII). Изолированное шифрованное хранение персональных данных и аудит доступа.

**Функции:**
- Изолированное хранение имени, email, даты рождения и других PII-полей.
- Шифрование AES-256-GCM на уровне Bun-приложения.
- Ключи хранятся в AWS Secrets Manager, ротация каждые 90 дней.
- Ведение журнала доступа через Kafka (pii.access.log).

##### Main API endpoints
НЕТ ОПИСАНИЯ
  
##### Kafka events (topics, schemas, version)
НЕТ ОПИСАНИЯ

##### Databases and collections used
- PostgreSQL (шифрованные поля).

##### External integrations (AWS S3, Secrets Manager, etc.)
НЕТ ОПИСАНИЯ
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
  <summary>Event-Driven Communication</summary>
Все микросервисы взаимодействуют через Apache Kafka, обмениваясь событиями по принципу publish/subscribe./
Каждый сервис имеет собственный набор топиков и умеет как публиковать, так и слушать сообщения./
Такой подход обеспечивает асинхронность и слабую связанность между компонентами системы./

**Основные принципы:**
- Все события структурированы и валидируются через Schema Registry.
- Топики Kafka версионируются в названии (user.events.v2, reader.content.v1).
- Схемы сообщений имеют внутренние версии (1, 2, 3 и т.д.) в Schema Registry.
- При изменении схемы проводится проверка совместимости (backward/forward).
- Kafka работает в защищённом режиме (SASL_SSL) с аутентификацией по сертификатам.

--- ТУТ БУДЕТ СХЕМА: потоки событий Kafka и API-взаимодействий между сервисами ---
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
  **API versioning**
- Версии определяются в пути URL:
```
/api/v1/...  
/api/v2/...
```
- Старые версии поддерживаются параллельно, пока не завершён переход на новую.
- Плановый переход между версиями на production происходит без downtime.
</details>

### Kafka
<details>
  <summary>Саммари</summary>
- Названия топиков включают номер версии (user.events.v2).
- Schema Registry хранит историю изменений и обеспечивает совместимость.
- При выпуске новой версии микросервис может временно публиковать в оба топика (старый и новый), пока клиенты не перейдут.
</details>

### Schema Registry (schema versions)
<details>
  <summary>Саммари</summary>
  Здесь описание первой темы.
</details>

### Client version checks
<details>
  <summary>Саммари</summary>
- Каждый клиент (веб, мобильный, десктопный) передаёт свою версию при обращении к API.
- Если версия устарела и не поддерживает новые функции — пользователь видит сообщение с предложением обновить приложение.
- При централизованном обновлении контента пользователю показывается уведомление, что обновление может повлиять на его прогресс.
</details>

### Scheduled tasks and cron jobs (e.g., weekly cohort updates)
<details>
  <summary>Саммари</summary>
- Cron-задачи на бэкенде управляют глобальными обновлениями:
> пример — еженедельное обновление когорт слов (updateLearningCohorts()), запускается по понедельникам;
> cron-задачи публикуют системные события в Kafka (system.cron.weekly).
- Служебные события (ошибки, обновления схем, состояние брокеров) публикуются в системные топики (system.events.v1).
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

### Isolation of personal data 
<details>
  <summary>(PII Service)</summary>
  Здесь описание первой темы.
</details>

### Encryption: 
<details>
  <summary>AES-256-GCM, keys stored in AWS Secrets Manager</summary>
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

