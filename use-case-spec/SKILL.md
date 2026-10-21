---
name: use-case-spec
description: Use when drafting, refining, or reviewing a concise functional specification for a software feature using structured Use Cases, inputs/outputs, step-by-step flows, and significant alternatives. Not for implementation plans or code changes.
---

# Compact Use Case Specification

Prepare a concise, unambiguous, testable functional specification for developers familiar with the project. Use a lightweight adaptation of Alistair Cockburn's Fully Dressed Use Case, with PSPEC-style steps for internal backend processes. This is an output format skill, not a replacement for the host's requirements-discovery or implementation workflow.

Priority: **unambiguous behavior → essential completeness → readability → brevity**.

## Ground the requirements

1. Read the task, prior discussion, and supplied evidence. When a repository is available, inspect relevant contracts, code, and documentation.
2. Identify independently triggered processes by their goal and outcome. Separate confirmed requirements/current behavior from assumptions and suggestions.
3. Follow the active host workflow for elicitation. If important business decisions are missing, ask up to five focused questions when appropriate; do not ask for facts already established in the sources.
4. If unresolved details do not block the rest of the document, write the known behavior and keep the decision in **Открытые вопросы**. Never promote an inference to an approved requirement.
5. Do not reopen questions already resolved by confirmed code, contracts, or decisions.

## Output structure

Write the specification in the user's language. Use this structure, omitting genuinely empty optional sections:

```markdown
# <Название задачи; номер укажи только при наличии>

<Одно–два предложения: что меняется и зачем.>

## UC-01. <Название самостоятельного сценария>

**Точка входа**

<Инициатор и конкретный механизм запуска: HTTP method + path,
событие/consumer, scheduled job, запуск загрузчика при релизе и т. д.>

**Входные данные**

| Поле | Обязательное | Комментарий |
|---|---|---|
| `field_name` | Да / Нет / Условно | Назначение и ограничения |

**Результат (выходные данные)**

<Успешный результат или компактная таблица того же формата.>

**Основной сценарий**

1. <Логическое действие системы.>
2. <Следующее действие.>

**Альтернативные сценарии**

- **2a.** <Условие → действие системы → результат.>

## UC-02. <Следующий самостоятельный сценарий, если есть>

<Та же структура.>

## Бизнес-правила и ограничения

<Только общие правила, которые нельзя удобно выразить в одном UC.>

## Открытые вопросы

<Только нерешённые существенные решения.>
```

## Authoring rules

### Use Case boundaries and entry points

- **One UC = one cohesive behavior scenario.** Separate independently meaningful modes with materially different flows, goals, results, or lifecycle triggers, even when they share an endpoint. Do not split trivial actions or routine error branches into separate UCs.
- The **entry point** is required for every UC. Identify both the trigger and the actual interface/mechanism (HTTP method + path, message consumer, scheduled job, deployment hook, etc.). If not established, mark it as unresolved; do not invent an endpoint or handler.
- Share contracts and rules across UCs only once. Introduce a named subordinate operation only if it materially improves readability.

### Inputs, outputs, and JSON

- Use tables when the scenario has several meaningful structured fields. Otherwise a brief description is sufficient.
- Keep **only fields actually used by that UC**. Do not add absent fields or fields belonging to another mode.
- Copy **exact technical field names** from real schemas/code, never translate them into prose. Use dotted paths for nesting (`document.data`, `items[].amount`). For path/query/header parameters, use their actual identifiers and do not pretend they are JSON body fields.
- `Обязательное` describes requirement **within this particular UC**, not necessarily the global schema.
- If a new field name or requiredness has not been decided, record the missing decision outside the table rather than inventing a name.
- For unchanged existing contracts, reference the schema and document only changed or relevant fields. Include a minimal example JSON only when it clarifies a complex new/changed structure and is backed by confirmed data; do not duplicate large opaque payloads.

### Behavior and exceptions

- Number main-flow steps in execution order; each step describes one logical action.
- Describe ordinary decisions, precedence, and fallback inline under the relevant step. Do not force the reader to jump between numbered steps.
- Put only significant divergent flows or errors in **Альтернативные сценарии**, keyed to the main step (`3a`, `4a`). Specify resulting behavior. Omit the section entirely when there are none.
- Describe observable behavior and required constraints, not Java classes, method calls, patterns, or internal implementation choices unless specifically mandated.

### Evidence, scope, and brevity

- Describe **new or changed behavior**; refer to existing code/contracts instead of restating unchanged mechanics.
- Separate approved business requirements and repository facts from hypotheses and implementation recommendations. Never silently add retries, timeouts, fallback, atomicity, idempotency, or authorization policies.
- Cite meaningful sources with repository URLs or relative paths, never machine-specific absolute paths.
- Do not repeat requirements across scenarios, tables, and global rules. Do not add acceptance-criteria chapters, test plans, diagrams, or technical implementation plans unless explicitly requested.
- Keep the document readable for a developer familiar with the project: remove sentences that do not affect implementation or verification.

## Final check

Before returning the specification, verify each UC has an **entry point → inputs → outcome → ordered algorithm**, real identifiers and scenario-specific requiredness, significant deviations, and no contradictory or unapproved behavior. Keep unresolved material decisions visible without inventing an answer.

Return only the requested Markdown specification when the user asks for a finished spec. Do not start implementing, modifying source code, or decomposing into tickets unless separately requested.
