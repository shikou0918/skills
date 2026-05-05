---
name: frontend-implementation-from-design
description: Implement frontend code from design documents such as ui-design.md, overview-design.md, openapi.yaml, api-design.md, or requirements.md. Use when the user wants Vue, React, TypeScript, Vite, Next.js, screen, component, API client, form, table, UI state, or frontend test implementation based on project design docs. Enforce code quality, responsibility separation, accessibility, API boundaries, UI states, and validation while implementing. Do not use for backend-only work or for writing UI design documents without code changes.
---

# Frontend Implementation From Design

Use this skill to implement frontend features from design documents while preserving UI intent, API contracts, accessibility, and project conventions.

## Inputs

Read the relevant documents before coding:

- `ui-design.md` for screen layout, UI states, interactions, dialogs, and API mapping.
- `overview-design.md` for screen list, workflows, entities, and non-functional constraints.
- `openapi.yaml` for API operations and request/response contracts.
- `api-design.md` for endpoint behavior, validation, and error policy.
- `requirements.md` when broader product intent is needed.
- Existing frontend code, package config, components, API client, types, tests, and styling conventions.

If expected design documents are not found in the current directory, search the project root, `docs/`, `frontend/docs/`, the parent directory, and nearby parent `docs/` directories up to 2-3 levels before proceeding. If the required UI behavior or API contract cannot be determined from documents or the user's latest instruction, stop before editing and report the missing inputs and search scope instead of implementing from guesses.

If design documents conflict, prefer the user's latest instruction, then `ui-design.md` for UI behavior, then `openapi.yaml` for API contracts, then `api-design.md`, then `overview-design.md`, then `requirements.md`. Do not silently change UI/API behavior; surface conflicts as assumptions or questions when they affect implementation.

## Workflow

1. Identify the requested frontend scope.
   - Screen, component, composable/hook, API client, form, table, dialog, chart, or test.
   - UI states and interactions from `ui-design.md`.
   - API operations from `openapi.yaml` or API design.
   - Restate critical UI/API invariants before coding: state transitions, blocking validation, destructive/saving disabled states, API operations, and sensitive-data handling.

2. Inspect existing frontend conventions.
   - Framework and build tool.
   - TypeScript, lint, formatter, and test setup.
   - Component structure and naming.
   - State management: local state, composables/hooks, Pinia/Redux/etc.
   - API client/generator pattern.
   - Styling and component library.

3. Plan a small implementation slice.
   - Name files/components/composables before editing.
   - Keep unrelated UI redesigns and refactors out of scope.
   - Preserve user changes in the worktree.
   - If a UI choice would change the design, stop and call it out before coding.

4. Implement with clear responsibility boundaries.
   - Page/screen components compose layout and orchestrate screen-level state.
   - Presentational components render props and emit events.
   - Composables/hooks own reusable stateful behavior.
   - API client layer owns endpoint paths, serialization, response parsing, and error normalization.
   - Generated or schema-aligned types are preferred when available.

5. Validate.
   - Run formatter, lint, typecheck, tests, and relevant UI checks when available.
   - If commands are missing or cannot run, report that plainly.

## TypeScript and API Rules

- Avoid unnecessary `any`; narrow unknown data at API boundaries.
- If generated/shared API types do not exist yet, define narrow temporary request/response types at the API boundary and mark them for replacement.
- Keep endpoint paths and fetch/axios options out of page components.
- Map backend errors into UI-friendly states.
- Keep nullable and optional fields distinct.
- Do not log sensitive user or financial data.

## Vue Defaults

When implementing Vue 3:

- Prefer Composition API and `<script setup lang="ts">`.
- Use props/emits with explicit TypeScript types.
- Use composables for reusable stateful logic and API flows.
- Use Pinia only for shared cross-screen state or established local convention.
- Avoid mutating props directly.
- Keep template expressions simple; move complex logic to computed values.

## React Defaults

When implementing React:

- Prefer function components and hooks.
- Extract custom hooks for reusable stateful behavior.
- Avoid effects for pure derivations.
- Keep server/client boundaries explicit in Next.js App Router projects.

## UI State Rules

Every data-driven screen should handle:

- Initial state.
- Loading state.
- Empty state.
- Error state with retry when appropriate.
- Success state.
- Mutation pending state.
- Mutation failure recovery.

For table-heavy screens:

- Filters reset pagination.
- Sort and pagination are explicit state.
- Inline edits show row/cell-level pending and error states.
- Large tables may scroll horizontally; text must not overlap.

For destructive actions:

- Use confirmation dialogs.
- Show object names and affected counts when knowable.
- Disable confirm while running.
- Show success transition and error recovery.

## Accessibility

- Use semantic HTML before custom roles.
- Associate labels, descriptions, and errors with inputs.
- Make focus states visible.
- Do not rely on color alone.
- Dialogs need clear cancel and confirm actions.
- Common form/table/dialog flows should be keyboard-friendly.

## Tests

Use focused tests based on risk:

- API client mapping and error normalization.
- Composables/hooks with branching state.
- Forms and validation.
- Destructive confirmation flows.
- Critical screen flows through component or e2e tests when tooling exists.

## Final Response

Summarize:

- Files changed.
- Design documents used.
- UI/API decisions made.
- Validation commands run and results.
- Any assumptions, skipped tests, or open questions.
