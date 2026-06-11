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

If you want the two-minute version first, read [Quick Intro](user/quick-intro.md).

If you are deciding how ALB should be used operationally, read [Planning Modes Overview](user/planning-modes/index.md) next.

## Standalone user

Use ALB as your own planning display.

- Choose the timeline you want.
- Use `EAT:LT` as the normal planning mode unless you deliberately want the rougher AR-based method.
- Monitor the landing picture and correct aircraft that drift away from the expected sequence.
- Change layout locally to feeder-style or runway-style views.

## Regular peer workflow

Open ALB the same way, then check who is coordinating the destination airport.

- If another controller is the active FMR, treat shared planning controls as read-only unless you have agreed otherwise.
- You can still use your own local display tools such as timeline choice and layout selection.
- If you need a sequence change, coordinate with the FMR rather than independently reshuffling shared traffic.

## FMR for `<ICAO>`

Claim manual FMR with:

```text
.alb fmr <ICAO>
```

As FMR you normally own the shared plan for that airport:

- planned landing rate
- via-fix arrival rates in AR/fallback operation
- shared EAT mode
- shared ETA source
- hold/EAT policy controls such as `HLS`, `FPC`, and `HLW`
- shared sequence actions

If an aircraft is already in hold and prerequisites are met, you can also set EAT with:

```text
.alb seat <Callsign> <HHMM>
```

The recommended modern method is to monitor and correct the landing timeline in `EAT:LT`, not to drive the plan mainly through scenario switching.

For the next step, read [Planning Modes Overview](user/planning-modes/index.md), [EAT:LT Landing Timeline Planning](user/planning-modes/eat-lt.md), [Interface](user/interface.md), and [Buttons & Menus](user/buttons-and-menus.md).
