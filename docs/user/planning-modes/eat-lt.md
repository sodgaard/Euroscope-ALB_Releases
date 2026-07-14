# EAT:LT Landing Timeline Planning

## What it is

- LT means Landing Timeline.
- It is the preferred and current normal operating method.
- It plans against the landing sequence and runway-side timeline.
- It is finer than AR because it works from the final landing picture instead of only equal spacing at each via-fix.
- It now combines airport PLR with WTC-dependent adjacent-pair landing spacing and an informational Expected LR forecast.

## What the FMR manipulates

- Planned Landing Rate (`PLR`)
- Monitoring the landing timeline and sequence conformance
- `Advance 1` when one aircraft should move one position earlier in the landing sequence
- `Resequence` when an aircraft should be released back into ALB's normal planning logic
- Operational correction when an aircraft is no longer following ALB's expected order, for example with headings, directs, or other controller instructions outside ALB
- `ETA:ES` versus `ETA:ALB` policy where relevant
- Hold/EAT coordination where relevant

## How you actually change LT behavior

- change `PLR` with the stats-area `PLR` field:
  - left-click decreases by 1
  - right-click increases by 1
- or use:

```text
.alb plr <rate>
```

- switch into `EAT:LT` with the top-row `EAT:LT` button
- change the ETA branch with the top-row `ETA:ES` or `ETA:ALB` button
- use aircraft right-click actions such as `Advance 1` and `Resequence` when a
  specific aircraft needs explicit sequence treatment
- adjust airport-specific WTC LT spacing through
  `airports.<ICAO>.eat_lt_spacing` in `alb-config.json`, then apply it with
  `.alb reload`

## What the FMR normally does not manipulate in LT

- The FMR does not normally tweak via-fix AR values in LT mode.
- AR may still be visible as legacy/context information, but it is not the active planning driver.
- Current normal LT operation is monitoring and correction, not constant manual AR tuning.

## What peers do

- Peers follow the FMR plan.
- Peers monitor and use ALB as shared situational awareness.
- Peers should not independently change shared planning unless they are the FMR or are explicitly working standalone/local.

## How LT spacing works

ALB applies WTC-aware spacing to the established canonical landing order. It
does not reorder aircraft just because a different WTC pairing would produce a
different spacing result.

For each adjacent landing pair:

- the leader is the aircraft in front
- the follower is the aircraft behind
- ALB compares three spacing inputs in seconds:
  - the PLR-derived spacing
  - the minimum spacing required behind that leader
  - the minimum spacing required in front of that follower
- the largest of those three becomes the required pair spacing

Operationally that means:

- a natural traffic gap larger than the required spacing is preserved
- WTC spacing constrains the canonical order but does not independently
  optimize it
- missing or unknown WTC falls back to the airport's configured fallback
  category, which defaults to `M`

Controller or maintainer control path:

- day-to-day operators mainly influence the LT result through `PLR`, explicit
  sequence actions, and the chosen ETA branch
- maintainers or local configurators influence the WTC pair-spacing policy by
  editing `airports.<ICAO>.eat_lt_spacing` in `alb-config.json`
- the WTC spacing policy is not a top-row button or `.alb` command of its own

## Expected LR

`Expected LR` is the read-only forecast that ALB derives from the current
canonical future PLTs in `EAT:LT`.

It answers a practical question:

```text
How many aircraft does the current canonical landing plan place within
the configured future window, expressed as an hourly rate?
```

That means it is:

- demand-limited
- based on final canonical LT PLTs
- already influenced by PLR, WTC spacing, natural gaps, holds, frozen slots,
  manual interventions, and the other constraints already present in the plan

It is not:

- a replacement for PLR
- a warning by itself
- a direct capacity calculator
- a feedback input that changes sequencing

How to make ALB show it:

- keep ALB in `EAT:LT`
- ensure the timeline has canonical future PLTs available
- if needed, change the forecast window through
  `airports.<ICAO>.eat_lt_spacing.expectedRateWindowMinutes` in
  `alb-config.json`, then apply with `.alb reload`

## What the legend row means

Below PLR, ALB can show:

```text
Expected LR: 34 #/h (17/30m, vs PLR -6)
```

Meaning:

- `34 #/h` is the expected hourly landing rate
- `17/30m` means 17 eligible canonical PLTs within the next 30 minutes
- `vs PLR -6` means the forecast is 6 aircraft per hour below the current PLR

If ALB is not in `EAT:LT`, the row shows:

```text
Expected LR: -- (EAT:LT only)
```

A lower Expected LR is not automatically a problem. It may simply mean the
current traffic demand is below the planned landing rate.

## Sorting and filtering IO

### Inputs

- active destination airport and selected timeline
- selected layout
- destination, runway, and target-fix relevance
- assigned runway or runway override
- landing sequence keys, PLT, and ELT
- aircraft visibility and relevance
- current flight phase and arrival state
- terminal or post-via status
- hold state and manual EAT if present
- FMR policy and peer state

### Decisions

1. Is the aircraft relevant for the active destination and timeline?
   If no, ALB hides or excludes it.
2. Is the aircraft landed, cancelled, diverting, terminal, or otherwise outside active planning?
   If yes, ALB suppresses it or moves it out of the active planning picture.
3. Is the aircraft assigned to a runway or target-fix that belongs in this timeline?
   If no, ALB hides or dims it depending on layout and config.
4. Is the aircraft already committed, sequenced, locked, or frozen?
   If yes, ALB preserves the stable landing order unless an explicit operational trigger happens.
5. Is the aircraft in a protected state such as `FZ2`, final, landing, or other terminal treatment?
   If yes, ALB protects it from movement.
6. Is there a feasible LT slot for the aircraft without breaking the stable plan?
   If yes, ALB can place or gap-fill it.
7. Is the aircraft failing to follow ALB's expected sequence?
   If yes, ALB highlights the conformance problem and expects controller correction.

### Outputs

- aircraft included, excluded, or dimmed
- landing timeline placement
- PLT, ELT, gain-lose, and countdown display
- conformance warnings and route or sequence notes
- indication that the aircraft may need controller correction

## Feeder and Runway effects in LT

- `EAT:LT` can be used in both feeder and runway views.
- In runway view, the sequence and action context is the global landing sequence.
- In feeder view, the display can still be stream-oriented even while LT remains the selected planning mode.
- The view choice is about operational role and display context, not about whether the engine mode is `AR` or `LT`.
- See [Feeder View vs Runway View](views.md) for the exact difference in ordering, deselection, and aircraft actions.

## When to intervene

- Do not fight small ETA jitter.
- Intervene when aircraft are not following ALB's planned sequence or when an operational trigger makes the plan wrong.
- Use `Advance 1` for a one-position correction.
- Use `Resequence` to release old fixed/timing baggage and let ALB place the aircraft again under the current plan.
- Use Expected LR as information about the current canonical plan, not as an
  automatic instruction to change PLR.

## Backend seqsync boundary

Backend seqsync modes do not change the LT algorithm itself.

- local LT ordering, gap-fill, PLT, and correction logic stay the same
- `throttled` and `horizon` affect how canonical backend sequence state is shared with peers
- `horizon` may suppress far-floating LT candidates from canonical backend sync while keeping operationally relevant or uncertain traffic canonical
- `suspend` suppresses canonical `SET2` TX, but local LT calculations continue
