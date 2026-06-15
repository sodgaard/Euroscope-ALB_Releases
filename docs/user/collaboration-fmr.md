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
- hold/EAT policy controls such as `HLS`, `FPC`, and `HLW`
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
