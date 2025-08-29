# Arrival Load Ballance (ALB) for EuroScope 
A simple arrival manager plugin for EuroScope. Uses the position predictions provided by EuroScope to visualize the arrival flow for a given airport or waypoint.
The project is based on the project AMAN with somewhat different objectives. The development of AMAN was stalled at one point.

## Capabilities
- Can show one or more timelines via drop-down menu
- Can show stats
- Can dim non-selected via-Fixes via drop-down menu
- Can update Arrival rate via Scenarios drop-down menu
- Can update individual arrivalrates via .alb ar <fix> <interval_minuts>
- Can vary layout via drop-down menu

## Known issues as of 0.2.1a
- inTMA is a rough implementation based on ac passing the next fix after the holding Fix, For EKCH this is particularly important to understand for ERNOV arrivals.
- Currently the Controlpanel overlays the top of the ribon

## Euroscope gotya
HOLD & XHOLD are transmittet by TopSky via ScratchPad messages that are overwritten. Newly opened clients will not have the history.
ALB will only process these after the initial Open command.

## Implemented dataprocessing
- Aircraft in the TMA are ignored by the plugin.
- Aircraft beyond 60 mins are ignored in the sum.
- A change in Planned landing rate will result in an adjustment of times without a recalc of sequence.

## Download, installation and start up
The plugin .dll-file can be found under https://github.com/sodgaard/Euroscope-ALB_Releases/releases
Place the .dll and a config file in the same directory.
Add the dll in the Euroscope plugin menu. It does not currently need permission to write on the main ES window.
The ALB-display will appear in a separate window once the plugin has been loaded and window opened.

## Configuration

Timelines are loaded from `alb-config.json` which must be placed in the same directory as the plugin `dll`. The file content can be reloaded at run time through the menu. 

Example `alb-config.json`:

