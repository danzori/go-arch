# go-arch

> Pragmatic Go microservice architecture based on DDD, Clean Architecture and Hexagonal Architecture.

> [!IMPORTANT]
> This architecture is not intended to be the perfect or universally correct one. It is simply my opinion on a practical way to structure Go microservices. 

`go-arch` is a pragmatic project structure for building maintainable Go microservices.

The architecture combines:

* **Domain-Driven Design (DDD)** - business logic is organized around the domain.
* **Clean Architecture** - dependencies are directed toward stable business logic.
* **Hexagonal Architecture** - application dependencies are isolated through ports and adapters.
* **Idiomatic Go** - consumer-owned interfaces, composition over unnecessary abstraction, explicit dependencies and minimal ceremony.

The goal is not to follow any of these approaches dogmatically, but to combine their useful principles into a practical Go architecture.

---

## Project Structure

```text
.
├── api
│   ├── asyncapi
│   ├── openapi
│   └── proto
├── cmd
│   └── <service>
├── config
├── internal
│   ├── application
│   │   └── <domain-name>
│   │       └── mocks
│   ├── bootstrap
│   ├── config
│   ├── delivery
│   │   ├── <other>
│   │   ├── cli
│   │   │   └── <command>
│   │   ├── event
│   │   │   └── <broker>
│   │   ├── grpc
│   │   │   ├── handler
│   │   │   ├── mapper
│   │   │   └── server
│   │   │       └── interceptor
│   │   └── http
│   │       ├── dto
│   │       ├── handler
│   │       ├── mapper
│   │       └── server
│   │           └── middleware
│   ├── domain
│   │   └── <domain-name>
│   ├── infrastructure
│   │   ├── cache
│   │   │   └── <cache>
│   │   ├── client
│   │   │   └── <client>
│   │   ├── messaging
│   │   │   └── <broker>
│   │   ├── observability
│   │   │   ├── logger
│   │   │   │   └── <provider>
│   │   │   ├── metrics
│   │   │   │   └── <provider>
│   │   │   └── tracing
│   │   │       └── <provider>
│   │   ├── persistence
│   │   │   └── <database>
│   │   └── storage
│   │       └── <storage>
│   └── worker
│       └── <worker>
├── migrations
└── test
    ├── e2e
    └── integration
```

## Architecture Dependency Direction

<img src="https://github.com/user-attachments/assets/860fc3c4-4667-41b9-a2b1-d6ef1c1b6df8" alt="Architecture dependency direction" width="800">

---

## Directory Reference

### Root directories

| Directory    | Purpose                                                                                                                  |
|--------------|--------------------------------------------------------------------------------------------------------------------------|
| `api/`       | External contracts for HTTP, gRPC and asynchronous messaging.                                                            |
| `cmd/`       | Process entry points. Keeps executable startup code thin and delegates application composition to `internal/bootstrap/`. |
| `config/`    | Application configuration files.                                                                                         |
| `internal/`  | Private application implementation.                                                                                      |
| `migrations` | Database migrations.                                                                                                     |
| `test/`      | Cross-package tests.                                                                                                     |

---

### `api/`

| Directory   | Purpose                                  |
|-------------|------------------------------------------|
| `openapi/`  | HTTP API contracts                       |
| `proto/`    | gRPC / Protocol Buffers contracts        |
| `asyncapi/` | Asynchronous event and message contracts |

---

### `cmd/`

| Directory    | Purpose                                |
|--------------|----------------------------------------|
| `<service>/` | Entry point for a specific executable. |

---

### `internal/`

#### `application/` - Application use cases and orchestration

| Directory / File         | Purpose                                                             |
|--------------------------|---------------------------------------------------------------------|
| `<domain-name>/`         | Application logic for a specific business area.                     |
| `<domain-name>/ports.go` | Interfaces for dependencies required by the application layer.      |
| `<domain-name>/mocks/`   | Generated mocks for interfaces defined in `<domain-name>/ports.go`. |

#### `domain/` - Domain model and business rules

| Directory / File     | Purpose                                                                                                       |
|----------------------|---------------------------------------------------------------------------------------------------------------|
| `<domain-name>/`     | Domain logic grouped by a specific business area.                                                             |
| `<domain-name>/*.go` | Entities, aggregates, value objects, domain services, domain errors, domain events and other domain concepts. |

#### `delivery/` - Inbound adapters

| Directory  | Purpose                                                                           |
|------------|-----------------------------------------------------------------------------------|
| `http/`    | HTTP inbound adapter.                                                             |
| `grpc/`    | gRPC inbound adapter.                                                             |
| `event/`   | Inbound event adapters.                                                           |
| `cli/`     | CLI inbound adapter.                                                              |
| `<other>/` | Additional adapters can be added here as needed, e.g. WebSocket, QUIC or GraphQL. |

