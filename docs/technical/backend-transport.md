# Backend Transport

ALB treats backend A2A as the normal transport for shared authority and shared
planning state.

Backend sequence synchronization is part of that backend-primary design. It
sends canonical per-aircraft sequence state from the FMR to peers so they can
mirror the same committed picture without inventing their own competing order.

## Primary versus fallback

Current design split:

- backend A2A is the primary path for shared ALB authority and normal peer synchronization
- scratchpad intercom remains the compatibility and fallback path

When backend-primary is healthy and no scratchpad fallback is active,
scratchpad is no longer the normal ALB command bus.

## Important transport rule

Canonical per-aircraft sequence state is backend-only.

That includes the current backend sequence families such as canonical `SET2`,
legacy `SET`, and `DEL` style sequence ownership updates. Those messages must
not be recreated through scratchpad fallback.

The practical meaning is:

- the FMR remains authoritative for canonical sequence state
- peers mirror backend-owned canonical state
- peers must not independently resequence already committed shared traffic
- `DEL+AC` clears backend-owned canonical overlay state without deleting factual EuroScope flight-plan data

## Seqsync load-management modes

`backendSeqSync` controls how canonical backend sequence traffic is sent.

| Mode | Purpose | SET2 behavior | DEL behavior | Notes |
|---|---|---|---|---|
| `normal` | Standard operation | Immediate canonical `SET2` | Immediate operational `DEL` | Default mode |
| `throttled` | Smooth backend sequence TX bursts | `SET2` queued as latest-state-wins and budgeted | Operational `DEL` queued at high priority | No suppression; all canonical candidates stay canonical |
| `horizon` | Reduce churn from far-floating aircraft | Operationally relevant `SET2` queued and budgeted; far-floating `SET2` may be suppressed | Operational `DEL` queued at high priority; overlay-clear `DEL` can clear prior canonical state | Unknown or uncertain cases stay canonical |
| `suspend` | Emergency or controlled degraded mode | Normal canonical `SET2` TX suppressed | Operational `DEL` still allowed and bounded | Local FMR calculations continue; peers may retain last backend overlay until recovery, explicit `DEL`, FMR change, or local reset |

`normal` remains the steady-state default.

`throttled`, `horizon`, and `suspend` are explicit seqsync transport modes.
They are not planning modes like `EAT:AR` or `EAT:LT`.

## Technical mode behavior

### `normal`

- Immediate canonical `SET2`
- Immediate operational `DEL`
- No suppression
- No queue requirement in steady state

### `throttled`

- Canonical `SET2` is queued as latest-state-wins per aircraft key
- Queue drain happens from the backend poll or timer path
- Per-second and per-poll budgets limit how fast queued work is sent
- No far-floating suppression is applied
- Operational `DEL` stays prioritized

### `horizon`

- Uses the same TX queue and budgets as `throttled`
- Far-floating candidates may be suppressed from canonical backend sync
- Unknown or uncertain classification stays canonical
- Previously canonical aircraft that become far-floating can receive queued overlay-clear `DEL`
- Overlay-clear traffic is rate-limited and should not burst
- Re-canonicalization cancels pending overlay-clear where appropriate

### `suspend`

- Suppresses normal canonical `SET2` TX at the FMR candidate point
- Local AR or LT calculations continue
- Peer RX and apply behavior is unchanged
- Pending `SET2` and non-essential horizon-clear entries are dropped on suspend entry
- Operational `DEL` remains allowed and bounded
- Leaving suspend schedules bounded recovery resync

## Stage 4.1 bounded return to normal

`normal` is immediate in steady state.

However, when returning to `normal` from a queued mode with inherited queued
work, ALB temporarily uses bounded recovery drain so the backlog does not burst
in one timer poll.

Current behavior:

- `throttled` or `horizon` to `normal` drains inherited queued work through the configured budgets
- once the inherited backlog is empty, `normal` returns to ordinary immediate behavior
- `suspend` to `normal` also uses bounded recovery resync instead of uncapped flush behavior

## EAT boundary

Seqsync modes do not change the planning algorithms themselves.

- `EAT:AR` calculation is unchanged
- AR release logic is unchanged
- `EAT:LT` calculation is unchanged
- PLT calculation is unchanged
- LT ordering, gap-fill, and manual resequence behavior are unchanged

Seqsync modes only affect how canonical backend sequence state is transmitted,
queued, suppressed, or cleared.

In `horizon`, far-floating AR or LT candidates may be suppressed from canonical
backend sync, while operationally relevant or uncertain candidates remain
canonical.

In `suspend`, AR or LT-derived canonical `SET2` TX is suppressed, but local AR
or LT calculations continue.

## Peer RX and apply boundary

Stages 2 through 4.1 changed backend seqsync transmission behavior, not the
peer apply contract.

Peer RX and apply still follow the same boundary:

- `SET2` stale, duplicate, and self-echo guards remain in place
- `DEL` handling remains stale-safe
- `DEL+AC` clears backend-owned canonical overlay state
- no peer-owned resequencing was introduced
- the one-`modelUpdated()`-per-backend-RX-batch behavior remains unchanged

## Families at a high level

Backend-primary families include:

- FMR and peer authority shadow state
- shared preference and sequencing policy messages
- AR and PLR updates
- IVF and SEAT coordination
- pre-via sequence swap request and commit traffic
- canonical backend sequence synchronization

Scratchpad fallback remains for:

- legacy peer discovery and compatibility envelopes
- fallback use of smaller shared command families when backend is unavailable or fallback is active

## FLOAT note

Stage 5 FLOAT or advisory protocol is not implemented.

Current code is internally consistent without FLOAT. It should only be
considered later if runtime testing shows that peers need advisory far-aircraft
data in addition to the current `normal`, `throttled`, `horizon`, and
`suspend` modes.
