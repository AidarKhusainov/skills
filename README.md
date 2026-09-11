# Production Java Backend Agent Skills

Domain-specific skills for agents working on production Java/Spring backend systems.

This repository intentionally does **not** own the complete software-development workflow. It composes with a host workflow or framework such as [Superpowers](https://github.com/obra/superpowers), while keeping Java/backend engineering policy and specialist review knowledge here.

## Responsibility layers

Use three separate layers:

1. **General agent workflow** — the host or workflow framework owns brainstorming, planning, debugging process, test sequencing, worktrees, delegation, review lifecycle/output contracts, completion verification, and branch/merge workflow.
2. **Backend domain skills** — this repository owns Java/Spring semantics, DDD and architecture boundaries, contracts, persistence and migrations, resilience, security, observability, performance, runtime concerns, backend review evidence, and backend-specific proof obligations.
3. **Repository-local instructions** — `AGENTS.md` and project documentation own explicit project decisions and conventions specific to one codebase, such as approved libraries, module rules, migration tooling, naming, and exact verification commands.

A domain skill should not redefine generic process rules merely because those rules happen while changing backend code.

## Skills

### `production-java-backend-development`

Use for production Java/Spring implementation, debugging, refactoring, testing decisions, or technical evaluation of backend review findings.

It determines which backend semantics are at risk, which domain rules apply, what compatibility/safety constraints must hold, what evidence can prove them, and which unresolved product or operational decisions require escalation.

### `strict-backend-code-review`

Use for dedicated review-only analysis of Java/Spring diffs and PRs.

It supplies backend issue discovery, focused passes, evidence validation, false-positive challenge, negative-space analysis, and finding thresholds. When the active host review workflow defines severity, recommendations, verdict, or output format, that host contract controls presentation.

## Composition

```text
user request
    |
    v
host workflow / Superpowers
(plan -> implement/debug -> review/receive -> verify -> finish)
    |
    +--> production-java-backend-development
    |    (domain constraints + backend proof/evaluation)
    |
    +--> strict-backend-code-review
         (backend review discovery + evidence)
```

The boundary is:

> **The active workflow decides how the agent works and how workflow outputs are presented. Backend skills decide what correct and safe backend engineering must preserve, inspect, and prove.**

Process frameworks may intentionally be stricter than Codex/OpenAI defaults. For example, Superpowers can impose mandatory test-first development. Such rules are opt-in workflow policy; these backend skills stay neutral about sequencing and specify the backend semantics that require proof.

## Ownership matrix

| Concern | Owner |
| --- | --- |
| Requirements discovery, planning, generic debugging process | host workflow / Superpowers |
| Test-first sequencing and generic completion verification | host workflow / Superpowers |
| Worktrees, delegation, branch completion | host workflow / Superpowers |
| Review output format, severity, recommendations, verdict | active host review workflow when defined |
| Incoming-review clarification, implementation readiness/order | active host review-reception workflow when defined |
| Java/Spring semantics | `production-java-backend-development` |
| DDD, invariants, bounded contexts, dependency direction | `production-java-backend-development` |
| API/event compatibility | `production-java-backend-development` |
| Transactions, persistence, migrations | `production-java-backend-development` |
| Retries, idempotency, messaging, distributed failure semantics | `production-java-backend-development` |
| Authentication, authorization, tenancy, sensitive data | `production-java-backend-development` |
| Observability, runtime health, performance, service-owned container/Kubernetes behavior | `production-java-backend-development` |
| Backend PR/diff issue discovery and evidence | `strict-backend-code-review` |
| Explicit project decisions and project-specific conventions | repository `AGENTS.md` / project docs |

## Composition rules

- Do not fork or copy a general workflow framework into these skills just to customize Java/backend behavior.
- Do not duplicate generic `MUST` rules for planning, TDD sequencing, debugging, delegation, review lifecycle, or completion verification.
- Domain skills may specify **what backend behavior needs proof**, **which evidence supports a review finding**, and **which test or measurement boundary can prove a property**.
- When a host review workflow defines output fields, severity, recommendations, or verdict, `strict-backend-code-review` supplies backend analysis inside that contract; its standalone handoff format is a fallback only.
- When a host workflow governs incoming review feedback, `production-java-backend-development` evaluates backend findings but does not decide clarification gates, implementation readiness, or fix ordering.
- Explicit user/repository instructions and documented decisions govern within higher-priority constraints. If they intentionally accept a backend risk, preserve that decision and make the consequence explicit rather than silently overriding it.
- Inferred or repeated local conventions are evidence, not authority; do not copy an unsafe pattern merely for consistency.
- Load only references for materially affected concerns; file type alone is not enough reason to load every reference.
- Cross-skill references should remain optional unless the companion skill is guaranteed to exist in the target environment.
- These skills do not require Superpowers. When it is absent, the host's own workflow governs process.

## Maintaining the skills

Treat skill changes as behavior changes, not prose cleanup. Keep descriptions concise and discriminating: state triggering conditions and task context, preferably as `Use when...`; include a boundary only when it prevents likely misrouting. Keep workflow details in the body. Prefer narrow corrections backed by representative scenarios over accumulating universal rules.
