# Config File description
This document describes the JSON configuration file used by the ALB plugin.
A sample file can be found here: [alb-config.json](./config-file-sample.md).

Note: the plugin uses the EuroScope font which is based on the [ANSI/Windows-1252 character encoding](http://www.alanwood.net/demos/ansi.html). If you want to use special symbols like ¶, ¤, •, |, ©, ®, ¬, ‡ or º in your "static" tag fields you must save the JSON-file using that encoding (select "ANSI" encoding in notepad). Also note that some of these symbols have a custom representation in the EuroScope font - like ¶ which is displayed as a telephone, and ¤ which is a filled rectangle.

## Disabling parts of the config
When loading `timelines` and `tagLayouts`, any **ID/key** ending with `-off` or `_off` (case-insensitive) is **skipped**.
- Example: `"EKCH 04L/04R-SI_off"` or `"Expanded-off"`.

## Global options (top-level)
| Property | Type | Default | Description |
|---|---:|---:|---|
| `configFormatVersion` | int | 1 | Config schema version. The DLL validates this against its supported version range and rejects unsupported versions. |
| `openAutomatically` | bool | `true` | If true, the ALB window is opened when the config is loaded. |
| `logging` | bool | `false` | Enables ALB logging and (re)opens the log file at config load. |
| `debugging` | bool | (unchanged) | If present, overrides the current debug mode. If missing, the existing debug mode remains. |
| `experimental` | bool | `false` | Enables experimental UI features. |
| `scratchpadRunwayOverride` | bool | `false` | Enables parsing of runway override from scratchpad (used by `assignedRunwayOverride` tag source). |
| `timeSyncBeacons` | bool | `false` | Enables “time-sync beacons” support. |
| `timelineSort` | string | `targetFixEta` | Default timeline sort mode. Accepted values: `targetFixEta` (aliases: `target`, `tfe`) and `landingEta` (aliases: `landing`, `lde`, `lnd`). |
| `holdEatLockWindowMinutes` | int | (internal default) | Lock window for HOLD/EAT logic (minutes → seconds internally). Only applied if > 0. |
| `holdEatRecentChangeMinutes` | int | (internal default) | “Recent change” window for HOLD/EAT logic (minutes → seconds internally). Only applied if > 0. |
| `aircraftLastSeenSweepMinutes` | int | (internal default) | How often to sweep persisted aircraft state for expiry (minutes → seconds internally). Only applied if > 0. |
| `aircraftOfflineResetMinutes` | int | (internal default) | Offline reset threshold for persisted aircraft state / sequencing (minutes → seconds internally). Only applied if > 0. |
| `glEatCombiDisplay` | object | (internal defaults) | Optional configuration for the combined **G/L / EAT** tag field (`glEatCombi`). See below. |

### `glEatCombiDisplay` block
This block controls what the `glEatCombi` tag field shows depending on arrival state / hold / passage timing.

**State strings** are case-insensitive and normalized (non-alphanumerics removed). Accepted values:
- `GainLoose` (or `GL`)
- `Eat`
- `Countdown` (or `CD`)
- `DeltaAtPassage` (or `Delta`)
- `SwitchGainLooseEat` (aliases: `SwitchGLEat`, `Switch`, `SwitchGLEAT` etc.)
- `Blank` (aliases: `Empty`, `None`)

| Property | Type | Description |
|---|---:|---|
| `unknownState` | string | What to show when arrival state is unknown. |
| `unsequencedState` | string | What to show when not sequenced. |
| `sequenced1State` | string | What to show for sequenced state #1. |
| `sequenced2State` | string | What to show for sequenced state #2. |
| `frozen1State` | string | What to show for frozen state #1. |
| `frozen2State` | string | What to show for frozen state #2. |
| `switchPeriodSec` | int | Period (seconds) for the switch-state cycling. Values < 1 are clamped to 1. |
| `holdXSec` | int | HOLD override threshold in seconds. Values < 0 are clamped to 0. |
| `holdGtXState` | string | When in HOLD and remaining > X seconds, show this state. |
| `holdLeXState` | string | When in HOLD and remaining ≤ X seconds, show this state. |
| `afterPassYMinutes` | int | After-passage window length in minutes. Values < 0 are clamped to 0. |
| `afterPassFirstYState` | string | For the first Y minutes after passage, show this state. |
| `afterPassAfterYState` | string | After the first Y minutes, show this state. |

## Timelines
`timelines` is an object mapping **timeline id → timeline config**.

| Property | Type | Default | Description |
|---|---:|---:|---|
| `targetFixes` | string[] | (required) | Any aircraft expected to pass one of these fixes is shown in the timeline. If exactly two fixes are specified, a dual timeline is shown with the first fix on the left side and the second on the right side (unless `forceSingleTimeline` is true). |
| `tagLayout` | string | (none) | Tag layout id to use (must exist in `tagLayouts`). |
| `viaFixes` | string[] | `[]` | (optional) Defines “lanes” / via-fix classification. You may use the literal placeholder `"-----"` to visually separate lanes; placeholders are ignored for color/scenario sizing logic. |
| `ArrivalScenarios` | object | `{}` | (optional) Scenario name → array of ints. **Important:** the array must be sized to the number of **non-placeholder** viaFixes (i.e. entries where viaFix != `"-----"`). The loader expands it to match `viaFixes` and inserts 0 for placeholders. |
| `destinationAirports` | string[] | `[]` | (optional) If non-empty, aircraft are included only if their destination matches one of these ICAOs. Also used to pick the airport used for TMA matching (first element). |
| `runways` | string[] | `[]` | (v3) (optional) Runways relevant for this timeline. Used when matching a `tmaAreas` polygon by runway. |
| `runwayElevFt` | object | (legacy) | (v2 legacy) Used only for v2 configs to derive runway ids (keys of the object). In v3 you should use `runways` + `airports.<ICAO>.runways`. |
| `defaultTimeSpan` | uint | `30` | (optional) Initial zoom level (minutes) when the timeline is loaded. |
| `forceSingleTimeline` | bool | `false` | If true, forces a single timeline even when `targetFixes` has two fixes. |
| `viaFixColors` | string[] | (palette) | (optional) Custom colors for via-fix lanes. Each entry is a 6-hex RGB string like `"00FF7F"`. Applied only to **real** via-fixes (placeholders skipped). Missing/malformed entries fall back to the built-in color palette. |

### Notes on `ArrivalScenarios`
- The loader only reads the key **exactly** named `ArrivalScenarios`.
- If you keep alternate scenario sets in the JSON (e.g. `ArrivalScenarios-backup`), they are ignored by the current loader.

## Tag layouts
`tagLayouts` is an object mapping **layout id → layout definition**.

Two formats are supported:
1. **Legacy format (array):**
   ```json
   "Standard": [ {"source":"callsign","width":8}, ... ]
   ```
2. **Object format (recommended):**
   ```json
   "Standard": {
     "hideDeselectedViaFixes": true,
     "timelineLayout": "Runway",     // or "Feeder"
     "timelineSort": "targetFixEta", // optional
     "items": [ {"source":"callsign","width":8}, ... ]
   }
   ```

### Tag layout properties (object format)
| Property | Type | Default | Description |
|---|---:|---:|---|
| `hideDeselectedViaFixes` | bool | `false` | If true, aircraft whose via-fix lane is deselected/hidden are removed from the timeline. |
| `timelineLayout` | string | (plugin default) | Optional: layout type hint. Accepted values (case-insensitive): `runway` (aliases: `rwy`, `runwayLayout`, `lrw`) and `feeder` (aliases: `feed`, `feederLayout`, `lfd`). |
| `timelineSort` | string | (plugin default) | Optional per-layout sort mode (same accepted values as global `timelineSort`). |
| `items` | TagItem[] | (required) | The list of tag items. |

### Tag item properties
| Property | Type | Default | Description |
|---|---:|---:|---|
| `source` | string | `""` | Where to get the value from. |
| `width` | uint | `1` | Reserved character width; longer values are truncated. |
| `rightAligned` | bool | `false` | Right-align the value within its width. |
| `isViaFixIndicator` | bool | `false` | If true, the value will be colored based on the via-fix lane. |
| `defaultValue` | string | `""` | Used for `source: "static"` (and also for sources that may be blank). |

### Tag item sources
The plugin accepts a wide set of `source` strings. The list below includes the **original documented sources** plus the **additional ones present in the current sample config**.

| Name | ID | Description |
|---|---:|---|
| `static` | `` | A static text, specified in `defaultValue`. |
| `callsign` | `ACID` | Aircraft callsign. |
| `ATCShort` | `ATC` | Tracking Controller ID - short. |
| `ATCLong` | `ATC` | Tracking Controller ID - long. |
| `assignedRunway` | `RWY` | Assigned landing runway. |
| `assignedRunwayOverride` | `RWY*` | Assigned landing runway with scratchpad override (requires `scratchpadRunwayOverride`). |
| `assignedStar` | `STAR` | Assigned STAR. |
| `aircraftType` | `TYPE` | ICAO aircraft code. |
| `aircraftWtc` | `WTC` | Wake turbulence category (L/M/H/S). |
| `minutesBehindPreceedingRounded` | `TdM` | Time behind preceding aircraft at target fix (rounded minutes). |
| `timeBehindPreceeding` | `TdT` | Time behind preceding aircraft at target fix (mm:ss). |
| `remainingDistance` | `NM` | Distance to target fix (nautical miles). |
| `estimatedLandingTime` | `ELT` | Estimated landing time (hh:mm). |
| `targetFixEta` | `(ETF)` | Estimated time over target fix (hh:mm). |
| `targetFixATCTime` | `[ETF]` | Estimated time over via-fix + delay (hh:mm). |
| `directRouting` | `DIR` | Direct routing (if any) given by ATC. |
| `groundSpeed` | `GS` | Calculated ground speed. |
| `groundSpeed10` | `GS/10` | Calculated ground speed (in tens). |
| `altitude` | `ALT` | Altitude (pressure altitude or FL). |
| `scratchPad` | `SCR` | Scratch pad value. |
| `viaFix` | `VIA` | Via-fix lane. |
| `viaFixETE` | `EVTE` | Estimated time enroute to via-fix (hh:mm). |
| `viaFixETO` | `EVTO` | Estimated time over via-fix (hh:mm). |
| `viaFixLooseGain` | `G/L` | Calculated Gain / Loose (minutes). |
| `inboundGrouping` | `GROUP` | Grouping within which the aircraft currently falls. |
| `FlightPhase` | `FPh` | Determined Flight Phase. |
| `ArrivalState` | `ASt` | Determined Arrival State. |
| `holdingFix` | `HOLD` | Holding fix (if available). |
| `holdingEAT` | `EAT` | Holding Estimated Release (arrival) time (hh:mm) if set. |
| `targetFix` | `TFIX` | Which target fix the aircraft is currently associated with (useful when multiple target fixes exist). |
| `arrivalSeqNo` | `SEQ` | Raw arrival sequencing number. |
| `arrivalSeqMin` | `Smin` | Minimum airborne sequence number (airport-wide). |
| `arrivalSeqMax` | `Smax` | Maximum sequence number assigned so far (airport-wide). |
| `arrivalSeqDisp` | `Sdisp` | Display sequence position (relative to min airborne). |
| `arrivalSeqFrontier` | `Sfr` | Sequencing frontier marker (internal). |
| `glEatCombi` | `GL/EAT` | Combined Gain/Loose + EAT field (behavior controlled by `glEatCombiDisplay`). |
| `holdPhase` | `HPh` | Holding phase indicator (entering/in-hold/exiting, etc.). |
| `underlyingEAT` | `uEAT` | Underlying EAT time (hh:mm), before display/lock logic. |
| `underlyingEAThms` | `uEATs` | Underlying EAT time (hh:mm:ss). |
| `viaFixETOhms` | `EVTOs` | Estimated time over via-fix (hh:mm:ss). |
| `viaFixDistNmToGo` | `VNM` | Distance (NM) from aircraft to via-fix. |
| `viaACToViaFixSec` | `Vsec` | Seconds from aircraft to via-fix (time-to-go). |
| `viaFixToLandingSec` | `Lsec` | Seconds from via-fix to landing (time-to-go). |
| `viaFixSeqIndexCurrent` | `Vidx` | Current via-fix sequence index (internal). |
| `viaFixSeqIndexFixed` | `VidxF` | Fixed via-fix sequence index (internal). |
| `acno` | `AC#` | Aircraft number/ordinal (internal). |

> Notes:
> - Some of the newer/"internal" sources (e.g. `acno`, `arrivalSeqFrontier`, `viaFixSeqIndex*`) are primarily for debugging/advanced layouts.
> - If a `source` string is unknown to the runtime, it will typically render blank.

## TMA areas and airports (configFormatVersion ≥ 3)
The plugin can determine whether an aircraft is “in the TMA” by matching a timeline to a `tmaAreas` polygon and/or an airport fallback circle.

### `tmaAreas`
`tmaAreas` is an array of polygon definitions.

| Property | Type | Description |
|---|---:|---|
| `id` | string | Optional identifier (not currently used by the loader, but useful for humans). |
| `airport` | string | (v3) Primary airport ICAO for this area. |
| `airports` | string[] | (v2/v3) Alternate form: list of airports. |
| `runways` | string[] | Runways this polygon applies to. If empty, airport match alone is enough. |
| `poly` | `{lat,lon}[]` | Polygon points (lat/lon numbers). **Required** for the area to be kept. |
| `center` | object | (legacy v2) `{lat,lon,radiusNm}` circle center+radius; in v3 this is generally replaced by `airports.<ICAO>.tmaFallback`. |

Matching logic (summary):
- The timeline airport is taken as `destinationAirports[0]`.
- The first `tmaAreas` entry whose airport matches and whose runways overlap the timeline’s `runways` (if specified) is used.
- In v3, if no polygon matches, the plugin can fall back to the airport’s `tmaFallback` circle (if configured).

### `airports` (v3)
`airports` is an object mapping **airport ICAO → airport config**.

| Property | Type | Description |
|---|---:|---|
| `tmaFallback` | object | Optional fallback circle: `{ "center": {"lat": <num>, "lon": <num>}, "radiusNm": <uint> }`. |
| `runways` | object | Runway metadata keyed by runway id: e.g. `"04L": {"elevFt": 17}`. |

## Deprecated / ignored keys (current loader)
The current loader ignores unknown keys. In the provided sample config, for example:
- `glEatCombiDisplay-off` is ignored because the loader only reads the exact key `glEatCombiDisplay`.
- Timeline keys like `aimingPointFt` and `viaFixColors-safe` are not read by the current loader.
- Alternate scenario sets such as `ArrivalScenarios-ssoBackup*` are ignored (only `ArrivalScenarios` is read).

