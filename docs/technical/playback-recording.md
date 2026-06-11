# Playback & Recording

This page covers the technical recording and replay direction behind ALB's collaboration work.

## Backend recording

ALB can produce structured backend recording artifacts for later analysis and deterministic replay work.

The current recording model includes:

- backend TX and RX records
- manifest and manifest-update records
- snapshot records
- event-slot related records

## Artifact shape

The source repo documents a JSONL-based recording family plus a rebuildable index. The important design point is that ALB keeps replay-relevant raw payloads together with a summarized view that is easier to inspect.

## Coverage classes

The current coverage model distinguishes between sessions that were recorded from connection start and sessions that were joined late and had to recover with or without a usable snapshot.

That distinction matters because later replay or analysis quality depends on how complete the captured shared state really was.

## Replay direction

The current source documentation describes replay mode scaffolding, but not a finished end-user replay product. In other words:

- recording support exists as technical groundwork
- replay-related modes and fingerprints exist in runtime state
- this should still be treated as infrastructure, not normal operator workflow

## Housekeeping relationship

Runtime housekeeping and loader housekeeping are separate concerns.

- loader housekeeping belongs to the loader side
- runtime housekeeping belongs to ALBCore runtime logs and backend recording artifacts

This is why the public config docs cover normal runtime housekeeping knobs, while deeper recording implementation detail stays in the technical lane.
