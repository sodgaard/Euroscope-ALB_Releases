# Legend & Status

This page explains the short labels you will commonly see in ALB. The exact columns depend on the active layout, so not every timeline shows every abbreviation all the time.

## Arrival state abbreviations

- `UKN`: unknown arrival state
- `UNS`: unsequenced
- `SQ1`: sequenced
- `SQ2`: locked
- `FZ1`: frozen stage 1
- `FZ2`: frozen stage 2
- `DIV`: diverting
- `CXL`: cancelled

These are the main sequence-progress states ALB uses when deciding whether an aircraft is still part of the active arrival plan.

## Hold phase abbreviations

- `HDE`: entering hold
- `HD1`: in hold with estimated EAT still moving
- `HD2`: in hold with EAT locked
- `HDX`: exiting hold

If the hold phase column is blank, the aircraft is not currently treated as holding traffic.

## Common field labels

- `PLR`: planned landing rate
- `AR`: arrival spacing for a via-fix stream, shown as minutes between releases
- `Cp`: capacity derived from the current spacing
- `TMA`: aircraft already in the terminal area
- `OBS`: observation or warning slot in the selected layout
- Orange timing: ALB's configured route or STAR-based track-mile timing model used as part of the `ELT-ALB` or `ETA:ALB` estimate before terminal live estimates take over

## Peer and status indicators

- `Peers(n)`: number of currently online ALB peers shown for the active airports
- `EAT` or `PLT` button: which value the compact combi field is currently showing
- `*` in a top button label: that option is enabled
- `-` in a top button label: that option is disabled

## Screenshot note

Some screenshots in this site come from older ALB versions. The overall structure is still useful, but exact spacing and some labels may differ slightly from the latest build.


