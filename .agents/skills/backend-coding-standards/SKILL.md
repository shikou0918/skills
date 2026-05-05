---
name: backend-coding-standards
description: Apply reusable backend coding standards when implementing, reviewing, or refactoring backend services. Use for Clean Architecture, handler/usecase/domain/repository boundaries, API handlers, database access, transactions, error handling, logging, validation, tests, and backend lint/format conventions. Adapt to the detected language and framework, with Go defaults when the project uses Go. Do not use for frontend-only code.
---

# Backend Coding Standards

Use this skill when writing or reviewing backend code. The default architecture is Clean Architecture: business rules stay independent of frameworks, databases, HTTP, and delivery mechanisms.

## Discovery

Before coding, inspect the project for:

- Language and framework.
- Existing directory layout and naming.
- API contract: OpenAPI, API design docs, routes.
- Database and migration tooling.
- Existing logging, validation, error, and test patterns.
- Existing dependency injection style.

Prefer existing consistent conventions over these defaults, but do not preserve patterns that violate explicit project architecture.

## Clean Architecture Rules

Dependency direction must point inward:

```text
Delivery / Frameworks
  -> Interface Adapters
    -> Use Cases
      -> Domain
```

Rules:

- Domain does not import database, HTTP, framework, ORM, logger, or config packages.
- Use cases orchestrate business operations and depend on interfaces, not concrete infrastructure.
- Repository interfaces live in the inner layer that needs them, usually usecase or domain.
- For application-specific persistence workflows, place repository interfaces in the usecase/application layer by default; move them to domain only when they represent a stable domain concept.
- Repository implementations live in infrastructure/adapter layers.
- HTTP handlers/controllers translate transport input/output; they do not contain business rules.
- Database transactions are coordinated at the application/usecase boundary when a use case needs atomicity.
- Framework-specific models must not leak into domain entities.
- Keep DTOs, request/response models, and persistence models separate when their reasons to change differ.

## Recommended Layer Responsibilities

| Layer | Responsibility | Must not contain |
|---|---|---|
| Domain | Entities, value objects, domain rules | HTTP, SQL, ORM, framework annotations |
| Usecase/Application | Business workflows, transaction orchestration, repository interfaces | Router setup, SQL details, UI concerns |
| Interface Adapter | HTTP handlers, presenters, request validation, DTO mapping | Core business decisions |
| Infrastructure | DB repositories, external clients, filesystem, migrations | Domain policy decisions |
| Entrypoint | Server boot, wiring, config | Business logic |

## Go Defaults

When the project uses Go:

- Use `gofmt` and `go vet` compatible code.
- Keep packages small and named by responsibility, not by vague types like `common` or `utils`.
- Pass `context.Context` to operations that do I/O, DB access, or cancellation-sensitive work.
- Return errors explicitly; do not panic for ordinary failures.
- Wrap errors with useful context at boundaries.
- Keep sentinel/domain errors in inner layers when callers need branching.
- Keep HTTP status mapping in handlers/presenters, not usecases.
- Do not expose GORM models as API responses or domain entities by default.
- Keep GORM tags and persistence-only fields in infrastructure models.
- Prefer constructor functions when a type has required dependencies.

Example package direction for Go:

```text
cmd/server
internal/domain
internal/usecase
internal/adapter/http
internal/adapter/repository
internal/infra/database
internal/infra/config
```

Adjust names to the project, but keep the dependency direction.

## API Handlers

Handlers/controllers should:

- Parse path/query/body/input.
- Call a usecase.
- Map domain/usecase errors to HTTP responses.
- Return response DTOs.
- Avoid direct DB calls.
- Avoid business branching beyond transport-level validation.

## Validation

- Transport validation checks request shape, required fields, enum values, and parseability.
- Usecase validation checks business invariants and operation-specific rules.
- Domain validation protects entity/value object invariants.
- Do not trust frontend validation.

## Database and Transactions

- Keep SQL/ORM code in repositories or infrastructure.
- Keep migrations versioned and reviewable.
- A usecase that changes multiple aggregates/tables should define the transaction boundary.
- Do not start hidden transactions deep inside repositories when the usecase needs atomic behavior across multiple repositories.
- If the project has no transaction abstraction, introduce a narrow `TxManager` / `UnitOfWork` style interface consumed by the usecase; do not pass ORM handles such as `*gorm.DB` into inner layers.
- Avoid logging sensitive data from rows, request bodies, or financial records.

## Errors and Logging

- Use a common error response shape when the API contract defines one.
- Keep user-facing messages separate from internal error details when needed.
- Log enough context to debug, but do not log secrets or sensitive personal/financial data.
- Map expected domain/usecase errors to stable API codes.

## Tests

Scale tests to risk:

- Domain rules: unit tests.
- Usecases: unit tests with fake repositories.
- Repository implementations: integration tests with a test database when practical.
- HTTP handlers: handler tests for status mapping and request/response shape.
- Migration and transaction-sensitive flows: integration tests.

## Final Check

Before finishing backend work:

- Run formatter, linter, and tests when available.
- Check dependency direction did not invert.
- Check handlers do not contain business logic.
- Check domain/usecase layers do not import infrastructure.
- Check errors are mapped to API response shape.
- Note any commands that could not be run.
