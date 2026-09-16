# Hybrid Athletic Trainer — Engineering Operating Model

## Purpose

This document defines how Hybrid Athletic Trainer is built and maintained. It separates product authority, implementation responsibility, independent review, and athlete acceptance so AI-assisted development is deliberate, reviewable, and reproducible.

`ARCHITECTURE.md` explains **what the system is**. `ROADMAP.md` explains **what is next**. This document explains **how work is produced**.

## Stable roles, replaceable execution environments

| Role | Core responsibility |
| --- | --- |
| ChatGPT — Product Owner / Architect | Product vision, requirements, acceptance criteria, architecture, roadmap, and tradeoffs |
| Lead Engineer | Turns a ready issue into a plan, selects a proportional team, coordinates delivery, resolves conflicts, and owns implementation consistency |
| Implementation specialists | Bounded backend/cloud, integration, iOS, data, AI/coach, or other discipline work |
| UX / product design | Information architecture, flows, terminology, accessibility, mobile UX, notification quality, and cognitive load; reviews before implementation |
| QA / validation | Independent test strategy, edge cases, regression, data integrity, integration validation, and acceptance evidence |
| Security / reliability | Secrets, permissions, privacy, failure modes, retries, idempotency, observability, backups, and unattended-operation criteria |
| Joe — athlete / product acceptance | Real-world fitness feedback and final acceptance that the product is useful, safe, and desirable |

Claude, Kiro, KiroCrew, and similar systems are replaceable execution environments. They can fill one or more roles; they do not become the architecture or product authority simply by writing code.

## Proportional teams

| Work level | Example | Minimum operating shape |
| --- | --- | --- |
| Level 1 — simple | Documentation correction, narrow bug fix, isolated report metric | Lead/implementer + QA review |
| Level 2 — feature | Daily athlete briefing, coaching-data view | Lead + relevant specialist(s) + UX + QA |
| Level 3 — system | Notifications, mobile client, Trainer Event Engine | Product/architecture + Lead + backend/cloud + AI/coach + data + UX + security/reliability + QA |

Roles may be sequential. The requirement is independent, meaningful review—not a ritualized agent count.

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
