# Event-Driven Notification Service

A backend system for processing notification events asynchronously with retries, idempotency, and failure handling.

## Features
- Asynchronous event processing using Spring Scheduler
- Idempotent event creation using Idempotency-Key
- Retry with backoff strategy
- Dead-letter handling for failed events
- Admin APIs for monitoring and statistics
- Swagger/OpenAPI documentation

## Event Lifecycle
PENDING → SUCCESS  
PENDING → FAILED → RETRY → DEAD

## Architecture
The system decouples request ingestion from processing.  
Events are persisted first and processed asynchronously by a scheduler to ensure reliability and non-blocking APIs.

## Tech Stack
- Java
- Spring Boot
- Spring Data JPA
- MySQL
- Swagger/OpenAPI


## Architecture Diagram
![Architecture](docs/architecture.png)
