# Hybrid Athletic Trainer — Architecture

## Purpose

Hybrid Athletic Trainer is a small, safety-conscious coaching system for one athlete. It connects durable program knowledge, an AI trainer, and the SmartGym training record:

1. Program rules and constraints guide a coaching decision.
2. The trainer reads program files, live SmartGym state, history, and athlete feedback.
3. The trainer recommends or, within an approved future policy, makes a narrowly scoped adjustment.
4. SmartGym records the routine and completed work; actual completed work overrides the plan.

The repository is durable program memory, not a chat archive or a replacement for SmartGym.

## Principles

- Separate **intent** (repository), **reasoning** (trainer), and **record** (SmartGym).
- Use stable SmartGym identifiers, narrow changes, immediate verification, and explicit uncertainty.
- Joe is the final decision-maker and athlete/product-acceptance authority.
- Roles and contracts belong to the project; Claude, Kiro, KiroCrew, or another capable environment may execute them.
- Trainer, Product Owner/Architect/Auditor, and Lead Engineer are kept in separate Claude Projects/sessions with no shared conversation history, so no single session reasons across all three roles at once; see ENGINEERING.md's Role separation section.
- Preserve enough documentation and evidence for a new execution environment to reproduce the system safely.

## Current architecture

| Component | Responsibility | Boundary |
| --- | --- | --- |
| Joe / athlete | Goals, readiness and joint-status feedback, completed training, product acceptance | Final decision-maker; actual work is authoritative |
| GitHub repository | Program specification, constraints, routine snapshot, cardio framework, and short-term plan | Durable intent, not the live workout record |
| Claude + SmartGym MCP | Current trainer interface: reads, reasons, proposes, and can make authorized routine writes | Does not redesign the program without approval |
| SmartGym | Routine execution, exercise library, logging, history, and device sync | Live execution and training record |
| `smart-gym-mcp` | Local bridge to the SmartGym database | Controlled integration surface, not a coach |
| Claude (Product Owner / Architect / Auditor session) | Product Owner, architect, and auditor | Product/architecture authority; not primary trainer or record system |

```text
repository program files ──> trainer decision ──> SmartGym MCP ──> SmartGym
         ^                       |                    |              |
         |                       v                    v              v
 durable rules and docs <── athlete feedback    backup + re-read  completed history
```

The trainer starts with relevant repository files and a live SmartGym read, then considers recent training, recovery, joint status, and schedule. `04_TODAY.md` is deliberately short-lived rather than an archive.

### Repository responsibilities

- `01_MASTER_PROGRAM.md` — athlete profile, weekly architecture, equipment, constraints, and programming principles.
- `02_SMARTGYM_CURRENT_ROUTINES.md` — routine snapshot and identity conventions; live SmartGym is more current.
- `03_CARDIO_PLAN.md` — cardio framework and adaptive decision procedure.
- `04_TODAY.md` — current-week prescription.
- `ARCHITECTURE.md`, `ROADMAP.md`, and `ENGINEERING.md` — system, product direction, and build operating model.

## SmartGym integration and identities

Routine names are currently `NULL`. Use routine `z_pk` for every MCP query or write; human labels are only conversational.

| Routine `z_pk` | Day | Human label | SmartGym `days` |
| --- | --- | --- | --- |
| 24 | Monday | Upper A | 2 |
| 25 | Tuesday | Lower A | 3 |
| 23 | Thursday | Upper B | 5 |
| 26 | Friday | Lower B | 6 |

- A catalog exercise `z_pk` identifies an exercise that can be added.
- A `ue_pk` identifies a routine-specific exercise slot.
- They are not interchangeable. A post-write read must verify slot identity, order, sets, reps, notes, and rest.

### Verified Lower B state

On 2026-09-15, Lower B (`z_pk` 26) was redesigned and re-read after writing:

| Order | Exercise | Catalog `z_pk` | Slot `ue_pk` | Prescription |
| --- | --- | --- | --- | --- |
| 1 | Single Leg Deadlift with Dumbbell | 1096 | 189 | 3 × 8 |
| 2 | Smith Hip Thrust | 1415 | 190 | 3 × 10 |
| 3 | Step Up with Dumbbell | 1445 | 191 | 3 × 8 |
| 4 | Lying Single Leg Curl | 1145 | 192 | 3 × 11 |
| 5 | Leg Extension Machine | 1467 | 193 | 3 × 12 |
| 6 | Single Arm Farmer's Walk with Kettlebell | 1177 | 194 | 3 × 35 seconds per side |

All six began with weight unset/0 and use 60-second rest. This is a verified snapshot, not a substitute for a fresh read.

