# Quick Start

Users of ALB can take one of three roles:

1. Standalone user

2. Part of a team for ```<ICAO>``` as regular user

3. Part of a team for ```<ICAO>``` as Flow Manager

## 1. Standalone user

1. Launch EuroScope with `ALB.dll` enabled.

2. Open the ALB window via the command ```.alb open``` if it did not already open automatically from config.

3. Choose relevant Timeline from the drop down menu. NB: Make sure that the runway selection in the Euroscope menu matches the timeline selection in ALB.

Your options now depend on your connection status in Euroscope. You can use ALB online, in playback, or on a sweatbox. As a standalone user you can adjust scenario, arrival rate, sorting, and layout locally for your own instance.

## 2. Part of a team for ```<ICAO>``` as regular user
Launch is as for standalone user.

If you are part of a team for a given airport `<ICAO>` and someone else has claimed the role as Flow Management Responsible (FMR), that will be indicated in the interface. In that case you should normally avoid changing scenario or arrival rate for that airport, as the active FMR plan will take precedence.

You can still change your local layout and other display-oriented preferences.

## 3. Part of a team for ```<ICAO>``` as Flow Manager
Claim FMR with:

```
.alb fmr <ICAO>
```

As FMR you coordinate the shared arrival plan for that airport, including scenario and arrival-rate changes. If an aircraft is already in hold and prerequisites are met, you can also set EAT with:

```
.alb seat <Callsign> <HHMM>
```

For command details, see [Workflows](user/workflows.md). For the operational EAT model, see [Coordination of sequencing and EAT](msgs/eat_detailing.md).



Next: [Using the UI](user/ui.md)



