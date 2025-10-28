# User Interface

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

![ALB stats](../img/ALB-Stats area.png)

## Drop down menues
You adjust the plugin via the drop down menues:
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