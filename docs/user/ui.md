# User Interface

** Images taken from version 0.2.7 **

The userinterface essentially consists of two different areas

1. The Timeline showing inbound aircraft to the airports as defined for the selected Timeline(s).

2. The inbound statistics for the same airports

The base data for the timelines and statistics is defined in the config file  See [Config File Description](../config/config-description.md)

## Main window

The total view can be seen below:
![ALB main window](../img/Full screenshot ESandALB.png)

[Open full size](../img/Full screenshot ESandALB.png)

## Arrival statistics

The statistics area is preseted in the top right and will overlap the upper part of the timeline.

For each ViaFix the following is displayed.
- AR: Arrival Rate (minuts between each release)
- Cp: Capacity (60 min / AR)
- ViaFx: The Inbound fix in question (typically the holding fix)
- 30': The PERCENTAGE distribution of the aicraft arriving within the first 30 mints (excl. aircraft in the TMA)
- 60': The NUMBER of aircraft arriving within the next 60 mints
- Hold: The NUMBER of aircraft cleared in hold
- 15': 4 intervals of 15 minuts with the NUMBER of aircraft in the given interval (0-15, 15-30, 30-45, 45-60)
- TMA: The NUMBER of aircraft in the TMA.

ViaFix lines with ------ are subtotals.
The TOTAL line is self explanatory
The Capacity line is based on the Planned Arrival Rate shown on the top line. Eg. 40 arrivals per hour leads to 10 each 15 mints.

![ALB stats](../img/ALB-Stats area.png)

## Drop down menues
You adjust the plugin via the drop down menues.

You can select one or more timelines.
A timeline is based on Final Approach Fixes with a number of associated ViaFixes
![ALB Dropdown Timeline](../img/ALB-Popup-Timelines.png)

You can dim all aircraft for ViaFixes not of interest via the menu:
![ALB Dropdown ViaFixes](../img/ALB-Popup-ViaFixes.png)

You can update all Arrival Rates at once by clicking one of the predefined scenarios
![ALB Dropdown Scenarios](../img/ALB-Popup-Scenarios.png)

You can choose between defined layouts of the timeline as defined in the config file:
![ALB Dropdown Layout](../img/ALB-Popup-Layout.png)

## Other Manipulation of the User interface
- You can left/right click on the Arrival Rate (AR) for a given ViaFix - to decrease or increase the rate by one minut. (min 1, max 20)
- You can click on an aircraft in the list and ASEL of Euroscope will update accordingly. You can use this for eg. the .point command
- You can click on an aircraft in any list in TopSky or on Euroscope and a box will surround the aircraft if it is relevant and on the timeline.