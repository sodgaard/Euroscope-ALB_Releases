# EAT:LT Landing Timeline Planning

## What it is

- LT means Landing Timeline.
- It is the preferred and current normal operating method.
- It plans against the landing sequence and runway-side timeline.
- It is finer than AR because it works from the final landing picture instead of only equal spacing at each via-fix.

## What the FMR manipulates

- Planned Landing Rate (`PLR`)
- Monitoring the landing timeline and sequence conformance
- `Advance 1` when one aircraft should move one position earlier in the landing sequence
- `Resequence` when an aircraft should be released back into ALB's normal planning logic
- Operational correction when an aircraft is no longer following ALB's expected order, for example with headings, directs, or other controller instructions outside ALB
- `ETA:ES` versus `ETA:ALB` policy where relevant
- Hold/EAT coordination where relevant

## What the FMR normally does not manipulate in LT

- The FMR does not normally tweak via-fix AR values in LT mode.
- AR may still be visible as legacy/context information, but it is not the active planning driver.
- Current normal LT operation is monitoring and correction, not constant manual AR tuning.

## What peers do

- Peers follow the FMR plan.
- Peers monitor and use ALB as shared situational awareness.
- Peers should not independently change shared planning unless they are the FMR or are explicitly working standalone/local.

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

## Backend seqsync boundary

Backend seqsync modes do not change the LT algorithm itself.

- local LT ordering, gap-fill, PLT, and correction logic stay the same
- `throttled` and `horizon` affect how canonical backend sequence state is shared with peers
- `horizon` may suppress far-floating LT candidates from canonical backend sync while keeping operationally relevant or uncertain traffic canonical
- `suspend` suppresses canonical `SET2` TX, but local LT calculations continue
