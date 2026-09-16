# Hybrid Athletic Trainer — System Architecture

## Purpose

This system turns the hybrid-training program into a repeatable coaching loop:

1. Program rules and constraints guide decisions.
2. SmartGym supplies the live routine state and completed-workout record.
3. Claude coaches and, when authorized, applies routine changes through SmartGym MCP.
4. The athlete performs and reports the actual training; completed work is the source of truth.

The system is deliberately small. Its durable memory is the GitHub repository, not a chat transcript.

## Components and responsibilities

| Component | Responsibility | Authority |
| --- | --- | --- |
| Athlete | Sets goals, reports readiness/joint status, completes training, approves material changes | Final decision-maker; actual completed work overrides the plan |
| GitHub repository | Program specification, constraints, current routine snapshot, cardio framework, and current-week plan | Durable program memory |
| Claude + SmartGym MCP | Trainer: reads program/history, prescribes sessions, manages progression and recovery, and performs approved routine-level writes | Coaching and routine execution within the operating policy |
| SmartGym | Routine execution, workout logging, exercise library, weights/reps/sets, history, and device sync | Live training record and execution system |
| ChatGPT | Architect/auditor: designs the operating model, challenges decisions, documents it, and audits changes | Advisory; does not replace the trainer or SmartGym record |
| `smart-gym-mcp` | Local bridge from Claude to the SmartGym database | Controlled read/write integration |

## Data flow

```text
GitHub program files ──> Claude coaching decision ──> SmartGym MCP ──> SmartGym
        ^                         |                       |                 |
        |                         v                       v                 v
   updated durable rules <── athlete feedback       backup + re-read     completed history
```

Claude begins with the program files and current SmartGym state, then accounts for recent training, recovery, joint status, and the upcoming schedule. SmartGym remains the operational system of record for routines and completed workouts. The repository holds the intent and decision rules; `04_TODAY.md` is a short-lived weekly plan, not an archive.

## Repository map

- `01_MASTER_PROGRAM.md` — athlete profile, weekly strength/cardio architecture, equipment, constraints, and programming principles.
- `02_SMARTGYM_CURRENT_ROUTINES.md` — routine identity and routine snapshot. Re-read SmartGym before relying on descriptive exercise lists; the live app may be newer than this file.
- `03_CARDIO_PLAN.md` — cardio framework and adaptive decision procedure.
- `04_TODAY.md` — current-week prescription; overwrite or update as the week unfolds.
- `ARCHITECTURE.md` — this operating model and reproducibility reference.

## SmartGym identifiers

Routine names are currently `NULL` in SmartGym. Human-readable names are for conversation; use `z_pk` for any MCP query or write.

| `z_pk` | Day | Routine | SmartGym `days` value |
| --- | --- | --- | --- |
| 24 | Monday | Upper A | 2 |
| 25 | Tuesday | Lower A | 3 |
| 23 | Thursday | Upper B | 5 |
| 26 | Friday | Lower B | 6 |

Other identifier types:

- `z_pk` on an exercise-library item identifies the catalog exercise to add.
- `ue_pk` identifies one routine-specific exercise slot. It is not interchangeable with an exercise-library `z_pk`.
- Routine changes must target the intended `z_pk` explicitly and verify the resulting `ue_pk` slots afterward.

### Verified Lower B state

On 2026-09-15, Lower B (`z_pk` 26) was intentionally redesigned and re-read after the write. Its six active slots, in order, are:

1. Single Leg Deadlift with Dumbbell — exercise `z_pk` 1096, slot `ue_pk` 189, 3 × 8
2. Smith Hip Thrust — `z_pk` 1415, `ue_pk` 190, 3 × 10
3. Step Up with Dumbbell — `z_pk` 1445, `ue_pk` 191, 3 × 8
4. Lying Single Leg Curl — `z_pk` 1145, `ue_pk` 192, 3 × 11
5. Leg Extension Machine — `z_pk` 1467, `ue_pk` 193, 3 × 12
6. Single Arm Farmer's Walk with Kettlebell — `z_pk` 1177, `ue_pk` 194, 3 × 35 seconds per side

