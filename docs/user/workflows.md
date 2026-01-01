# Workflows

In the following the available workflows are presented.

---

## Open and activate the plugin

```
.alb open
```

## Close and deactivate the plugin

```
.alb close
```

## Reload configuration (re-read alb-config.json)

Reloads the stored config file path (same config used at startup) and applies changes.

```
.alb reload
```

---

## Timeline sorting

Show current timeline sort mode:

```
.alb sort
```

Set timeline sort mode (accepted aliases shown):

- **Target fix ETA**: `target`, `targetfixeta`, `tfe`
- **Landing ETA**: `landing`, `landingeta`, `lde`, `lnd`

```
.alb sort <target|landing>
```

---

## Toggle runtime log file

Enables/disables ALB runtime logging to a session log file (useful for troubleshooting).

```
.alb log
```

---

## Experimental UI toggle

Toggles (or explicitly sets) “experimental UI” features. Useful when testing new UI elements.

```
.alb experimental
.alb experimental on
.alb experimental off
.alb experimental status
```

> Note: `.alb reload` will re-read config afterwards, if you also changed flags in the config file.

---

## EAT loop compensation toggle

Toggles (or explicitly sets) EAT loop compensation.

```
.alb eatloop
.alb eatloop on
.alb eatloop off
.alb eatloop status
```

---

## Planned landing rate (landings per hour)

Sets PLR for all *active* ICAOs (derived from active timelines).  
If there are no active ICAOs, the rate is applied locally only.

Valid range: **5–80 ops/hr**.

```
.alb plr <rate>
```

## Planned departure rate (departures per hour)

Sets the local planned departure rate.

Valid range: **1–99 ops/hr**.

```
.alb pdr <rate>
```

## Arrival rate via fix (minutes interval)

Sets arrival spacing in **minutes** for a given via fix, applied to all *active* ICAOs (derived from active timelines).  
Typical use: hold/arrival management for a specific entry fix.

Valid range: **0–20 minutes**.

```
.alb ar <ViaFix> <minutes>
```

---

## Become / resign FMR for an airport

Toggles FMR ownership for a destination ICAO (claim if not FMR, resign if currently FMR).

```
.alb fmr <ICAO>
```

---

## Set Estimated Arrival Time (SEAT)

There are a couple of prerequisites to be able to set the EAT:

- The aircraft need to have already been marked as cleared into a hold, i.e. visible on a Holding List.
- **HOLDFIX must be set** for the aircraft (AdditionalACData / HOLDFIX).
- **You must be FMR** for the aircraft’s destination.
- The controller who has the aircraft assumed must have ALB version 0.2.7 or newer installed and open.

```
.alb seat <Callsign> <HHMM>
```

---

## Status output

Toggle periodic status updates (legacy/diagnostics):

```
.alb status
```

Print status once:

```
.alb statusonce
```

## Status refresh time

Sets the status refresh time (minutes) used by the status updater.

Valid range: **1–99 minutes**.

```
.alb refreshtime <minutes>
```

---

## Sound tests

Plays built-in sounds (useful to verify your audio setup):

- `dong` (also accepts `plr` or `ar`) – the “dong” used for PLR/AR changes
- `ma`, `ga`, `goaround`, `missed`, `pointout` – go-around / missed-approach sound

```
.alb sound <dong|ma|ga|goaround|missed|pointout>
```

---

## Carrier permission test (scratchpad write probe)

Checks whether ALB can write/verify the scratchpad for the given callsign (restores the original text afterwards).

```
.alb cpt <Callsign>
```

---

## Debugging commands

### Toggle debug output

Turns debug output on/off (file sink etc.).

```
.alb debug
```

### Set debug level

- `off`
- `functional` (or `func`)
- `calling` (or `callflow`, `trace`)

```
.alb debug <off|functional|calling>
```

### Rotate debug file

Forces a new debug log file (useful to “start fresh” without restarting).

```
.alb debugfile
.alb debugnew
.alb debugrotate
```

### Print-once debugging

Arms a “print once” mode (for troubleshooting one-off events).

```
.alb debug printonce
```

### Per-aircraft debug watch list

Show current watch list:

```
.alb debugac
.alb debugac list
```

Clear watch list:

```
.alb debugac clear
```

Toggle one or more callsigns (space/comma/semicolon separated):

```
.alb debugac <CS1> [CS2] [...]
```

### Per-aircraft debug interval

Sets the minimum interval between per-aircraft debug prints.

```
.alb debugacinterval <seconds>
```

### Per-aircraft debug minimum phase

Only print per-aircraft debug once the aircraft is at/after the given phase.

Accepted phases: `EN1 EN2 ARR IAP FINAL GA ANY`

```
.alb debugacminphase <phase>
```

---

## Cloud log test (developer)

Sends a single “TEST” line to the cloud log endpoint.

```
.alb testcloudlog
```

---

## Reserved / placeholders

Currently a no-op placeholder:

```
.alb XXX
```

Not currently implemented (present in code but disabled/commented out):

```
.alb version
```
