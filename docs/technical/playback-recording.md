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

## Seqsync diagnostics and PERF summaries

The current backend recording and PERF output also cover backend seqsync
load-management behavior.

Important summary lines include:

- `BACKEND_SEQ_SYNC_SUMMARY`
- `BACKEND_SEQ_TX_THROTTLE`
- `BACKEND_SEQ_HORIZON`
- `BACKEND_SEQ_SUSPEND`
- `BACKEND_SEQ_NORMAL_RECOVERY`

Important field families include:

- `backendSeq.txQueue.*`
- `backendSeq.horizon.*`
- `backendSeq.suspend.*`
- `backendSeq.normalRecoveryDrain.*`
- `backendSeq.rxSet2.*`
- `backendPoll.*`

High-level reading guide:

- Stage 1 counters describe `SET2` candidate and class pressure
- Stage 2 counters describe queueing and throttle behavior
- Stage 3 counters describe horizon canonical versus suppressed behavior
- Stage 4 counters describe suspend suppression and resume behavior
- Stage 4.1 counters describe temporary bounded recovery drain when returning to `normal`

## Runtime validation pending

Backend seqsync load-management is implemented and build-validated in the
implementation thread, but live EuroScope and performance validation is still
pending.

Useful command sequence for validation:

```text
.alb seqsync status
.alb seqsync normal
.alb seqsync throttled
.alb seqsync horizon
.alb seqsync suspend
.alb seqsync normal
.alb perftest peers on 300
```

During validation, inspect:

- queue depth while throttled
- far-floating suppression in horizon
- overlay-clear `DEL` not bursting
- `SET2` suppression in suspend
- operational `DEL` during suspend
- bounded recovery after suspend
- bounded recovery from throttled or horizon back to normal
- peer RX or apply timing
- `backendPoll.maxMs`
- `messageDispatchMs`
- `rxSet2.applied`
- budget-hit counters
- `modelUpdated` count
