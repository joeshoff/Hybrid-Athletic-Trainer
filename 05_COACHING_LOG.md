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
