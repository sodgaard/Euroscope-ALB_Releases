# Config File Reference

This page documents the public and advanced-user configuration surface for
`alb-config.json`.

A sample file is available here:
[alb-config.json](./config-file-sample.md)

## Overview

`alb-config.json` is the live operational ALB config.

Important packaging facts:

- EuroScope should load `ALB.dll`
- `ALB.dll` loads `ALBCore.dll`
- `alb-config.json` is the live config and must not be silently overwritten by
  the loader
- `alb-config.default.json` is the shipped template/default file

Place `alb-config.json` in the same plugin folder as `ALB.dll` and
`ALBCore.dll`.

After changing config, apply it with `.alb reload` or the `[Reload config]`
entry in the `Timelines` menu. A successful reload now preserves the active
runtime session more cleanly than older builds, including current policy
selections such as `EAT`, `ETA`, and `HLW`, while also trying to keep the
current timeline zoom. Reload does not auto-open the ALB window again if it is
currently closed.

## Current config version support

The current ALB runtime supports:

- `configFormatVersion: 2`
- `configFormatVersion: 3`

For new configs, use:

```json
"configFormatVersion": 3
```

## Encoding note

The plugin uses the EuroScope font, which is based on
[ANSI/Windows-1252](http://www.alanwood.net/demos/ansi.html).

If you use special symbols in `static` tag fields, save the JSON file using
ANSI encoding.

## Scope of this page

This page documents the operational and advanced-user config surface.

A live config file may also contain additional developer-only keys used for
internal validation workflows. Those are intentionally not described on the
public documentation site.

## Disabling blocks with `-off` / `_off`

When loading `timelines` and `tagLayouts`, any key ending with `-off` or `_off`
(case-insensitive) is skipped.

Examples:

- `"EKCH 04L/04R-SI_off"`
- `"Expanded-off"`

This disable pattern does **not** apply globally to all config keys. For many
top-level settings, the runtime reads only the exact expected key name.

## Top-Level Options

| Property | Type | Default | Description |
|---|---:|---:|---|
| `configFormatVersion` | int | required in practice | Config schema version. Use `3` for new configs. |
| `openAutomatically` | bool | `true` | Opens the ALB window on the initial config load. It does not re-open the window on later config reloads. |
| `logging` | bool | `false` | Enables ALB runtime text logging. |
| `scratchpadRunwayOverride` | bool | `false` | Enables scratchpad runway override parsing for `assignedRunwayOverride`. |
| `timeSyncBeacons` | bool | `false` | Enables time-sync beacon support. |
| `timelineSort` | string | `targetFixEta` | Default timeline sort mode. Accepted values include `target` / `tfe` and `landing` / `lde` / `lnd`. |
| `eatModeOnStartup` | string | `AR` | Startup EAT mode, applied only during the initial config load. Accepted values are `AR`, `TF`, and `LT` case-insensitively. |
| `etaModeOnStartup` | string | `ES` | Startup ETA source, applied only during the initial config load. Accepted values are `ES` and `ALB` case-insensitively. |
| `holdEatWriteOnStartup` | bool | `false` | Startup state for local `HLW`, applied only during the initial config load. |
| `holdEatLockWindowMinutes` | int | `5` | Hold/EAT lock window in minutes. Applied only if greater than 0. |
| `holdEatRecentChangeMinutes` | int | `2` | Recent-change window for hold/EAT logic in minutes. Applied only if greater than 0. |
| `aircraftLastSeenSweepMinutes` | int | `2` | How often ALB sweeps persisted aircraft state for expiry. Applied only if greater than 0. |
| `aircraftOfflineResetMinutes` | int | `5` | Threshold after which unseen aircraft have persisted sequencing/state cleared. Applied only if greater than 0. |
| `housekeeping` | object | defaults below | Startup housekeeping settings for ALB logs and backend recording folders. |
| `soundMute` | object | all `false` | Startup mute state for ALB sounds. |
| `layout` | object | defaults below | EAT display precision/suffix formatting settings. |
| `backendSeqSync` | object | defaults below | Backend sequence synchronization transport mode and TX-budget settings. |
| `backendPeerHealth` | object | defaults below | Passive backend peer update-cycle health monitoring and warning thresholds. |
| `glEatCombiDisplay` | object | defaults below | Optional display mapping for the combined `glEatCombi` field. |
| `timelines` | object | required | Timeline definitions. |
| `tagLayouts` | object | required | Tag layout definitions. |
| `tmaAreas` | array | optional | TMA polygon definitions. |
| `airports` | object | optional | Airport-specific fallback circles, runway metadata, and advanced airport tuning. |

### Startup-only policy keys

`eatModeOnStartup`, `etaModeOnStartup`, and `holdEatWriteOnStartup` are read
only during the initial config load.

That means:

- they are useful for your preferred startup baseline
- they do not force the runtime back to those values on every `Reload config`
- missing or invalid values fall back independently to safe defaults

Current independent fallbacks are:

- `eatModeOnStartup` -> `AR`
- `etaModeOnStartup` -> `ES`
- `holdEatWriteOnStartup` -> `false`

## Housekeeping

`housekeeping` controls startup cleanup of ALB runtime files.

Example:

```json
"housekeeping": {
  "enabled": true,
  "textLogRetentionDays": 16,
  "textLogMaxFilesPerFamily": 20,
  "backendRecordingRetentionDays": 16,
  "backendRecordingMaxFolderMb": 500
}
```

| Property | Type | Default | Accepted range | Description |
|---|---:|---:|---:|---|
| `enabled` | bool | `true` | n/a | Enables runtime housekeeping. |
| `textLogRetentionDays` | int | `14` | `1-365` | Retention period for ALB text log families. |
| `textLogMaxFilesPerFamily` | int | `20` | `5-500` | Max number of files kept per text-log family. |
| `backendRecordingRetentionDays` | int | `14` | `1-365` | Retention period for backend recording material. |
| `backendRecordingMaxFolderMb` | int | `500` | `50-10000` | Max size for backend recording folder before pruning. |

If values are invalid, the runtime falls back to defaults and logs a warning.
Out-of-range integers are clamped.

## Sound Mute

`soundMute` sets the startup mute state for ALB audio categories.

Example:

```json
"soundMute": {
  "all": false,
  "missed": false,
  "ar": {
    "all": false,
    "unselected": false
  }
}
```

| Property | Type | Default | Description |
|---|---:|---:|---|
| `all` | bool | `false` | Mutes all ALB sounds. |
| `missed` | bool | `false` | Mutes missed-approach/go-around related sounds. |
| `ar.all` | bool | `false` | Mutes all arrival-rate related sounds. |
| `ar.unselected` | bool | `false` | Mutes arrival-rate sounds for unselected/non-focused contexts. |

## Layout

`layout` controls how EAT-style times are displayed in the timeline.

Example:

```json
"layout": {
  "EatDisplayPrecisionSec": 15,
  "EatDisplayRecalcPeriodSec": 15,
  "EatDisplayIntervalChars": ["", "a", "b", "c", "d"]
}
```

| Property | Type | Default | Accepted values | Description |
|---|---:|---:|---|---|
| `EatDisplayPrecisionSec` | int | `60` | `15` or `60` | Display precision for EAT-style time presentation. |
| `EatDisplayRecalcPeriodSec` | int | `0` | `0-600` | Display regeneration throttle. `0` means automatic. |
| `EatDisplayIntervalChars` | string[] | `["","a","b","c","d"]` | up to 5 one-character strings | Suffix characters used by `HHMMx` / `HH:MMx` formats. |

### Precision and suffix behavior

If `EatDisplayPrecisionSec` is `60`, ALB displays normal minute precision.

If `EatDisplayPrecisionSec` is `15`, ALB uses five second-bucket suffix slots
across each minute. With the default suffix characters this behaves roughly as:

- `00:00` to `00:07.5` -> no suffix
- `00:07.5` to `00:22.5` -> `a`
- `00:22.5` to `00:37.5` -> `b`
- `00:37.5` to `00:52.5` -> `c`
- `00:52.5` to `01:00` -> `d`

### Recalc throttle

`EatDisplayRecalcPeriodSec` controls how often display strings are regenerated.

- `0` = automatic
- automatic means `15` seconds when precision is `15`
- automatic means `1` second when precision is `60`

## Backend SeqSync

`backendSeqSync` controls backend sequence synchronization load-management.

This block affects how ALB sends canonical backend sequence state to peers. It
does not change FMR ownership, `EAT:AR` versus `EAT:LT`, scratchpad fallback,
or the local sequencing algorithms themselves.

Example:

```json
"backendSeqSync": {
  "mode": "normal",
  "txMaxSet2PerSecond": 10,
  "txMaxSet2PerPoll": 10,
  "txQueueMaxAgeSec": 30,
  "authorityLeadMinutes": 10
}
```

Accepted mode values:

- `normal`
- `throttled`
- `horizon`
- `suspend`

Fallback behavior:

- missing `mode` falls back to `normal`
- malformed or unknown `mode` falls back to `normal`

| Property | Type | Default | Accepted range or values | Description |
|---|---:|---:|---|---|
| `mode` | string | `normal` | `normal`, `throttled`, `horizon`, `suspend` | Backend seqsync transport mode. `normal` is immediate in steady state. |
| `txMaxSet2PerSecond` | int | `10` | `1-200` | Per-second budget for queued canonical `SET2` transmission. Used by `throttled`, `horizon`, `suspend` recovery, and temporary normal recovery drain. |
| `txMaxSet2PerPoll` | int | `10` | `1-200` | Per-poll budget for queued canonical `SET2` transmission. Used by the same queued or recovery paths as above. |
| `txQueueMaxAgeSec` | int | `30` | `1-600` | Queue age budget for backend seqsync queue handling. |
| `authorityLeadMinutes` | int | `10` | `1-60` | Horizon relevance window in minutes. Used by `horizon` to distinguish near-horizon traffic from far-floating traffic. |

Operational notes:

- `normal` is immediate in steady state
- `throttled`, `horizon`, `suspend`, and temporary recovery drain use the TX queue budgets
- `horizon` may suppress far-floating canonical candidates while keeping operationally relevant or uncertain aircraft canonical
- `suspend` suppresses normal canonical `SET2` TX, but operational `DEL` remains allowed and bounded
- returning from `throttled` or `horizon` to `normal` uses temporary bounded recovery drain until inherited queue backlog is empty
- edit this block in `alb-config.json` when you want different startup or
  default transport behavior, then apply it with `.alb reload` or by
  restarting ALB
- use `.alb seqsync normal`, `.alb seqsync throttled`, `.alb seqsync horizon`,
  or `.alb seqsync suspend` when you want to change the live runtime mode
  immediately without editing config
- use `.alb seqsync status` to inspect the current live mode and queue picture

## Backend Peer Health

`backendPeerHealth` controls passive backend peer update-cycle health
monitoring.

This is an advanced diagnostic block. It does not change FMR ownership or
canonical seqsync rules. It helps the FMR detect when a peer appears slow to
apply shared backend updates and surfaces backend-only warn or recover events.

Example:

```json
"backendPeerHealth": {
  "enabled": true,
  "warnAverageMs": 333,
  "recoverAverageMs": 200,
  "windowSec": 30,
  "consecutiveWarnWindows": 3,
  "consecutiveRecoverWindows": 2,
  "repeatWarnCooldownSec": 60
}
```

| Property | Type | Default | Accepted range | Description |
|---|---:|---:|---:|---|
| `enabled` | bool | `true` | n/a | Enables passive backend peer health monitoring. |
| `warnAverageMs` | int | `333` | `100-5000` | Average cycle threshold above which ALB starts treating a peer cycle window as slow. |
| `recoverAverageMs` | int | `200` | `50-warnAverageMs` | Average cycle threshold below which ALB may treat the peer as recovered. |
| `windowSec` | int | `30` | `10-300` | Measurement window length in seconds. |
| `consecutiveWarnWindows` | int | `3` | `1-10` | Number of bad windows required before warning. |
| `consecutiveRecoverWindows` | int | `2` | `1-10` | Number of good windows required before recovery. |
| `repeatWarnCooldownSec` | int | `60` | `30-600` | Cooldown before repeat warnings may be sent again. |

Operational notes:

- these warnings are passive monitoring, not transport control
- warnings are backend-only and intended to help the FMR notice slow peer update cycles
- `.alb seqsync status` can show peer-health summary fields when this monitoring is enabled

## `glEatCombiDisplay`

This block controls what the `glEatCombi` field shows depending on arrival
state, hold state, and recent passage timing.

Example:

```json
"glEatCombiDisplay": {
  "unknownState": "SwitchGainLooseEat",
  "unsequencedState": "SwitchGainLooseEat",
  "sequenced1State": "GainLoose",
  "sequenced2State": "Eat",
  "frozen1State": "Countdown",
  "frozen2State": "Countdown",
  "switchPeriodSec": 2,
  "gainLooseEmptyText": "",
  "holdXSec": 120,
  "holdGtXState": "Eat",
  "holdLeXState": "Countdown",
  "afterPassYMinutes": 2,
  "afterPassFirstYState": "DeltaAtPassage",
  "afterPassAfterYState": "Blank"
}
```

Accepted state values are case-insensitive and normalized. Common values:

- `GainLoose` or `GL`
- `Eat`
- `Countdown` or `CD`
- `DeltaAtPassage` or `Delta`
- `SwitchGainLooseEat`
- `Blank`

| Property | Type | Description |
|---|---:|---|
| `unknownState` | string | Display when arrival state is unknown. |
| `unsequencedState` | string | Display when aircraft is unsequenced. |
| `sequenced1State` | string | Display for sequenced state 1. |
| `sequenced2State` | string | Display for sequenced state 2. |
| `frozen1State` | string | Display for frozen state 1. |
| `frozen2State` | string | Display for frozen state 2. |
| `switchPeriodSec` | int | Cycling period for `SwitchGainLooseEat`. Values below 1 are clamped to 1. |
| `gainLooseEmptyText` | string | Optional trimmed placeholder used only when `GainLoose` is the selected display state but the actual Gain/Lose value is empty. |
| `holdXSec` | int | Hold threshold in seconds. Values below 0 are clamped to 0. |
| `holdGtXState` | string | Display while in hold and remaining time is greater than `X`. |
| `holdLeXState` | string | Display while in hold and remaining time is less than or equal to `X`. |
| `afterPassYMinutes` | int | After-passage window length in minutes. Values below 0 are clamped to 0. |
| `afterPassFirstYState` | string | Display in the first `Y` minutes after passage. |
| `afterPassAfterYState` | string | Display after the first `Y` minutes after passage. |

Operational notes:

- `gainLooseEmptyText` is optional and is only used when `glEatCombi` is in a
  Gain/Lose-based state and the real Gain/Lose text is empty
- if `gainLooseEmptyText` is blank or omitted, ALB keeps the older behavior and
  simply shows nothing in that case
- this placeholder does not replace normal EAT, PLT, countdown, or
  post-passage behavior
- `holdXSec` and `holdGtXState` seed the local runtime hold-display control on
  startup and on a successful config reload
- the top-row hold-display button then lets each client adjust that threshold
  and pre-threshold hold display locally at runtime
- the normal hold-display button cycle is `Eat` -> `GainLoose` ->
  `SwitchGainLooseEat` -> `Blank` -> back to `Eat`
- `holdLeXState` is the inside-threshold display and is normally kept as
  `Countdown`
- unsupported `holdGtXState` values are normalized to `Blank`

The hold-display button uses compact labels such as:

- `2 EAT`
- `2 GL`
- `2 SW`
- `2 ---`

The number is the minutes before EAT where ALB switches to countdown. The
suffix shows what `glEatCombi` displays while the aircraft is still farther out
than that threshold.

## Timelines

`timelines` maps a timeline id to a timeline definition.

| Property | Type | Default | Description |
|---|---:|---:|---|
| `targetFixes` | string[] | required | Aircraft expected to pass one of these fixes are shown in the timeline. Exactly two fixes create a dual timeline unless `forceSingleTimeline` is `true`. |
| `tagLayout` | string | none | Tag layout id to use. Must exist in `tagLayouts`. |
| `viaFixes` | string[] | `[]` | Optional via-fix lanes. The placeholder `"-----"` can be used as a visual separator. |
| `ArrivalScenarios` | object | `{}` | Optional legacy scenario-name to integer-array mapping. Retained for compatibility/history; not recommended as the normal operating method. Arrays must match the number of non-placeholder via-fixes. |
| `destinationAirports` | string[] | `[]` | Optional destination ICAO filter. If non-empty, only matching destinations are included. Active-timeline destination coverage also controls whether `glEatCombi` can appear for that traffic. |
| `runways` | string[] | `[]` | Optional runways relevant for this timeline. |
| `defaultTimeSpan` | uint | `30` | Initial visible timeline span in minutes. |
| `forceSingleTimeline` | bool | `false` | Forces a single timeline even when two target fixes are configured. |
| `viaFixColors` | string[] | palette | Optional custom RGB colors for real via-fix lanes. |

### Notes on `ArrivalScenarios`

- Operational note: `ArrivalScenarios` is retained for compatibility and history. Scenarios are no longer part of the recommended normal ALB workflow. Current operation should normally use `EAT:LT` landing-timeline monitoring and correction instead of scenario switching.
- The loader reads the key exactly named `ArrivalScenarios`
- alternate scenario blocks under other names are ignored
- placeholder via-fixes such as `"-----"` do not consume scenario values
- JSON order is preserved for scenario display menus

## Tag Layouts

`tagLayouts` maps a layout id to a layout definition.

Two formats are supported:

1. legacy array format
2. object format with metadata plus `items`

Recommended format:

```json
"Maestro-Feeder": {
  "hideDeselectedViaFixes": true,
  "timelineLayout": "Feeder",
  "timelineSort": "targetFixEta",
  "items": [
    { "source": "callsign", "width": 8 }
  ]
}
```

### Layout properties

| Property | Type | Default | Description |
|---|---:|---:|---|
| `hideDeselectedViaFixes` | bool | `false` | If `true`, aircraft in deselected via-fix lanes are hidden from the timeline. |
| `timelineLayout` | string | runtime default | Layout hint. Accepted values include `runway` and `feeder`. |
| `timelineSort` | string | runtime default | Optional per-layout sort mode. |
| `items` | array | required | Ordered list of tag items. |

## Tag Item Properties

| Property | Type | Default | Description |
|---|---:|---:|---|
| `source` | string | `""` | Source field to render. |
| `width` | uint | `1` | Reserved character width. |
| `rightAligned` | bool | `false` | Right-aligns the rendered value. |
| `isViaFixIndicator` | bool | `false` | Colors the field using the via-fix lane color. |
| `defaultValue` | string | `""` | Fallback/default value, especially for `static`. |
| `format` | string | `""` | Optional time formatting override for time-backed sources. |

### `format` strings for time values

Supported `format` values are:

| Format | Width | Meaning |
|---|---:|---|
| `HHMM` | 4 | UTC time as `2359` |
| `HH:MM` | 5 | UTC time as `23:59` |
| `HHMMSS` | 6 | UTC time as `235959` |
| `HH:MM:SS` | 8 | UTC time as `23:59:59` |
| `HHMMx` | 5 | `HHMM` plus optional EAT precision suffix |
| `HH:MMx` | 6 | `HH:MM` plus optional EAT precision suffix |

If a recognized `format` is present, ALB infers the effective field width from
the format, so the explicit `width` value is no longer the controlling width.

### Common time-capable sources for `format`

These sources can use a `format` string:

- `estimatedLandingTime`
- `estimatedLandingTimeEs`
- `estimatedLandingTimeSso`
- `estimatedLandingTimeAlb`
- `plannedLandingTime`
- `plannedLandingTimeEs`
- `plannedLandingTimeSso`
- `plannedLandingTimeAlb`
- `underlyingEAT`
- `underlyingEAThms`
- `underlyingEATEs`
- `underlyingEATAlb`
- `viaFixETO`
- `viaFixETOhms`
- `timelineAnchor`
- `timelineAnchorHms`

## Tag Item Sources

Below are the common operational and advanced layout sources used by the
current runtime.

| Source | Meaning |
|---|---|
| `static` | Static text from `defaultValue`. |
| `callsign` | Aircraft callsign. |
| `ATCShort` | Tracking controller short id. |
| `ATCLong` | Tracking controller long id. |
| `assignedRunway` | Assigned landing runway. |
| `assignedRunwayOverride` | Scratchpad-based runway override. |
| `assignedStar` | Assigned STAR. |
| `aircraftType` | ICAO aircraft type. |
| `aircraftWtc` | Wake turbulence category. |
| `minutesBehindPreceedingRounded` | Spacing behind the preceding aircraft in rounded minutes. |
| `timeBehindPreceeding` | Spacing behind the preceding aircraft in `mm:ss`. |
| `remainingDistance` | Remaining distance to target fix in NM. |
| `remainingDistance1d` | Remaining distance with one decimal place. |
| `distanceGapNmToPrevious` | NM spacing/gap to the previous aircraft. |
| `viaFixDistNmToGo` | Remaining NM to the via-fix. |
| `viaACToViaFixSec` | Seconds from aircraft to via-fix. |
| `viaFixToLandingSec` | Planned seconds from via-fix to landing. |
| `viaFixToLandingSecEs` | ES-based via-fix-to-landing seconds. |
| `viaFixToLandingSecSso` | SSO/ALB-based via-fix-to-landing seconds. |
| `estimatedLandingTime` | Active estimated landing time. |
| `estimatedLandingTimeEs` | ES landing ETA branch. |
| `estimatedLandingTimeSso` / `estimatedLandingTimeAlb` | SSO/ALB landing ETA branch. |
| `plannedLandingTime` | Active planned landing time. |
| `plannedLandingTimeEs` | ES planned landing time. |
| `plannedLandingTimeSso` / `plannedLandingTimeAlb` | SSO/ALB planned landing time. |
| `underlyingEAT` | Active underlying EAT display value. |
| `underlyingEAThms` | Underlying EAT with seconds. |
| `underlyingEATEs` | ES-side underlying EAT branch. |
| `underlyingEATAlb` / `underlyingEATSso` | SSO/ALB-side underlying EAT branch. |
| `glEatCombi` | Combined gain/lose and EAT field. For radar-tag/Combi use, ALB now leaves it blank unless the aircraft destination belongs to a currently active timeline. |
| `viaFixLooseGain` | Via-fix gain/lose display. |
| `landingLooseGain` | Landing/threshold-based gain/lose display. |
| `viaFix` | Via-fix lane. |
| `viaFixETE` | Estimated time enroute to via-fix. |
| `viaFixETO` | Estimated time over via-fix. |
| `viaFixETOhms` | Estimated time over via-fix with seconds. |
| `viaFixSeqIndexCurrent` | Current per-via-fix sequence index. |
| `viaFixSeqIndexFixed` | Fixed per-via-fix sequence index. |
| `landingSeqIndexCurrent` | Current landing sequence index. |
| `landingSeqIndexFixed` | Fixed landing sequence index. |
| `targetFix` | Active target fix for the aircraft. |
| `targetFixEta` | Estimated time over target fix. |
| `targetFixATCTime` | Estimated time over target fix including delay logic. |
| `timelineAnchor` / `timelineAnchorHms` | Active timeline anchor time used for ordering/display. |
| `timelineAnchorSource` | Source of the timeline anchor. |
| `etaSource` | Current ETA branch/source identifier. |
| `holdingFix` | Holding fix if available. |
| `holdingEAT` | Hold-related EAT text. |
| `holdingEATm` | Manual-hold-EAT marker. |
| `holdPhase` | Holding phase indicator. |
| `FlightPhase` | Determined flight phase. |
| `ArrivalState` | Determined arrival state. |
| `inboundGrouping` | Current inbound grouping bucket. |
| `arrivalSeqNo` | Raw arrival sequence number. |
| `arrivalSeqMin` | Minimum airborne arrival sequence number. |
| `arrivalSeqMax` | Maximum assigned arrival sequence number. |
| `arrivalSeqDisp` | Display sequence number. |
| `arrivalSeqFrontier` | Sequencing frontier marker. |
| `obsNote3` | 3-character observation/note abbreviation. |
| `bookingStatus` | Backend booking/event-slot status text. |
| `acno` | Internal aircraft ordinal number. |
| `isPastViaFix` | `Y`/`N` flag for via-fix passage. |
| `groundSpeed` | Ground speed. |
| `groundSpeed10` | Ground speed in tens. |
| `altitude` | Altitude/flight level display. |
| `transitionAltitude` | Transition altitude. |
| `scratchPad` | Scratchpad text. |
| `directRouting` | Direct-to or next-route-fix display. Explicit Direct-To takes precedence; otherwise the fallback shows the aircraft's current semantic next route fix. |
| `cadFinalAlt` | Controller-assigned final altitude. |
| `cadTempAlt` | Controller-assigned temporary altitude. Special values `1` and `2` display as `cINS` and `cVIS`. |
| `cadAssignedHeading` | Controller-assigned heading. |
| `cadAssignedSpeedMach` | Controller-assigned speed (`K...`) or Mach (`M...`). |
| `cadAssignedRate` | Controller-assigned rate. |
| `cadGroundState` | Controller-assigned ground state. |
| `cadClearanceFlag` | Clearance flag (`CLR`). |
| `cadScratchpad` | CAD scratchpad mirror. |

If a source string is unknown to the runtime, it typically renders blank.

## TMA Areas

`tmaAreas` is an array of polygon definitions used for TMA matching.

| Property | Type | Description |
|---|---:|---|
| `id` | string | Optional human-readable identifier. |
| `airport` | string | v3 single-airport form. |
| `airports` | string[] | Alternate multi-airport form. |
| `runways` | string[] | Runways this polygon applies to. |
| `poly` | `{lat,lon}[]` | Required polygon points. |
| `center` | object | Legacy fallback circle form. |

Matching summary:

- the timeline airport comes from `destinationAirports[0]`
- the first matching area with airport and runway overlap is used
- if no polygon matches, v3 config can fall back to `airports.<ICAO>.tmaFallback`

## Airports

`airports` maps airport ICAO to airport-specific metadata.

### Common airport properties

| Property | Type | Description |
|---|---:|---|
| `tmaFallback` | object | Optional fallback circle using `center` plus `radiusNm`. |
| `runways` | object | Runway metadata keyed by runway id, e.g. `"04L": {"elevFt": 17}`. |

### `tmaFallback`

Example:

```json
"tmaFallback": {
  "center": { "lat": 55.64336491, "lon": 12.24763954 },
  "radiusNm": 29
}
```

### `runways`

Example:

```json
"runways": {
  "04L": { "elevFt": 17 },
  "04R": { "elevFt": 17 }
}
```

### Advanced airport options

These are used by the current runtime and are valid advanced config.

#### `eat_lt_spacing`

Airport-specific WTC-aware landing-spacing and Expected LR settings for
`EAT:LT`.

This block is optional. If it is missing, ALB uses complete built-in defaults.
Existing configs therefore remain valid, and no config-format version increase
was required for this feature.

Example:

```json
"eat_lt_spacing": {
  "unknownCategory": "M",
  "expectedRateWindowMinutes": 30,
  "behindSec": {
    "L": { "L": 30,  "M": 30,  "H": 30,  "J": 30 },
    "M": { "L": 60,  "M": 60,  "H": 60,  "J": 60 },
    "H": { "L": 120, "M": 120, "H": 120, "J": 120 },
    "J": { "L": 180, "M": 180, "H": 180, "J": 180 }
  },
  "inFrontSec": {
    "L": { "L": 120, "M": 120, "H": 120, "J": 120 },
    "M": { "L": 60,  "M": 60,  "H": 60,  "J": 60 },
    "H": { "L": 60,  "M": 60,  "H": 60,  "J": 60 },
    "J": { "L": 60,  "M": 60,  "H": 60,  "J": 60 }
  }
}
```

| Property | Type | Default | Accepted values or range | Description |
|---|---:|---:|---|---|
| `unknownCategory` | string | `M` | `L`, `M`, `H`, `J` | Fallback WTC category used when an aircraft WTC is missing, blank, or unrecognized. |
| `expectedRateWindowMinutes` | int | `30` | `5-120` | Future canonical-PLT window used for the Expected LR forecast. |
| `behindSec` | object | built-in matrix | complete `L/M/H/J` matrix of integer seconds | Minimum spacing behind the leader. |
| `inFrontSec` | object | built-in matrix | complete `L/M/H/J` matrix of integer seconds | Minimum space required in front of the follower. |

All matrix values are seconds.

Valid WTC categories are:

- `L`
- `M`
- `H`
- `J`

Matrix orientation matters:

- `behindSec[row leader][column follower]`
- `inFrontSec[row follower][column leader]`

Built-in default matrices:

### Default `behindSec`

| Leader \ follower | L | M | H | J |
|---|---:|---:|---:|---:|
| L | 30 | 30 | 30 | 30 |
| M | 60 | 60 | 60 | 60 |
| H | 120 | 120 | 120 | 120 |
| J | 180 | 180 | 180 | 180 |

### Default `inFrontSec`

| Follower \ leader | L | M | H | J |
|---|---:|---:|---:|---:|
| L | 120 | 120 | 120 | 120 |
| M | 60 | 60 | 60 | 60 |
| H | 60 | 60 | 60 | 60 |
| J | 60 | 60 | 60 | 60 |

Validation behavior:

- `unknownCategory` must be a valid WTC category or the airport keeps the
  built-in default fallback of `M`
- `expectedRateWindowMinutes` must be an integer from `5` through `120`
- each matrix must be a complete `L/M/H/J` object-of-objects
- each matrix value must be a finite non-negative integer number of seconds
- malformed or incomplete matrix definitions are rejected as a whole rather
  than partly applied

Operational notes:

- config overrides are optional
- a complete valid airport matrix can override the built-in defaults
- unknown WTC uses the configured airport fallback category
- Expected LR remains a local read-only forecast based on canonical future PLTs

#### `viafix_track_nm_orange`

Maps STAR name to configured track miles for ALB's orange timing model.

Orange timing is ALB's route or STAR-based timing estimate from the via-fix or holding-fix area toward touchdown. The value gives ALB a configured distance basis when it builds the corrected ALB estimate branch (`ELT-ALB` or `ETA:ALB`) before terminal or post-via live estimates become the better source.

This is advanced airport tuning. Wrong values can make `ELT-ALB`, EAT planning, and landing-timeline scheduling too early or too late.

Example:

```json
"viafix_track_nm_orange": {
  "TESPI4C": 84.8,
  "TUDLO4C": 93.0
}
```

Rules:

- values must be numeric
- values must be greater than `0`
- values must be less than `500`

How ALB uses these values:

- ALB first tries an exact STAR-name match for the aircraft's assigned or
  persisted STAR
- if there is no exact STAR match, ALB may fall back by via-fix and use the
  largest configured orange distance among STARs that map to that same via-fix
- the configured value represents track miles from the via-fix or holding-area
  side of the arrival toward touchdown

### Orange timing model behind `ELT-ALB`

Before the aircraft is deep into terminal or post-via handling, ALB can build
its own landing estimate branch from:

1. the current via-fix timing anchor or EVTO
2. the configured orange distance
3. a descent-speed model that converts track miles into seconds

The generic baseline model splits the orange distance into three buckets:

- final `10 NM`
- the preceding `20 NM` TMA segment
- any remaining higher-distance segment before that

The baseline bucket assumptions are roughly:

- higher segment: about `250 KIAS` near `FL100`
- TMA segment: about `180 KIAS`
- final `10 NM`: about `145 KIAS`

ALB then converts those IAS assumptions into approximate TAS and time. In the
generic fallback model that is roughly:

- `250 KIAS` -> about `290 KTAS`
- `180 KIAS` -> about `185 KTAS`
- `145 KIAS` -> about `149 KTAS`

### Aircraft-aware refinement, wind, and clamping

When aircraft performance data is available, ALB can refine the orange timing
using the flight plan's descent-speed information instead of only the generic
jet baseline.

The current aircraft-aware model generally uses:

- a higher descent speed around the aircraft's filed level
- a lower descent speed around `6000 ft`
- a final descent speed around `3000 ft`
- wake-category-aware bounds so the final segment does not become unrealistic

When upper-wind data is available, ALB also lets that wind influence the orange
timing by adjusting the approximate ground speed along the route.

To avoid an unrealistically optimistic ALB branch, the orange result is also
bounded against the live ES branch. In practice that means `ELT-ALB` is not
allowed to run too far ahead of the live EuroScope-based landing estimate.

### Practical tuning guidance

- treat `viafix_track_nm_orange` as airport-specific planning calibration, not
  as a cosmetic display tweak
- if the value is too short, `ELT-ALB` and ALB-based landing planning become
  too early
- if the value is too long, `ELT-ALB` and ALB-based landing planning become too
  late
- once the aircraft is terminal or post-via, ALB should stop relying on orange
  distance timing and follow the live ES branch where appropriate

For a more operational explanation, see
[ICAO / Airport Setups](./icao-setups.md). For the behavior boundary between
`ELT-ES` and `ELT-ALB`, see
[Sequencing Model](../technical/sequencing-model.md).

#### `eat_lt_sequence_lock`

Airport-specific lock threshold tuning for EAT/LT sequence behavior.

Example:

```json
"eat_lt_sequence_lock": {
  "enabled": true,
  "baseDistanceNm": 115.0,
  "extraDistanceNm": 10.0,
  "extraBufferIasKt": 280.0,
  "extraBufferAltFt": 15000.0
}
```

| Property | Type | Description |
|---|---:|---|
| `enabled` | bool | Enables this advanced airport-specific threshold. |
| `baseDistanceNm` | number | Base distance used in threshold derivation. |
| `extraDistanceNm` | number | Additional buffer distance. |
| `extraBufferIasKt` | number | IAS used to estimate the extra buffer conversion to time. |
| `extraBufferAltFt` | number | Altitude used with IAS for buffer time estimation. |

## Common Ignored or Legacy Keys

Unknown keys are ignored by the current loader.

In real configs you may commonly see keys like these:

- `timelineSort-off`
- `glEatCombiDisplay-off`
- `viaFixColors-safe`
- `aimingPointFt`
- `ArrivalScenarios-ssoBackup`
- `ArrivalScenarios-ssoBackup2`

Important:

- only the exact key `glEatCombiDisplay` is read
- only the exact key `timelineSort` is read
- only the exact key `ArrivalScenarios` is read
- `viaFixColors-safe` and `aimingPointFt` are not read by the current loader
