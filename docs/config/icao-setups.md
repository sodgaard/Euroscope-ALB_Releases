# ICAO / Airport Setups

Use this page when you want to tune ALB for a destination airport or airport family.

## Main airport-related blocks

- `timelines.<name>.destinationAirports`: which destination ICAOs belong to that operational timeline
- `timelines.<name>.runways`: which runways that timeline is intended to represent
- `airports.<ICAO>`: airport-specific metadata and fallback tuning
- `tmaAreas`: TMA polygons and fallback matching

## Destination airports

Inside each timeline, `destinationAirports` is the main filter for which arrivals belong to that timeline.

If `destinationAirports` is non-empty, only matching destination ICAOs are included in that timeline.

## Runways

There are two common runway-related config surfaces:

- `timelines.<name>.runways`: operational runway filter for that timeline
- `airports.<ICAO>.runways`: runway metadata such as elevation

Example:

```json
"airports": {
  "EKCH": {
    "runways": {
      "04L": { "elevFt": 17 },
      "04R": { "elevFt": 17 }
    }
  }
}
```

## TMA matching

`tmaAreas` is where you define polygon-based TMA matching for airports and runways.

Typical uses:

- airport and runway-specific TMA polygons
- fallback circle behavior when no polygon match exists

If no polygon matches, ALB can fall back to `airports.<ICAO>.tmaFallback`.

## Advanced airport tuning

The `airports.<ICAO>` block can also hold advanced airport-specific tuning such as:

- `viafix_track_nm_orange`
- `eat_lt_sequence_lock`

`viafix_track_nm_orange` is part of the airport timing model. It tunes the orange route or STAR track-mile estimate used by the ALB estimate branch before terminal or post-via phases.

These are valid runtime settings, but they belong in configuration/customization rather than the normal operator guide.

## Related page

Use [Timeline / Via-fix / Runway Setups](timelines.md) for the target-fix, via-fix, and layout side of the setup.
