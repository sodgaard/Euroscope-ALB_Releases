# Arrival Load Balancer (ALB)

This site is split into three lanes:

- **Operational Guide** for controllers using ALB during a session
- **Configuration & Customization** for users editing `alb-config.json`, layouts, timelines, and airport setup
- **Developer / Internals** for maintainers working across the ALB code, release, and documentation repos

ALB helps you keep an arrival picture, coordinate via-fix and landing planning, and share that plan with other ALB users for the same destination airport.

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

![ALB main window](img/Full-screenshot-ESandALB.png)

[Open full size](img/Full-screenshot-ESandALB.png)
