---
name: backend-implementation-from-design
description: Implement backend code from design documents such as overview-design.md, api-design.md, openapi.yaml, requirements.md, or backend design notes. Use when the user wants backend implementation, Go API work, database/repository/usecase/handler code, or Clean Architecture backend changes based on project design docs. Enforce code quality, responsibility separation, tests, and validation while implementing. Do not use for frontend-only work or for writing design documents without code changes.
---

# Backend Implementation From Design

Use this skill to implement backend features from design documents while preserving architecture, code quality, and project conventions. The default architecture is Clean Architecture.

## Inputs

Read the relevant documents before coding:

- `requirements.md` when present.
- `overview-design.md` for architecture, entities, workflows, and non-functional constraints.
- `api-design.md` for endpoint intent, validation, and error policy.
- `openapi.yaml` for request/response contracts and operationIds.
- Existing backend code, tests, package config, migrations, and scripts.

If expected design documents are not found in the current directory, search the project root, `docs/`, `backend/docs/`, the parent directory, and nearby parent `docs/` directories up to 2-3 levels before proceeding. If the required backend behavior cannot be determined from documents or the user's latest instruction, stop before editing and report the missing inputs and search scope instead of implementing from guesses.

If design documents conflict, prefer the user's latest instruction, then `openapi.yaml` for API contract details, then `api-design.md`, then `overview-design.md`, then `requirements.md`. Do not silently change product/API behavior; surface conflicts as assumptions or questions when they affect implementation.

## Workflow

1. Identify the requested backend scope.
   - Endpoint, usecase, repository, migration, importer, job, or test.
   - Inputs and outputs from OpenAPI/API design.
   - Entities and rules from overview design.
   - Restate endpoint invariants before coding: persistence behavior, duplicate handling, validation errors, transaction/rollback expectations, and sensitive-data logging constraints.

2. Inspect existing backend conventions.
   - Directory layout and package names.
   - Handler/router style.
   - Usecase/service style.
   - Repository and transaction patterns.
   - Error response shape.
   - Logging and validation patterns.
   - Test and command scripts.

3. Plan a small implementation slice.
   - Name files and packages before editing.
   - Keep unrelated refactors out of scope.
   - Preserve user changes in the worktree.
   - If an implementation choice would change the design, stop and call it out before coding.

4. Implement using Clean Architecture.
   - Domain: entities, value objects, domain rules only.
   - Usecase/application: business workflow, validation beyond transport shape, transaction orchestration, repository interfaces.
   - Interface adapters: HTTP handlers/controllers, request/response DTOs, presenters, error mapping.
   - Infrastructure: GORM/SQL repositories, migrations, filesystem/external clients, config.
   - Entrypoint: server boot, routing, dependency wiring.

5. Validate.
   - Run formatter, lint, tests, and contract checks when available.
   - If commands are missing or cannot run, report that plainly.

## Clean Architecture Rules

- Dependency direction points inward: delivery/frameworks -> adapters -> usecases -> domain.
- Domain does not import HTTP, GORM/SQL, framework, logger, config, or filesystem packages.
- Usecases depend on interfaces, not concrete infrastructure.
- For application-specific persistence workflows, place repository interfaces in the usecase/application layer by default.
- Move repository interfaces to domain only when they represent a stable domain concept shared by multiple usecases.
- GORM models are persistence models, not API responses or domain entities by default.
- HTTP handlers parse transport input, call usecases, map errors, and return DTOs. They do not contain business rules.
- Transaction boundaries for multi-table operations belong at the usecase/application boundary.
- If no transaction abstraction exists, introduce a narrow `TxManager` / `UnitOfWork` style interface consumed by the usecase. Do not pass `*gorm.DB` into inner layers.
- Preferred Go transaction shape when no local pattern exists: `WithinTx(ctx, func(ctx context.Context, repos TxRepositories) error) error`, or the smallest equivalent interface needed by the usecase.

## Go Defaults

When implementing Go backend code:

- Use `gofmt` and `go vet` compatible code.
- Pass `context.Context` to I/O, DB, and cancellation-sensitive operations.
- Return errors explicitly; do not panic for expected failures.
- Wrap errors with useful context at boundaries.
- Keep HTTP status mapping outside usecases.
- Keep request/response DTOs separate from domain and persistence models when their reasons to change differ.
- Avoid vague packages such as `common` or `utils`; prefer responsibility-based names.
- Do not log sensitive user, financial, request body, or row-level data unless explicitly safe.

## API Contract Rules

- Implement endpoint behavior according to `openapi.yaml` when present.
- Use operationId names as a useful implementation guide, not as mandatory function names when local conventions differ.
- Keep `{ code, message, details }` style error responses when the API design defines it.
- Preserve status codes from the API design unless the user approves a change.
- Validate request shape at the transport boundary.
- Validate business invariants in usecase/domain.
- If generated contract validation is not present, implement minimal manual validation and response-shape tests aligned with OpenAPI.

## Tests

Use focused tests based on risk:

- Domain rules: unit tests.
- Usecases: unit tests with fake repositories and fake transaction manager.
- Repositories: integration tests with a test database when practical.
- Handlers: request parsing, status mapping, response shape, and expected error mapping.
- Multi-table transactions: rollback/commit behavior.

## Final Response

Summarize:

- Files changed.
- Design documents used.
- Architecture decisions made.
- Validation commands run and results.
- Any assumptions, skipped tests, or open questions.
