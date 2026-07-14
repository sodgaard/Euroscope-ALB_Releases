# EAT:AR Arrival Rate Fallback Planning

## What it is

- AR means Arrival Rate or via-fix release interval planning.
- It is the older, rougher fallback method.
- It gives equal or configured spacing per via-fix stream.
- It can be useful as a simple fallback, but it is not the recommended normal method when LT is available.

## What the FMR manipulates

- Arrival Rate (`AR`) per via-fix
- Via-fix stream monitoring
- `Advance 1` within the relevant stream or context
- `Resequence` to release an aircraft back into normal AR planning
- Hold/EAT coordination where relevant
- `ETA:ES` versus `ETA:ALB` policy where relevant

## How you actually change AR behavior

- change one stream `AR` in the stats area:
  - left-click decreases the interval by 1 minute
  - right-click increases it by 1 minute
- or use:

```text
.alb ar <ViaFix> <minutes>
```

- switch into `EAT:AR` with the top-row `EAT:AR` button
- change the ETA branch with the top-row `ETA:ES` or `ETA:ALB` button
- use aircraft right-click actions such as `Advance 1` and `Resequence` when a
  specific aircraft needs explicit sequence treatment

## What the FMR must keep watching

- AR requires more manual tweaking.
- Equal spacing per via-fix is rough and may not produce the best landing timeline.
- The FMR must adjust via-fix ARs as traffic changes.

## What peers do

- Peers follow the FMR plan.
- Peers monitor the same streams and use ALB as shared situational awareness.
- Peers should not independently alter shared AR planning unless they are the FMR or are explicitly working standalone/local.

## Sorting and filtering IO

### Inputs

- active destination airport and selected timeline
- selected layout
- destination, runway, and target-fix relevance
- selected or hidden via-fixes
- aircraft visibility and relevance
- current flight phase and arrival state
- via-fix stream
- via-fix ETO, EAT, and release interval
- hold state and manual EAT if present
- FMR policy and peer state

### Decisions

1. Is the aircraft relevant for the active destination and timeline?
   If no, ALB hides or excludes it.
2. Is the aircraft landed, cancelled, diverting, terminal, or otherwise outside active planning?
   If yes, ALB suppresses it or moves it out of the active planning picture.
3. Does the aircraft belong to a visible via-fix stream?
   If no, ALB hides or dims it depending on layout and config.
4. Is the aircraft already sequenced, locked, or frozen?
   If yes, ALB preserves the stable order instead of constantly reshuffling it.
5. Is there a hold or manual EAT override?
   If yes, ALB applies and shows that branch according to the active hold policy.
6. Has the aircraft deviated from expected routing or sequence?
   If yes, ALB warns about it rather than automatically churning the whole order.

### Outputs

- aircraft included, excluded, or dimmed
- via-fix stream placement
- displayed sequence and order
- EAT, gain-lose, or countdown display
- warnings or notes about route anomaly, hold state, or direct-routing issues

## Feeder and Runway effects in AR

- `EAT:AR` can be used in both feeder and runway views.
- Feeder view is often useful when you want the upstream or area-controller stream picture directly.
- Runway view is often useful when you still want to read the traffic against the runway-side picture while the engine mode remains `EAT:AR`.
- The view choice is about operational role and display context, not about whether the engine mode is `AR` or `LT`.
- See [Feeder View vs Runway View](views.md) for the exact difference in ordering, deselection, and aircraft actions.

## When to intervene

- Tune `AR` when stream spacing is wrong.
- Use `Advance 1` for a one-position sequence correction.
- Use `Resequence` when ALB should place an aircraft again under the current AR logic.

## Backend seqsync boundary

Backend seqsync modes do not change the AR algorithm itself.

- local AR calculation and release logic stay the same
- `throttled` and `horizon` affect how canonical backend sequence state is shared with peers
- `horizon` may suppress far-floating AR candidates from canonical backend sync
- `suspend` suppresses canonical `SET2` TX, but local AR calculations continue
