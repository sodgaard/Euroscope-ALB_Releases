# Sequencing Model

This page captures the sequencing rules that should stay stable even as the UI evolves.

## Stability first

The collaboration contract in the source says ALB should prefer stable sortable order keys over repeated global renumbering. The goal is simple:

- planned order should not churn just because live ETA, ELT, or EVTO fluctuates
- peers should reproduce the same order as the FMR
- manual actions should be explicit operational decisions, not automatic jitter responses

## Two sequence views

ALB currently supports two main sequence worlds:

- via-fix stream order in feeder-style layouts
- airport-wide landing order in runway-style layouts

That split is why `Advance 1` has two different implementations.

The feeder versus runway split is a view and operational-context distinction, not an `EAT:` mode distinction. `EAT:AR` and `EAT:LT` are separate engine or planning-mode concepts and should be documented separately from the view choice.

## AR versus LT

`EAT:AR` means arrival-regulated planning over the via-fix stream. It is the rougher model and does not fully model the downstream landing picture.

`EAT:LT` means landing-timeline planning. It is the preferred current model and uses landing-sequence and slot logic rather than only per-via-fix release spacing.

## How users influence sequencing

The sequencing model is not driven by one single transport or algorithm switch.
Different parts are influenced through different control surfaces:

- `PLR`: stats-area clicks or `.alb plr <rate>`
- per-via-fix `AR`: stats-area clicks or `.alb ar <ViaFix> <minutes>`
- planning mode: top-row `EAT:AR`, `EAT:LT`, or `EAT:TF`
- ETA branch: top-row `ETA:ES` or `ETA:ALB`
- explicit landing-order intervention: aircraft right-click actions such as
  `Advance 1` and `Resequence`
- hold EAT: `HLW` local write gate and legacy `.alb seat <Callsign> <HHMM>`
- airport LT spacing policy: `airports.<ICAO>.eat_lt_spacing` in
  `alb-config.json`, then `.alb reload`

That is the practical boundary this page relies on: the technical rules explain
what ALB does, while the control surface above explains how a user or
configurator makes ALB behave differently.

## WTC-aware LT pair spacing

In `EAT:LT`, ALB applies WTC-aware spacing to the existing canonical landing
order. It does not independently reorder traffic to optimize the WTC mix.

For two consecutive aircraft:

```text
leader   = aircraft in front
follower = aircraft behind
```

ALB calculates:

```text
plrSpacingSec =
    3600 / plannedLandingRate

behindSpacingSec =
    behindSec[leader][follower]

inFrontSpacingSec =
    inFrontSec[follower][leader]

requiredSpacingSec =
    max(
        plrSpacingSec,
        behindSpacingSec,
        inFrontSpacingSec
    )
```

The follower's PLT is then constrained by:

```text
followerPLT =
    max(
        followerELT,
        leaderPLT + requiredSpacingSec
    )
```

Natural traffic gaps larger than the required spacing remain unchanged.

## Built-in default matrices

The runtime contains complete built-in defaults, so an airport does not need an
`eat_lt_spacing` config block for WTC-aware LT spacing to operate.

All values below are seconds.

### `behindSec`

Rows are leaders. Columns are followers.

| Leader \ follower | L | M | H | J |
|---|---:|---:|---:|---:|
| L | 30 | 30 | 30 | 30 |
| M | 60 | 60 | 60 | 60 |
| H | 120 | 120 | 120 | 120 |
| J | 180 | 180 | 180 | 180 |

Lookup orientation:

```text
behindSec[leader][follower]
```

### `inFrontSec`

Rows are followers. Columns are leaders.

| Follower \ leader | L | M | H | J |
|---|---:|---:|---:|---:|
| L | 120 | 120 | 120 | 120 |
| M | 60 | 60 | 60 | 60 |
| H | 60 | 60 | 60 | 60 |
| J | 60 | 60 | 60 | 60 |

Lookup orientation:

```text
inFrontSec[follower][leader]
```

The two matrices do not use the same orientation. That distinction is part of
the current model.

## PLR 40 examples

At PLR 40:

```text
plrSpacingSec = 3600 / 40 = 90 seconds
```

Examples:

```text
Leader L, follower L:
max(90, 30, 120) = 120 seconds
```

```text
Leader M, follower L:
max(90, 60, 120) = 120 seconds
```

```text
Leader M, follower H:
max(90, 60, 60) = 90 seconds
```

```text
Leader H, follower M:
max(90, 120, 60) = 120 seconds
```

```text
Leader J, follower L:
max(90, 180, 120) = 180 seconds
```

Effective default table at PLR 40:

