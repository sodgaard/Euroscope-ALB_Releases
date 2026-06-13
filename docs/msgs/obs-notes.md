# OBS Notes

The `OBS` field is ALB's short observation and warning slot for one aircraft.

It is there to tell you:

- something in the route, phase, sequence, or hold picture needs attention
- ALB is unsure about part of the aircraft's current planning state
- a tactical event such as a missed approach or broken-off approach has happened

## How OBS is shown

- Some layouts show only the 3-character abbreviation such as `RDA` or `SDR`.
- Internally, ALB has a fixed set of OBS conditions with a display priority.
- If more than one OBS condition is active at the same time, ALB can cycle between them.
- Not every active internal condition is always shown. Some notes are suppressed when they are no longer operationally actionable.

## OBS notes currently used by ALB

| Code | Text | What it means | What usually lies behind it | What to check or do |
|---|---|---|---|---|
| `MAP` | Missed | The aircraft is currently treated as a missed approach. | ALB's phase logic detected a missed approach from final, landing, or broken-off approach handling. | Treat the aircraft as a go-around case first. Rebuild the plan around the missed approach, then let ALB re-sequence it as the situation stabilizes. |
| `BKN` | BrokenOff | The aircraft left the expected final approach picture before landing. | ALB detected a broken-off approach. This can include a genuine break-off or a sidestep/re-intercept situation. | Check whether the aircraft is re-intercepting the same runway, sidestepping to another runway, or no longer matching the expected final approach. |
| `ODR` | Overdue Rel | The aircraft is overdue relative to its planned release or hold-related timing. | ALB sees the planned release timing as late versus the current situation, typically in hold-planning or late-live-basis cases. The note is intentionally suppressed once the aircraft is no longer release-manageable. | Check whether the aircraft is still in hold, has left hold later than planned, or now needs the plan rebuilt. Re-plan, re-sequence, or coordinate the hold/timing picture instead of ignoring it. |
| `NFX` | No Fix | ALB could not match the aircraft to the expected final fix for the active timeline. | The route does not contain the final fix ALB expected for that timeline, or the aircraft no longer matches that final-fix geometry. | Check route, runway, timeline choice, and whether the aircraft really belongs in that timeline. If the route updates, the note can clear automatically. |
| `NVF` | No Via | ALB could not resolve the expected via-fix or inbound stream for the aircraft. | The aircraft does not fit the expected via-fix stream picture for the active timeline. | Check STAR, via-fix assignment, timeline choice, and whether the aircraft belongs in the active stream picture. |
| `RDA` | Rt Anom | Route Distance Anomaly. The route or distance picture looks inconsistent. | ALB detected that the route-based geometry or remaining-distance picture does not match what it expects. This is often associated with directs, vectoring, or route changes. | Check whether the aircraft has been tactically re-routed, vectored, or given directs. If the tactical picture is correct, this may resolve once the route and geometry settle. |
| `SDR` | Slot Drift | The aircraft's live timing is materially late against its committed slot anchor. | In LT-style planning, ALB detected the live estimate drifting late versus the slot anchor by at least about a minute. Tactical heading or hold intervention can also be part of the reason. | Check whether the aircraft is no longer conforming to the expected plan. If needed, correct tactically, use `Advance 1`, or `Resequence` once the aircraft should be returned to normal planning logic. |
| `FPU` | Phase UKN | Flight phase unknown. | ALB's phase classifier could not place the aircraft confidently into its normal flight-phase model. | Check whether the aircraft has incomplete or unusual live data, route geometry, or a transition state that ALB has not stabilized yet. Often this is a temporary classifier problem rather than an immediate tactical issue. |
| `ASU` | Arr UKN | Arrival state unknown. | ALB could not classify the current arrival-state bucket cleanly. | Check the aircraft's current relevance to the active timeline and whether the live timing/route state is still settling. |
| `HIG` | Hold Ign | Hold ignored. | ALB has deliberately stopped using the current hold data for sequencing because the hold picture appears stale or no longer operationally credible. This can happen in terminal/final phases or when stale hold data persists far from the hold without supporting evidence. | Check whether the aircraft is genuinely still in hold, whether `HOLDFIX`/`HOLDEAT` information is stale, and whether tracking/controller ownership has changed. Restore the real hold picture or let the stale hold data clear. |

## Practical reading guidance

- `MAP` and `BKN` are high-priority tactical situation notes.
- `ODR`, `RDA`, and `SDR` usually mean the aircraft no longer fits the current timing or sequence plan cleanly.
- `NFX` and `NVF` usually mean ALB cannot place the aircraft properly in the current route or stream picture.
- `FPU` and `ASU` usually mean ALB is missing confidence in its internal classification and may need time or cleaner live data.
- `HIG` means ALB is protecting the sequencing logic from stale hold data rather than trusting that hold picture blindly.

## Resolution pattern

When you see an OBS note, the safest order is usually:

1. Confirm the aircraft still belongs in the active timeline and view.
2. Check whether the route, runway, STAR, via-fix, or hold picture has changed.
3. Decide whether the issue is only a temporary data/classification problem or an actual operational mismatch.
4. If the aircraft no longer matches the current plan, correct tactically and then use `Advance 1` or `Resequence` when appropriate.

## Related pages

- [Legend & Status](legend-status.md)
- [Aircraft Actions](../user/aircraft-actions.md)
- [Feeder View vs Runway View](../user/planning-modes/views.md)
- [EAT Coordination](eat_detailing.md)
