# Collaboration & FMR

ALB can work as a standalone planning tool, but it is also built to let several ALB instances follow one shared plan for the same destination airport.

## What FMR means

**FMR** means **Flow Management Responsible**.

For a given destination ICAO, the FMR is the ALB instance that should be treated as the authority for the shared arrival plan.

That normally includes:

- planned landing rate
- via-fix arrival rates when the older `EAT:AR` fallback method is being used
- shared EAT mode
- shared ETA source
- hold/EAT policy controls such as shared EAT policy, legacy `HLS` compatibility state, underlying `FPC` shared state, and `HLW`
- explicit sequence actions that affect the shared order

## Manual FMR

Manual FMR is claimed with:

```text
.alb fmr <ICAO>
```

If you claim manual FMR, other peers should normally stop changing shared planning for that airport and treat your ALB instance as the coordinating one.

## Auto-FMR in user language

ALB can also keep track of who currently appears to own the plan even when nobody has manually claimed it.

Operationally, the important part is simple:

- if nobody has explicitly taken manual FMR, ALB can still work collaboratively
- if someone does explicitly take manual FMR, that controller becomes the clear owner for shared plan changes

The low-level election details belong in the technical section, not in normal controller workflow.

## Who should change what

The source logic is slightly more permissive than the simplest FMR story:

- if a manual FMR exists for the active airport, only that manual FMR should change shared operational policy
- if no manual FMR exists, active peers can still change the shared policy

For day-to-day operations, the safest habit is still:

- one controller owns the shared plan
- the other peers use ALB mainly to follow that plan

## What peers should and should not do

Peers should:

- use the same timeline picture
- follow the shared timeline and spacing plan
- coordinate with the FMR before deliberate resequence actions

Peers should normally not:

- independently apply shared flow changes while a remote FMR is clearly active
- make competing sequence interventions on the same traffic

When a manual FMR is active, ALB now treats that more strictly for shared
policy edits:

- non-FMR peers should expect shared `LT`, `PLR`, `AR`, and scenario changes to be ignored locally
- this is deliberate protection against accidental competing edits while a manual FMR is already in charge

In runway-sequence collaboration, a non-FMR peer can still request actions such
as `Advance 1` or `Resequence`, but the request is sent toward the authority and
the peer then waits for canonical backend sync instead of applying a competing
local result first.

## How ALB shares state

From an operator point of view, ALB shares planning state with peers automatically when network and backend conditions allow. The normal intent is that all peers reproduce the same operational picture without you needing to manage message details yourself.

That includes canonical backend sequence state.

- the FMR sends the authoritative backend sequence picture
- peers mirror that picture instead of inventing competing committed sequence changes
- backend seqsync load-management modes change how canonical sequence traffic is transmitted, not who owns the shared plan

For the transport and authority mechanics behind that, see [Collaboration Internals](../technical/collaboration-internals.md) and [Backend Transport](../technical/backend-transport.md).

## FMR control overview

This is the practical summary of the ALB manipulations an FMR normally uses to
control shared flow and behavior for an airport.

## First ownership step

Manual FMR claim is the clearest way to make ALB treat your instance as the
shared owner for that airport.

See [Workflows](workflows.md) and [Quick Start](../quick-start.md) for the
broader operational flow.

Use:

```text
.alb fmr <ICAO>
```

That is the clearest user-facing way to tell peers that your ALB instance owns
the shared plan for that airport.

## Shared planning controls

These are the main controls an FMR normally uses to shape the shared plan for
flow, sequencing, and timing policy.

See [Buttons & Menus](buttons-and-menus.md),
[EAT:LT Landing Timeline Planning](planning-modes/eat-lt.md),
[EAT:AR Arrival Rate Fallback Planning](planning-modes/eat-ar.md), and
[Aircraft Actions](aircraft-actions.md) for the detailed behavior behind each
one.

Available manipulations:

