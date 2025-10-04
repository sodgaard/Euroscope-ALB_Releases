# Example config file:

```json
{
    "openAutomatically": false,
    "timelines": {
        "EKCH 04L/04R": {
            "destinationAirports": [ "EKCH" ],
            "targetFixes": [ "CH4LF", "CH4RF" ],
            "viaFixes": [  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],
            "ArrivalScenarios": {
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
            "tagLayout": "Expanded"
        },
        "EKCH 22L/22R": {
            "destinationAirports": [ "EKCH" ],
            "targetFixes": [ "CH2LF", "CH2RF" ],
            "viaFixes": [  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],
            "ArrivalScenarios": {
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
            "tagLayout": "Expanded"
        },
        "EKCH 12": {
            "destinationAirports": [ "EKCH" ],
            "targetFixes": [ "VECJA" ],
            "viaFixes": [  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],
            "ArrivalScenarios": {
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
             "tagLayout": "Expanded"
        },
        "EKCH 30": {
            "destinationAirports": [ "EKCH" ],
            "targetFixes": [ "ORZIH" ],
            "viaFixes": [  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],
            "ArrivalScenarios": {
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
        "Simple": [
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

