# Release Notes

These notes summarize release highlights. They are not a complete commit log.

For detailed asset-by-asset history, use the GitHub releases page:

- [Euroscope-ALB_Releases Releases](https://github.com/sodgaard/Euroscope-ALB_Releases/releases){target="_blank" rel="noopener"}

## 0.3.1R

This release follows `0.3.1Q`.

### Added

- Added optional `glEatCombiDisplay.gainLooseEmptyText` config support for a
  trimmed placeholder when Gain/Lose is the selected Combi state but the actual
  Gain/Lose value is empty.

### Changed

- `Reload config` no longer re-opens the ALB window if it is currently closed.
- Combi radar-tag output is now gated by active timeline destination coverage,
  so only aircraft whose destination belongs to a currently active ALB timeline
  can show Combi output.
- Route-based `directRouting` fallback now shows the actual current next fix
  instead of the following waypoint.
- Corrected certain compact field labels, including the combined Combi and
  via-fix timing headings.

### Fixed

- Prevented unrelated non-ALB traffic from showing configured empty Gain/Lose
  placeholders in Combi output.

### Technical notes

- `openAutomatically` remains the startup auto-open behavior. It is no longer
  reapplied during later config reloads.
- `gainLooseEmptyText` is backward-compatible and optional. If omitted or blank,
  ALB keeps the older empty-string behavior for empty Gain/Lose cases.

## 0.3.1Q

This release is the next published step after `0.3.1N`.

It includes the public-facing work developed through the internal `0.3.1O` and
`0.3.1P` source milestones as well as the final `0.3.1Q` transport and
continuity hardening.

### Added

- Added airport-configurable WTC-aware `EAT:LT` landing spacing for `L`, `M`,
  `H`, and `J`.
- Added the `Expected LR` landing-rate forecast row below `PLR`.
- Added startup-only baseline keys for preferred startup policy and hold-write
  state:
  - `eatModeOnStartup`
  - `etaModeOnStartup`
  - `holdEatWriteOnStartup`
- Added a local runtime hold-display control for the compact `glEatCombi`
  field.

### Changed

- `EAT:LT` now uses the larger of PLR spacing, minimum spacing behind the
  leader, and minimum space required in front of the follower while preserving
  the established canonical landing order.
- Runtime `EAT`, `ETA`, and `HLW` selections are preserved more cleanly across
  `Reload config`.
- Config reload is now transactional and also tries to preserve the current
  timeline zoom.
- Backend reconnects and mixed backend or scratchpad peer routing are hardened
  so collaboration continuity is safer through reconnect windows.

### Fixed

- Hardened ALB close or teardown behavior around config reload and controller
  finalization.
- Improved AR debounce shutdown handling on ALB window close.
- Hardened cloud send timeout handling so failed setup, send, or receive paths
  fail safely.

### Technical notes

- Mixed-peer reconnect continuity is automatic. Operators still use the normal
  FMR, seqsync, and config-reload controls.
- `Expected LR` remains informational. It does not automatically change PLR,
  LT order, EAT, or PLT.

## 0.3.1P (Source-only)

### Changed

- Made config reload transactional instead of publishing partial live state.
- Preserved active UI and intercom state more cleanly across successful reload.

### Fixed

- Replaced the detached ALB window finalizer with a joinable controller worker.
- Fixed finalizer publication and closed-ordering races.
- Destroyed the ALB controller before plugin singleton and log teardown.
- Fixed ALB window AR debounce worker lifetime on close.

## 0.3.1O (Source-only) - WTC-aware EAT:LT spacing and Expected LR

### WTC-aware EAT:LT spacing

- Added airport-configurable WTC-dependent landing spacing for `L`, `M`, `H`, and `J`.
- Landing spacing now uses the largest of:
  - PLR-derived spacing
  - minimum spacing behind the leader
  - minimum space required in front of the follower
- Added complete built-in default matrices, making config overrides optional.
- Missing or unknown WTC uses the configured airport fallback category, defaulting to `M`.
- Applied pair spacing consistently through normal sequencing, forced placement, deferred placement, FIFO gap-fill, protected-`FZ2` endpoint checks, and comparison chains.
- Preserved natural traffic gaps and the established canonical landing order.

### Expected landing rate

- Added a demand-limited Expected LR forecast based on final canonical LT PLTs.
- The default forecast window is 30 minutes and may be configured per airport.
- Added an `Expected LR` row directly below PLR.
- The row shows:
  - expected hourly landing rate
  - eligible aircraft count
  - forecast window
  - difference from PLR
- A result below PLR is informational and is not automatically shown as a warning.
- No smoothing, trend, or automatic PLR adjustment is applied.

### Hardening

- Improved duplicate-callsign handling in the Expected LR calculation.
- Preserved candidate-specific spacing during LT gap-fill.
- Strengthened protected-`FZ2` adjacent-pair checks.
- Extended WTC-spacing and Expected LR diagnostics.
- Corrected gap-fill preview diagnostics to use the final assigned PLT.

### Compatibility

- Existing configurations remain valid.
- WTC matrix overrides are optional.
- No `SET2`, `POL`, `PREF`, scratchpad, backend, or cloud-schema change was required.
- FMR remains authoritative for canonical sequence, EAT, and PLT.
- LT slot tolerance remains PLR-based.

### Validation status

- Implementation, static review, and direct `ALBCore.dll` build verification are complete.
- Operational live or playback validation remains pending.

## 0.3.1N (Source-only)

This section summarizes the `0.3.1N` change set from the development source
tree.

### Added

- Added a local runtime control for the combined hold-display threshold and pre-threshold display mode used by `glEatCombi`.
- Added grouped display buttons for the compact `EAT` or `PLT` combi selection and the hold-display selection.
- Added `holdEatWriteOnStartup` so `HLW` can start in a chosen local state on the initial config load.

### Changed

- `eatModeOnStartup` and `etaModeOnStartup` are now applied only during the initial config load instead of being reapplied on every config reload.
- Startup `EAT` and `ETA` mode strings now accept valid values case-insensitively and fall back independently to `AR` and `ES` if missing or invalid.
- Runtime `EAT`, `ETA`, and `HLW` selections are now preserved across `Reload config`.
- Hold-display runtime settings are seeded from config and reset on a successful config reload.
- The new hold-display button keeps hold-inside-threshold display fixed to countdown while making the pre-threshold display easier to adjust locally.
- Control-bar display buttons now use grouped styling, fixed-width labels, and more stable spacing so neighboring buttons do not shift.

### Fixed

- Unsupported `holdGtXState` values are now normalized more safely to `Blank`.
- Tooltip and window cleanup around the new hold-display controls is handled more safely during shutdown.

### Technical notes

- This change set is mainly startup-config and local display/runtime cleanup.
- Shared sequencing logic, peer synchronization, and the normal ALB planning models are not intentionally changed by the new hold-display controls.

## 0.3.1M (Source-only)

### Added

- Added a cleaner normal operator surface by hiding more legacy-visible control-bar items.

### Changed

- Hid the visible `FPC` control-bar button while preserving underlying `FPC` state, protocol behavior, peer sync, and logging behavior.
- Hid the visible `Scenarios` menu while preserving internal scenario config and apply logic for compatibility.
- Moved `Peers(...)` to appear immediately after `Layout` in the visible menu order.

### Technical notes

- This step is mainly public UI cleanup rather than a change to ALB planning logic.

## 0.3.1L (Source-only)

### Added

- Expanded cloud operational logging across ALB authority and policy flows.

### Technical notes

- This step is mainly operational logging and internal traceability work.

## 0.3.1J (Source-only)

### Added

- Added backend-authority timeout and timeout-now release handling for reduced-sync and horizon coverage-loss cases.
- Added throttled backend-health and auto-fallback diagnostics.
- Added clearer FMR recovery and backend owner-shadow diagnostics.
- Added alias telemetry for the `MODEL_HANDOFF` performance cause.

### Changed

- Runway `Advance 1` peer requests can now proceed without requiring local predecessor context.
- `Advance 1` direct local apply and runway peer-request routing are now handled more explicitly.
- Natural resequence reset now clears committed-slot and LT gap latches more cleanly.
- Repeated same-fix `HOLD` scratchpad assignments now preserve `HOLD_EAT` state more safely.
- `HOLD` and `XHOLD` scratchpad ingestion is now treated more like event handling instead of implicit state clear on scratchpad restore.
- Near-origin `RDA` visual-clear diagnostics were clarified.

### Technical notes

- This step is a mixed backend-recovery, peer-sequencing, and hold-ingestion cleanup pass.

## 0.3.1I (Source-only)

### Added

- Added a non-reentrant snapshot guard for structured backend recording.
- Added pending-retry tracking for temporary structured-recording snapshot collisions.

### Changed

- Temporary device-busy or resource-busy snapshot collisions are now treated as recoverable instead of disabling structured recording for the session.

### Fixed

- Fixed carrier-list self-lock and recording-lock callouts.

## 0.3.1H (Source-only)

### Fixed

- Corrected a thrown exception.

## 0.3.1F (Source-only)

### Added

- Added peer performance telemetry and FMR warning or report logging.
- Added lower-noise backend sequence authority-transition and blocked-write diagnostics.
- Added more focused apply-path and `HOLD_EAT` performance diagnostics while reducing `SET2` receive churn.

### Changed

- Separated active backend sequence authority from stale receive-revision memory so `DEL` clears peer write-blocking authority without losing stale-message protection.
- Aligned source comments with the backend sequence `DEL` authority teardown behavior.

## 0.3.1E (Released)

### Changed

- Replaced the peer-list `HLS` display column with an EAT-policy column while keeping ETA as the `ES` or `ALB` branch indicator.
- Cleared `HIG` and RT-issue state more cleanly on `/XHOLD/` and related non-hold scratchpad updates.
- Preserved exiting-hold `HOLD_EAT` protection more safely.
- Kept OBS-list removal retryable when flight-plan removal is temporarily unavailable.
- Kept peer PRA display informational only while improving PR-count resolution by ICAO and callsign in the active-timeline context.

## 0.3.1D (Released)

### Changed

- Fixed FMR EAT-mode policy synchronization on peers.
- Clarified `HOLD_EAT` authority so `HLW` is the active local write gate.
- Retired the `HLS` control-bar button from normal operator use while keeping legacy compatibility state.
- Kept `SEAT` as legacy or fallback-only hold-EAT handling rather than part of the normal backend-primary path.
- Refreshed timeline notes more reliably on peer clear, expiry, and count changes.

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
