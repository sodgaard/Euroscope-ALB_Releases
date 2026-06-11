# Overview

# Overview

ALB is an arrival-planning tool for EuroScope. In controller language, it gives you one shared place to watch inbound streams, compare demand against plan, and coordinate timing changes before the aircraft reaches the runway.

## What ALB is good at

- Showing inbound traffic by timeline and via-fix stream
- Comparing current demand with planned landing rate and stream spacing
- Letting you switch between feeder-style and runway-style planning views
- Coordinating hold-related EAT work for aircraft already established in hold
- Sharing one plan between multiple ALB users working the same destination airport

## What you will usually do in ALB

- Select the relevant `Timeline`
- Use `Scenarios` or AR clicks to shape stream release spacing
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

Next: [Interface](interface.md)