| Leader \ follower | L | M | H | J |
|---|---:|---:|---:|---:|
| L | 120 | 90 | 90 | 90 |
| M | 120 | 90 | 90 | 90 |
| H | 120 | 120 | 120 | 120 |
| J | 180 | 180 | 180 | 180 |

This effective table changes when PLR changes, because PLR spacing is one of
the three inputs to the pair-spacing maximum.

## Unknown WTC fallback

ALB uses the EuroScope WTC categories:

- `L` = Light
- `M` = Medium
- `H` = Heavy
- `J` = Super

Missing, blank, or unrecognized WTC values use the airport's configured
fallback category. The built-in default fallback is `M`.

## Gap-fill and protected FZ2 boundary

The same adjacent-pair spacing model is used across the normal LT chain rather
than only in one narrow placement path.

That includes:

- normal sequencing comparisons
- forced or deferred placement decisions
- FIFO-style gap-fill checks
- protected `FZ2` endpoint checks

Candidate-specific pair spacing is retained when ALB tests whether a candidate
can fit into a visible landing hole.

## Pair spacing versus slot tolerance

Required adjacent-pair spacing is not the same thing as LT slot-excess
tolerance.

Required pair spacing is:

```text
max(
    PLR spacing,
    behind spacing,
    in-front spacing
)
```

LT slot-excess tolerance remains PLR-based. The current tolerance model is
still one third of PLR spacing, bounded by the existing minimum and maximum
limits, and then added on top of the planned spacing to form the visible-hole
threshold.

Example:

```text
required pair spacing = 120 seconds
slot-excess tolerance = 30 seconds

candidate feasibility limit =
    pair target + 30 seconds
```

These concepts remain distinct:

- required adjacent-pair spacing
- slot-excess tolerance
- visible-hole threshold
- `FZ2` freeze timing

## Advance 1

Feeder layout:

- swaps the selected aircraft with the previous aircraft in the same via-fix stream
- requires the aircraft to be in a sequenced, locked, or frozen state
- refuses to advance aircraft that are already past the via-fix unless they are still in hold
- clears downstream EAT from the swap point so the queue behind it is rebuilt

Runway layout:

- swaps the selected aircraft with the previous aircraft in the airport-wide landing sequence
- also requires a sequenced, locked, or frozen state
- clears downstream landing compare products so PLT and related landing products are rebuilt

## Resequence

`ManualResequenceAircraft()` is intentionally not a direct move command.

It clears fixed sequence and timing baggage, then lets the next normal sequencing pass place the aircraft naturally again. The code comments explicitly describe this as similar to giving the aircraft a fresh re-entry into normal planning logic.

## Sequence authority

If shared authority exists and the local instance is not the owner, a manual pre-via swap is not applied locally as a silent fork. Instead, the local side sends a request toward the authority and waits for the authoritative result.

That behavior is essential: peers must not invent competing canonical orders.

For backend-primary peer sequencing, canonical means the official shared
sequence truth sent by the FMR. That canonical state is what peers should use
for order, EAT, PLT, timeline anchor, sequence influence, and special
treatment. Local model work can still continue around it for live or
presentational data, but peers should not treat local fallback output as a
competing canonical sequence while active backend authority still exists.

## Sorting and filtering in EAT:AR

### Inputs

- active destination and selected timeline
- selected layout and selected via-fixes
- aircraft relevance and visibility
- flight phase and arrival state
- via-fix stream and via-fix ordering fields
- via-fix ETO, EAT, and release interval
- hold state and manual EAT if present
- FMR policy and peer authority state

### Decisions

1. Is the aircraft relevant for the active destination and timeline?
2. Is it outside active planning because it is landed, cancelled, diverting, or otherwise tactically irrelevant?
3. Does it belong to a visible via-fix stream?
4. Is it already sequenced, locked, or frozen and therefore protected by a stable order key?
5. Does a hold or manual EAT override apply?
6. Has the aircraft deviated from expected routing or sequence enough to require warning rather than automatic churn?

### Outputs

- inclusion, exclusion, dimming, or hiding
- via-fix stream placement
- stable displayed stream order
- EAT and gain-lose style display products
- route, hold, or sequence warnings

## Sorting and filtering in EAT:LT

### Inputs

- active destination and selected timeline
- selected layout
- runway and target-fix relevance
- assigned runway or runway override
- landing-sequence keys, PLT, and ELT branch data
- aircraft relevance, visibility, and arrival state
- terminal or post-via status
- hold state and manual EAT if present
- FMR policy and peer authority state

### Decisions

