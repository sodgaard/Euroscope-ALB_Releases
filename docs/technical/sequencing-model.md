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

Orange timing must not be used to keep a stale separate ALB landing estimate alive after the aircraft has become terminal, post-via, on approach, final, landing, or past final fix. In those states, `ELT-ALB` should follow the live branch where appropriate or be cleared when no live estimate is available.

This keeps old route or STAR geometry from misleading the landing timeline once the aircraft is already deep into terminal handling.

## Design invariant

When documenting or changing ALB, keep this invariant intact:

- automatic live data may refine the plan
- only explicit operational actions should deliberately reorder the plan