All six use 60-second rest intervals and began with weight unset/0. Treat this as a verified snapshot, not a substitute for a fresh read before making changes.

## Operating and authorization model

The trainer is Claude. ChatGPT is the architect/auditor. SmartGym records execution; it does not decide programming.

Claude may make ordinary, routine-level adjustments when they follow established program rules—for example, progressing load or reps within a stated range, autoregulating after an unusually difficult session, or choosing a joint-friendlier established modality.

Claude must first explain the rationale and obtain explicit approval for a material change, including:

- a new training block or change to the weekly architecture;
- exercise-family substitutions that alter program intent;
- routine rebuilds or multi-exercise adds/removals;
- changes that materially increase intensity, volume, impact, or injury risk;
- changes outside the documented constraints.

Every SmartGym write follows this transaction:

1. Read and identify the target by `z_pk`.
2. Compare the proposed change with the repository rules and recent training.
3. State the scope, expected effect, and whether approval is required.
4. Make only the approved change.
5. Re-read the affected routine and list routines to confirm exact state and that no unrelated routine changed.
6. Report what changed, why, and any uncertainty.

## SmartGym MCP safety and limitations

The MCP server creates a database backup before each real write. The observed backup location is `/Users/joe.hoff/.smartgym-mcp/backups`. Backups and post-write verification are mandatory safeguards; neither excuses an unreviewed broad change.

Known integration constraints:

- No exposed operation swaps one routine slot's catalog exercise in place. A redesign may require remove + add.
- `smartgym_remove_exercise` soft-deletes the routine slot (`ue_pk`) and its unlogged template sets. Logged historical sets are not deleted by that operation.
- The currently exposed MCP operations do not provide a confirmed way to list or restore a soft-deleted exercise slot. Do not treat a removed slot as readily recoverable.
- `smartgym_update_exercise` changes slot metadata such as note, rest, or index; it cannot repoint the slot to another catalog exercise.
- `smartgym_reorder_routine` reorders the existing slots and requires every existing `ue_pk` exactly once; it cannot add or remove a slot.
- Routine names being `NULL` means name matching is unsafe. Always use the routine table above.
- Exercise history appears associated with catalog exercises in observed results, but this has not been confirmed from the database schema. Treat it as an observation, not a permanent rule.

## Reproducibility and setup

The local MCP project is `/Users/joe.hoff/smart-gym-mcp`, cloned from `https://github.com/sla1k/smart-gym-mcp`. It requires macOS, SmartGym, Python 3.11 or later, and `uv`.

Claude Desktop's MCP configuration is at:

`/Users/joe.hoff/Library/Application Support/Claude/claude_desktop_config.json`

The active SmartGym entry launches:

```json
{
  "command": "/Users/joe.hoff/.local/bin/uv",
  "args": [
    "run",
    "--directory",
    "/Users/joe.hoff/smart-gym-mcp",
    "smartgym-mcp"
  ]
}
```

After a move, reinstall, or configuration change, update the project path if needed, restart Claude Desktop, and call `smartgym_health`. A healthy response confirms the connection; then list routines and verify the four `z_pk` values before any coaching write.

For a portable recovery procedure:

1. Clone the repository and the `smart-gym-mcp` project.
2. Install the MCP project's dependencies with `uv sync`.
3. Configure Claude Desktop with the `uv run --directory <smart-gym-mcp path> smartgym-mcp` command.
4. Confirm `smartgym_health`.
5. Read `01_MASTER_PROGRAM.md`, `03_CARDIO_PLAN.md`, this file, and live SmartGym routines before prescribing or editing.
6. Keep this repository updated when durable program rules, routine identity, or the operating policy changes.
