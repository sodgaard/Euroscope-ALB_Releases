# Planning Modes Overview

ALB has two planning concepts:

- `EAT:LT` is the normal modern landing-timeline method
- `EAT:AR` is the older rough arrival-rate fallback method

New users should learn `EAT:LT` first.

## Side-by-side comparison

| Topic | EAT:LT | EAT:AR |
|---|---|---|
| Recommended use | Normal modern ALB planning method | Fallback / older rough flow method |
| Planning basis | Landing timeline / PLT / stable landing sequence | Via-fix arrival rate / release interval |
| Main FMR control | Planned Landing Rate, monitoring, Advance 1, Resequence, operational correction | Arrival Rate per via-fix, monitoring, Advance 1, Resequence |
| AR relevance | AR is legacy/context information, not the active planning driver | AR is the active rough flow-control input |
| Controller workload | Mostly monitor ALB's plan and correct aircraft not following it | More manual tweaking of each via-fix stream |
| Sequence concept | Global landing sequence / landing timeline | Stream-based via-fix sequencing |
| Relation to view | Independent of feeder versus runway view | Independent of feeder versus runway view |
| Best for | Fine, state-of-the-art planning | Simple/fallback flow control |

## Which page should you read first

- If you are learning normal ALB use, start with [EAT:LT Landing Timeline Planning](eat-lt.md).
- If you deliberately want the older stream-spacing method, read [EAT:AR Arrival Rate Fallback Planning](eat-ar.md).
- If you need to understand why the same aircraft action behaves differently in different layouts, read [Feeder View vs Runway View](views.md).

## Short operator message

In current ALB use:

- `EAT:LT` is the normal operating method
- `EAT:AR` remains available as a useful fallback
- Feeder and runway layouts can show the same traffic with different planning meaning
- Feeder versus runway is a view or role choice, not an `EAT:` mode choice