## Current write, verification, and backup behavior

The demonstrated live loop is: **program knowledge → SmartGym inspection → proposed change → explicit athlete authorization → MCP write → immediate verification**.

Every SmartGym write must:

1. Read and identify the target by `z_pk`.
2. Compare the proposal with repository rules, recent training, and athlete status.
3. State scope, expected outcome, and authorization requirement.
4. Apply only the approved change.
5. Immediately re-read the target and list routines as needed to prove exact state and no unrelated change.
6. Report rationale, verification evidence, and uncertainty.

The MCP creates a full database backup before each real write; the observed location is `/Users/joe.hoff/.smartgym-mcp/backups`. A backup and read-back are safeguards, not permission for a broad change.

### Known limits

- No exposed in-place exercise swap exists; a redesign may require remove + add.
- `smartgym_remove_exercise` soft-deletes a routine slot (`ue_pk`) and unlogged template sets; logged historical sets remain.
- The exposed operations do not confirm a way to list or restore a soft-deleted exercise slot.
- `smartgym_update_exercise` changes metadata, not the catalog exercise identity.
- `smartgym_reorder_routine` only reorders existing slots and requires every `ue_pk` exactly once.
- Observed exercise-history behavior is not confirmed from schema and must not become a permanent rule without evidence.

## Authorization and safety boundaries

Current behavior is intentionally conservative: read-only coaching is allowed; a real routine write requires explicit, scoped athlete authorization. The full autoregulation contract is deliberately future work.

Explicit approval is required for a new block or weekly architecture, major movement-family substitution, routine rebuild or multi-exercise add/remove, material increase in load/volume/impact/injury risk, changes outside documented constraints, and meaningful pain or injury signals.

No agent may treat a backup as authorization, infer broad permission from a narrow request, or make unrequested follow-on “cleanup” changes. Athlete feedback and completed training override automated assumptions.

## Target architecture

```text
                   repository: program, decisions, specs
                                      │
                                      ▼
                            Trainer Event Engine
              ┌───────────────┼────────────────┐
              ▼               ▼                ▼
       coaching state     policy engine    audit/event log
              │               │                │
              └───────> approved integrations <───────┐
                              │                         │
              ┌───────────────┼───────────────┐         │
              ▼               ▼               ▼         │
          SmartGym       Apple Health      AWS services  │
              │               │               │          │
              └───────────────┴───────────────┴──> iOS / Apple Watch
```

The future Trainer Event Engine turns meaningful events—completed or missed workout, readiness or joint-status change, recovery signal, or schedule change—into explainable actions: inform, recommend, seek approval, or execute within explicitly delegated scope. It must have an audit trail, idempotency, verification of external writes, and the existing safety boundaries.

The athlete-facing mobile direction is iOS, with Apple Watch supporting low-friction readiness and in-workout interactions. The product should emphasize daily briefing, next action, session view, reflection, and carefully timed notifications—not dashboard sprawl. SmartGym remains the routine/execution record unless a deliberate migration decision changes that contract.

AWS is a future foundation for authenticated APIs, event processing, storage, notifications, observability, and secure integrations. It is not needed for the current local MCP loop. Any introduction must minimize health-data exposure, protect secrets, use least privilege, and make retries, idempotency, and auditability first-class.

## Deliberately not being built yet

- A commercial multi-athlete product, social network, marketplace, or general fitness platform.
- Autonomous program redesign or unrestricted unattended SmartGym writes.
- A SmartGym logging replacement or speculative data migration.
- Permanent all-knowing chat memory.
- A fixed autoregulation contract before enough real coaching sessions establish safe boundaries.
- AWS infrastructure, iOS/Watch clients, or notification automation before the event and policy contracts are specified and tested.

## Reproducibility and recovery

The local MCP project is `/Users/joe.hoff/smart-gym-mcp`, cloned from `https://github.com/sla1k/smart-gym-mcp`. It requires macOS, SmartGym, Python 3.11+, and `uv`. Claude Desktop’s MCP configuration is at `/Users/joe.hoff/Library/Application Support/Claude/claude_desktop_config.json` and runs `uv run --directory <smart-gym-mcp path> smartgym-mcp`.

After a move, reinstall, or configuration change: update the project path, restart the execution environment, call `smartgym_health`, list routines, and verify `z_pk` 24, 25, 23, and 26 before acting.

1. Clone this repository and `smart-gym-mcp`.
2. Run `uv sync` in the MCP project and configure its launcher.
3. Confirm health and routine identity.
4. Read the master program, cardio plan, this architecture, and live routines before coaching.
5. Preserve decision records, verification evidence, and durable-rule changes in GitHub.