```json
{
    "openAutomatically": false,
    "timelines": {
        "EKCH 04L/04R": {
            "destinationAirports": [ "EKCH", "EKRK" ],
            "targetFixes": [ "CH4LF", "CH4RF" ],
            "viaFixes": [  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],
            "ArrivalScenarios": {
                "test":       [  1,  2,  3,  4,  5 ],
                "Green":      [  2,  2,  2,  2,  2 ],
                "Yellow":     [  4, 10,  8,  8, 10 ],
                "Yellow-ER":  [  3, 10,  6,  6,  6 ],
                "Yellow-TI":  [  4,  6,  6, 10,  6 ],
                "Yellow-ERTI":[  4,  6,  8,  8,  6 ],
                "Red-ER":     [  3, 10,  6, 10,  6 ],
                "Red-TI":     [  6,  6,  6, 10,  6 ],
                "Red-ERTI":   [  6,  6,  8,  6,  8 ],
                "Priority-O": [  3, 10, 12, 12, 10 ],
                "Priority-W": [  6, 10,  6,  6, 10 ],
                "Stop":       [  0,  0,  0,  0,  0 ]
             },
            "ArrivalScenarios-ssoBackup": {
                "test":      [  1,  2,  3,  4,  5 ],
                "Default":   [  2,  2,  2,  2,  2 ],
                "FreeFlow":  [  2,  2,  2,  2,  2 ],
                "Standard":  [  4, 10,  8,  8, 10 ],
                "PO":        [  3, 10, 12, 12, 10 ],
                "PW":        [  6, 10,  6,  6, 10 ],
                "Stop":      [  0,  0,  0,  0,  0 ]
             },
            "tagLayout": "Expanded"
        },
        "EKCH 22L/22R": {
            "destinationAirports": [ "EKCH", "EKRK" ],
            "targetFixes": [ "CH2LF", "CH2RF" ],
            "viaFixes": [  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],
            "ArrivalScenarios": {
                "test":       [  1,  2,  3,  4,  5 ],
                "Green":      [  2,  2,  2,  2,  2 ],
                "Yellow":     [  4, 10,  8,  8, 10 ],
                "Yellow-ER":  [  3, 10,  6,  6,  6 ],
                "Yellow-TI":  [  4,  6,  6, 10,  6 ],
                "Yellow-ERTI":[  4,  6,  8,  8,  6 ],
                "Red-ER":     [  3, 10,  6, 10,  6 ],
                "Red-TI":     [  6,  6,  6, 10,  6 ],
                "Red-ERTI":   [  6,  6,  8,  6,  8 ],
                "Priority-O": [  3, 10, 12, 12, 10 ],
                "Priority-W": [  6, 10,  6,  6, 10 ],
                "Stop":       [  0,  0,  0,  0,  0 ]
             },
            "ArrivalScenarios-ssoBackup": {
                "test":      [  1,  2,  3,  4,  5 ],
                "Default":   [  2,  2,  2,  2,  2 ],
                "FreeFlow":  [  2,  2,  2,  2,  2 ],
                "Standard":  [  4, 10,  8,  8, 10 ],
                "PO":        [  3, 10, 12, 12, 10 ],
                "PW":        [  6, 10,  6,  6, 10 ],
                "Stop":      [  0,  0,  0,  0,  0 ]
             },
            "tagLayout": "Expanded"
        },
        "EKCH 12": {
            "destinationAirports": [ "EKCH", "EKRK" ],
            "targetFixes": [ "VECJA" ],
            "viaFixes": [  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],
            "ArrivalScenarios": {
                "test":       [  1,  2,  3,  4,  5 ],
                "Green":      [  2,  2,  2,  2,  2 ],
                "Yellow":     [  4, 10,  8,  8, 10 ],
                "Yellow-ER":  [  3, 10,  6,  6,  6 ],
                "Yellow-TI":  [  4,  6,  6, 10,  6 ],
                "Yellow-ERTI":[  6,  6,  8,  6,  8 ],
                "Red-ER":     [  3, 10,  6, 10,  6 ],
                "Red-TI":     [  6,  6,  6, 10,  6 ],
                "Red-ERTI":   [  6,  6,  8,  6,  8 ],
                "Priority-O": [  3, 10, 12, 12, 10 ],
                "Priority-W": [  6, 10,  6,  6, 10 ],
                "Stop":       [  0,  0,  0,  0,  0 ]
             },
            "ArrivalScenarios-ssoBackup": {
                "test":      [  1,  2,  3,  4,  5 ],
                "Default":   [  2,  2,  2,  2,  2 ],
                "FreeFlow":  [  2,  2,  2,  2,  2 ],
                "Standard":  [  4, 10,  8,  8, 10 ],
                "PO":        [  3, 10, 12, 12, 10 ],
                "PW":        [  6, 10,  6,  6, 10 ],
                "Stop":      [  0,  0,  0,  0,  0 ]
             },
            "tagLayout": "Expanded"
        },
        "EKCH 30": {
            "destinationAirports": [ "EKCH", "EKRK" ],
            "targetFixes": [ "ORZIH" ],
            "viaFixes": [  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],
            "ArrivalScenarios": {
                "test":       [  1,  2,  3,  4,  5 ],
                "Green":      [  2,  2,  2,  2,  2 ],
                "Yellow":     [  4, 10,  8,  8, 10 ],
                "Yellow-ER":  [  3, 10,  6,  6,  6 ],
                "Yellow-TI":  [  4,  6,  6, 10,  6 ],
                "Yellow-ERTI":[  6,  6,  8,  6,  8 ],
                "Red-ER":     [  3, 10,  6, 10,  6 ],
                "Red-TI":     [  6,  6,  6, 10,  6 ],
                "Red-ERTI":   [  6,  6,  8,  6,  8 ],
                "Priority-O": [  3, 10, 12, 12, 10 ],
                "Priority-W": [  6, 10,  6,  6, 10 ],
                "Stop":       [  0,  0,  0,  0,  0 ]
             },
            "ArrivalScenarios-ssoBackup": {
                "test":      [  1,  2,  3,  4,  5 ],
                "Default":   [  2,  2,  2,  2,  2 ],
                "FreeFlow":  [  2,  2,  2,  2,  2 ],
                "Standard":  [  4, 10,  8,  8, 10 ],
                "PO":        [  3, 10, 12, 12, 10 ],
                "PW":        [  6, 10,  6,  6, 10 ],
                "Stop":      [  0,  0,  0,  0,  0 ]
             },
            "tagLayout": "Expanded"
        }
    },
    "tagLayouts": {
        "Standard": [
            { "source": "assignedRunway", "width": 4 },
            { "source": "callsign", "width": 8 },
            { "source": "static", "defaultValue": " ", "width": 2, "isViaFixIndicator": true },
            { "source": "aircraftType", "width": 5 },
            { "source": "aircraftWtc", "width": 2 },
            { "source": "timeBehindPreceeding", "width": 5, "rightAligned": true },
            { "source": "remainingDistance", "width": 4, "rightAligned": true },
            { "source": "static", "width": 1 },
            { "source": "directRouting", "width": 5, "rightAligned": true, "defaultValue": "-----" },
            { "source": "static", "width": 1 },
            { "source": "scratchPad", "width": 4 }
        ],
        "Expanded": [
            { "source": "assignedRunway", "width": 4 },
            { "source": "acno", "width": 3 },
            { "source": "callsign", "width": 8 },
            { "source": "static", "width": 1 },
            { "source": "timeBehindPreceeding", "width": 5, "rightAligned": true },
            { "source": "static", "width": 1 },
            { "source": "targetFixEta", "width": 7, "rightAligned": true },
            { "source": "targetFixATCTime", "width": 7, "rightAligned": true },
            { "source": "remainingDistance", "width": 4, "rightAligned": true },
            { "source": "static", "width": 1 },
            { "source": "viaFix", "width": 7 },
            { "source": "inboundGrouping", "width": 7 },
            { "source": "holdingFix", "width": 5 },
            { "source": "static", "width": 1 },
            { "source": "directRouting", "width": 5, "rightAligned": true, "defaultValue": "-----" },
            { "source": "static", "width": 1 },
            { "source": "ATCShort", "width": 4 } 
        ],
        "simpleLayout": [
            { "source": "assignedRunway", "width": 4, "isViaFixIndicator": true },
            { "source": "callsign", "width": 8 }
        ]
    },
    "TMACtr" : [
            { "lat": 55.64336491 },
            { "lon": 12.24763954 },
            { "radius": 29 }

    ],
    "TMAPoly" : [
           { "lat": 55.24944444444444, "lon": 11.680833333333332 },
           { "lat": 55.42722222222222, "lon": 11.41 },
           { "lat": 55.84666666666667, "lon": 11.362777777777778 },
           { "lat": 55.985, "lon": 11.825833333333334 },
           { "lat": 56.156388888888884, "lon": 12.412777777777778 },
           { "lat": 56.16416666666667, "lon": 12.44 },
           { "lat": 56.032777777777774, "lon": 12.656944444444445 },
           { "lat": 56.032777777777774, "lon": 12.679444444444444 },
           { "lat": 55.99944444444444, "lon": 12.732222222222223 },
           { "lat": 55.976111111111116, "lon": 12.865555555555556 },
           { "lat": 55.73277777777778, "lon": 13.115555555555556 },
           { "lat": 55.24944444444444, "lon": 12.998888888888889 },
           { "lat": 55.195277777777775, "lon": 11.979444444444445 },
           { "lat": 55.24944444444444, "lon": 11.680833333333332 }

    ]
}

```

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

