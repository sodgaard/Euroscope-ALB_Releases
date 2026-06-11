# Buttons & Menus

This page is the quick reference for the controls across the top of the ALB window.

## Top buttons

- `EAT` or `PLT`: changes what the compact combi field shows in the aircraft rows. It is a local display toggle and does not change the shared sequence logic.
- `TXE*` or `TXE-`: controls whether EAT-related policy is transmitted when you are the manual FMR. In normal operations, only the manual FMR can change this.
- `EAT:AR`, `EAT:LT`, and sometimes `EAT:TF`: selects the planning basis used for EAT.

- `EAT:AR` means via-fix or arrival-rate based planning
- `EAT:LT` means landing-timeline based planning
- `EAT:TF` means target-fix based planning when that mode is present

- `ETA:ES` or `ETA:ALB`: selects whether the ETA basis follows EuroScope or the ALB calculation.
- `HLS*` or `HLS-`: controls whether hold EAT stays synchronized with the holding list. Right-clicking this button opens a reset menu for HOLD_EAT overrides.
- `FPC*` or `FPC-`: toggles fix-passage correction, which lets ALB correct downstream planning after actual fix passage.
- `HLW*` or `HLW-`: allows this ALB instance to write accepted hold timing back to the TopSky holding list.

## Who can change these buttons

- `EAT` or `PLT` is local display only
- `TXE` is restricted to the manual FMR
- `EAT`, `ETA`, `HLS`, `FPC`, and `HLW` are shared-planning controls and should normally be changed by the controller currently responsible for the shared plan

See [Collaboration & FMR](collaboration-fmr.md) for the authority rules behind that.

## Dropdown menus

### Timelines

Chooses which timeline definitions are active.

- You can select one or more timelines
- Timelines come from the ALB config
- They define destination airport coverage, streams, scenarios, and default layout behavior

![ALB Dropdown Timeline](../img/ALB-Popup-Timelines.png)

### Via-fixes

Lets you focus on the streams you care about.

- In some layouts, deselected streams are dimmed
- In other layouts, deselected streams may be hidden
- The exact behavior depends on the selected layout

![ALB Dropdown ViaFixes](../img/ALB-Popup-ViaFixes.png)

### Scenarios

Applies a predefined set of via-fix arrival rates to the active timeline.

- Use this for broad flow changes
- Use direct AR clicks for fine tuning after the scenario is in place

![ALB Dropdown Scenarios](../img/ALB-Popup-Scenarios.png)

### Peers

Shows an informational list of ALB peers for the active airport or airports.

- This menu is for awareness, not for actions
- It is the quick way to see whether another ALB instance is already coordinating the plan

### Layout

Changes how each aircraft row is displayed.

- Layouts come from config
- A layout can make ALB feel feeder-oriented or runway-oriented
- The available choices depend on the loaded config

![ALB Dropdown Layout](../img/ALB-Popup-Layout.png)

## Stats-area click actions

- `PLR`: left-click decreases planned landing rate by 1. Right-click increases it by 1.
- `AR`: left-click decreases the stream interval by 1 minute. Right-click increases it by 1.

In `EAT:LT` mode, AR adjustment is disabled even though the AR values are still visible.
