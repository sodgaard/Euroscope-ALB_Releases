# Arrival Load Balancer (ALB)

This site is split into two lanes:

- **Operator documentation** for controllers using ALB during a session
- **Technical documentation** for maintainers working across the ALB code, release, and documentation repos

ALB helps you keep an arrival picture, coordinate via-fix and landing planning, and share that plan with other ALB users for the same destination airport.

The installed package uses a small loader/runtime split:

- `ALB.dll` is the EuroScope-facing loader
- `ALBCore.dll` is the ALB runtime plugin

EuroScope should load `ALB.dll`.

## Start Here

- [Download & Install](download-install.md)
- [Quick Start](quick-start.md)
- [User Overview](user/overview.md)

## Operator Guide

- [Interface](user/interface.md)
- [Buttons & Menus](user/buttons-and-menus.md)
- [Aircraft Actions](user/aircraft-actions.md)
- [Workflows](user/workflows.md)
- [Collaboration & FMR](user/collaboration-fmr.md)
- [Legend & Status](msgs/legend-status.md)
- [EAT Coordination](msgs/eat_detailing.md)

## Technical Guide

- [Technical Overview](technical/index.md)
- [Architecture](technical/architecture.md)
- [Collaboration Internals](technical/collaboration-internals.md)
- [Sequencing Model](technical/sequencing-model.md)
- [Backend Transport](technical/backend-transport.md)
- [Playback & Recording](technical/playback-recording.md)

## Configuration And Support

- [Config Description](config/config-description.md)
- [Sample Config](config/config-file-sample.md)
- [FAQ](other/faq.md)
- [Troubleshooting](other/troubleshooting.md)
- [Release Notes](other/release-notes.md)
- [Discord](https://discord.gg/CH4N7WbT){target="_blank" rel="noopener"}

![ALB main window](img/Full screenshot ESandALB.png)

[Open full size](img/Full screenshot ESandALB.png)