1. Is the aircraft relevant for the active destination and timeline?
2. Is it outside active planning because it is landed, cancelled, diverting, or terminal-filtered?
3. Does its runway or target-fix belong in this timeline?
4. Is it already committed, sequenced, locked, or frozen and therefore protected by a stable landing order key?
5. Is it in a protected state such as FZ2, final, or other terminal treatment?
6. Is there a feasible LT slot without destabilizing the retained plan?
7. Is the aircraft failing conformance and therefore better handled by an explicit operational correction?

### Outputs

- inclusion, exclusion, dimming, or hiding
- landing timeline placement
- PLT, ELT, and countdown products
- conformance, route, or sequence warnings
- a stable order that changes only on explicit or justified triggers

## ES versus ALB estimate branch invariant

The operator UI talks about `ETA:ES` and `ETA:ALB`, while layouts may show
`ELT-ES` and `ELT-ALB`.

The technical invariant is:

- `ELT-ES` is the live EuroScope-style landing estimate branch
- `ELT-ALB` may use ALB correction before terminal or post-via phases
- once the aircraft is terminal or post-via, ALB must stop inventing a separate stale corrected landing branch and should follow the live branch where appropriate

Orange timing is part of the pre-terminal `ELT-ALB` branch. It may be used while the aircraft is before TMA, before via-fix, or before terminal handling, depending on the current sequencing state and available live data.

The current orange model is not just a raw fixed time. In practical terms:

- config provides orange track miles to touchdown, preferably by exact STAR
- if the exact STAR is not available, ALB may fall back by via-fix and use the
  largest configured orange distance for that stream
- ALB converts that distance into time with three buckets:
  - final `10 NM`
  - the preceding `20 NM`
  - any remaining higher-distance segment
- the generic baseline assumes roughly `145 KIAS` on the final segment,
  `180 KIAS` in the TMA segment, and `250 KIAS` in the higher segment
- when flight-plan performance data exists, ALB can refine those segment speeds
  for the aircraft instead of relying only on the generic jet baseline
- when upper-wind data exists, ALB can also adjust the approximate ground speed
- the resulting ALB branch is still bounded against the live ES branch so the
  corrected estimate does not run unrealistically far ahead

Orange timing must not be used to keep a stale separate ALB landing estimate alive after the aircraft has become terminal, post-via, on approach, final, landing, or past final fix. In those states, `ELT-ALB` should follow the live branch where appropriate or be cleared when no live estimate is available.

This keeps old route or STAR geometry from misleading the landing timeline once the aircraft is already deep into terminal handling.

## Expected LR

Expected LR is a demand-limited forecast derived from eligible canonical future
PLTs.

It counts aircraft in:

```text
scenarioNow < PLT <= windowEnd
```

It then calculates:

```text
expectedLandingRatePerHour =
    eligibleAircraftCount * 60 / windowMinutes
```

Because the forecast uses final canonical PLTs, it already reflects:

- PLR
- WTC pair spacing
- natural traffic gaps
- holds
- frozen or protected slots
- manual sequence changes
- gap-fill
- other constraints already represented by the canonical plan

It is output-only. There is no documented feedback path from Expected LR back
into PLR, WTC spacing, sequence order, EAT, ELT, PLT, or gap-fill decisions.

The current implementation also resolves duplicate callsigns before counting
eligible aircraft, so the legend forecast is based on one representative row
per callsign rather than blindly double-counting every duplicate row.

## FMR and peer boundary for Expected LR

FMR remains authoritative for canonical order, EAT, and PLT.

Peers mirror canonical state. They do not independently resequence traffic just
because they compute an Expected LR value locally.

Expected LR is calculated locally from the canonical PLTs already available to
that client. The WTC tables themselves are not transmitted to peers as a
separate live shared object, and this feature does not require a new `SET2`,
`POL`, `PREF`, scratchpad, backend, or cloud-schema field.

## Design invariant

When documenting or changing ALB, keep this invariant intact:

- automatic live data may refine the plan
- only explicit operational actions should deliberately reorder the plan

## Backend seqsync boundary

Backend seqsync load-management sits after the sequencing decision, not inside
the sequencing algorithm itself.

- AR and LT logic still decide the local canonical sequence picture
- seqsync modes decide how that canonical result is transmitted to peers
- `horizon` can suppress far-floating aircraft from canonical backend sync without changing the local AR or LT calculation
- `suspend` suppresses canonical `SET2` TX without stopping local AR or LT calculations

On peers, that means the transport layer can change whether canonical backend
authority remains active even while the local calculation pipeline continues
running. A received backend `DEL` clears active per-aircraft authority so local
fallback writes may resume, while stale-message memory remains separate.

That is why the documentation treats seqsync as backend transport behavior
rather than as a new planning mode.
