# Hybrid Athletic Trainer — Architecture

## Purpose

Hybrid Athletic Trainer is a small, safety-conscious coaching system for one athlete. It connects durable program knowledge, an AI trainer, and the SmartGym training record:

1. Program rules and constraints guide a coaching decision.
2. The trainer reads program files, live SmartGym state, history, and athlete feedback.
3. The trainer recommends or, within an approved future policy, makes a narrowly scoped adjustment.
4. SmartGym records the routine and completed work; actual completed work overrides the plan.

The repository is durable program memory, not a chat archive or a replacement for SmartGym.

This repository is the shared home for a small suite of sibling pillars under one personal healthy-lifestyle-management system for the same athlete: Hybrid Athletic Trainer (training, described above) and, as of 2026-09-19, Nutritionist (menu, meal-planning, shopping, and eventually macro-tracking guidance) — with room for more later. Each pillar keeps its own reasoning session and its own record system where one exists (SmartGym for training; none yet for nutrition), while this repository stays their shared durable intent. See Current architecture and ENGINEERING.md's Role separation section for how pillars relate, and Nutritionist (menu, meal-planning, and shopping guidance) below for what's built versus planned. This shape mirrors Bevel (bevel.health), an existing all-in-one AI health coach connecting training, nutrition, and wearable data that Joe used and found did what he's after here — but abandoned over reliability problems (corrections not persisting, false prompts, degraded responsiveness). This project's emphasis on narrow scope, immediate verification, and explicit uncertainty is a direct response to that failure mode, not just a stylistic preference.

## Principles

- Separate **intent** (repository), **reasoning** (trainer), and **record** (SmartGym).
- Use stable SmartGym identifiers, narrow changes, immediate verification, and explicit uncertainty.
- Joe is the final decision-maker and athlete/product-acceptance authority.
- Roles and contracts belong to the project; Claude, Kiro, KiroCrew, or another capable environment may execute them.
- Trainer, Nutritionist, Product Owner/Architect/Auditor, and Lead Engineer are kept in separate Claude Projects/sessions with no shared conversation history, so no single session reasons across every role at once; see ENGINEERING.md's Role separation section.
- Preserve enough documentation and evidence for a new execution environment to reproduce the system safely.

## Current architecture

| Component | Responsibility | Boundary |
| --- | --- | --- |
| Joe / athlete | Goals, readiness and joint-status feedback, completed training, product acceptance | Final decision-maker; actual work is authoritative |
| GitHub repository | Program specification, constraints, routine snapshot, cardio framework, and short-term plan | Durable intent, not the live workout record |
| Claude + SmartGym MCP | Current trainer interface: reads, reasons, proposes, and can make authorized routine writes | Does not redesign the program without approval |
| SmartGym | Routine execution, exercise library, logging, history, and device sync | Live execution and training record |
| `smart-gym-mcp` | Local bridge to the SmartGym database | Controlled integration surface, not a coach |
| Google Drive (SmartGym state cache) | Best-effort snapshot of live SmartGym state (routines, current program, recent workout history), refreshed by a session with live Mac/SmartGym MCP access | Read-only fallback for sessions without Mac access; may be stale; never the record of truth and never a write path |
| Claude (Product Owner / Architect / Auditor session) | Product Owner, architect, and auditor | Product/architecture authority; not primary trainer or record system |
| Claude (Nutritionist session) | Menu, recipe, and shopping guidance from stated goals and constraints; future macro tracking | Does not redesign the training program, edit `ARCHITECTURE.md`/`ROADMAP.md`, or write to SmartGym |

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

Nutrition-specific documents (menu guidance, meal plans, shopping lists) will be added here once the Nutritionist pillar produces durable content worth preserving; none exist yet.

## SmartGym integration and identities

Use routine `z_pk` for every MCP query or write; human labels are only conversational. Routines have been recreated at least once, so a `z_pk` is only as good as its last verification. Re-verified 2026-09-23 via `smartgym_list_routines` (names are now populated; earlier they were `NULL`). The previous IDs 24/25/23/26 no longer exist; a query against them returns "not found."

