# Timeline / Via-fix / Runway Setups

Use this page when you want to define how ALB groups traffic into operational timelines.

## Where timeline behavior is defined

The main operational grouping lives in `timelines`.

Each timeline definition can control:

- `targetFixes`
- `viaFixes`
- `destinationAirports`
- `runways`
- `defaultTimeSpan`
- `forceSingleTimeline`
- `viaFixColors`
- `ArrivalScenarios`

See [Config File Reference](config-description.md) for the full property list.

## Target fixes and via-fixes

- `targetFixes` defines which arrival target fixes feed the timeline
- exactly two target fixes create a dual timeline unless `forceSingleTimeline` is `true`
- `viaFixes` defines the stream lanes shown in the timeline
- the placeholder `"-----"` is a visual separator, not a real via-fix lane

This is the main place to change which streams and target fixes belong to a given airport setup.

## Feeder versus runway layout behavior

Feeder versus runway behavior is selected by the layout, not by a separate operator toggle.

The relevant config lives in `tagLayouts.<layout>`:

- `timelineLayout: "Feeder"` for feeder-oriented presentation
- `timelineLayout: "Runway"` for runway-oriented presentation
- `hideDeselectedViaFixes` to hide deselected via-fix traffic instead of dimming it

Important practical note:

- `timelineLayout` and `hideDeselectedViaFixes` are display behavior
- feeder versus runway layout is an operational-role or display choice
- they do not change sequencing policy by themselves
- they are independent of `EAT:AR` versus `EAT:LT`

## Via-fix deselection behavior

- If `hideDeselectedViaFixes` is `true`, deselected stream traffic is hidden in that layout.
- If it is `false`, deselected stream traffic remains visible but dimmed.
- This is why two layouts can show the same traffic with different visual emphasis.

## Single versus dual timeline

`forceSingleTimeline` forces a single timeline even when two target fixes are configured.

Use it when you want one operational picture instead of a split dual-target-fix presentation.

## Legacy scenarios

`ArrivalScenarios` is still read by the loader and still appears in many real config files.

Operationally, it is retained for compatibility and history. Scenarios are retired from the recommended normal workflow, which should normally use `EAT:LT` landing-timeline monitoring and correction instead of scenario switching.

## Common real-world notes

- `timelineSort` can exist as a top-level default and as a per-layout override
- `viaFixColors-safe` commonly appears in real configs, but it is not read by the current loader
- `aimingPointFt` commonly appears in real configs, but it is not read by the current loader

Use [Sample Config File](config-file-sample.md) when you want to compare your own config against a fuller example.
