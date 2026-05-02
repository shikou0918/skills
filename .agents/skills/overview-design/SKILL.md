---
name: overview-design
description: Create a Japanese overview design document from a requirements definition document, especially one produced by the requirements-definition skill. Use this skill whenever the user wants 概要設計, 基本設計, Design Doc, architecture/design documentation, or an implementation-ready design from requirements.md, 要件定義書, product requirements, or a rough app/system plan. Trigger even when the user only says "次は概要設計", "設計書を作って", "要件定義から設計に落として", or "Design Docにして".
---

# Overview Design Skill

Create a practical Japanese overview design document from a requirements definition document.

This skill is for the step between requirements definition and implementation. It should translate "what to build" into "how the system is shaped", while preserving the reasons behind important design choices.

It is designed to pair with `requirements-definition`: first create a 要件定義書 from a rough app idea, then use this skill to turn that requirements document into an implementation-ready overview/basic design.

## Inputs

Use the most relevant input the user provides:

- `requirements.md`
- 要件定義書
- PRD / product requirements
- Chat description of an app or internal tool
- Existing README, issue, or planning document

If a requirements file exists in the project, read it first. If the user gives both a file and chat instructions, treat the chat instructions as the latest clarification.

When the input is a document produced by `requirements-definition`, map its sections as follows:

| Requirements section | Design interpretation |
|---|---|
| 背景・目的 | 設計方針、システム目的、優先する品質 |
| ターゲットユーザー | ロール、権限、主要利用シーン |
| ユースケース | 主要処理フロー、画面遷移、ユーザー操作とシステム応答 |
| 機能要件 | 画面、API、処理フロー、データモデル |
| 非機能要件 | セキュリティ、性能、可用性、運用、保守性 |
| 画面構成案 | 画面一覧、画面責務、画面遷移 |
| 今後のステップ（確認事項） | 未決事項、設計上の仮定、次工程 |

## Workflow

1. Read the requirements and identify:
   - product purpose
   - target users
   - user roles and permissions
   - MVP scope
   - functional requirements
   - non-functional requirements
   - screens and user journeys
   - main entities and data ownership
   - data sources and external interfaces
   - open questions

2. Decide the design level:
   - Keep it at overview/basic design level.
   - Do not over-specify individual functions, classes, or implementation details unless they are important architectural decisions.
   - Include enough detail that implementation tasks can be split after the document.

3. Create or update `overview-design.md` unless the user requests another filename.

4. Use Japanese for the document unless the user requests another language.

5. Add diagrams with Mermaid when they clarify the design:
   - system context
   - screen transitions
   - data flow
   - import/processing flow
   Use diagrams sparingly. Prefer clear diagrams over decorative diagrams.

6. When a design decision is based on an assumption, say so clearly.

7. Preserve traceability to the requirements:
   - Do not silently drop functional or non-functional requirements.
   - If a requirement is intentionally postponed, list it in 初期スコープ外 or 未決事項.
   - If the requirements are vague, make a reasonable assumption and label it as an assumption instead of blocking the design.

## Output Structure

Use this structure by default.

```markdown
# [アプリケーション名] 概要設計書

## 1. 設計方針
### 1.1 目的
### 1.2 前提
### 1.3 スコープ
### 1.4 初期スコープ外

## 2. 全体アーキテクチャ
### 2.1 システム構成
### 2.2 技術スタック
### 2.3 採用理由
### 2.4 クライアント / サーバー責務分担

## 3. 画面設計
### 3.1 画面一覧
### 3.2 画面遷移
### 3.3 各画面の責務

## 4. データ設計
### 4.1 エンティティ一覧
### 4.2 主要テーブル
### 4.3 主なリレーション
### 4.4 制約・保持方針

## 5. 処理設計
### 5.1 主要ユースケース
### 5.2 主要処理フロー
### 5.3 API / バックエンド処理方針
### 5.4 エラーハンドリング

## 6. 外部インターフェース
### 6.1 外部入力
### 6.2 外部出力
### 6.3 外部サービス連携

## 7. 非機能設計
### 7.1 セキュリティ
### 7.2 パフォーマンス
### 7.3 保守性
### 7.4 運用・バックアップ

## 8. ディレクトリ構成案

## 9. 設計上の判断

## 10. 未決事項

## 11. 要件トレーサビリティ

## 12. 次の開発ステップ
```

## Writing Guidance

- Write for a developer who will implement the app next.
- Keep the document concrete enough to reduce implementation ambiguity.
- Prefer tables for screen lists, data models, and technology choices.
- Separate confirmed requirements from assumptions.
- Include "why this design" for meaningful choices such as local DB, frontend framework, encoding strategy, storage approach, and duplicate detection.
- Use 要件トレーサビリティ to show how major functional and non-functional requirements are reflected in the design.
- For items inferred by the agent, write "仮定:" or place them under 前提 / 未決事項. Do not present uncertain choices as confirmed user decisions.
- Avoid turning the overview design into a detailed implementation spec. If a section starts listing many function names or low-level validation branches, summarize and push the details into "次の開発ステップ" or "詳細設計で扱う項目".

## Recommended Design Decisions To Cover

For data import and analysis apps, explicitly cover:

- file encoding handling
- CSV/header assumptions
- import preview
- validation and error display
- duplicate detection
- data persistence
- aggregation basis
- category/rule application timing
- privacy and local processing
- export/backup strategy

For web apps, explicitly cover:

- routing/screen structure
- client/server responsibility split
- database choice
- API boundary
- authentication and authorization model
- chart/rendering strategy
- local development and deployment assumptions

For AI-assisted apps, explicitly cover:

- input source boundaries
- model/API boundary
- human review or approval points
- prompt/configuration management
- data minimization and privacy handling
- audit logs for generated, edited, or approved content

## Output Behavior

When editing a repository:

- Save the document as `overview-design.md` in the same directory as `requirements.md`, unless the user asks for a different location.
- Do not overwrite a non-empty existing overview design without first reading it and preserving useful content.
- If the requirements contain sensitive examples, avoid copying raw personal data into the design document. Use anonymized examples.
- If only a previously generated 要件定義書 is available in chat, create the design in chat unless the user asks to save it.

When answering in chat only:

- Provide the full overview design in Markdown.
- Keep it concise enough to be reviewable, but include all major sections.

## Quality Checklist

Before finishing, verify that the overview design:

- traces back to the requirements
- identifies MVP scope and out-of-scope items
- explains the system architecture
- lists main screens and responsibilities
- defines primary data entities
- explains major processing flows
- covers roles, permissions, and authentication assumptions
- covers security/privacy implications
- lists open questions separately
- includes a traceability section connecting requirements to design choices
- ends with concrete next development steps

## Example Trigger Prompts

- `requirements.mdから概要設計書を作って`
- `要件定義の次に基本設計を作成したい`
- `このアプリのDesign Docを書いて`
- `設計書をoverview-design.mdとして追加して`
- `MVP実装前にシステム構成とデータ設計を整理して`
