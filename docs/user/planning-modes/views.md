# Feeder View vs Runway View

## Why there are two views

- Feeder view is centered on via-fix streams, and is most relevant for Area Controllers not covering the TMA.
    - In Feeder view the Aircraft is attached to the timeline tape at its calculated EAT.
- Runway view is centered on the runway and landing sequence, and is most relevant for Controllers covering the TMA.
    - In Runway view the Aircraft is attached to the timeline tape at its calculated PLT.
- This view choice is independent of `EAT:AR` versus `EAT:LT`.
    - Both can show overlapping aircraft data, but the operational meaning of ordering and actions differs.

## Side-by-side table

| Topic | Feeder View | Runway View |
|---|---|---|
| Main question answered | How is each via-fix stream flowing? | What is the landing order and timeline? |
| Natural operational fit | Area-controller or upstream feeding picture | TMA or runway-side planning picture |
| Ordering focus | Aircraft grouped and ordered by via-fix stream | Aircraft ordered by the global landing sequence |
| Via-fix deselection | Hides or dims that via-fix stream depending on layout/config | Filters visibility or context, but does not mean the global landing sequence stops existing |
| `Advance 1` effect | Moves relative to the previous eligible aircraft in that via-fix stream | Moves relative to the previous eligible aircraft in the global landing sequence |
| `Resequence` effect | Releases the aircraft back into normal stream planning | Releases the aircraft back into normal landing timeline planning |
| Best use | Monitoring feeder balance and stream picture | Managing the runway-side landing picture |

## Via-fix menu behaviour

- In layouts with `hideDeselectedViaFixes: true`, deselected via-fix aircraft are hidden from that layout.
- In layouts without that setting, deselected via-fix aircraft remain visible but are dimmed.
- This is a display-only layout behavior. It does not change sequencing policy, EAT mode, ETA mode, or FMR authority.
- In feeder layouts, deselection is primarily a stream visibility tool.
- In runway layouts, deselection is still a display/filtering aid. The runway and landing sequence remain global even if one stream is hidden in that layout.

## Advance 1 difference

- In feeder view, `Advance 1` is stream-relative.
- In runway view, `Advance 1` is landing-sequence-relative and global.
- This same distinction is repeated on [Aircraft Actions](../aircraft-actions.md).

## Recommended use

- Choose feeder or runway view according to the operational role and picture you need.
    - In operational terms, feeder view usually matches the area-controller or upstream feeding picture, while runway view usually matches the TMA or runway-side picture.
- That choice is separate from `EAT:AR` versus `EAT:LT`.
- For current normal operations, `EAT:LT` is still the preferred engine mode.
- Use feeder view to understand or troubleshoot via-fix stream balance.
- Use runway view when the landing-order picture is the more important one to manage.
