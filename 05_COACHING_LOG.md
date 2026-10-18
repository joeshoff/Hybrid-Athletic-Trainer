# Coaching Log

This is the durable, append-only record of athlete observations, watch items, and coaching findings — physiological and training-pattern signals surfaced during sessions. It is not the day-by-day prescription artifact (that's 04_TODAY.md, which is overwritten weekly and is not an archive) and it is not an engineering/system finding (those live in ARCHITECTURE.md's Known limits and ROADMAP.md). Entries are dated and stay in place.

## Aerobic decoupling — first observation, 9/19

Sat 9/19 long run (77 min, out-and-back, zone 2 target 108–125): split the
session at the midpoint and compared first half vs second half.

- First half: ~119 bpm avg, ~14:00/mi pace
- Second half: ~120 bpm avg, ~15:20/mi pace
- Efficiency factor (speed/HR) dropped ~10% first half to second

HR held essentially flat while pace cost ~9–10% more to maintain it —
classic within-session decoupling. Terrain ruled out (athlete-confirmed
out-and-back, so elevation profile is symmetric by route design).

Ground contact time dropped in the second half (157ms → 140ms), not the
usual rise you'd expect from fatigue-driven form breakdown. More
consistent with deliberate pace management to hold the HR ceiling than
mechanical fatigue.

Sitting alongside two other open watch items — resting HR trending up
(57 → 63 since July) and right-side single-leg deadlift stability (first
noted 9/18) — as signals that aren't yet actionable individually. Not
acted on. If the same decoupling pattern shows up on the next long zone 2
session, that's two data points in the same direction as the resting HR
trend, and worth a real conversation with Joe at that point.

**Method for next time:** split any long steady-state cardio session at
the midpoint, compare avg HR and avg pace (or speed) each half. Needs
formalizing as a repeatable check — worth a roadmap item for automating
this from the Health Auto Export data rather than doing it by hand each
time.

