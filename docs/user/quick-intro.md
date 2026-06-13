# Quick Intro

ALB is an arrival sequencing and planning aid for EuroScope.

In one or two minutes, the important picture is this:

- ALB helps controllers watch arrival order, planned timing, via-fix flow, landing timeline, holding and EAT coordination, and peer collaboration
- one ALB instance normally acts as the **FMR** for a destination airport and shares planning decisions to peers
- the recommended modern method is `EAT:LT`, where ALB plans against the landing timeline and the FMR monitors conformance
- `EAT:AR` is still available as a rougher fallback method based on via-fix release spacing
- feeder versus runway view is a separate display and operational-role choice, not an `EAT:` mode choice

ALB does not fly aircraft or replace controller judgement.

It gives the FMR and peers a shared plan, highlights deviations, and helps the team keep a stable arrival sequence.

## What the FMR usually does

- choose the relevant timeline and layout
- monitor the landing picture
- correct aircraft that stop following the expected sequence
- use `Advance 1`, `Resequence`, and hold-related EAT actions when the plan needs explicit intervention

## What peers usually do

- follow the same planning picture
- treat the shared plan as coordinated by the FMR
- use local display choices without independently fighting the shared sequence

## Where to go next

- [Overview](overview.md)
- [Interface](interface.md)
- [Buttons & Menus](buttons-and-menus.md)
- [Workflows](workflows.md)
