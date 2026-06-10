# Config File Description

This document describes the public configuration surface for `alb-config.json`.

A sample file can be found here:
[alb-config.json](./config-file-sample.md)

`alb-config.json` is the live operational ALB config. It should live in the
same folder as the ALB plugin files.

Important:

- EuroScope loads `ALB.dll`
- `ALB.dll` loads `ALBCore.dll`
- the loader must not silently overwrite `alb-config.json`
- `alb-config.default.json` is the template/default file, not the live config

## Encoding note

The plugin uses the EuroScope font, which is based on
[ANSI/Windows-1252](http://www.alanwood.net/demos/ansi.html). If you use
special symbols in `static` tag fields, save the JSON file using ANSI encoding.

## Disabling parts of the config

When loading `timelines` and `tagLayouts`, any key ending with `-off` or `_off`
(case-insensitive) is skipped.

Example:

- `"EKCH 04L/04R-SI_off"`
- `"Expanded-off"`

## Top-level options

| Property | Type | Default | Description |
|---|---:|---:|---|
| `configFormatVersion` | int | 1 | Config schema version accepted by the runtime. |
| `openAutomatically` | bool | `true` | Opens the ALB window when the config is loaded. |
| `logging` | bool | `false` | Enables ALB runtime logging. |
| `scratchpadRunwayOverride` | bool | `false` | Enables runway override parsing from scratchpad for `assignedRunwayOverride`. |
| `timeSyncBeacons` | bool | `false` | Enables time-sync beacon support. |
| `timelineSort` | string | `targetFixEta` | Default sort mode. Accepted values include `target`/`tfe` and `landing`/`lde`/`lnd`. |
| `holdEatLockWindowMinutes` | int | runtime default | Lock window for hold/EAT behavior. Applied only if greater than 0. |
| `holdEatRecentChangeMinutes` | int | runtime default | Recent-change window for hold/EAT behavior. Applied only if greater than 0. |
| `aircraftLastSeenSweepMinutes` | int | runtime default | Sweep interval for persisted aircraft state expiry. Applied only if greater than 0. |
| `aircraftOfflineResetMinutes` | int | runtime default | Offline reset threshold for persisted aircraft/sequencing state. Applied only if greater than 0. |
| `glEatCombiDisplay` | object | runtime defaults | Optional configuration for the combined `glEatCombi` tag field. |

## `glEatCombiDisplay`

This block controls what the `glEatCombi` tag field shows depending on arrival
state, hold state, and recent passage timing.

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
| `unsequencedState` | string | Display when aircraft is not sequenced. |
| `sequenced1State` | string | Display for sequenced state 1. |
| `sequenced2State` | string | Display for sequenced state 2. |
| `frozen1State` | string | Display for frozen state 1. |
| `frozen2State` | string | Display for frozen state 2. |
| `switchPeriodSec` | int | Cycle period in seconds for switch-style display. |
| `holdXSec` | int | HOLD threshold in seconds. |
| `holdGtXState` | string | Display while in HOLD and remaining time is greater than `X`. |
| `holdLeXState` | string | Display while in HOLD and remaining time is less than or equal to `X`. |
| `afterPassYMinutes` | int | After-passage window length in minutes. |
| `afterPassFirstYState` | string | Display in the first `Y` minutes after passage. |
| `afterPassAfterYState` | string | Display after the first `Y` minutes after passage. |

## Timelines

`timelines` maps a timeline id to a timeline definition.

| Property | Type | Default | Description |
|---|---:|---:|---|
| `targetFixes` | string[] | required | Aircraft expected to pass one of these fixes are shown in the timeline. Exactly two fixes create a dual timeline unless `forceSingleTimeline` is true. |
| `tagLayout` | string | none | Tag layout id to use. Must exist in `tagLayouts`. |
| `viaFixes` | string[] | `[]` | Optional via-fix lanes. Use `"-----"` as a visual separator if needed. |
| `ArrivalScenarios` | object | `{}` | Optional scenario name to integer-array mapping. Arrays must match the number of non-placeholder via-fixes. |
| `destinationAirports` | string[] | `[]` | Optional destination ICAO filter. If non-empty, only matching destinations are included. |
| `runways` | string[] | `[]` | Optional runway ids relevant for this timeline. |
| `runwayElevFt` | object | legacy | Legacy v2 runway-elevation structure. In v3 prefer `runways` plus `airports.<ICAO>.runways`. |
| `defaultTimeSpan` | uint | `30` | Initial timeline span in minutes. |
| `forceSingleTimeline` | bool | `false` | Forces a single timeline even when exactly two target fixes are configured. |
| `viaFixColors` | string[] | palette | Optional custom RGB colors for real via-fix lanes. |

### Notes on `ArrivalScenarios`

- The loader reads the key exactly named `ArrivalScenarios`
- alternate scenario sets under different keys are ignored
- placeholder via-fixes such as `"-----"` do not consume scenario values

## Tag layouts

`tagLayouts` maps a layout id to a layout definition.

Two formats are supported:

1. Legacy array format
2. Object format with metadata plus `items`

Example object format:

```json
"Standard": {
  "hideDeselectedViaFixes": true,
  "timelineLayout": "Runway",
  "timelineSort": "targetFixEta",
  "items": [
    { "source": "callsign", "width": 8 }
  ]
}
```

### Tag layout properties

| Property | Type | Default | Description |
|---|---:|---:|---|
| `hideDeselectedViaFixes` | bool | `false` | Hides aircraft whose via-fix lane is deselected. |
| `timelineLayout` | string | runtime default | Layout hint. Accepted values include `runway` and `feeder`. |
| `timelineSort` | string | runtime default | Optional per-layout sort mode. |
| `items` | TagItem[] | required | The ordered list of tag items. |

### Tag item properties

| Property | Type | Default | Description |
|---|---:|---:|---|
| `source` | string | `""` | Source field to render. |
| `width` | uint | `1` | Reserved character width. |
| `rightAligned` | bool | `false` | Right-aligns the rendered value. |
| `isViaFixIndicator` | bool | `false` | Colors the field using the via-fix lane color. |
| `defaultValue` | string | `""` | Used for `static` and other blank-capable fields. |

### Common tag item sources

| Name | Meaning |
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
| `remainingDistance` | Distance to target fix in NM. |
| `estimatedLandingTime` | Estimated landing time. |
| `targetFixEta` | Estimated time over target fix. |
| `targetFixATCTime` | Estimated time over target fix with delay handling. |
| `directRouting` | Direct routing if any. |
| `groundSpeed` | Calculated ground speed. |
| `groundSpeed10` | Calculated ground speed in tens. |
| `altitude` | Altitude or flight level. |
| `scratchPad` | Scratchpad value. |
| `viaFix` | Via-fix lane. |
| `viaFixETE` | Estimated time enroute to via-fix. |
| `viaFixETO` | Estimated time over via-fix. |
| `viaFixLooseGain` | Gain/lose value. |
| `inboundGrouping` | Current inbound grouping. |
| `FlightPhase` | Determined flight phase. |
| `ArrivalState` | Determined arrival state. |
| `holdingFix` | Holding fix if available. |
| `holdingEAT` | Hold-related EAT if set. |
| `targetFix` | Active target fix for the aircraft. |
| `arrivalSeqNo` | Raw arrival sequence number. |
| `arrivalSeqMin` | Minimum airborne sequence number. |
| `arrivalSeqMax` | Maximum assigned sequence number. |
| `arrivalSeqDisp` | Display sequence position. |
| `arrivalSeqFrontier` | Internal sequencing frontier marker. |
| `glEatCombi` | Combined gain/lose and EAT field. |
| `holdPhase` | Holding phase indicator. |
| `underlyingEAT` | Underlying EAT time. |
| `underlyingEAThms` | Underlying EAT time with seconds. |
| `viaFixETOhms` | Via-fix overflight time with seconds. |
| `viaFixDistNmToGo` | NM remaining to via-fix. |
| `viaACToViaFixSec` | Seconds to via-fix. |
| `viaFixToLandingSec` | Seconds from via-fix to landing. |
| `viaFixSeqIndexCurrent` | Current via-fix sequence index. |
| `viaFixSeqIndexFixed` | Fixed via-fix sequence index. |
| `acno` | Internal aircraft ordinal. |

If a `source` string is unknown to the runtime, it typically renders blank.

## TMA areas and airports (`configFormatVersion >= 3`)

The plugin can determine whether an aircraft is in the TMA by matching the
timeline to a `tmaAreas` polygon and/or an airport fallback circle.

### `tmaAreas`

`tmaAreas` is an array of polygon definitions.

| Property | Type | Description |
|---|---:|---|
| `id` | string | Optional human-readable identifier. |
| `airport` | string | Primary airport ICAO for the area. |
| `airports` | string[] | Alternate multi-airport form. |
| `runways` | string[] | Runways the polygon applies to. |
| `poly` | `{lat,lon}[]` | Required polygon points. |
| `center` | object | Legacy v2 fallback circle structure. |

Matching summary:

- the timeline airport is taken from `destinationAirports[0]`
- the first matching area with airport and runway overlap is used
- if no polygon matches, v3 config can fall back to `airports.<ICAO>.tmaFallback`

### `airports`

`airports` maps airport ICAO to airport metadata.

| Property | Type | Description |
|---|---:|---|
| `tmaFallback` | object | Optional fallback circle using `center` plus `radiusNm`. |
| `runways` | object | Runway metadata keyed by runway id, e.g. `"04L": {"elevFt": 17}`. |

## Ignored or deprecated keys

Unknown keys are ignored by the current loader.

Examples:

- `glEatCombiDisplay-off` is ignored because the loader only reads `glEatCombiDisplay`
- timeline keys such as `aimingPointFt` and `viaFixColors-safe` are not read
- alternate scenario blocks such as `ArrivalScenarios-ssoBackup*` are ignored
