# Config File description

The config setup is described below. A sample file can be found here: [alb-config.json](./config-file-sample.md).

Note: the plugin uses the EuroScope font which is based on the [ANSI/Windows-1252 character encoding](http://www.alanwood.net/demos/ansi.html). If you want to use special symbols like ¶, ¤, •, |, ©, ®, ¬, ‡ or º in your "static" tag fields you must save the JSON-file using that encoding (select "ANSI" encoding in notepad). Also note that some of these symbols have a custom representation in the EuroScope font - like ¶ which is displayed as a telephone, and ¤ which is a filled rectangle.

### Timelines

| Property         | Description
|------------------|---------------
| `targetFixes`    | Based on the assigned route, any aircraft expected to pass one of these fixes are shown in the timeline. When exactly two fixes are specified, a dual timeline is shown with the first fix on the left side and the second on the right side.
| `tagLayout`      | The id of the tag-layout that should be used for this timeline.
| `viaFixes`       | (optional) Each fix will be assigned a color, and aircraft with a route initially (any direct routings ignored) going through one of these fixes will be marked with the color. For example, this can give a better overview of which direction each aircraft is coming from. Only eight different colors are available at the moment.
| `defaultTimeSpan`| (optional) If used, this will be the initial "zoom"-level (in minutes) when the timeline is loaded.
| `destinationAirports` | (optional) If used, aircraft whose destination is not in `destinationAirports` will not be included.

### Tag layouts

A tag layout has a set of tag values, which will be drawn in the specified order. Each tag value is configured using the following properties:

| Property            | Description
|---------------------|---------------
| `source`            | Where to get the value from (see table below).
| `width`             | The number of characters that the value should "reserve". If the value is longer than `width` it will be truncated.
| `rightAligned`      | (optional) Defaults to `false`.
| `isViaFixIndicator` | (optional) If true, the value will be colored based on the "via fix". Defaults to `false`.
| `defaultValue`      | (optional) Can only be used if `source` is `directRouting` or `static`. Defaults to `""`.

The following `source`s are available:

| Name                             | ID  | Description
|----------------------------------|-----|---------------
| `static`                         | ``  | A static text, specified in the `defaultValue` property.
| `callsign`                       | `ACID` | Aircraft callsign.
| `assignedRunway`                 | `RWY` | Assigned landing runway.
| `assignedStar`                   | `STAR`| Assigned STAR.
| `aircraftType`                   | `XXXX`| ICAO aircraft code.
| `aircraftWtc`                    | `XXXX`| Wake turbulence category (L/M/H/S).
| `minutesBehindPreceedingRounded` | `XXXX`| Time behind preceeding aircraft (rounded to nearest minute).
| `timeBehindPreceeding`           | `XXXX`| Time behind preceeding aircraft (mm:ss).
| `remainingDistance`              | `NM`| Distance to target fix (nautical miles).
| `estimatedLandingTime`           | `EAT`| Estimated landing time (hh:mm).
| `targetFixEta`                   | `(ETF)`|  (hh:mm).
| `targetFixATCTime`               | `[ATC]`|  (hh:mm).
| `directRouting`                  | `DIR`| Direct routing (if any) given by ATC.
| `groundSpeed`                    | `XXXX`| Calculated ground speed.
| `groundSpeed10`                  | `XXXX`| Calculated ground speed (in tens).
| `altitude`                       | `XXXX`| Altitude (pressure altitude or FL).
| `scratchPad`                     | `XXXX`| Scratch pad value.
| `viaFix`                         | `XXXX`| ViaFix
| `inboundGrouping`                | `XXXX`| Grouping within which the AC currently falls.
| `FlightPhase`                    | `FPh`| Determined Flight Phase
| `ArrivalState`                  | `ASt`| Determined Arrival State
| `holdingFix`                  | `HOLD`| Holding fix (if available)
| `holdingEAT`                  | `EAT`| Holding fix Estimated Release (arrival) time - if set (hh:mm)


### TMA Centre
If relevant a TMA centre can be defined with a radius. This is used as a simple means of defining when an AC is in the TMA
| `lat`                         | Latitude in whole degre and decimal
| `lon`                         | Longitude in whole degre and decimal
| `radius`                      | Radius in nm


