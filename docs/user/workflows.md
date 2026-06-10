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

Set timeline sort mode:

- **Target fix ETA**: `target`, `targetfixeta`, `tfe`
- **Landing ETA**: `landing`, `landingeta`, `lde`, `lnd`

```
.alb sort <target|landing>
```

---

## EAT loop compensation toggle

Toggles or explicitly sets EAT loop compensation.

```
.alb eatloop
.alb eatloop on
.alb eatloop off
.alb eatloop status
```

---

## Planned landing rate (landings per hour)

Sets PLR for all active ICAOs derived from active timelines. If there are no active ICAOs, the rate is applied locally only.

Valid range: **5-80 ops/hr**.

```
.alb plr <rate>
```

## Planned departure rate (departures per hour)

Sets the local planned departure rate.

Valid range: **1-99 ops/hr**.

```
.alb pdr <rate>
```

## Arrival rate via fix (minutes interval)

Sets arrival spacing in minutes for a given via fix, applied to all active ICAOs derived from active timelines.

Typical use: hold and arrival management for a specific entry fix.

Valid range: **0-20 minutes**.

```
.alb ar <ViaFix> <minutes>
```

---

## Become or resign FMR for an airport

Toggles FMR ownership for a destination ICAO.

- Claim if you are not currently FMR
- Resign if you are currently FMR

```
.alb fmr <ICAO>
```

---

## Set Estimated Arrival Time (SEAT)

Prerequisites:

- The aircraft must already have been marked as cleared into a hold, i.e. visible on a Holding List.
- `HOLDFIX` must be set for the aircraft.
- You must be FMR for the aircraft destination.
- The controller who has the aircraft assumed must have a compatible ALB version installed and open.

```
.alb seat <Callsign> <HHMM>
```

---

## Status output

Toggle periodic status updates:

```
.alb status
```

Print status once:

```
.alb statusonce
```

## Status refresh time

Sets the status refresh time used by the status updater.

Valid range: **1-99 minutes**.

```
.alb refreshtime <minutes>
```

---

## Sound tests

Plays built-in sounds to verify audio setup:

- `dong` also accepts `plr` or `ar`
- `ma`, `ga`, `goaround`, `missed`, `pointout`

```
.alb sound <dong|ma|ga|goaround|missed|pointout>
```
