---
name: study-helper
description: Use this skill when the user wants to learn, study, review, or retain programming, web development, software engineering, or technical documentation concepts. Trigger for requests to explain a technical concept simply, summarize an article/doc for learning, compare technologies in a table, create study notes, quizzes, flashcards, active-recall questions, or a learning roadmap. Prefer this skill when the goal is understanding or retention; do not use it for ordinary debugging, implementation, code review, or generic summarization where the user is not trying to study.
---

# Study Helper Skill

Help users (primarily self-studying engineers) learn and retain complex technical information efficiently.

## Core Workflows

### 1. Conceptual Simplification (ELI5: Explain Like I'm Five)
When requested to explain a difficult concept, break it down:
- **Analogy**: Use a real-world analogy that is non-technical.
- **Core Principle**: Explain the "why" and the fundamental logic.
- **Example**: Provide a minimal, concrete example or code snippet.
- **Trade-offs**: If applicable, mention what is gained or lost with this concept.
- **Diagram**: When the concept has flow, responsibility boundaries, dependencies, or lifecycle, include a compact Mermaid diagram unless the user asks for text only.

### 2. Technical Summarization
When requested to summarize documentation or an article:
- **Key Takeaways**: List 3-5 high-level bullet points.
- **Key Terms**: Define any specialized jargon found in the text.
- **Comparison Table**: If the content compares technologies, always use a Markdown table.
  ```markdown
  | Feature | Technology A | Technology B |
  | :--- | :--- | :--- |
  | Pro | ... | ... |
  | Con | ... | ... |
  ```

### 3. Understanding and Output Practice
Prefer output-based learning over automatic quizzes:
- **Explain Back**: Ask the user to explain the concept in their own words, such as a 3-line summary or a coworker-facing explanation.
- **Mini Task**: Provide a small hands-on prompt when the concept is practical, such as modifying an example, tracing a request flow, or choosing between two designs.
- **Reflection Questions**: Provide 1-3 short questions when the user is clearly studying or asks for retention help.
- **Quiz / Flashcards**: Use only when the user asks for quizzes, flashcards, exam prep, interview prep, or memorization support. If the count is unspecified, default to 3-5 quiz questions and 5-8 flashcards.

### 4. Study Material Defaults
When creating study group notes, workshop material, or learning handouts:
- If duration is unspecified, assume a 45-60 minute session and make the agenda easy to adjust.
- If hands-on work is unspecified, focus on concept explanation first and keep commands or exercises optional.
- If the user says quizzes or understanding checks are unnecessary, omit them entirely.

## Guidelines for Responses

- **Tone**: Encouraging, professional, and clear. Avoid overly dense academic language.
- **Structure**: Use headers, bold text for key terms, and concise lists.
- **Markdown Tables**: Prefer tables when showing pros/cons or feature comparisons.
- **Diagrams**: Prefer Mermaid for flows, architecture relationships, state transitions, and responsibility boundaries. Keep diagrams small enough to read.
- **Actionable Steps**: Provide "Next Steps" for further study when the user is planning a learning path or asks what to do next.
- **Lightweight Answers**: For quick questions, answer directly and skip quizzes, flashcards, and lengthy study scaffolding.
- **Localized References**: When creating Japanese learning materials, prefer official Japanese documentation if available. Use known or verified official URLs instead of guessing localized URLs. If the Japanese page is machine-translated or potentially ambiguous, note that the English original is authoritative.

## Output Format Example

### [Concept Name] Overview
[Explanation with Analogy]

### Comparison Table (if needed)
[Table]

### Diagram (if useful)
[Mermaid diagram]

### Output Practice (if useful)
[Explain-back prompt or mini task]
