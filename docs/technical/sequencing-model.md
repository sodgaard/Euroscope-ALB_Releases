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

## Design invariant

When documenting or changing ALB, keep this invariant intact:

- automatic live data may refine the plan
- only explicit operational actions should deliberately reorder the plan
