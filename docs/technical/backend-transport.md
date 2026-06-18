# Backend Transport

ALB treats backend A2A as the normal transport for shared authority and shared
planning state.

Backend sequence synchronization is part of that backend-primary design. It
sends canonical per-aircraft sequence state from the FMR to peers so they can
mirror the same committed picture without inventing their own competing order.
In backend-primary healthy operation, canonical per-aircraft EAT authority is
carried through backend `SEQ/SET2+AC`, not through a normal `SEAT` command-bus
workflow.

## Primary versus fallback

Current design split:

- backend A2A is the primary path for shared ALB authority and normal peer synchronization
- scratchpad intercom remains the compatibility and fallback path

When backend-primary is healthy and no scratchpad fallback is active,
scratchpad is no longer the normal ALB command bus.
Scratchpad may still appear as the final aircraft-visible side effect, such as
writing `/HOLD_EAT/HHMM/` locally after canonical backend `SET2` EAT has been
accepted and applied by the responsible peer.

## Important transport rule

Canonical per-aircraft sequence state is backend-only.

That includes the current backend sequence families such as canonical `SET2`,
legacy `SET`, and `DEL` style sequence ownership updates. Those messages must
not be recreated through scratchpad fallback as a competing authority path.

The practical meaning is:

- the FMR remains authoritative for canonical sequence state
- peers mirror backend-owned canonical state
- peers must not independently resequence already committed shared traffic
- canonical backend state is the peer authority for order, EAT, PLT, timeline anchor, sequence influence, and special treatment
- peers may still compute local live or presentational data such as flight phase, live distance, countdown formatting, warnings, and tag decoration
- canonical HOLD_EAT authority in backend-primary operation follows received `SEQ/SET2` EAT
- the final `/HOLD_EAT/HHMM/` write is only an aircraft-visible local side effect gated by `HLW`
- `DEL+AC` clears backend-owned canonical overlay state without deleting factual EuroScope flight-plan data

## Active authority versus revision memory

ALB deliberately keeps two separate concepts on peers:

- active backend authority decides whether local writes to canonical sequence fields should be blocked
- revision memory decides whether an incoming backend message is stale

`ALB_BACKEND_SEQ_REV` or RX revision memory must not be interpreted as active
backend authority by itself. Revision memory answers "is this incoming message
stale?" Active authority answers "should local writes to canonical sequence
fields be blocked?" These are deliberately separate states.

In practical terms:

- active authority comes from active persisted backend overlay markers
- delete revisions remain useful for stale-message rejection
- a delete revision must not keep backend authority alive after `DEL`

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

### `SET2` on peers

An accepted newer backend `SET2`:

- activates or refreshes backend canonical authority for that aircraft
- writes the persisted canonical overlay fields used by peer timelines and display surfaces
- updates revision memory for later stale-message rejection
- does not stop the normal local timer or model pipeline; local calculation generally continues, but canonical writes stay subject to backend authority

### `DEL` on peers

`SEQ/DEL+AC` is the explicit teardown for backend-owned per-aircraft canonical
overlay state.

Current peer-side meaning:

- active backend authority for that aircraft is cleared
- backend-owned overlay markers such as `ALB_BACKEND_SEQ_ICAO`, `ALB_BACKEND_SEQ_VER`, and persisted `ALB_BACKEND_SEQ_REV` are cleared
- local factual EuroScope-derived data such as `FP_DEST` is not deleted
- stale-message protection is preserved separately through RX revision or flavor caches rather than through persisted active-authority markers

That split matters because local fallback writes should not stay blocked merely
because a prior delete revision exists.

### What happens when `SET2` stops

- if `SET2` simply stops without `DEL`, the last canonical overlay may remain intentionally sticky for short reconnect or continuity windows
- if `DEL` is received, active backend authority is explicitly cleared and local fallback may resume
- a later newer `SET2` can be accepted again and reactivate backend authority
- there is no separate "restart local calculations" step, because local calculations generally continue running throughout

## HORIZON and SUSPEND relevance

`horizon` and `suspend` make the authority teardown behavior especially
important on peers.

- `horizon` may send `DEL` clears for far-floating aircraft
- after such a `DEL`, a peer must not be left in a blank-canonical but still write-blocked state
- `suspend` can pause canonical `SET2` TX without stopping local calculations
- later recovery back toward `normal` may send replacement `SET2`, which reactivates backend authority

## Authority diagnostics

The authority-related diagnostics are intended to prove the peer state machine,
not just transport traffic:

- `BACKEND_SEQ_AUTH_ACTIVE`: a backend canonical message activated or refreshed active per-aircraft backend authority
- `BACKEND_SEQ_AUTH_CLEARED`: a backend `DEL` explicitly tore down active per-aircraft backend authority
- `BACKEND_SEQ_WRITE_BLOCKED`: a local canonical write attempt was blocked because active backend authority still owned that aircraft

## Families at a high level

Backend-primary families include:

- FMR and peer authority shadow state
- shared preference and sequencing policy messages
- AR and PLR updates
- IVF and other smaller shared-control families when backend is healthy
- pre-via sequence swap request and commit traffic
- canonical backend sequence synchronization

Scratchpad fallback remains for:

- legacy peer discovery and compatibility envelopes
- fallback use of smaller shared command families when backend is unavailable or fallback is active

For hold-EAT coordination, the normal backend-primary authority path is:

- canonical per-aircraft EAT carried by backend `SEQ/SET2+AC`
- local `HLW` permission deciding whether this client may write `/HOLD_EAT/HHMM/`
- the `/HOLD_EAT/HHMM/` write itself as the aircraft-visible side effect on the responsible client

`SEAT` may still exist as legacy or compatibility handling, but it should be
treated as fallback-only. It is not the normal backend-healthy transport and
must not override newer canonical `SET2` state.

For runway-sequence peer request or reply handling such as `Advance 1` and
`Resequence`, the current design should stay aligned with backend-primary
authority. In other words, the peer request path should not silently invent a
competing local fallback result while waiting for canonical backend sync.

## FLOAT note

Stage 5 FLOAT or advisory protocol is not implemented.

Current code is internally consistent without FLOAT. It should only be
considered later if runtime testing shows that peers need advisory far-aircraft
data in addition to the current `normal`, `throttled`, `horizon`, and
`suspend` modes.
