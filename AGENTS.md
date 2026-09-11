# Agent Guidance

This repository contains reusable production Java/Spring backend skills. Keep it focused on domain engineering and specialist review, not generic SDLC orchestration.

## Sources of truth

- `README.md` — composition model and ownership boundaries.
- Each `SKILL.md` — activation boundary and core contract for that skill.
- `references/` — domain-specific rules loaded only when relevant.

## Editing rules

- Preserve explicit user intent and scope; do not broaden a deliberately scoped task or review.
- Explicit user/repository instructions and documented decisions govern within higher-priority constraints. If they intentionally accept a backend risk, preserve that decision and make the consequence explicit.
- Treat inferred or repeated local conventions as evidence, not authority; do not preserve an unsafe pattern merely for consistency.
- Keep generic planning, TDD sequencing, debugging workflow, delegation, review lifecycle/output contracts, worktrees, completion verification, and branch lifecycle out of these domain skills.
- Keep Java/Spring/backend semantics, safety analysis, review evidence, and proof obligations here.
- Keep project-specific decisions and conventions in the target project's `AGENTS.md` or equivalent documentation, not in these reusable skills.
- Prefer decision criteria and observable invariants over rigid step-by-step procedure unless the risk genuinely requires a fixed sequence.
- Keep descriptions concise and discriminating: state triggering conditions and task context, preferably as `Use when...`; include a boundary only when it prevents likely misrouting. Keep workflow details in the body.
- Use progressive disclosure: keep always-needed guidance in `SKILL.md`; move conditional detail to focused references.
- Avoid duplicated rules across `SKILL.md` and references.
- Cross-skill references should be optional unless the companion skill is guaranteed to exist in the target environment.

## Validation

Before declaring a skill change complete:

- inspect the final diff for accidental scope growth or duplicated process rules;
- verify frontmatter, skill/folder naming, and relative reference paths;
- check that every reference is reachable from the relevant `SKILL.md`;
- run the current Codex skill validator when available;
- for behavior-shaping changes, validate with a fresh-context baseline without the change and the same scenario with the change; a contract-level walkthrough is not a substitute;
- if an independent runner is unavailable, record the validation gap and do not declare the behavior change complete;
- verify observable routing/behavior rather than wording or headings.
