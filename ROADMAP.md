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
| P0 | Sessions without a `ZWORKOUT` row stay visible in history | Foundation | Low | Issue #9. Follow-up to #7: anchor history on `ZHISTORY` so a logged session never reads as "didn't train" |
| P0 | SmartGym write tools: explicit weight unit + plausibility guard | Foundation | Medium | Issue #8. Follow-up to #7: prevents lb values being written as kg into live prescriptions |
| P0 | Workout-scoped SmartGym set history + per-workout detail read (`smartgym_get_workout_detail`) | Foundation — merged 236af09 (Issue #7), release pending | Medium | Fixes `smartgym_get_routine` grouping sets by row-creation date instead of workout; gives the Trainer a post-workout read. Blocks accurate progression decisions (found 2026-09-23) |

Note: 2026-09-23 — `smartgym_get_routine` in `smart-gym-mcp` v0.2.0 reports per-exercise history wrong once a routine repeats (first seen on Lower A, `z_pk` 25, after workout_pk 11 on 9/22). A workout that repeats the previous one set for set collapses into a single session dated with the older workout; partial repeats split across dates. Loads and reps are correct. Code read: the history query groups `ZVALUES` rows by `date(ZDATEADDED)` (the day the set row was created), does not exclude template sets (`ZDATELOGGED IS NULL`), and never links a set to its workout. It does no content deduplication. Decision: fix in place in a Joe-owned fork of `smart-gym-mcp` rather than a new access module (see ARCHITECTURE.md Known limits). Tracked as a GitHub Issue; read-only against the DB.

## Phase 1 — Explainable and durable coaching decisions

| Priority | Capability | Status | Effort | Dependency / outcome |
| --- | --- | --- | --- | --- |
| P1 | Decision and change log | Planned | Low | Inputs, rationale, authorization, and verification survive the session |
| P1 | Trainer Event Engine contract | Planned | Medium | Event types, coaching state, action classes, audit records, idempotency |
| P1 | Autoregulation policy proposal and simulation | Planned | Medium | Requires observed sessions; no automatic write authority until approved/tested |
| P1 | Readiness and joint-status input | Planned | Medium | Explainable day-of cardio and strength adjustments |
| P1 | Longitudinal performance and recovery view | Planned | Medium | Reliable ingestion and athlete-facing interpretation |
| P1 | SmartGym integration hardening | Planned | Medium | Capability inventory, error paths, recovery drill, history/schema verification |
| P1 | SmartGym read cache (Google Drive) | Planned | Low-Medium | Decided 2026-09-20 (see ARCHITECTURE.md); read-only, timestamped snapshot for sessions without Mac access; never authoritative |

Note: a 2026-09-17 finding sharpens this item's scope — a successful MCP write does not guarantee timely or complete iOS sync (delayed, partially diverged, or silently reverted by an unrelated app action), and the app surfaces no staleness or divergence indicator; see ARCHITECTURE.md's Known limits. This occurred on a safety-relevant change (an injury-risk exercise swap), not only a cosmetic one. The fix approach — confirmation-required writes, a sync-status check surfaced to the coach, eventual-consistency warnings, or another design — is an open architectural decision, not yet made.

Note: a 2026-09-19 coaching-log observation (see 05_COACHING_LOG.md) proposes automating within-session aerobic-decoupling detection (split a long steady-state session at the midpoint, compare avg HR vs. avg pace/speed each half) from Health Auto Export data, rather than doing it by hand. This sharpens this item's scope as a candidate sub-capability; it is not yet a committed roadmap item — raised here as a flag for Product Owner review, not decided.

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
| P2 | Apple Health advisory fallback for missing SmartGym workouts | Planned | Low | Decided 2026-09-20 (see ARCHITECTURE.md); advisory-only, explicitly labeled unverified, never written into SmartGym |

Note: a personal Apple Health data pipeline (Health Auto Export → Google Drive primary, local MCP on-demand fallback) is already running for the athlete outside the product — see ARCHITECTURE.md's "Apple Health data ingestion" section. It supplies raw export data only; the consent, privacy model, and reconciliation work in the Apple Health integration row above remains Planned.

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

## Nutrition

New sibling pillar, decided 2026-09-19 (see ARCHITECTURE.md's Purpose, Current architecture, and Nutritionist section; ENGINEERING.md's Role separation). Phase numbering here starts fresh rather than continuing Phase 0-4 above, which remain Trainer-specific.

| Priority | Capability | Status | Effort | Dependency / outcome |
| --- | --- | --- | --- | --- |
| P0 | Nutritionist Claude Project, reading this repository per the Trainer role-separation pattern | Foundation | Low | Same read-only, no-shared-history boundary Trainer has |
| P1 | Menu-photo restaurant recommendation | Foundation | Low | Already demonstrated ad hoc, outside this repository; formalize as a repeatable capability |
| P1 | Meal-prep and grocery-list menu planning | Planned | Medium | Builds on the menu-photo capability |
| P2 | Macro tracking | Planned | Medium | Requires a food-logging backend/integration decision and a decision on whether it couples to training load / macro targets (see ARCHITECTURE.md's Deliberately not being built yet) |

## Sequencing rule

First document and verify the loop; then formalize decisions and events; then add proactive notifications; then mobile interaction; then the service reliability needed to operate confidently. Prototypes may explore later phases, but may not bypass earlier safety and decision contracts.
