# SmartGym — Current Strength Routines

These are the strength routines currently created in SmartGym.

SmartGym is the execution/tracking system. These routines represent the current implementation of the hybrid program.

## Monday — Upper A

1. Dumbbell Bench Press — 3 × 8
2. Seated Cable Row — 3 × 8
3. Dumbbell Shoulder Press — 3 × 8
4. Face Pull — 3 × 12
5. Dumbbell Lateral Raise — 3 × 12
6. Cable Triceps Pushdown — 2 × 12
7. Dumbbell Hammer Curl — 2 × 10
8. Cable Palloff Press — 3 × 10
9. Farmer's Walk with Dumbbells — 3 × 40 seconds

  SmartGym currently represents this as 3 sets. The original program specification called
  for 2 sets, but the current SmartGym routine state consistently reports 3 sets.

  The SmartGym MCP does not expose a separate target/template field, so the 3-set
  configuration is being treated as the current SmartGym prescription.   

Note: Farmer's Walk with Dumbbells was manually added because the SmartGym text importer did not reliably accept the carry when imported by text.

## Tuesday — Lower A

1. Hack Squat — 3 × 8
2. Smith Machine Romanian Deadlift — 3 × 8
3. Incline Leg Press — 3 × 10
4. Standing Leg Curl — 3 × 10
5. Machine Standing Calf Raise — 3 × 12
6. Cable Abduction — 2 × 12
7. Cable Palloff Press — 3 × 10

## Thursday — Upper B

Current active SmartGym routine.

## Friday — Lower B

Current active SmartGym routine.

## Saturday — Optional Wild Card

CURRENT STATUS: Not a required SmartGym routine.

## SmartGym Import Rules Learned Empirically

The current SmartGym 8.0.3 installation has been unusually sensitive to text-import syntax.

Known reliable format:

Day

1. Exact Exercise Library Name, X sets, Y reps

Keep imports extremely simple.

Avoid:

- rest-duration instructions
- muscle-group identifiers
- stretch/non-stretch identifiers
- coaching notes
- extra metadata
- timed exercises when possible

Use exact SmartGym exercise-library names.

If a timed carry/hold is required, add it manually when the importer rejects it.

SmartGym remains the execution engine, not the source of overall program philosophy.

## SmartGym Routine Identity

SmartGym currently stores the names of the four active routines as NULL.
Do not attempt to resolve these routines by name.

Use the SmartGym primary key (`z_pk`) as the authoritative routine identifier:

- z_pk 24 = Monday Upper A
- z_pk 25 = Tuesday Lower A
- z_pk 23 = Thursday Upper B
- z_pk 26 = Friday Lower B

SmartGym `days` encoding for these routines:
- 2 = Monday
- 3 = Tuesday
- 5 = Thursday
- 6 = Friday

When referring to these routines in coaching discussions, use the human-readable
names above, but use the corresponding `z_pk` when querying SmartGym through MCP.