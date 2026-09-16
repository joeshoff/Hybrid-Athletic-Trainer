# Hybrid Athletic Trainer — Roadmap

## How to use this roadmap

This is a prioritized capability backlog, not a date promise. It protects the working coaching loop from future-product ambition. A capability moves only when its dependencies, safety boundary, and acceptance criteria are clear.

| Status | Meaning |
| --- | --- |
| Proven | Demonstrated in the current system |
| Foundation | Necessary next work |
| Planned | Direction agreed; not started |
| Deferred | Intentionally not near-term |

## Phase 0 — Preserve and document the working loop

| Priority | Capability | Status | Effort | Dependency / outcome |
| --- | --- | --- | --- | --- |
| P0 | Durable program, routine, cardio, and weekly documents | Proven | Low | Inspectable source of program intent |
| P0 | SmartGym MCP read, scoped write, backup, and read-back verification | Proven | Medium | Verified Lower B `z_pk` 26 write |
| P0 | Stable routine identity contract | Proven | Low | Query/write by `z_pk`, not `NULL` routine names |
| P0 | Repeatable coaching-session checklist and evidence capture | Foundation | Low | Consistent inspect → propose → authorize → verify loop |
| P0 | Reconcile repository snapshots with live SmartGym | Foundation | Low | Prevents stale documentation from driving decisions |

## Phase 1 — Explainable and durable coaching decisions

| Priority | Capability | Status | Effort | Dependency / outcome |
| --- | --- | --- | --- | --- |
| P1 | Decision and change log | Planned | Low | Inputs, rationale, authorization, and verification survive the session |
| P1 | Trainer Event Engine contract | Planned | Medium | Event types, coaching state, action classes, audit records, idempotency |
| P1 | Autoregulation policy proposal and simulation | Planned | Medium | Requires observed sessions; no automatic write authority until approved/tested |
| P1 | Readiness and joint-status input | Planned | Medium | Explainable day-of cardio and strength adjustments |
| P1 | Longitudinal performance and recovery view | Planned | Medium | Reliable ingestion and athlete-facing interpretation |
| P1 | SmartGym integration hardening | Planned | Medium | Capability inventory, error paths, recovery drill, history/schema verification |

## Phase 2 — Proactive coaching and notifications

| Priority | Capability | Status | Effort | Dependency / outcome |
| --- | --- | --- | --- | --- |
| P1 | Daily athlete briefing | Planned | Medium | Event engine, current plan, readiness input, and next action |
| P1 | Proactive notification policy | Planned | Medium | Consent, quiet hours, preferences, deduplication, and non-spam UX |
| P1 | Useful trigger set | Planned | Medium | Completed/missed workout, readiness change, scheduled session, recovery concern, approval need |
| P1 | Notification delivery and audit trail | Planned | Medium | Authenticated service, retries/idempotency, delivery status, controls |
| P2 | Escalation and exception flow | Planned | Medium | Pain/injury, uncertainty, and material-program decisions route to Joe |

Notifications must be timely, explainable, actionable, rate-limited, easy to suppress, and never generic motivation spam.

## Phase 3 — Athlete-facing mobile product

| Priority | Capability | Status | Effort | Dependency / outcome |
| --- | --- | --- | --- | --- |
| P1 | iOS mobile UI | Planned | High | Product/UX specification, secure API, event engine; briefing, session, readiness, explanation |
| P1 | Mobile-first UX and accessibility | Planned | Medium | UX review before implementation; concise actions over dashboard sprawl |
| P2 | Apple Health integration | Planned | High | Consent, privacy model, data contract, reconciliation |
| P2 | Apple Watch companion | Planned | High | iOS foundation; low-friction readiness and in-workout support |
| P2 | Post-session reflection | Planned | Medium | RPE, pain, completion, and qualitative signals for the event engine |

## Phase 4 — Service foundation and reliability

| Priority | Capability | Status | Effort | Dependency / outcome |
| --- | --- | --- | --- | --- |
| P1 | AWS service foundation | Planned | High | Authenticated API, event processing, storage, notifications, observability |
| P1 | Security and privacy controls | Planned | High | Least privilege, secrets, encryption, retention, health-data access review |
| P1 | Reliability controls | Planned | High | Idempotency, retries, dead letters, audit logs, recovery exercises, alerting |
| P2 | Integration abstraction | Planned | Medium | SmartGym, Apple Health, and delivery providers remain replaceable |

## Deferred

- Commercial multi-athlete launch, social features, or monetization.
- Unsupervised program redesign or broad SmartGym write authority.
- Replacing SmartGym’s workout log before a deliberate migration decision.
- Cloud/mobile build-out before a tested event-policy contract.

## Sequencing rule

First document and verify the loop; then formalize decisions and events; then add proactive notifications; then mobile interaction; then the service reliability needed to operate confidently. Prototypes may explore later phases, but may not bypass earlier safety and decision contracts.
