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

### How orange timing feeds `ELT-ALB`

`viafix_track_nm_orange` is the airport-side input to ALB's corrected landing
estimate branch.

In simplified form:

1. ALB starts from the aircraft's current via-fix timing anchor or EVTO.
2. It looks up an orange distance-to-land, preferably by exact STAR name.
3. It converts that distance into seconds using a staged descent model.
4. It adds that time to the via-fix anchor to build `ELT-ALB`.

Current lookup behavior:

- exact STAR match is preferred
- if there is no exact STAR match, ALB may fall back by via-fix and use the
  largest configured orange distance among STARs for that same stream

Current timing assumptions:

- final `10 NM` uses a final-approach style speed assumption
- the `20 NM` before that uses a TMA-style speed assumption
- any remaining distance uses a higher, pre-terminal descent assumption
- when aircraft performance and upper-wind data are available, ALB can refine
  the estimate instead of relying only on the generic baseline

Operationally, that means the orange values are not arbitrary display numbers.
They directly affect how early or late `ELT-ALB` and ALB-based landing planning
appear before the aircraft reaches the more live terminal picture.

Once the aircraft is in terminal or post-via handling, ALB should stop leaning
on the orange distance model and follow the live ES branch where appropriate.

For the detailed property-level reference, see
[Config File Reference](./config-description.md#viafix_track_nm_orange).

These are valid runtime settings, but they belong in configuration/customization rather than the normal operator guide.

## Related page

Use [Timeline / Via-fix / Runway Setups](timelines.md) for the target-fix, via-fix, and layout side of the setup.
