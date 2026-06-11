# Aircraft Actions

ALB aircraft rows support both selection and a right-click action menu.

The effect of sequence actions depends on whether the current layout is feeder-oriented or runway-oriented. See [Feeder View vs Runway View](planning-modes/views.md) for the high-level planning picture.

## Left-click

Left-clicking an aircraft row selects that aircraft in ALB. In normal live use this also follows EuroScope selection behavior, which makes the row useful as a quick bridge to other controller actions outside ALB.

## Right-click menu

Right-click an aircraft row to open:

- `Advance 1`
- `Resequence`
- `Clear manual EAT`

## Advance 1

`Advance 1` moves the aircraft one slot earlier in the current planned order.

In feeder layout:

- it swaps with the previous eligible aircraft in the same via-fix stream
- ALB then recalculates EAT behind the swap point

In runway layout:

- it swaps with the previous eligible aircraft in the airport-wide landing sequence
- ALB then rebuilds landing-timeline products behind the swap point

That difference is important:

- in feeder view, `Advance 1` is stream-relative
- in runway view, `Advance 1` is global and landing-sequence-relative

`Advance 1` is only available when ALB can see a valid previous aircraft to swap with and the aircraft is already in a sequenced, locked, or frozen part of the plan.

## Resequence

`Resequence` is not a forced move to a chosen position.

It clears old fixed sequence and timing baggage for that aircraft, then lets the next normal sequencing pass place it naturally again. A good mental model is:

`release this aircraft back into ALB's normal planning logic`

Use it when an aircraft no longer belongs where ALB had previously pinned it.

Operationally, `Resequence` returns the aircraft to the current planning logic of the active view:

- stream-oriented re-placement in feeder/AR-style work
- landing-timeline re-placement in runway/LT-style work

## Clear manual EAT

`Clear manual EAT` removes the aircraft's manual EAT override.

In normal live use, this is only available when you are the tracking controller for that aircraft.

## Shared-session note

In collaborative use, explicit sequence actions should normally come from the controller who currently owns the shared plan for that airport.
