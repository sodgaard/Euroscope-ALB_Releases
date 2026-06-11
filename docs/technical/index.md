# Technical Overview

This section is for maintainers, not day-to-day operators.

It documents how the public documentation and release repo relates to the ALB codebase, and where to find the main collaboration and sequencing rules that shape the user-facing behavior.

## Repo split

- `Euroscope-ALB_Releases` is the public release and documentation repo
- `ES-ArrivalLoadBallance` is the runtime code repo

In practice:

- user-facing docs are published from this repo
- runtime behavior must be verified against the code repo before the docs are updated
- release packaging reflects the loader/core split described below

## Where to look

- [Architecture](architecture.md): component boundaries and loader/core split
- [Collaboration Internals](collaboration-internals.md): FMR ownership and shared-plan authority
- [Sequencing Model](sequencing-model.md): order stability, `Advance 1`, and `Resequence`
- [Backend Transport](backend-transport.md): backend-primary versus scratchpad fallback
- [Playback & Recording](playback-recording.md): recording artifacts and replay-related design

## Intent

The operator pages should answer "what do I click and why?".

The technical pages should answer:

- where shared authority is decided
- which messages belong on backend only
- why the planned order should remain stable even when live ETA moves around
