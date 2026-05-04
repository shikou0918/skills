---
name: overview-api-design
description: Create an API design document and OpenAPI 3.1 specification from an existing overview-design.md or overview design document. Use when the user wants to derive REST API endpoints, request/response schemas, validation rules, error responses, and an openapi.yaml from an overview-design skill output or high-level application design. Do not use for unrelated API implementation work unless an overview design is the source material.
---

# Overview API Design

Use this skill to turn an existing overview design into a concrete API contract. The usual input is `overview-design.md`; the usual outputs are `api-design.md` and `openapi.yaml` beside it.

## Workflow

1. Locate and read the source overview design.
   - Prefer the path the user names.
   - If no path is given, look for `overview-design.md` in the current project.
   - Preserve the user's latest chat decisions over older content in the document.

2. Extract API-relevant intent.
   - Product goal and users.
   - Architecture, frontend/backend stack, persistence, auth, external integrations.
   - Domain entities and relationships.
   - User workflows that cross the frontend/backend boundary.
   - Existing endpoint candidates, if any.
   - Validation, import/export, batch jobs, and error handling requirements.

3. Normalize the API as a REST contract.
   - Model stable resources as nouns: `/transactions`, `/categories`, `/imports`.
   - Use HTTP methods for actions: `GET`, `POST`, `PATCH`, `DELETE`.
   - Use nested routes only when the child has no useful identity outside the parent.
   - For process-like operations, prefer resource names over verbs when clear:
     - `POST /import-previews` instead of `POST /imports/preview`
     - `POST /classification-runs` instead of `POST /analysis/reapply`
   - Keep `/api/v1` only if the overview design or user explicitly wants path-based versioning; otherwise put versioning in the OpenAPI `servers` URL or leave it out for small local apps.
   - Do not invent authentication if the overview design says the app is local or single-user.

4. Write `api-design.md`.
   Include these sections when applicable:
   - API design goals and assumptions
   - Versioning and base URL
   - Common conventions
   - Error response format
   - Endpoint list
   - Request/response details
   - Validation rules
   - Pagination, filtering, and sorting
   - Import/export flow
   - Open questions

5. Write `openapi.yaml`.
   The spec must be OpenAPI 3.1 YAML and include:
   - `openapi: 3.1.0`
   - `info`
   - `servers`
   - `tags`
   - `paths`
   - `components.schemas`
   - `components.parameters`
   - `components.responses`
   - `components.examples` only when examples materially clarify behavior
   - `components.securitySchemes` only when auth exists

6. Validate the contract before finishing.
   - Check every `$ref` target exists.
   - Check every path parameter is declared.
   - Check list endpoints have clear response envelopes.
   - Check create endpoints return `201` when they create persisted resources.
   - Check import/preview endpoints do not imply saving data unless the workflow says so.
   - Check field names are consistent across schemas and prose.

## Output Conventions

Default file names:

- `api-design.md`
- `openapi.yaml`

Default location:

- The same directory as the source `overview-design.md`.

Do not overwrite existing non-empty API design files blindly. Read them first and preserve useful decisions unless the user asks for a clean rewrite.

## OpenAPI Style

Use stable, SDK-friendly `operationId` values:

- `listTransactions`
- `createImport`
- `previewImport`
- `updateCategory`
- `deleteCategoryRule`

Use a common error shape:

```yaml
Error:
  type: object
  required:
    - code
    - message
  properties:
    code:
      type: string
    message:
      type: string
    details:
      type: object
      additionalProperties: true
```

Use list envelopes when the client needs pagination metadata:

```yaml
TransactionListResponse:
  type: object
  required:
    - items
    - pagination
  properties:
    items:
      type: array
      items:
        $ref: "#/components/schemas/Transaction"
    pagination:
      $ref: "#/components/schemas/Pagination"
```

For CSV import flows, separate preview from persistence:

- Preview request: accepts a file or file reference, parses it, infers mapping candidates, and returns preview rows plus validation errors.
- Import request: accepts a confirmed mapping or preview token/file hash, then persists transactions.
- The spec should not assume fixed CSV column counts unless the overview design explicitly says so.

## Final Response

Summarize:

- Files created or updated.
- Major API design decisions.
- Any assumptions or open questions.
- Whether OpenAPI validation tooling was run. If no validator was available or run, say so plainly.
