# Overview

Great - you are ready to dive further in to the functionality of ALB.

The purpose of the plugin in its current state is to:

- Provide an overview of incomming traffic split on each STAR stream and with subtotals according to Approach sector split (eg East vs West).
- Communicate Arrival rates per STAR defined in minuts in between each release

## Capabilities
- Can show one or more timelines via drop-down menu
- Can show inbound stats
- Can dim non-selected via-Fixes via drop-down menu
- Can claim or resign as Flow Management Responsible for a one or more airports (must be in the visible Timelines) via the command ```.alb fmr <ICAO>```
- Can update all Arrival Rate via Scenarios drop-down menu
- Can update individual Arrival rate via click on the Arrival Rate indicator
- Can vary layout via drop-down menu
- Can set Estimated Approach Time (EAT) on aircraft already cleared in hold via ```.alb seat <AC> <HHMM>```

## Euroscope gotya
Certain commands are transmittet when an incidence (like putting a flight in Hold) occur. Clients connecting after the incidence has happened will not be updated on historical events.

ALB will only process these after the initial ```.alb Open``` command.

Euroscope path and timing calculation is based on current groundspeed, and does not contain anticipated slow down as the aircraft descends. The resulting sequencing may currently be influenced by this and will lead to incorrect estimates.

## Implemented dataprocessing
- Aircraft in the TMA are not processed by the plugin.
- Aircraft beyond 60 mins are ignored in the sum.
- A change in Planned landing rate will result in an adjustment of times without a recalc of sequence.

