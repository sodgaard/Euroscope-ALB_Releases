# Collaboration Internals

This page describes the shared-plan authority model behind the FMR concept.

## Core rule

For each destination ICAO, ALB tracks one FMR owner. Shared plan changes should follow that owner so peers reproduce the same operational picture.

## Manual and auto ownership

The code distinguishes between:

- manual FMR ownership
- auto or inferred ownership when nobody has explicitly claimed manual FMR

Manual FMR is created by `.alb fmr <ICAO>` and is treated as the strongest user-facing ownership signal.

## Policy authority versus ownership

One important implementation detail is that ownership and policy editing are related, but not identical concepts.

The live helper used by the UI is `CanEditSharedOperationalPolicy()`. Its current behavior is:

- observers and non-active clients cannot edit shared policy
- if any active ICAO has a manual FMR, only that manual FMR may edit shared policy
- if no manual FMR exists for the active ICAOs, active peers may still edit shared policy

That is why the user docs recommend a simple operational habit even though the code allows a little more freedom.

## Button-level implications

The shared-policy rules currently drive the top-row controls like this:

- `TXE` is manual-FMR only
- `HLS` and `HLW` follow shared operational policy authority
- `EAT` mode and `ETA` source follow shared operational policy authority and also respect sequence-policy lock
- `FPC` follows the same shared-policy path

The local display-only `EAT` or `PLT` combi toggle is deliberately outside shared authority.

## Peer awareness

The `Peers` menu is informational. It gives the user a compact view of connected ALB peers and current airport context, but it is not the surface that changes authority.

## Backend-shadow display owner

When backend-primary collaboration is healthy, the displayed FMR owner can be mirrored from backend authority state rather than from legacy scratchpad FMR claim traffic. This keeps the user-facing FMR view aligned with the transport path that is actually in charge.

## Seqsync authority boundary

Backend sequence synchronization does not change who owns the shared plan.

- the FMR remains the authority for canonical per-aircraft sequence state
- peers mirror that backend-owned canonical state
- seqsync mode commands such as `.alb seqsync throttled` or `.alb seqsync horizon` change only transport behavior for canonical sequence traffic
- they do not change FMR ownership, shared policy authority, EAT mode, backend health, or scratchpad fallback state

That distinction matters because ALB is trying to reduce transport churn
without introducing peer-owned resequencing.
