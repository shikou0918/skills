---
name: frontend-coding-standards
description: Apply reusable frontend coding standards when implementing, reviewing, or refactoring frontend code in TypeScript-based web apps such as Vue, React, Vite, Next.js, or similar. Use for component design, state management, API client usage, UI states, accessibility, lint/format conventions, and frontend test expectations. Do not use for backend-only code or for creating UI design documents without code changes.
---

# Frontend Coding Standards

Use this skill when writing or reviewing frontend code. Adapt the rules to the project by reading existing code, package configuration, and design/API documents first.

## Discovery

Before coding, inspect the project for:

- Framework and build tool: Vue, React, Vite, Next.js, etc.
- Language and config: TypeScript, `tsconfig`, ESLint, Prettier.
- State management: Pinia, Redux, Zustand, Vue composables, local state.
- API layer: generated client, handwritten client, fetch/axios wrapper.
- Design system or UI component library.
- Existing folder and naming patterns.

Prefer existing project conventions over these defaults when they are clear and consistent.

## Core Rules

- Keep UI components focused on rendering and local interaction.
- Put data fetching and API details in an API/client or composable/service layer, not scattered across components.
- Model loading, empty, error, validation, success, and disabled states explicitly.
- Avoid `any` unless the boundary is genuinely unknown; narrow unknown data at the boundary.
- Keep domain-facing types in shared `types` or generated API types.
- Prefer small, named components when a UI block has its own state, behavior, or reuse pressure.
- Do not split components only to satisfy a size preference; split when it clarifies ownership or behavior.
- Keep form validation rules close to the form or in a reusable schema when multiple forms share them.
- Destructive actions require confirmation and clear failure recovery.

## TypeScript

- Enable and respect strict TypeScript where the project supports it.
- Use discriminated unions for UI state when it prevents impossible combinations.
- Prefer explicit request/response types from OpenAPI or the local API layer.
- Avoid duplicating backend schema names by hand when generated or shared types exist.
- Keep nullable and optional fields distinct.

## Vue Defaults

When the project uses Vue 3:

- Prefer Composition API and `<script setup lang="ts">`.
- Use composables for reusable stateful logic and API flows.
- Keep Pinia stores for shared cross-screen state, not one-off component state.
- Use props/emits with explicit TypeScript types.
- Avoid mutating props directly.
- Keep template expressions simple; move complex derivations to computed values.

## React Defaults

When the project uses React:

- Prefer function components and hooks.
- Keep server/client boundaries explicit when using Next.js App Router.
- Extract custom hooks for reusable stateful behavior.
- Avoid effects for pure derivations; use memoization only when it solves a real cost or identity issue.

## API Integration

- Prefer a single API client boundary.
- Keep endpoint paths, fetch options, serialization, and error normalization outside page components.
- Map backend errors into UI-friendly error states.
- Do not log sensitive user data.
- If OpenAPI exists, prefer generated or schema-aligned types.
- If generated/shared API types do not exist yet, define narrow request/response types at the API boundary and mark them for replacement once the contract or generator is available.

## UI States

For every data-driven screen, define or implement:

- Initial state.
- Loading state.
- Empty state.
- Error state with retry when appropriate.
- Success state.
- Mutation pending state.
- Mutation failure recovery.

For table-heavy screens:

- Filters reset pagination when changed.
- Sort and pagination state are explicit.
- Inline edits show row/cell-level pending and error states.
- Large tables may scroll horizontally; text must not overlap.

## Accessibility

- Use semantic HTML before custom roles.
- Associate labels, descriptions, and errors with inputs.
- Make focus states visible.
- Avoid color-only status communication.
- Dialogs must have clear cancel and confirm actions.
- Keyboard operation should work for common form, table, and dialog flows.

## Tests

Scale tests to risk:

- API client mapping and error normalization: unit tests.
- Composables/hooks with branching state: unit tests.
- Critical forms and destructive confirmations: component tests when test tooling exists.
- Main user flows: e2e tests when the project has Playwright/Cypress or similar.

## Final Check

Before finishing frontend work:

- Run the project formatter/linter/test commands when available.
- Verify no unrelated UI churn was introduced.
- Confirm loading/error/empty states for changed screens.
- Confirm API calls stay behind the project API boundary.
- Note any commands that could not be run.