## Fri 9/25 Lower B (SmartGym workout 14, 20:16, 69m, avg HR 91 / max 122)
- Single-leg DL, bench-supported: 25 lb (11.3 kg). SmartGym shows 4x8, likely the template default; actual was one light set. Left side: less back, little glute. Right side: much more back, back fatigues first. Right glute strain from 9/15 hill VO2 (~2 wks), "fine, something there." Hold 25 lb, load set by right side.
- Smith hip thrust 90 lb x10x3: easy, quads > glutes, no glute feel. Next: feet out, 2s pause with pelvic tuck. Hold 90.
- Step-up 15 lb x8x4 (16" box): 3-5 reps left, knee normal, hop risk at higher load. Next: 17.5 lb.
- Standing leg curl 35 lb x10x3: 2-3 left. Next: 37.5 lb x10.
- Leg extension 65 lb x10x3 (corrected in SmartGym from 8): ~5 left. Next: 75 lb x8, knee check.
- Single-arm KB farmer's walk: 53 lb (24 kg), 45s x3/side, grip fine (no gloves, relaxed hand). SmartGym still shows 0 lb x10 (no edit path). Next: 70 lb (32 kg).
- Tuesday VO2: no steep hills until right glute is 100%. Use bike or flat treadmill.
- Sat 9/26: 90+ min hike, no vest (or 10-15 lb max), Z2 to low Z3, pre-effort check applies. Optional evening upper-body-only session.
- Pipeline: sync re-created the workout under a new pk (13 to 14).

## Sun 9/27 — week review, elbow, and mechanism change

**SmartGym history confirmed for all four strength sessions (9/21 Upper A, 9/22 Lower A, 9/24 Upper B, 9/25 Lower B)** via `smartgym_get_workout_history` + `smartgym_get_workout_detail(workout_pk)`. Pattern across nearly every session: planned load bumps written into SmartGym exercise notes were not taken — weight stayed at the app's pre-filled template value even where reps were maxed (row 85→held, lateral raise 10→held, pushdown 40→held, hammer curl 25→held, hip thrust 90→held, leg extension 65→held). Third time this has shown up.

**Notes-as-target-channel retired.** Joe's decision: lean on SmartGym's own post-workout "Update" suggestions instead of the trainer writing exercise notes. Update is a flat +1-rep suggestion with no load-bump logic, so it only produces the right number on pure rep-progression targets — every load-bump target still needs the weight entered by hand. 04_TODAY.md now marks each exercise Update-OK or manual every week.

**Elbow — distal biceps tendon (prior PRP site) still tender.** Twinge during hammer curls first reported 9/24; confirmed still tender 9/27. Hammer Curl cut entirely from both Upper A and Upper B (not reduced load) starting the week of 9/28. Reassess weekly; escalate to the PRP provider if it isn't clearly improving.

**Farmer's walk weight logging, still not resolved.** Single-arm KB farmer's walk (Lower B): 9/18 never logged; 9/25 reps logged (10×3) but weight logged as 0. Real load per 9/25 note above is 53 lb (24 kg) with headroom to 70 lb (32 kg). No post-workout edit path in the app either way — instruction stands to log the actual weight live.

**Documentation-integrity note — not a training issue, flagged to PO/Architect separately:** Friday's actual pattern (~30m boxing + ~5m trailing core) has now held for three weeks (9/18, 9/25, 9/28 week) against 03_CARDIO_PLAN.md's Core Work section, which explicitly excludes Friday. Repeated flagging without a written resolution — needs an actual decision in that Project, not a fourth note here.

## Sun 10/11: week review and 10/12 plan

SmartGym read after Joe synced the macOS app. Before the sync, history stopped at 10/1. The smartgym local MCP also timed out twice (60s) before it responded, so the first read said "no sessions" when there were three. HAE exports read afterward (workouts folder has only 10/5, 10/6 and 10/10 for the week; metrics folder has every day). Mon 10/5: elliptical 54.5m, watch avg 119, max 141, core 10.5m. Tue 10/6: indoor cycling 38.7m, avg 130, max 166 (the VO2 intervals happened), core 10.5m. Wed 10/7 to Fri 10/9: no workouts, 4 to 13 exercise minutes a day, HR max 89 to 109, so no Thursday cardio and no boxing. Sat 10/10: Variety Day A 78.5m (no HR) then outdoor cycling 26.9m, avg 142, max 162, 4.6 mi, tempo not Zone 2. Sun 10/11: rest. Resting HR 61 (10/7), 63, 64, 72 (single sample at 00:02 on 10/10, HRV median 21 that day), 64 (10/11). The Tuesday gate moved from 63 to 64 for that reason: rested days ran 61 to 64, so 63 would fail a normal day.

- Upper A, 10/5 (workout 18, 21:14, 43m): bench 45×10×3, row 90×8×3, press 25×10×3, face pull 12×3, lateral 15×12×3, pushdown 45×12×3, Pallof 32.5×10×3, farmer's 30 lb 40s×3 (time logged this time).
- Lower A, 10/6 (workout 20, 20:17, 57m): hack 140×10×3, RDL 140×11×3, leg press 200×8×3, leg curl 40×11×3, calf 200×12×3, abduction 12.5×12×3, Pallof 32.5×11×3.
- Sat 10/10, Variety Day A (workout 19, 78m, 0 HR): landmine squat and press 25×8×3, KB swing 35×15×4, goblet squat 53×10×3, landmine row 35×10×3, plus bodyweight finishers, battle ropes, jump rope. No HR means the watch was not recording.
- Not run: Upper B (last 9/24) and Lower B (last 9/25). No SmartGym sessions 10/8 or 10/9.
- Manual bumps on Lower A mostly did not land (RDL 150, leg curl 45, abduction 20, Pallof 35). Reps ran past the range instead, so those loads carry into 10/13 as the target. Upper A manual bumps did land. Reps-left was not collected for Lower A; the feel-set rule covers it.
- Elbow: Joe reports the biceps tendon is the only open issue. Hammer curl stays cut.
- Plan: Upper A and Lower A progress. Upper B and Lower B run on their programmed targets after a 2.5-week gap, with the feel-set fallback.

Proposal for the Product Owner/Architect: the trainer session read an empty week as fact when the sync was stale and the MCP slow. A staleness check (latest workout date vs. latest HAE workout file) before any "nothing happened" conclusion would have caught it.

Equipment constraint, 10/11: Joe pointed out 27.5 lb dumbbells don't exist (25 or 30), which is part of why some planned bumps never happened. Shoulder press moved to 25 × 11–12 (rep progression, Update-OK) and pushdown to 50 × 10 (45 has been easy). Other targets that may not be real weights and are unchecked: incline DB 52.5, lateral raise 17.5, step-up 17.5, plate-loaded steps (hack 150, RDL 150, hip thrust 105). Asked Joe for the actual increments. Before writing any load target, check it against the increments he gives; do not assume 2.5 lb steps.

Increments, Mon 10/12: Joe confirmed dumbbells go 7.5, 10, 12.5, 15, then 5 lb steps; cable stacks move in 2.5s (consistent with logged 32.5, 37.5, 42.5). Checked the 9/17 and 9/24 Upper B and 9/18 and 9/25 Lower B logs: incline was 50 x 10 x 3 (not 52.5), Upper B row 85 x 10, Upper B lateral raise 10 lb x 12, Upper B pushdown 40 x 12 (2 sets), step-up 15 x 8 x 4, hip thrust 90 x 10 x 3. Replaced the nonexistent targets: incline 55 x 8, lateral raise 15 x 13-15 (Mon) and 15 x 10 (Thu, fallback 12.5), step-up 15 x 10, leg curl 42.5 x 8 (Lower A), shoulder press 25 x 11-12 (Mon) and 25 x 9-10 (Thu). Still unverified: plate steps on hack squat (150), Smith RDL (150), Smith hip thrust (105), leg press (200). Asked Joe.


## Sat 10/17: week review and 10/19 plan

SmartGym read from a copy of GymModel.sqlite that Joe placed in the connected folder (the smartgym local MCP failed with `unable to open database file` because macOS privacy blocked the container folder; Terminal got access after Full Disk Access, Claude Desktop's launched server still failed). Read with python sqlite3. Core Data tables: ZHISTORY, ZWORKOUT, Z_11SETSDONE, ZVALUES (ZSECONDVALUE reps, ZTHIRDVALUE weight in kg), ZUNIQEXERCISE, ZEXERCISE.

- Upper A 10/12 (50m): bench 50x8x3, row 90x8, press 25x11, face pull 14, lateral 15x12, pushdown 50x12, Pallof 32.5x10, farmer's 35 lb (40s/80s/80s logged).
- Lower A 10/13 (61m): identical to 10/6 on all seven exercises (hack 140x10, calf 200x12, leg press 200x8, RDL 140x11, curl 40x11, abduction 12.5x12, Pallof 32.5x11). Joe confirmed 10/17 these are what he lifted, hack maybe not a clean 10, RDL can take the bump.
- Upper B 10/15 (46m): lat pulldown 65x9, row 90x8, pushdown 50x12, stand-up 20x12, incline 55x10, press 25x9, hammer curl 25x10 (template weight, Joe used less, elbow felt good), lateral 15x12.
- Lower B moved to Sat 10/17 09:05 (47m): SLDL 25x8x4, hip thrust 90x10, step-up 15x8x4, curl 40x10, leg extension 65x8 (was 65x10), KB farmer's 44 lb x 40s (template weight; 53 lb had a grip limit). Then a long bike starting 11:35, about 1,057 kcal active, exhausting per Joe.
- Fri 10/16 boxing 20.5m (FightCamp via HAE, avg 116, max 134) plus 5m core.
- Not verified: Mon/Tue/Thu AM cardio, Sat bike HR, resting HR. HAE JSON for 10/17 is 3.4 MB, too large to read through the Drive connector.
- Lesson: SmartGym logs the template weight when Joe doesn't edit it. A logged weight is not proof of the load lifted. Ask when a number looks like a carry-over.

Proposals for the Product Owner/Architect:
- smartgym local MCP needs a read path that survives macOS privacy blocks (or a documented Full Disk Access step, and a snapshot fallback).
- HAE workouts need a reader that returns per-session summaries (type, start, duration, avg/max HR) without the full JSON.
- 04_TODAY.md plan for 10/12 said Lower B Friday. It slid to Saturday and stacked with a long ride. A rule that Lower B and the long Saturday session do not share a day.
