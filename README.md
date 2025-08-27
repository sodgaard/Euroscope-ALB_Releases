# Arrival Load Ballance (ALB) for EuroScope 
A simple arrival manager plugin for EuroScope. Uses the position predictions provided by EuroScope to visualize the arrival flow for a given airport or waypoint.
The project is based on the project AMAN with somewhat different objectives. The development of AMAN was stalled at one point.

## Known issues as of 0.0.0.8h
- inTMA is a rough implementation based on ac passing the next fix after the holding Fix, For EKCH this is particularly important to understand for ERNOV arrivals.
- Reload of the config file does not work yet. You need to restart Euroscope or remove the plugin from the plugin list and add it again

## Euroscope gotya
HOLD & XHOLD are transmittet by TopSky via ScratchPad messages that are overwritten. Newly opened clients will not have the history.
ALB will only process these after the initial Open command.

## Implemented dataprocessing
- Aircraft in the TMA are ignored by the plugin.
- Aircraft beyond 60 mins are ignored in the sum.
- A change in Planned landing rate will result in an adjustment of times without a recalc of sequence.

## Download, installation and start up
The plugin .dll-file can be found under [Releases](https://github.com/EvenAR/euroscope-aman/releases. 
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
            "targetFixes": [ "CH4LF", "CH4RF" ],

            "viaFixes": ["TIDVU", "OLPIB", "-----", "ERNOV", "LUGAS", "ROSBI", "-----"],
            "tagLayout": "SSOtest",
            "destinationAirports": [ "EKCH" ]
        },
        "EKCH 22L/22R": {
            "targetFixes": [ "CH2LF", "CH2RF" ],
            "viaFixes": [ "TIDVU", "OLPIB", "-----", "ERNOV", "LUGAS", "ROSBI", "-----"],
            "tagLayout": "SSOtest",
            "destinationAirports": [ "EKCH" ]
        },
        "EKCH 12": {
            "targetFixes": [ "VECJA" ],
            "viaFixes": [ "TIDVU", "OLPIB", "-----", "ERNOV", "LUGAS", "ROSBI", "-----"],
            "tagLayout": "SSOtest",
            "destinationAirports": [ "EKCH" ]
        },
        "EKCH 30": {
            "targetFixes": [ "ORZIH" ],
            "viaFixes": [ "TIDVU", "OLPIB", "-----", "ERNOV", "LUGAS", "ROSBI", "-----"],
            "tagLayout": "SSOtest",
            "destinationAirports": [ "EKCH" ]
        }
    },
    "tagLayouts": {
        "myLayout": [
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
        "SSOtest": [
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
            { "source": "holdingFix", "width": 5 },
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





![Window](https://i.gyazo.com/abd832a844331f03635ee72e5562ee13.png)

![Window](https://gyazo.com/f48c47b6b9004a6428d16ab38e817757.png)

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
