# Buttons & Menus

This page is the quick reference for the controls across the top of the ALB window.

## Top buttons

- `EAT` or `PLT`: changes what the compact combi field shows in the aircraft rows. It is a local display toggle and does not change the shared sequence logic.
- `TXE*` or `TXE-`: controls whether EAT-related policy is transmitted when you are the manual FMR. In normal operations, only the manual FMR can change this.
- `EAT:AR`, `EAT:LT`, and sometimes `EAT:TF`: selects the planning basis used for EAT.
  - `EAT:AR` means via-fix or arrival-rate based planning
  - `EAT:LT` means landing-timeline based planning
  - `EAT:TF` means target-fix based planning when that mode is present
  - `EAT:LT` is the recommended modern operating mode
- `ETA:ES` or `ETA:ALB`: selects whether the ETA basis follows EuroScope or the ALB calculation.
- `HLS*` or `HLS-`: controls whether hold EAT stays synchronized with the holding list. Right-clicking this button opens a reset menu for HOLD_EAT overrides. Turning `HLS` off clears the current HOLD_EAT override influence and returns hold EAT handling to the automatic ALB calculation path.
- `FPC*` or `FPC-`: toggles fix-passage correction, which lets ALB correct downstream planning after actual fix passage.
- `HLW*` or `HLW-`: allows this ALB instance to write accepted hold timing back to the TopSky holding list.

## Who can change these buttons

- `EAT` or `PLT` is local display only
- `TXE` is restricted to the manual FMR
- `EAT`, `ETA`, `HLS`, `FPC`, and `HLW` are shared-planning controls and should normally be changed by the controller currently responsible for the shared plan

See [Collaboration & FMR](collaboration-fmr.md) for the authority rules behind that.

## EAT:AR versus EAT:LT

`EAT:AR` is the simpler fallback method.

- it uses per-via-fix release spacing as the active planning driver
- it often requires the FMR to keep watching and tweaking AR values
- it gives a rougher flow picture than LT

`EAT:LT` is the preferred current method.

- it plans against the landing timeline
- it gives ALB a stronger picture of the runway-side sequence
- the FMR mainly monitors conformance and corrects aircraft that do not follow the expected order

In `EAT:LT`, AR is not the active planning driver. Visible AR values are legacy/context information, and AR adjustment is not the normal control method.

## ETA and ELT naming

The top button uses `ETA:ES` and `ETA:ALB`, while some layouts may show row fields called `ELT`, `ELT-ES`, or `ELT-ALB`.

- `ETA:ES` or `ELT-ES` means the EuroScope or live estimate branch
- `ETA:ALB` or `ELT-ALB` means the ALB-calculated or corrected estimate branch

Operator-facing rule of thumb:

- before the aircraft is deep into terminal or post-via handling, ALB can use its own corrected branch to give a more useful planning estimate
- once the aircraft is in terminal or post-via phases, ALB stops forcing a separate corrected landing estimate and follows the live EuroScope branch where appropriate
- peers should normally follow the FMR's selected ETA policy

## Dropdown menus

### Timelines

Chooses which timeline definitions are active.

- You can select one or more timelines
- Timelines come from the ALB config
- They define destination airport coverage, streams, retained legacy scenario definitions, and default layout behavior

![ALB Dropdown Timeline](../img/ALB-Popup-Timelines.png)

### Via-fixes

Lets you focus on the streams you care about.

- In some layouts, deselected streams are dimmed
- In other layouts, deselected streams may be hidden
- The exact behavior depends on the selected layout

![ALB Dropdown ViaFixes](../img/ALB-Popup-ViaFixes.png)

### Scenarios

The `Scenarios` menu is retained as retired or legacy UI.

- It exists for compatibility and history
- It is not part of the recommended current operating method
- In normal modern ALB use, the FMR works mainly through `EAT:LT` monitoring and correction instead of scenario switching

![ALB Dropdown Scenarios](../img/ALB-Popup-Scenarios.png)

See [Retired: Scenarios](retired/scenarios.md).

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

In `EAT:LT`, AR is not the active planning driver. Visible AR values are legacy/context information, and AR adjustment is not the normal control method.
