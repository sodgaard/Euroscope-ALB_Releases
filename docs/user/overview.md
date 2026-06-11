# Overview

ALB is an arrival-planning tool for EuroScope. In controller language, it gives you one shared place to watch inbound streams, compare demand against plan, and coordinate timing changes before the aircraft reaches the runway.

## What ALB is good at

- Showing inbound traffic by timeline and via-fix stream
- Comparing current demand with planned landing rate and stream spacing
- Letting you switch between feeder-style and runway-style planning views
- Coordinating hold-related EAT work for aircraft already established in hold
- Sharing one plan between multiple ALB users working the same destination airport

## Recommended modern use

Current ALB use is centered on `EAT:LT`.

- `EAT:LT` is the preferred planning method for normal operation
- `EAT:AR` remains available as a rougher fallback method
- the FMR normally monitors conformance to the landing timeline and corrects aircraft that drift away from the expected order

Start with [Planning Modes Overview](planning-modes/index.md), then read [EAT:LT Landing Timeline Planning](planning-modes/eat-lt.md).

## What you will usually do in ALB

- Select the relevant `Timeline`
- Monitor the landing timeline and sequence conformance
- Use AR controls only when you deliberately want the rougher via-fix release method
- Watch the stats block to see demand, hold load, and near-term distribution
- Use `Layout` to switch between feeder-focused and landing-focused views
- Right-click an aircraft when you need an explicit sequence action

## Shared planning in plain language

When several ALB instances watch the same airport, one instance may act as **FMR**.

- The FMR is the normal authority for the shared arrival plan.
- Peers still see the same picture, but should avoid independent shared-plan changes when another controller is clearly acting as FMR.
- ALB keeps the collaboration automatic when transport conditions allow.

## A few practical limits

- ALB is a planning aid, not a separation tool.
- Local controller judgement still takes priority over the display.
- Some figures are based on live EuroScope routing and timing inputs, so they should be read as planning support rather than exact prediction.

Next: [Planning Modes Overview](planning-modes/index.md), [Feeder View vs Runway View](planning-modes/views.md), and [Interface](interface.md)
