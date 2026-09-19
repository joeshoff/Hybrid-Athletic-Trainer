# Hybrid Athletic Trainer — Engineering Operating Model

## Purpose

This document defines how Hybrid Athletic Trainer is built and maintained. It separates product authority, implementation responsibility, independent review, and athlete acceptance so AI-assisted development is deliberate, reviewable, and reproducible.

`ARCHITECTURE.md` explains **what the system is**. `ROADMAP.md` explains **what is next**. This document explains **how work is produced**.

## Stable roles, replaceable execution environments

| Role | Core responsibility |
| --- | --- |
| Claude — Product Owner / Architect / Auditor | Product vision, requirements, acceptance criteria, architecture, roadmap, tradeoffs, and independent audit of delivered work |
| Lead Engineer | Turns a ready issue into a plan, selects a proportional team, coordinates delivery, resolves conflicts, and owns implementation consistency |
| Implementation specialists | Bounded backend/cloud, integration, iOS, data, AI/coach, or other discipline work |
| UX / product design | Information architecture, flows, terminology, accessibility, mobile UX, notification quality, and cognitive load; reviews before implementation |
| QA / validation | Independent test strategy, edge cases, regression, data integrity, integration validation, and acceptance evidence |
| Security / reliability | Secrets, permissions, privacy, failure modes, retries, idempotency, observability, backups, and unattended-operation criteria |
| Joe — athlete / product acceptance | Real-world fitness feedback and final acceptance that the product is useful, safe, and desirable |

Claude, Kiro, KiroCrew, and similar systems are replaceable execution environments. They can fill one or more roles; they do not become the architecture or product authority simply by writing code.

### Role separation across sessions

