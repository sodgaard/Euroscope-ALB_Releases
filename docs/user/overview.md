# Overview



## Capabilities
- Can show one or more timelines via drop-down menu
- Can show stats
- Can dim non-selected via-Fixes via drop-down menu
- Can update Arrival rate via Scenarios drop-down menu
- Can update individual arrivalrates via **.alb ar <fix> <interval_minuts>**
- Can vary layout via drop-down menu

## Euroscope gotya
HOLD & XHOLD are transmittet by TopSky via ScratchPad messages that are overwritten. Newly opened clients will not have the history.
ALB will only process these after the initial Open command.


## Implemented dataprocessing
- Aircraft in the TMA are ignored by the plugin.
- Aircraft beyond 60 mins are ignored in the sum.
- A change in Planned landing rate will result in an adjustment of times without a recalc of sequence.