##### `http/`

| Directory            | Purpose                                      |
|----------------------|----------------------------------------------|
| `dto/`               | HTTP request and response models.            |
| `handler/`           | HTTP handlers.                               |
| `mapper/`            | Mapping between HTTP and application models. |
| `server/`            | HTTP server, routing and lifecycle.          |
| `server/middleware/` | HTTP middleware.                             |

##### `grpc/`

| Directory             | Purpose                                          |
|-----------------------|--------------------------------------------------|
| `handler/`            | gRPC handlers.                                   |
| `mapper/`             | Mapping between protobuf and application models. |
| `server/`             | gRPC server and lifecycle.                       |
| `server/interceptor/` | gRPC interceptors.                               |

##### `event/`

| Directory   | Purpose                                 |
|-------------|-----------------------------------------|
| `<broker>/` | Consumer for a specific message broker. |

##### `cli/`

| Directory    | Purpose                  |
|--------------|--------------------------|
| `<command>/` | Individual CLI commands. |

#### `worker/` - Background execution

| Directory   | Purpose                                                            |
|-------------|--------------------------------------------------------------------|
| `<worker>/` | Background tasks such as cron jobs, scheduled jobs, queue workers. |

#### `infrastructure/` - Outbound adapters and technical infrastructure

| Directory        | Purpose                                                   |
|------------------|-----------------------------------------------------------|
| `persistence/`   | Interaction with application databases.                   |
| `client/`        | Integration with external services and APIs.              |
| `messaging/`     | Publishing messages and events to external brokers.       |
| `cache/`         | Interaction with caching systems used by the application. |
| `storage/`       | Interaction with external object, blob or file storage.   |
| `observability/` | Technical components for application observability.       |

##### `persistence/`

| Directory     | Purpose                           |
|---------------|-----------------------------------|
| `<database>/` | Database-specific implementation. |

##### `client/`

| Directory   | Purpose                                                |
|-------------|--------------------------------------------------------|
| `<client>/` | Client implementation for a specific external service. |

##### `messaging/`

| Directory   | Purpose                                                |
|-------------|--------------------------------------------------------|
| `<broker>/` | Producer implementation for a specific message broker. |

##### `cache/`

| Directory  | Purpose                                                 |
|------------|---------------------------------------------------------|
| `<cache>/` | Cache implementation for a specific caching technology. |

##### `storage/`

| Directory    | Purpose                                                   |
|--------------|-----------------------------------------------------------|
| `<storage>/` | Storage implementation for a specific storage technology. |

##### `observability/`

| Directory             | Purpose                                              |
|-----------------------|------------------------------------------------------|
| `logger/<provider>/`  | Logging implementation based on a specific provider. |
| `metrics/<provider>/` | Metrics implementation based on a specific provider. |
| `tracing/<provider>/` | Tracing implementation based on a specific provider. |

#### `config/` - Configuration loading

| File   | Purpose                                                                         |
|--------|---------------------------------------------------------------------------------|
| `*.go` | Loads, parses and validates configuration from files and environment variables. |

#### `bootstrap/` - Composition root

| File   | Purpose                                                                                                               |
|--------|-----------------------------------------------------------------------------------------------------------------------|
| `*.go` | Dependency wiring, infrastructure initialization, application construction, delivery setup and worker initialization. |

---

### `test/`

| Directory      | Purpose            |
|----------------|--------------------|
| `integration/` | Integration tests. |
| `e2e/`         | End-to-end tests.  |

## Template Notation

| Notation        | Meaning                                                                                         | Example                        |
|-----------------|-------------------------------------------------------------------------------------------------|--------------------------------|
| `<service>`     | Executable name. Use `service` for a single binary or the binary name for multiple executables. | `service`, `seed`              |
| `<domain-name>` | Business domain or bounded context.                                                             | `user`, `order`                |
| `<command>`     | CLI command name.                                                                               | `migrate`                      |
| `<broker>`      | Message broker used for event consumption or publishing.                                        | `kafka`, `rabbitmq`, `nats`    |
| `<database>`    | Database technology used for persistence.                                                       | `postgres`, `mysql`, `mongo`   |
| `<client>`      | External service being integrated.                                                              | `payment`, `notification`      |
| `<cache>`       | Caching technology.                                                                             | `redis`, `memcached`           |
| `<storage>`     | Object/blob storage technology                                                                  | `minio`                        |
| `<provider>`    | Technology providing the corresponding observability component.                                 | `zap`, `otel`                  |
| `<worker>`      | Background task or worker name.                                                                 | `cleanup`                      |
| `<other>`       | Any additional inbound adapter not covered by the predefined examples.                          | `graphql`, `websocket`, `quic` |