Effective 2026-09-16, Claude holds the Product Owner / Architect / Auditor role directly (ChatGPT is unavailable). Effective 2026-09-19, this repository also hosts Nutritionist as a sibling pillar (see ARCHITECTURE.md's Purpose and Current architecture). To keep these from muddling with each other, each role runs in a separate Claude Project/session that never shares conversation history, all reading this same repository as the single source of truth:

- **Trainer** — the SmartGym-connected coaching Project. Daily/weekly coaching only: reads the repository, proposes and executes authorized SmartGym changes. Does not redesign the program, edit the roadmap, or open engineering issues.
- **Nutritionist** — the menu, meal-planning, and shopping-guidance Project. Reads the repository for shared athlete context; recommends from a menu, plans meals and shopping, and (once built) tracks macros. Does not redesign the training program, edit the roadmap, write to SmartGym, or open engineering issues.
- **Product Owner / Architect / Auditor** — a dedicated Project/session. Owns `ARCHITECTURE.md`, `ROADMAP.md`, and this document; classifies new ideas per Handling New Ideas below; opens and refines GitHub Issues; audits delivered work against acceptance criteria. Does not give workouts or write to SmartGym.
- **Lead Engineer + specialists** — a Cowork/Claude Code session working in this repository, scoped to a specific ready Issue. Implements, and requires independent QA/security review before merge.

In a Cowork/Claude Code session, the "team" in the Proportional teams table below is the Lead Engineer session invoking one subagent per relevant role (implementer, UX reviewer, QA, security/reliability) rather than one conversation reasoning from every perspective at once — sequential for Level 1, parallel/pipelined for Level 2–3.

## Proportional teams

| Work level | Example | Minimum operating shape |
| --- | --- | --- |
| Level 1 — simple | Documentation correction, narrow bug fix, isolated report metric | Lead/implementer + QA review |
| Level 2 — feature | Daily athlete briefing, coaching-data view | Lead + relevant specialist(s) + UX + QA |
| Level 3 — system | Notifications, mobile client, Trainer Event Engine | Product/architecture + Lead + backend/cloud + AI/coach + data + UX + security/reliability + QA |

Roles may be sequential. The requirement is independent, meaningful review—not a ritualized agent count.

## Handling New Ideas

Before anything Joe raises becomes an Issue, a roadmap line, or an architecture change, the Product Owner / Architect / Auditor session classifies it into exactly one of the categories below and says which one out loud. Classification is not drafting: a vague or incomplete idea is refined by asking Joe, not by silently rounding it up to whichever category is easiest to act on, and it is never silently promoted straight to a permanent requirement.

| Category | What it is | Where it goes | What happens next |
| --- | --- | --- | --- |
| Observation | A fact noticed during training, coaching, or system use — not yet a request for change | Logged in `05_COACHING_LOG.md` or the relevant document; no roadmap or architecture change | May later sharpen into another category; carries no commitment on its own |
| Requirement | A stated need or constraint the system must satisfy, but not yet a plan for how to satisfy it | Captured in `01_MASTER_PROGRAM.md`, `ARCHITECTURE.md`, or a roadmap line, depending on scope | Refined until it is either a roadmap item, an architectural decision, or folded into an existing one |
| Roadmap item | A capability worth building, not yet committed to a phase or priority | Added to `ROADMAP.md` under the appropriate phase, or under "Flagged for Product Owner review" when phase, scope, or a prerequisite decision isn't yet settled | Moves to Foundation/Planned status, with priority and dependencies, once it's ready to be written up |
| Architectural decision | A choice that changes what the system is, how its components relate, or a boundary in `ARCHITECTURE.md` | Documented in `ARCHITECTURE.md` (or explicitly recorded there as deferred) with the decision and its rationale | Requires Joe's explicit sign-off before any Lead Engineer work depends on it |
| Implementation task | A scoped, ready piece of work | Opened as a GitHub Issue only once it meets the Definition of Ready | Enters the Work flow below |
| Experiment | A time- or scope-boxed exploration meant to produce evidence, not a shipped capability | Noted in `ROADMAP.md` or a coaching-log entry as an experiment, naming the question it answers and how it concludes | Its outcome is itself the next idea to classify — a successful experiment does not silently become a permanent requirement |

An idea can span more than one category — a new athlete-facing capability is often a roadmap item *and* an architectural decision at once. When that happens, say so explicitly rather than picking one. No conversational idea skips this step on its way to an Issue or a code change; that is what keeps Lead Engineer sessions from inventing product scope, and it is why this classification belongs to the Product Owner / Architect / Auditor role and no other.

## Work flow

```text
GitHub Issue → Product readiness → Lead plan → UX/design review
     → implementation → independent QA/security review → Lead review
     → Product Owner + Joe acceptance when needed → merge/release
```

GitHub Issues are the work queue and decision record. Pull requests are the review mechanism. Non-trivial issues link relevant architecture, roadmap, requirements, UX decisions, test evidence, and operational notes instead of relying on chat history.

## Definition of Ready

An issue is ready when it has:

- user/problem statement, intended outcome, scope, and non-goals;
- observable or testable acceptance criteria;
- relevant architecture, data, integration, and UX boundaries;
- risk classification, including SmartGym, health-data, notification, and safety implications;
- dependencies and unresolved decisions visible;
- a proportionate test and verification approach.

Ambiguity is refined before implementation; it is not silently guessed into product behavior.

## Definition of Done

### Product and architecture

- [ ] Acceptance criteria are met and non-goals preserved.
- [ ] The change conforms to `ARCHITECTURE.md`, or an approved deviation is documented.
- [ ] Relevant roadmap, decision, operational, and user-facing documents are updated.

### Engineering and UX

- [ ] Lead Engineer review is complete.
- [ ] Implementation is understandable, bounded, and avoids unnecessary duplication.
- [ ] UX flow, mobile behavior, terminology, and accessibility are reviewed where applicable.
- [ ] Notifications are actionable, preference-aware, rate-limited, and non-spammy where applicable.

### QA, security, and reliability

- [ ] Appropriate unit, integration, regression, and failure/edge-case tests pass.
- [ ] Data integrity, retry/idempotency, and recovery behavior are tested where relevant.
- [ ] No secrets are committed; permissions are minimized; health data follows the approved privacy model.
- [ ] Logging, auditability, and observability match the risk level.

### Acceptance

- [ ] Product Owner confirms the product contract.
- [ ] Joe accepts changes that affect training behavior, safety, or daily usability.

## SmartGym change protocol

SmartGym changes affect a real training program and receive a higher review bar:

1. Read the live target by routine `z_pk`; never match on routine name.
2. Compare the change with documented program rules, recent training, and athlete status.
3. State target, scope, expected result, and authorization needed.
4. Obtain explicit athlete authorization for material or structural changes.
5. Apply the smallest supported operation.
6. Re-read and verify order, catalog IDs, `ue_pk` slots, sets, reps, notes, rest, and absence of unrelated changes.
7. Record rationale, authorization, backup/verification evidence, and uncertainty.

Backup-before-write is a safety net, not a rollback plan or substitute for authorization. Unsupported operations, unconfirmed recovery paths, pain/injury signals, or ambiguous scope stop the write.

## Review and testing gates

| Change risk | Required gate |
| --- | --- |
| Documentation or isolated non-user-facing change | Lead review and factual/document consistency check |
| Product feature without sensitive data or external writes | Lead + QA; UX when behavior changes |
| Notifications, mobile behavior, or health-data processing | Lead + UX + QA + security/reliability review |
| SmartGym writes, event automation, or cloud integration | Product/architecture + Lead + QA + security/reliability; explicit athlete approval when training behavior changes |

AI-generated implementation never bypasses a gate. Review checks the product contract and safety properties, not just whether code compiles.

## Documentation and reproducibility

- Version architectural facts, product decisions, runbooks, and acceptance evidence.
- Record assumptions and unresolved questions; do not turn observed integration behavior into a guarantee without evidence.
- Link issues and PRs to preserve provenance.
- Maintain enough setup guidance for a new agent or execution environment to reproduce and validate the MCP connection.
- Preserve `z_pk` contracts; annotate routine snapshots with verification date and source.
- Never commit secrets, raw private health data, or unnecessary backup contents.

## Release posture

The release question is not only “does it work?” It is “can it behave safely and explainably in the athlete’s real life?” Coaching actions, notifications, health-data handling, and unattended operation require evidence of safe failure, non-duplication, auditability, and meaningful athlete control.