| Name                             | Description
|----------------------------------|---------------
| `callsign`                       | Aircraft call sign.
| `assignedRunway`                 | Assigned landing runway.
| `assignedStar`                   | Assigned STAR.
| `aircraftType`                   | ICAO aircraft code.
| `aircraftWtc`                    | Wake turbulence category (L/M/H/S).
| `minutesBehindPreceedingRounded` | Time behind preceeding aircraft (rounded to nearest minute).
| `timeBehindPreceeding`           | Time behind preceeding aircraft (mm:ss).
| `remainingDistance`              | Distance to target fix (nautical miles).
| `estimatedLandingTime`           | Estimated landing time (hh:mm).
| `directRouting`                  | Direct routing (if any) given by ATC.
| `groundSpeed`                    | Calculated ground speed.
| `groundSpeed10`                  | Calculated ground speed (in tens).
| `altitude`                       | Altitude (pressure altitude or FL).
| `scratchPad`                     | Scratch pad value.
| `viaFix`                         | ViaFix
| `inboundGrouping`                | Grouping within which the AC currently falls.
| `static`                         | A static text, specified in the `defaultValue` property.

### TMA Centre
If relevant a TMA centre can be defined with a radius. This is used as a simple means of defining when an AC is in the TMA
| `lat`                         | Latitude in whole degre and decimal
| `lon`                         | Longitude in whole degre and decimal
| `radius`                      | Radius in nm