| Routine `z_pk` | Day | Human label | SmartGym `days` |
| --- | --- | --- | --- |
| 60 | Monday | Upper A | 2 |
| 65 | Tuesday | Lower A | 3 |
| 73 | Thursday | Upper B | 5 |
| 59 | Friday | Lower B | 6 |
| 74 | Unscheduled | Variety Day A | — |
| 75 | Unscheduled | Variety Day B | — |

- A catalog exercise `z_pk` identifies an exercise that can be added.
- A `ue_pk` identifies a routine-specific exercise slot.
- They are not interchangeable. A post-write read must verify slot identity, order, sets, reps, notes, and rest.

### Verified Lower B state

Historical: routine `z_pk` 26 and the `ue_pk` values below no longer exist (see the identity table above). Kept as the record of the first verified write.

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

## Apple Health data ingestion (Health Auto Export)

The athlete's iPhone runs Health Auto Export (a third-party iOS app) to bridge Apple Health data out to Claude sessions, ahead of the formal Apple Health integration described in Target architecture and ROADMAP.md Phase 3. Two independent channels are configured, by design, for different purposes:

| Channel | Role | Trigger | Characteristics |
| --- | --- | --- | --- |
| Google Drive automations (`Health Auto Export/Health Data`, `Health Auto Export/Health Auto Export`) | Primary | Scheduled export from the app | Durable, asynchronous, available to any session without the athlete present; separate automations for Health Metrics and Workouts (the app's data-type categories cannot be combined into one automation) |
| Local `health-auto-export` MCP server (`mcp-remote` to the phone's on-device REST server) | On-demand fallback | Explicit request in a live session | Real-time queryable (metrics, workouts, ECG, symptoms, and more); requires the phone and the Mac running Claude Desktop to be on the same LAN, and the local MCP config's URL to match the phone's current local IP |

Drive is primary because it requires no live session and no network coincidence to succeed — data lands where a future session can retrieve it on its own. The local MCP path exists for "I need it right now" queries; it is not automatically triggered and depends on a session being explicitly asked to use it.

**Known fragility:** the phone's local IP address changes whenever it joins a different Wi-Fi network (observed: moving from the corporate network to the home network broke the local MCP connection until the config's URL was manually updated to the phone's new address). A DHCP reservation for the phone on the home router mitigates this; without one, the failure will recur on every network change.

This is infrastructure for the future Apple Health integration, not the integration itself — there is no consent model, privacy review, or reconciliation into the Trainer Event Engine yet. Data pulled through either channel is raw Apple Health export, not yet a trusted coaching input.

## SmartGym state cache and Apple Health workout fallback (decided 2026-09-20)

**Problem.** A session reached remotely (see "Remote access and Claude Desktop trust" above) can only read live SmartGym state when Claude Desktop is running, trusted, and the host Mac is awake. Without that, a remote or mobile session has no SmartGym data to work from at all — including cases where a workout (e.g., a run) was captured by Apple Health on iOS but has not yet synced into SmartGym's macOS app.

**Decision — SmartGym cache.** A best-effort snapshot of live SmartGym state (routines, current program, recent workout history) is written to Google Drive by a session that has live Mac/SmartGym MCP access, mirroring the pattern Apple Health data ingestion already uses Drive for above — the same "always-on store" role Drive plays elsewhere in this architecture. The cache is:

- read-only — no session writes to SmartGym through the cache, ever;
- explicitly timestamped as of its last refresh, so any session reading it can state how stale it is;
- never treated as the record of truth — SmartGym via `smart-gym-mcp` remains the only write path and the only authoritative source when reachable.

**Decision — Apple Health workout fallback.** When SmartGym (live or cached) has no entry for a workout that Apple Health shows as completed, the Apple Health data may be surfaced as an advisory fallback only. It must be presented to Joe explicitly labeled as unverified and not yet reconciled into SmartGym (e.g., "Apple Health shows a run yesterday; not yet reflected in SmartGym") — never as if it were a logged SmartGym set. This holds the same conservative stance already stated above: raw Apple Health export is not yet a trusted coaching input. Promoting it to authoritative status, or building reconciliation logic that writes it into SmartGym or the coaching record, is future work and requires its own privacy/consent decision (see "Deliberately not being built yet").

**Rationale.** This does not solve the underlying remote-write dependency on the host Mac (see "Remote access and Claude Desktop trust" above); it only makes reads resilient. It reuses existing infrastructure (Drive is already the always-on store; Health Auto Export → Drive is already running) rather than building new infrastructure ahead of Phase 4's AWS service foundation. See ROADMAP.md Phase 1 ("SmartGym integration hardening") and Phase 3 ("Apple Health integration") for where the build work is tracked.

**Fallback read procedure (Issue #5, documented 2026-09-20).** Reconnaissance against the live Drive export, not a guessed schema:

- Data source: the Workouts automation lands in the Drive folder named **"Health Data"** (`Health Auto Export/Health Data`) — not the folder named "Health Auto Export" (`Health Auto Export/Health Auto Export`), which holds Health Metrics instead. The two folder names are swapped from what they suggest; confirmed by opening both on 2026-09-20. Reading the wrong one returns metrics, not workouts.
- File convention: one file per calendar day, `HealthAutoExport-YYYY-MM-DD.json`, shaped `{"data":{"workouts":[...]}}`. A day's file is not a closed snapshot — it can be rewritten for roughly two days after the date it names as later workouts sync in, and a same-day file may not exist yet at all (confirmed 2026-09-20: no file existed yet for that day's workouts while the Metrics automation already had one). Treat "no file for the target date" as "no data yet," never as "nothing happened" — the same staleness discipline the SmartGym cache above requires.
- Matching/dedup: drop any workout entry whose `source.identifier` equals `com.smartgymapp.smartgym` — SmartGym writes its own completed workouts back into HealthKit, and Health Auto Export re-exports them, so these already reached SmartGym by construction and are not a gap (confirmed from the 2026-09-17 and 2026-09-18 exports; only two samples, worth strengthening under QA). For everything else, check SmartGym's workout history (live, or cached once the read-cache above ships) for a same-day entry whose time window overlaps, and suppress the Apple Health entry if one exists — the defense against a workout Joe logged manually. Match on overlapping start/end, not date-plus-type alone, so two same-day workouts of the same type don't collide.
- Label, shared with the cache-staleness language above so the two never diverge into separate dialects: `"Apple Health shows <description> at <local start time> — not yet reflected in SmartGym."` Never phrased as logged or verified.
- Non-goals restated for this read path: no write to SmartGym, the repository, or anywhere else; Apple Health data never becomes authoritative or gets reconciled into SmartGym automatically; pull-based only, answered when asked, never a proactive check.
- Verification case: a real, current gap exists for this — an Outdoor Run on 2026-09-19, 09:34–10:51, 5.26mi, `source.identifier` on Apple Watch, no SmartGym-sourced entry that day. The originally observed gap (Issue #3, 2026-09-17) predates this Drive folder's creation (2026-09-17, 8:11pm) and was never captured by it; it's only checkable by looking at iOS SmartGym directly, which this fallback cannot reach either.

## Nutritionist (menu, meal-planning, and shopping guidance)

Demonstrated capability: given a photo of a restaurant menu and the athlete's stated goals, produce a recommendation. This was demonstrated ad hoc, outside this repository, before the pillar existed here (2026-09-19).

Not yet decided: a food-logging or macro-tracking backend; whether nutrition guidance reads training load or macro targets from the Trainer pillar, or the reverse; and where meal plans, shopping lists, and recurring guidance get preserved as durable documents versus staying conversational. Until these are decided, Nutritionist guidance is stateless per conversation — the same conservative default Trainer used before SmartGym writes were authorized.

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
- `smartgym_get_routine` per-exercise history is wrong in `smart-gym-mcp` v0.2.0 (found 2026-09-23 on Lower A, `z_pk` 25). The query joins sets to the routine slot correctly (`ZVALUES.ZEXERCISE → ZUNIQEXERCISE.Z_PK`) but groups them into "sessions" by `date(ZDATEADDED)`, the day each set row was created, and includes unlogged template sets. Symptom: a workout that repeats the previous one set for set collapses into one session dated with the older workout; loads and reps are correct. The query does no content deduplication. Nothing in the query links a set to the `ZWORKOUT` it was logged in; upstream's own docs list that linkage as unmapped (FEATURES.md F4). Until fixed, treat `smartgym_get_routine` history as unreliable for any routine that has run more than once, and use `smartgym_get_workout_history` only for session-level facts (date, duration, HR).
- Decided 2026-09-23, Joe signed off on the fork via Issue comment the same day: fix `smart-gym-mcp` in place rather than building a separate SmartGym access module. The DB connection, WAL handling, epoch math, and routine resolution are already correct and shared; a second module would duplicate them and still face the same unmapped set→workout linkage. The local clone tracks a third-party repo (`sla1k/smart-gym-mcp`), so the fix lands in a Joe-owned fork with its own PR review; the Reproducibility clone URL changes once the fork exists. An upstream PR is optional, not a dependency.
- A write reported as successful, and reflected immediately on macOS (the app the connector reads/writes locally), does not guarantee timely or complete iOS sync, and the app UI surfaces no staleness or divergence indicator. Observed on 2026-09-17 over a ~2 hour session: a safety-relevant exercise removal sat un-synced on iOS for over an hour; a 7-exercise routine replacement partially diverged, leaving macOS and iOS with different exercise lists under the same routine; an iOS archive/unarchive cycle silently reverted an MCP-applied rename. The only way found to read iOS's actual state independent of the app UI or the connector's own reads was decoding SmartGym's `backup.gym` export (an NSKeyedArchiver plist; routines keyed by `uniqueHashID`, with `dateRemoved` marking soft-deleted rows).

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
- A food-logging or macro-tracking backend, or coupling nutrition guidance to training load/targets, before that integration is explicitly designed.
- Treating Apple Health workout data as authoritative over SmartGym, or automatically reconciling the two, before a privacy/consent decision is made (see "SmartGym state cache and Apple Health workout fallback").

## Reproducibility and recovery

The local MCP project is `/Users/joe.hoff/smart-gym-mcp`, cloned from `https://github.com/sla1k/smart-gym-mcp`. It requires macOS, SmartGym, Python 3.11+, and `uv`. Claude Desktop’s MCP configuration is at `/Users/joe.hoff/Library/Application Support/Claude/claude_desktop_config.json` and runs `uv run --directory <smart-gym-mcp path> smartgym-mcp`.

After a move, reinstall, or configuration change: update the project path, restart the execution environment, call `smartgym_health`, list routines, and verify the routine `z_pk` values in the identity table above before acting.

1. Clone this repository and `smart-gym-mcp`.
2. Run `uv sync` in the MCP project and configure its launcher.
3. Confirm health and routine identity.
4. Read the master program, cardio plan, this architecture, and live routines before coaching.
5. Preserve decision records, verification evidence, and durable-rule changes in GitHub.

### Remote access and Claude Desktop trust

The SmartGym MCP connection is not a network service; it is a local process Claude Desktop starts on the host Mac. Claude Desktop shows its own local trust dialog on that Mac the first time it launches the `uv run --directory <smart-gym-mcp path> smartgym-mcp` command — a dialog only the person at the Mac's keyboard can answer.

A Trainer session reached remotely (for example through a linked-device bridge) can only use this connection if Claude Desktop is already running and already trusted on the host Mac; it cannot itself click through a prompt nobody is present to answer. Remote coaching therefore depends on three operational preconditions, none of them currently designed or verified: Claude Desktop staying open (not quit) and the Mac staying awake, the trust decision for `smartgym-mcp` already having been granted while physically at the machine, and the launch command staying stable (an unstable `uv`-resolved interpreter or venv path can cause the trust dialog to reappear). This is an unverified operational workaround, not a designed remote-access capability — see ROADMAP.md Phase 1, "SmartGym integration hardening," and Phase 4's future AWS service foundation for a genuinely network-reachable connection.

The SmartGym state cache decided above (see "SmartGym state cache and Apple Health workout fallback") mitigates this for reads only — a remote session can consult the Drive cache when it can't reach `smart-gym-mcp`. It does not change the write dependency described in this section.
