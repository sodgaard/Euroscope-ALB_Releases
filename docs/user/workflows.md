# Workflows

This page is about how to use ALB during a session, not just which commands exist.

## Standalone planning

Use this when ALB is only for your own picture.

1. Open ALB.
2. Select the timeline that matches the airport and runway setup you care about.
3. Pick a layout that suits the task.
4. Use `EAT:LT` as the normal planning method.
5. Watch the landing picture and use right-click aircraft actions only when you want to force an explicit sequence correction.

## Regular peer workflow

Use this when several controllers are watching the same destination.

1. Open ALB and choose the relevant timeline.
2. Open `Peers` and confirm who else is connected for that airport.
3. Check whether a controller is acting as FMR.
4. If another controller is FMR, treat shared planning controls as their tools.
5. Keep using local display controls such as timeline choice, via-fix filtering, and layout selection.

## FMR workflow

Use this when you are the controller coordinating the shared arrival plan.

1. Claim manual FMR with `.alb fmr <ICAO>`.
2. Confirm the correct timeline and layout for the active runway concept.
3. Choose the EAT and ETA policy buttons that match how you want the plan built.
4. In normal operations, monitor the landing timeline and correct aircraft that stop following the expected order.
5. Use aircraft right-click actions for deliberate sequence interventions.
6. Use hold-related EAT actions only when the aircraft is already in hold and the operational prerequisites are met.

## Hold and EAT workflow

For aircraft already established in hold:

1. Decide whether hold EAT should stay synchronized with the holding list using `HLS*` or `HLS-`.
2. If ALB should write accepted hold timing back to the holding list, enable `HLW*`.
3. If you need to assign a specific EAT, use:

```text
.alb seat <Callsign> <HHMM>
```

4. If older hold overrides are getting in the way, right-click `HLS` and reset the HOLD_EAT overrides.
5. Turning `HLS` off clears the current HOLD_EAT override influence and returns hold EAT handling to the automatic ALB calculation path.

See [EAT Coordination](../msgs/eat_detailing.md) for the operational meaning.

## Planning modes

### EAT:LT

`EAT:LT` is the recommended normal method today.

- LT means landing-timeline planning
- the main planning picture is built against the landing sequence rather than only per-via-fix release intervals
- the FMR usually monitors conformance and corrects aircraft that are not following the expected plan
- the controller still remains responsible for intervention, judgement, and correction

In LT mode, the main job is usually:

- watch gain or lose and sequence conformance
- use `Advance 1` or `Resequence` when the sequence needs an explicit correction
- correct route, heading, direct-routing, or hold-related issues when the aircraft no longer matches the expected order

### EAT:AR

`EAT:AR` is the rougher fallback method.

- AR means arrival-rate or via-fix release interval planning
- each stream is managed with equal or configured spacing per via-fix
- it is useful when you want a simple flow-control method
- it requires more active stream-by-stream tweaking by the FMR
- it does not describe the later runway picture as precisely as LT

### Why AR becomes less important in LT mode

In `EAT:LT`, AR is not the active planning driver. Visible AR values are legacy/context information, and AR adjustment is not the normal control method.

## Legacy scenarios

The `Scenarios` menu is retained for compatibility and history, but it is not part of the recommended modern workflow.

- In current ALB use, flow planning is normally managed through `EAT:LT` monitoring and correction rather than scenario switching.
- If the menu is still visible in the UI, treat it as retired functionality.

See [Retired: Scenarios](retired/scenarios.md).

## Sorting and filtering in EAT:AR

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

## Sorting and filtering in EAT:LT

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
5. Is the aircraft in a protected state such as FZ2, final, landing, or other terminal treatment?
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

## Useful commands

Open ALB:

```text
.alb open
```

Close ALB:

```text
.alb close
```

Reload the current config file:

```text
.alb reload
```

Claim or resign manual FMR:

```text
.alb fmr <ICAO>
```

Set planned landing rate:

```text
.alb plr <rate>
```

Set arrival spacing for one via-fix:

```text
.alb ar <ViaFix> <minutes>
```

Set hold EAT for an aircraft already in hold:

```text
.alb seat <Callsign> <HHMM>
```
