# Display Fields and Tag Layouts

Use this page when you want to change what each ALB row shows.

## Where displayed fields are defined

Displayed fields live in `tagLayouts`.

Each layout defines an ordered `items` array. Each item chooses one source field and how it should be rendered.

Example:

```json
"Maestro-Runway": {
  "timelineLayout": "Runway",
  "items": [
    { "source": "callsign", "width": 8 },
    { "source": "plannedLandingTime", "format": "HH:MMx" },
    { "source": "assignedRunway", "width": 4 }
  ]
}
```

## Common item properties

- `source`: which ALB field to show
- `width`: reserved character width when the field is width-based
- `rightAligned`: right-aligns the field
- `isViaFixIndicator`: colors the field using the via-fix lane color
- `defaultValue`: fallback or static text, especially for `static`
- `format`: time-format override for time-backed sources

## Time formatting

Recognized `format` values include:

- `HHMM`
- `HH:MM`
- `HHMMSS`
- `HH:MM:SS`
- `HHMMx`
- `HH:MMx`

`HHMMx` and `HH:MMx` use the configured EAT precision suffix behavior.

If a recognized `format` is present, ALB infers the field width from the format, so the explicit `width` value is no longer the controlling width.

For the full list of supported time-backed sources, see [Config File Reference](config-description.md).

## Global timing-display formatting

The top-level `layout` block controls how EAT-style times are formatted throughout the timeline.

Important properties:

- `EatDisplayPrecisionSec`
- `EatDisplayRecalcPeriodSec`
- `EatDisplayIntervalChars`

That block is where you change:

- minute-only versus higher-precision EAT display
- how often display strings are recalculated
- which suffix characters are used for timing buckets

## Layout-wide behavior

Inside each `tagLayouts.<layout>` object you can also control:

- `timelineLayout`: whether the layout behaves as a feeder or runway view
- `hideDeselectedViaFixes`: whether deselected via-fix traffic is hidden instead of dimmed
- `timelineSort`: optional per-layout sort preference

See [Timeline / Via-fix / Runway Setups](timelines.md) for the layout behavior side of those settings.

## `glEatCombi` practical note

`glEatCombi` is the compact combined Combi field for Gain/Lose and EAT-style
display behavior.

How ALB decides whether it can show:

- the layout must include `{"source": "glEatCombi", ...}`
- the aircraft must belong to traffic relevant to a currently active ALB
  timeline
- in practice that means the aircraft destination must be covered by an active
  timeline's `destinationAirports`

If no active timeline covers that destination, ALB now leaves the Combi output
blank instead of showing an unrelated placeholder on non-ALB traffic.

If you want a placeholder when Gain/Lose is selected but the real Gain/Lose
value is empty, use `glEatCombiDisplay.gainLooseEmptyText` in
[Config File Reference](config-description.md#gleatcombidisplay).

## `directRouting` practical note

`directRouting` is the route or control field that shows either:

- an explicit Direct-To when one is active, or
- the current next route fix as ALB understands it from the flight plan

In current ALB builds, the route-based fallback is intended to show the actual
current semantic next fix, not the following waypoint after it.
