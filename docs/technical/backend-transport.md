# Backend Transport

ALB now treats backend A2A as the normal transport for shared authority and shared planning state.

## Primary versus fallback

Current design split:

- backend A2A is the primary path for shared ALB authority and normal peer synchronization
- scratchpad intercom remains the compatibility and fallback path

When backend-primary is healthy and no scratchpad fallback is active, scratchpad is no longer the normal ALB command bus.

## Important transport rule

Canonical per-aircraft sequence state is backend-only.

That includes the current backend sequence families such as canonical `SET2`, legacy `SET`, and `DEL` style sequence ownership updates. Those messages must not be recreated through scratchpad fallback.

## Families at a high level

Backend-primary families include:

- FMR and peer authority shadow state
- shared preference and sequencing policy messages
- AR and PLR updates
- IVF and SEAT coordination
- pre-via sequence swap request and commit traffic

Scratchpad fallback remains for:

- legacy peer discovery and compatibility envelopes
- fallback use of smaller shared command families when backend is unavailable or fallback is active

## Why this matters for docs

Operator documentation should say only that ALB shares planning state automatically when conditions allow.

Technical documentation is where we keep the sharper statement:

- backend-primary is the intended normal path
- scratchpad fallback exists for compatibility and degraded-path operation
- backend-only sequence state must remain backend-only
