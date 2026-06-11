# Workflows

# Workflows

This page is about how to use ALB during a session, not just which commands exist.

## Standalone planning

Use this when ALB is only for your own picture.

1. Open ALB.
2. Select the timeline that matches the airport/runway setup you care about.
3. Pick a layout that suits the task.
4. Apply a scenario or adjust PLR/AR directly from the stats area.
5. Use right-click aircraft actions only when you want to force an explicit local sequence change.

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
3. Set the overall flow with `Scenarios`, PLR, and per-via-fix AR values.
4. Choose the EAT and ETA policy buttons that match how you want the plan built.
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

See [EAT Coordination](../msgs/eat_detailing.md) for the operational meaning.

## Scenario change workflow

When the airport changes mode or the arrival plan needs tightening:

1. Pick the active timeline first.
2. Use `Scenarios` for broad, preplanned changes.
3. Use AR clicks on individual via-fixes when you need stream-specific shaping.
4. Use PLR clicks when you need to change the landing plan target.
5. Recheck the timeline after the change instead of making several blind adjustments in a row.

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
