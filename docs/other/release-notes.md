# Release Notes

These notes summarize release highlights. They are not a complete commit log.

For detailed asset-by-asset history, use the GitHub releases page:

- [Euroscope-ALB_Releases Releases](https://github.com/sodgaard/Euroscope-ALB_Releases/releases){target="_blank" rel="noopener"}

## Unreleased - target 0.3.1D

This section summarizes the next post-`0.3.1C` change set heading toward the
next public ALB release. The development source tree has already been bumped
past `0.3.1D`, but no `0.3.1D` package has been published in this release repo
yet.

### Added

- Added clearer peer visibility for EAT policy in the `Peers` view.
- Added improved active-timeline PRA display resolution by ICAO and callsign context.

### Changed

- Fixed FMR EAT mode policy synchronization on peers.
- Clarified HOLD_EAT authority so `HLW` is the active local write gate.
- Retired the `HLS` control-bar button from normal operator use while keeping legacy compatibility state.
- Removed `SEAT` from the normal backend-primary authority path. Backend-primary `SET2` authority now carries canonical EAT, and the final `/HOLD_EAT/HHMM/` write is only an aircraft-visible side effect. Legacy `SEAT` handling remains only as fallback or compatibility code.
- Kept peer PRA display informational only.
- Replaced the peer-list `HLS` column with an EAT-policy view while keeping ETA as the `ES` or `ALB` branch indicator.
- Cleared stale `HIG` and RT-issue state more cleanly on `/XHOLD/` and related non-hold scratchpad updates.

### Technical notes

- These changes are aimed at clearer authority boundaries and cleaner peer display behavior.
- The development source tree has already moved on to a `0.3.1E` virgin version bump after these changes.
- They do not intentionally change the core `EAT:LT` or `EAT:AR` sequencing model.

## 0.3.1C

### Added

- Added backend sequence synchronization load-management work through Stage 4.1.
- Added diagnostics-only instrumentation for backend `SEQ/SET2` synchronization.
- Added `HORIZON` seqsync mode.
- Added seqsync status and diagnostic visibility for reduced-sync operation.
- Added passive peer update-cycle health monitoring.
- Added backend-only peer health warnings for the FMR when a peer appears slow to apply shared backend updates.

### Changed

- Backend sequence synchronization can now reduce traffic under load while preserving backend canonical sequence authority.
- Added bounded overlay-clear `DEL` handling.
- Added bounded return-to-normal recovery behavior after reduced-sync modes.
- When a manual FMR is active, non-FMR peers are now blocked from mutating shared `LT`, `PLR`, `AR`, and scenario policy locally.
- In runway-sequence collaboration, `Advance 1` and `Resequence` now use a request-reply path and wait for canonical backend sync instead of silently applying a competing local result.
- Tightened peer LT fallback gating so backend-primary runway-sequence handling stays aligned with canonical backend sync.
- Added timeout handling for pending runway peer `Advance 1` and `Resequence` requests.

### Technical notes

- Canonical per-aircraft sequence state remains backend-only.
- `FLOAT` remains parked and is not part of this release.
- This work does not intentionally alter the `EAT:LT` or `EAT:AR` sequencing algorithms.

## 0.3.1B

### Added

- Added backend poll sub-phase performance breakdown.
- Added backend poll timing and backlog visibility to performance reporting.

### Changed

- Improved diagnosis of backend-dominated timer spikes and performance pauses.

## 0.3.1A

### Changed

- Adjusted perftest reporting.
- Refined live ALB policy button authority so active non-observer peers may control shared live policy when no manual FMR is present, while manual FMR ownership remains exclusive when active.

## 0.3.0

### Added

- Established the `0.3.x` release line.
- Introduced the loader/core packaging model:
  - `ALB.dll` as the EuroScope loader
  - `ALBCore.dll` as the runtime core
- Updated public install and release documentation around the new packaging model.

### Changed

- Set the source version and minimum protocol floor to `0.3.0`.

## Notes for users

If you are updating from an older ALB installation:

- make sure EuroScope loads `ALB.dll`, not `ALBCore.dll`
- keep `alb-config.json` as your live config
- treat `alb-config.default.json` as a template or default file

For installation details, see [Download & Install](../download-install.md).

## Maintainer note

The `0.3.x` release tags may not line up cleanly with the release-version
pointer history. For future release-note maintenance, prefer:

1. `alb-plugin-release-version.txt` history in `Euroscope-ALB_Releases`
2. `MY_PLUGIN_VERSION` history in `ES-ArrivalLoadBallance`
3. commit ranges between those anchors
4. release tags only after checking what content they actually point to
