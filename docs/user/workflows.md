# Workflows

This page is the short workflow map for ALB during a session.

Use these pages for the detailed planning behavior:

- [Planning Modes Overview](planning-modes/index.md)
- [EAT:LT Landing Timeline Planning](planning-modes/eat-lt.md)
- [EAT:AR Arrival Rate Fallback Planning](planning-modes/eat-ar.md)
- [Feeder View vs Runway View](planning-modes/views.md)

## Normal recommended workflow: EAT:LT

1. Open ALB and select the timeline for the destination and runway setup you care about.
2. Pick the layout that gives you the best runway-side planning picture.
3. Use `EAT:LT` as the normal planning method.
4. Monitor the landing timeline and sequence conformance.
5. Use `Advance 1`, `Resequence`, or operational corrections only when the aircraft is no longer following the expected landing order.

## Fallback workflow: EAT:AR

1. Use `EAT:AR` only when you deliberately want the older rough flow method.
2. Watch the via-fix streams rather than treating the landing timeline as the main driver.
3. Adjust per-stream `AR` when the release picture is no longer right.
4. Use `Advance 1` or `Resequence` when a specific aircraft needs a local sequence correction.

## Standalone

- Use ALB as your own picture.
- Choose the timeline and layout you want.
- `EAT:LT` is still the recommended default unless you deliberately choose the fallback AR method.

## Peer

- Open `Peers` and confirm who else is connected.
- If another controller is FMR, treat shared planning controls as theirs unless you have explicitly coordinated otherwise.
- Keep using local display controls such as timeline choice, via-fix visibility, and layout selection.

## FMR

- Claim manual FMR with `.alb fmr <ICAO>`.
- Choose the correct timeline, layout, EAT mode, and ETA policy for the airport.
- In normal modern operation, monitor and correct the `EAT:LT` landing picture rather than continuously tuning AR values.
- Use hold-related EAT actions only when the aircraft is already in hold and the operational prerequisites are met.

## Backend seqsync operation

Backend seqsync is the backend transport layer that shares canonical
per-aircraft sequence state from the FMR to peers.

Recommended use:

- use `normal` unless load management or controlled testing requires otherwise
- use `throttled` if backend or peer load is caused by `SET2` bursts, but all aircraft should remain canonical
- use `horizon` if far-floating aircraft are creating unnecessary churn and only operationally relevant aircraft need canonical synchronization
- use `suspend` only as an emergency or deliberately degraded mode when canonical `SET2` TX must stop temporarily

Important limits:

- seqsync commands do not change FMR ownership
- they do not change `EAT:AR` versus `EAT:LT`
- they do not change ETA policy, backend transport health, or scratchpad fallback state
- they do not create peer-owned resequencing

In `suspend`, peers may retain the last backend-owned overlay until recovery
resync, explicit `DEL`, FMR ownership change, or local reset.

## Hold / EAT

- Decide whether hold EAT should stay synchronized with the holding list using `HLS*` or `HLS-`.
- If ALB should write accepted hold timing back to the holding list, enable `HLW*`.
- If you need to assign a specific EAT, use `.alb seat <Callsign> <HHMM>`.
- If older hold overrides are getting in the way, right-click `HLS` and reset the HOLD_EAT overrides.

See [EAT Coordination](../msgs/eat_detailing.md) for the hold/EAT meaning and [Retired: Scenarios](retired/scenarios.md) for the retired scenario workflow.

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

Show backend seqsync mode and queue status:

```text
.alb seqsync status
```

Change backend seqsync mode:

```text
.alb seqsync normal
.alb seqsync throttled
.alb seqsync horizon
.alb seqsync suspend
```
