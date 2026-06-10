# Config File Description

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
| `openAutomatically` | bool | `true` | Opens the ALB window when the config is loaded. |
| `logging` | bool | `false` | Enables ALB runtime text logging. |
| `scratchpadRunwayOverride` | bool | `false` | Enables scratchpad runway override parsing for `assignedRunwayOverride`. |
| `timeSyncBeacons` | bool | `false` | Enables time-sync beacon support. |
| `timelineSort` | string | `targetFixEta` | Default timeline sort mode. Accepted values include `target` / `tfe` and `landing` / `lde` / `lnd`. |
| `holdEatLockWindowMinutes` | int | `5` | Hold/EAT lock window in minutes. Applied only if greater than 0. |
| `holdEatRecentChangeMinutes` | int | `2` | Recent-change window for hold/EAT logic in minutes. Applied only if greater than 0. |
| `aircraftLastSeenSweepMinutes` | int | `2` | How often ALB sweeps persisted aircraft state for expiry. Applied only if greater than 0. |
| `aircraftOfflineResetMinutes` | int | `5` | Threshold after which unseen aircraft have persisted sequencing/state cleared. Applied only if greater than 0. |
| `housekeeping` | object | defaults below | Startup housekeeping settings for ALB logs and backend recording folders. |
| `soundMute` | object | all `false` | Startup mute state for ALB sounds. |
| `layout` | object | defaults below | EAT display precision/suffix formatting settings. |
| `glEatCombiDisplay` | object | defaults below | Optional display mapping for the combined `glEatCombi` field. |
| `timelines` | object | required | Timeline definitions. |
| `tagLayouts` | object | required | Tag layout definitions. |
| `tmaAreas` | array | optional | TMA polygon definitions. |
| `airports` | object | optional | Airport-specific fallback circles, runway metadata, and advanced airport tuning. |

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
| `holdXSec` | int | Hold threshold in seconds. Values below 0 are clamped to 0. |
| `holdGtXState` | string | Display while in hold and remaining time is greater than `X`. |
| `holdLeXState` | string | Display while in hold and remaining time is less than or equal to `X`. |
| `afterPassYMinutes` | int | After-passage window length in minutes. Values below 0 are clamped to 0. |
| `afterPassFirstYState` | string | Display in the first `Y` minutes after passage. |
| `afterPassAfterYState` | string | Display after the first `Y` minutes after passage. |

## Timelines

`timelines` maps a timeline id to a timeline definition.

| Property | Type | Default | Description |
|---|---:|---:|---|
| `targetFixes` | string[] | required | Aircraft expected to pass one of these fixes are shown in the timeline. Exactly two fixes create a dual timeline unless `forceSingleTimeline` is `true`. |
| `tagLayout` | string | none | Tag layout id to use. Must exist in `tagLayouts`. |
| `viaFixes` | string[] | `[]` | Optional via-fix lanes. The placeholder `"-----"` can be used as a visual separator. |
| `ArrivalScenarios` | object | `{}` | Optional scenario-name to integer-array mapping. Arrays must match the number of non-placeholder via-fixes. |
| `destinationAirports` | string[] | `[]` | Optional destination ICAO filter. If non-empty, only matching destinations are included. |
| `runways` | string[] | `[]` | Optional runways relevant for this timeline. |
| `defaultTimeSpan` | uint | `30` | Initial visible timeline span in minutes. |
| `forceSingleTimeline` | bool | `false` | Forces a single timeline even when two target fixes are configured. |
| `viaFixColors` | string[] | palette | Optional custom RGB colors for real via-fix lanes. |

### Notes on `ArrivalScenarios`

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
| `glEatCombi` | Combined gain/lose and EAT field. |
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
| `directRouting` | Direct-to or next-route-fix display. |
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

#### `viafix_track_nm_orange`

Maps STAR name to track miles from holding-fix area to touchdown for the orange
timing model.

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
