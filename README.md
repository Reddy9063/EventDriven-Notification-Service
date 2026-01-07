# Event-Driven Notification Service

A production-style backend system for processing notification events asynchronously with **retries, idempotency, failure handling, and observability**.

This project focuses on real-world backend problems such as duplicate request prevention, background processing, retry backoff, and dead-letter handling — instead of simple CRUD operations.

---

## 🔍 Problem Statement

In real systems (emails, payments, alerts), requests can:
- Fail temporarily
- Be retried multiple times
- Be sent more than once due to network issues

A backend system must:
- Avoid duplicate processing
- Retry failed operations safely
- Handle permanent failures gracefully
- Remain responsive and non-blocking

This project demonstrates how to solve these problems using Spring Boot.

---

## 🧠 High-Level Architecture

### Key Components
- **Controller Layer** – Accepts API requests
- **Service Layer** – Handles business logic and idempotency
- **Scheduler** – Processes events asynchronously
- **Database** – Stores event state and retry metadata
- **Email Service** – Sends notifications (SMTP)
- **Admin APIs** – Monitoring and observability
- **Swagger/OpenAPI** – API documentation

---

## 🔄 End-to-End Flow

1. Client sends a request to register a notification event
2. An **Idempotency-Key** is sent in request headers
3. Backend checks if the request was already processed
4. If new → event is stored with `PENDING` status
5. Scheduler wakes up periodically and processes events
6. On failure → retries with backoff
7. After max retries → event is marked `DEAD`
8. Admin APIs expose stats and event state

---

## 🧩 Event Lifecycle

### Status Meaning
- **PENDING** – Waiting to be processed
- **FAILED** – Temporary failure, will retry
- **SUCCESS** – Notification delivered
- **DEAD** – Max retries exceeded, manual action required

---

## 🔁 Retry & Backoff Strategy

| Retry Count | Delay |
|-----------|-------|
| 0 | Immediate |
| 1 | 10 seconds |
| 2 | 20 seconds |
| 3 | 40 seconds |
| >3 | DEAD |

Retries are **non-blocking** and handled by the scheduler.

---

## 🔐 Idempotency Design

To prevent duplicate processing:
- Client sends a unique `Idempotency-Key` header
- Backend checks DB for existing event with same key
- If found → returns existing response
- If not → creates a new event

This guarantees **safe retries**.

---

## 🌐 API Endpoints

### 1️⃣ Register Event (Idempotent)

**Endpoint**: http://localhost/8080/event/register

**Headers**: IdempotencyKey(random)

**Request Body**
```json
{
  "toEmail": "user@gmail.com",
  "fromEmail": "noreply@system.com",
  "message": "Reset your password",
  "eventType": "PASSWORD_RESET"
}
```

**Response**
```json
{
  "eventId": 12,
  "status": "CREATED"
}
```

**Admin Endpoints**
**Endpoint**: http://localhost/8080/admin/events/all   (GET) <br>
**Endpoint**: http://localhost/8080/admin/events/{id}  (GET) <br>
**Endpoint**: http://localhost/8080/admin/events?status=FAILED   (GET)   (Allowed Values = PENDING | FAILED | SUCCESS | DEAD) <br>

**📄 Swagger / OpenAPI**
**Endpoint**:http://localhost:8080/swagger-ui/index.html


***Architectural Representation***
![Uploading architecture.png.png…]()

