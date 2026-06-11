# Quick Start

ALB users normally fall into one of three working styles:

1. Standalone user
2. Regular peer in a team session
3. FMR for a destination airport

## First clicks

1. Launch EuroScope with `ALB.dll` enabled.
2. Open the ALB window with `.alb open` if it did not open automatically.
3. Pick the relevant `Timelines`.
4. Check `Peers` if you are working with others.
5. Pick a `Layout` that fits the task.

Make sure the runway setup you are working with in EuroScope matches the timeline you are watching in ALB.

## Standalone user

Use ALB as your own planning display.

- Choose the timeline you want.
- Pick a scenario or adjust AR/PLR as needed.
- Change layout locally to feeder-style or runway-style views.
- Use right-click aircraft actions if you want to tidy the sequence locally.

## Regular peer in a team session

Open ALB the same way, then check who is coordinating the destination airport.

- If another controller is the active FMR, treat shared flow planning as read-only unless you have agreed otherwise.
- You can still use your own local display tools such as timeline choice and layout selection.
- If you need a sequence change, coordinate with the FMR rather than independently reshuffling shared traffic.

## FMR for `<ICAO>`

Claim manual FMR with:

```
.alb fmr <ICAO>
```

As FMR you normally own the shared plan for that airport:

- scenario changes
- planned landing rate
- via-fix arrival rates
- shared EAT/ETA policy buttons
- shared sequence actions

If an aircraft is already in hold and prerequisites are met, you can also set EAT with:

```
.alb seat <Callsign> <HHMM>
```

For the next step, read [Interface](user/interface.md), [Buttons & Menus](user/buttons-and-menus.md), and [Workflows](user/workflows.md).

