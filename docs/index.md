# Arrival Load Balancer (ALB)

ALB is an arrival-planning tool for EuroScope. It helps controllers have a shared picture of inbound traffic and flow, monitor via-fix streams (STARs) and landing sequence development, and coordinate timing or sequence corrections before aircraft reach the runway.

In practice, ALB sits between the raw live EuroScope picture and the controller's and FMRs operational plan. It is not a separation tool on its own. It is a planning and coordination aid for live positions, team sessions, and event-style arrival management.

![ALB main window](img/Full-screenshot-ESandALB.png)

[Open full size](img/Full-screenshot-ESandALB.png)

## Core ideas

Before going deeper into the site, it helps to know the main ALB planning terms:

- `EVTO`: the estimated time over the relevant via-fix or inbound gate point
- `EAT`: the estimated arrival time used for flow and sequencing coordination
- `ELT`: the estimated landing time branch
- `PLT`: the planned landing time in the current ALB plan

Two planning modes matter most in normal use:

- `EAT:LT` is the preferred modern method and plans against the landing timeline
    - `PLR` Planned Landing Rate (aircraft per hour) - is the rate per airport ALB uses to separate aircraft.
- `EAT:AR` is the older rough fallback method and plans more directly by via-fix arrival spacing
    - `AR` Arrival Rate - is the rate (minuts separation) per ViaFix ALB uses to separate aircraft per ViaFix (STAR).

Two estimate branches also matter:

- `ETA:ES` or `ELT-ES` follows the live EuroScope-style estimate branch
- `ETA:ALB` or `ELT-ALB` follows ALB's own corrected estimate branch when that branch is useful

In shared use, one controller may act as **FMR**:

- **Flow Management Responsible**
- the controller who is treated as the authority for the shared arrival plan for a destination airport
- the person who normally owns the main planning choices, sequence interventions, and shared timing policy

This documentation is meant to help with three slightly different kinds of work:

- **Operational Guide** for controllers using ALB during a session
- **Configuration & Customization** for users editing `alb-config.json`, layouts, timelines, and airport setup
- **Developer / Internals** for maintainers working across the ALB code, release, and documentation repos

The installed package uses a small loader/runtime split:

- `ALB.dll` is the EuroScope-facing loader
- `ALBCore.dll` is the ALB runtime plugin

EuroScope should load `ALB.dll`.

## Start Here

- [Download & Install](download-install.md)
- [Quick Start](quick-start.md)
- [Quick Intro](user/quick-intro.md)
- [User Overview](user/overview.md)
- [Planning Modes Overview](user/planning-modes/index.md)

## Operational Guide

- [Quick Intro](user/quick-intro.md)
- [Overview](user/overview.md)
- [Planning Modes Overview](user/planning-modes/index.md)
- [EAT:LT Landing Timeline Planning](user/planning-modes/eat-lt.md)
- [EAT:AR Arrival Rate Fallback Planning](user/planning-modes/eat-ar.md)
- [Feeder View vs Runway View](user/planning-modes/views.md)
- [Interface](user/interface.md)
- [Buttons & Menus](user/buttons-and-menus.md)
- [Aircraft Actions](user/aircraft-actions.md)
- [Workflows](user/workflows.md)
- [Collaboration & FMR](user/collaboration-fmr.md)
- [Legend & Status](msgs/legend-status.md)
- [EAT Coordination](msgs/eat_detailing.md)
- [Retired: Scenarios](user/retired/scenarios.md)

## Configuration & Customization

- [Config Overview](config/index.md)
- [Display Fields and Tag Layouts](config/display-fields.md)
- [ICAO / Airport Setups](config/icao-setups.md)
- [Timeline / Via-fix / Runway Setups](config/timelines.md)
- [Config File Reference](config/config-description.md)
- [Sample Config File](config/config-file-sample.md)

## Developer / Internals

- [Technical Overview](technical/index.md)
- [Architecture](technical/architecture.md)
- [Collaboration Internals](technical/collaboration-internals.md)
- [Sequencing Model](technical/sequencing-model.md)
- [Backend Transport](technical/backend-transport.md)
- [Playback & Recording](technical/playback-recording.md)

## Support And Other

- [FAQ](other/faq.md)
- [Troubleshooting](other/troubleshooting.md)
- [Release Notes](other/release-notes.md)
- [Discord](https://discord.gg/CH4N7WbT){target="_blank" rel="noopener"}