- `EAT:LT`, `EAT:AR`, or `EAT:TF` top-row button to choose the shared planning mode
- `ETA:ES` or `ETA:ALB` top-row button to choose the shared ETA branch
- `PLR` stats-area clicks or:

```text
.alb plr <rate>
```

- per-via-fix `AR` stats-area clicks in `EAT:AR`, or:

```text
.alb ar <ViaFix> <minutes>
```

- aircraft right-click actions such as `Advance 1` and `Resequence`
- hold/EAT actions when operationally relevant

## Hold / EAT controls

These controls are for hold-related timing and the final aircraft-visible
hold-EAT publication side of the shared plan.

See [EAT Coordination](../msgs/eat_detailing.md),
[Buttons & Menus](buttons-and-menus.md), and [Workflows](workflows.md) for the
full hold/EAT behavior.

Available manipulations:

- `HLW*` or `HLW-` is the local gate for whether this ALB instance may write
  the final `/HOLD_EAT/HHMM/` side effect
- if a specific aircraft already in hold needs a manual EAT, use:

```text
.alb seat <Callsign> <HHMM>
```

- `SEAT` should be treated as legacy, fallback, or compatibility handling, not
  as the normal backend-primary authority path

## Backend transport controls

These controls do not change who the FMR is, but they do change how canonical
shared sequence traffic is transported to peers.

See [Backend Transport](../technical/backend-transport.md),
[Workflows](workflows.md), and
[Troubleshooting](../other/troubleshooting.md) for the detailed transport and
status behavior.

Available manipulations:

```text
.alb seqsync status
.alb seqsync normal
.alb seqsync throttled
.alb seqsync horizon
.alb seqsync suspend
```

Use them when transport load or a controlled degraded mode makes it necessary
to change seqsync behavior.

Backend reconnect continuity and mixed backend or scratchpad peer handling are
automatic transport behaviors. There is no separate FMR button for them; the
practical control surface remains FMR ownership, seqsync mode, and normal
config reload or restart.

## Config-driven behavior

Some shared-plan behavior is not changed by a live button or direct `.alb`
command, but through config and then reload or restart.

See [Config File Reference](../config/config-description.md),
[ICAO / Airport Setups](../config/icao-setups.md), and
[Workflows](workflows.md) for the detailed config meaning.

Available manipulations:

- airport-specific LT spacing policy in
  `airports.<ICAO>.eat_lt_spacing`
- Expected LR forecast window in
  `airports.<ICAO>.eat_lt_spacing.expectedRateWindowMinutes`
- seqsync default mode and budgets in top-level
  `backendSeqSync`

After changing config, apply it with:

```text
.alb reload
```

or restart ALB.

You can also use `[Reload config]` in the `Timelines` menu when you want the
same re-read from the UI.

## Local-only controls that are not FMR authority

These may still be useful to the FMR, but they do not change the shared plan
seen by peers.

See [Buttons & Menus](buttons-and-menus.md) and
[Feeder View vs Runway View](planning-modes/views.md) for the detailed local
display behavior.

Available manipulations:

- `EAT` or `PLT` compact display toggle
- hold-display button such as `2 EAT`, `2 GL`, `2 SW`, or `2 ---`
- `Timelines`, `Layout`, and via-fix visibility menus
- `Peers` as an informational surface

## Practical rule of thumb

If the action changes shared planning policy, sequence, EAT, PLT, or transport
behavior for peers, it belongs in the FMR toolbox.

If the action only changes what your own ALB window shows, it is a local
display tool rather than an FMR control.

For the detailed behavior behind each control, see:

- [Buttons & Menus](buttons-and-menus.md)
- [Aircraft Actions](aircraft-actions.md)
- [Workflows](workflows.md)
- [EAT:LT Landing Timeline Planning](planning-modes/eat-lt.md)
- [EAT:AR Arrival Rate Fallback Planning](planning-modes/eat-ar.md)
