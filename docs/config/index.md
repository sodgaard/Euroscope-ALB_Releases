# Config Overview

This section is for users customizing `alb-config.json`. It is not the day-to-day operator guide, and it is not the backend developer section.

Use this section when your question is:

- where do I change displayed fields
- where do I change widths, alignment, or time formatting
- where do I configure destination airports, runways, target fixes, or via-fixes
- where do I choose feeder versus runway layout behavior

## Start here

- [Display Fields and Tag Layouts](display-fields.md): row items, widths, alignment, and time formatting
- [ICAO / Airport Setups](icao-setups.md): airport metadata, TMA fallback, and runway metadata
- [Timeline / Via-fix / Runway Setups](timelines.md): timeline definitions, stream setup, feeder versus runway layout behavior
- [Config File Reference](config-description.md): full property-by-property reference
- [Sample Config File](config-file-sample.md): real-world style sample

## Practical split

- `tagLayouts` answers how rows look
- `timelines` answers which traffic belongs together operationally
- `airports` and `tmaAreas` answer airport-specific geometry and fallback data
- the top-level `layout` block answers how EAT-style time text is formatted
