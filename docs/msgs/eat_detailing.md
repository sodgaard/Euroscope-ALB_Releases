# ALB EAT Guide

This guide explains the public, operational view of **EAT** in ALB.

## What EAT means in ALB

In ALB, EAT is the planning time used to coordinate inbound flow, especially for
aircraft that are already established in hold.

From a controller point of view, there are two practical parts:

- ALB maintains its own arrival plan and sequencing view.
- Controllers may also work with hold-related EAT values visible in their
  operational tools.

Current normal ALB use is centered on `EAT:LT`, where the landing timeline is
the main planning driver and the controller monitors sequence conformance.
`EAT:AR` remains available as a rougher fallback method.

ALB can support that coordination, but it should be understood as a planning
aid rather than a replacement for controller judgement.

## FMR and coordination

For a given destination airport, one ALB user may act as
**Flow Management Responsible (FMR)**.

The FMR is the controller using ALB to coordinate the shared arrival plan for
that airport, including:

- landing-timeline planning
- arrival-rate changes where the rougher AR method is being used
- hold-related EAT coordination where applicable

If you are not the active FMR, your local display can still be used normally,
but shared planning decisions for that airport should normally come from the
FMR.

## Setting EAT for an aircraft in hold

ALB still supports the `SEAT` command surface for aircraft already in hold:

```text
.alb seat <Callsign> <HHMM>
```

Normal prerequisites are:

- the aircraft must already be in hold
- `HOLDFIX` must be available for that aircraft
- you must be FMR for the aircraft destination
- the responsible peer/controller must be using a compatible ALB version

Operational note:

- In backend-primary healthy operation, canonical per-aircraft EAT authority is carried by backend `SEQ/SET2+AC`, not by a normal `SEAT` command workflow.
- The final `/HOLD_EAT/HHMM/` write is an aircraft-visible side effect after canonical EAT has been accepted locally.
- `HLW` is the active local write permission for publishing accepted `HOLD_EAT` back to the holding list.
- `HLS` is legacy compatibility state and should not be read as the main modern operator control.
- `SEAT` remains available only as legacy, fallback, or compatibility handling when backend-primary authority is unavailable or fallback is active.

See [Workflows](../user/workflows.md) for the command reference.

## Manual controller judgement still applies

ALB helps coordinate timing and sequencing, but operational responsibility stays
with controllers.

Use ALB as a shared planning aid:

- when you need a common arrival picture
- when a destination airport has an active FMR
- when hold-related timing needs to be coordinated

Do not treat ALB output as a substitute for separation, vectoring, or local
controller judgement.

## Related pages

- [Quick Start](../quick-start.md)
- [Workflows](../user/workflows.md)
- [Overview](../user/overview.md)