## Available dot-commands

| Command                     | Description
|-----------------------------|---------------
| `.alb open`                 | Opens the window - remember to select runway configuration from the dropdown menu.
| `.alb close`                | Closes the window
| `.alb statusonce`           | Print out status for inbounds viaFixes or further out. Outside is split in [total] <0-30''> 30-60 60+ (in TMA)
| `.alb status`               | Toggle auto updates per 1 minut.
| `.alb refreshtime {minuts}` | - Changing the refresh rate time in minuts (Default 1 minut, MAX 99 minuts).
| `.alb plr {opsPHr}`         | - Changing the planned landing rate time in Operations per Hour (Default 35 minut, MAX 99 minuts).
| `.alb pdr {opsPHr}`         | - Changing the planned departure rate time in Operations per Hour (Default 35 minut, MAX 99 minuts).





![Window](https://i.gyazo.com/a17c178138339ff8994755e77b0400b9.png)


Search tags:
> Vatsim, air traffic control, ATC, flight simulator

# APPLICATION DEVELOPMENT STRATEGY

Two threads - ES and WINDOW

## ES

Collect data needed for data processing.
Avoid calculations as far as possible to minimize load on main thread.

## WINDOW

Not so time critical as ES thread, thus do cals here whenever possible.

# Understanding the Plug-in interaction with ES

## Main call hiearchy

### ES Datastructure essentials:
```
  CRadarTarget CPlugin.RadarTargetSelectFirst() // Handle for first RadarTarget
  const char * CRadarTarget.GetCorrelatedFlightPlan().GetFlightPlanData().GetDestination() // String pointer to Airport ID - to be verified.
  CFlightPlanExtractedRoute CRadarTarget.GetCorrelatedFlightPlan().GetExtractedRoute(); // Handle for the Route

``` 

### THREAD ES:
```
CAlbApp (Alb.cpp)
  gpAlbPlugin* of type AmanPlugin (global pointer declared and exported to Euroscope in Main.cpp)
    AlbPlugin (declared and defined in AlbPlugin.h/.cpp) - Top level with link to ES
      .loadTimelines (read .json file)
      AlbController  (declared and defined in AlbController.h/.cpp) 
        AlbWindow(this) : Window (declared and defiend in AmanWindow.h/.cpp)
          TitleBar
          MenuBar
        .modelUpdated()
          AlbPlugin* albModel->getTimelines
            for IDs
              for TimeLines
                for Fixes
                  .getInboundsForFix()
          AlbPlugin* amanModel->getAvailableIDs
          albWindow->update(timelines)
            renderTimelinesMutex.lock();
            timelinesToRender = timelines; // Handle to vector of TimeLines
            renderTimelinesMutex.unlock();
          albWindow->setAvailableTimelines(loadedDefinitions)
      AlbTimelines (declared and defined in AlbTimeline.h/.cpp)
OnCompileCommand (ES virtual/callback defined in AmanPlugin.cpp)
  ".alb open" - AlbController->openWindow()
  ".alb close" - AlbController->closeWindow()
  ".alb <etc etc>" - other commands not published
OnTimer (ES virtual/callback defined in AlbPlugin.cpp)  // Called once every second after all Data models have been updated.
  AlbController->modelUpdated()              
```
### THREAD WINDOW:
```
AlbWindow::drawContent() // Draw all selected timeLineViews
  renderTimelinesMutex.lock();
  AlbTimelineView::render() // This is where all the drawing happens.
     From AlbWindow.h: typedef std::shared_ptr<std::vector<std::shared_ptr<AlbTimeline>>> timelineCollection;
  renderTimelinesMutex.lock();

```
