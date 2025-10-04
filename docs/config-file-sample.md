# Example `alb-config.json`:

# 

# ```json

# {

# &nbsp;   "openAutomatically": false,

# &nbsp;   "timelines": {

# &nbsp;       "EKCH 04L/04R": {

# &nbsp;           "destinationAirports": \[ "EKCH" ],

# &nbsp;           "targetFixes": \[ "CH4LF", "CH4RF" ],

# &nbsp;           "viaFixes": \[  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],

# &nbsp;           "ArrivalScenarios": {

# &nbsp;               "Green":      \[  2,  2,  2,  2,  2 ],

# &nbsp;               "Yellow":     \[  4, 10,  8,  8, 10 ],

# &nbsp;               "Yellow-ER":  \[  3, 10,  6,  6,  6 ],

# &nbsp;               "Yellow-TI":  \[  4,  6,  6, 10,  6 ],

# &nbsp;               "Yellow-ERTI":\[  4,  6,  8,  8,  6 ],

# &nbsp;               "Red-ER":     \[  3, 10,  6, 10,  6 ],

# &nbsp;               "Red-TI":     \[  6,  6,  6, 10,  6 ],

# &nbsp;               "Red-ERTI":   \[  6,  6,  8,  6,  8 ],

# &nbsp;               "Priority-O": \[  3, 10, 12, 12, 10 ],

# &nbsp;               "Priority-W": \[  6, 10,  6,  6, 10 ],

# &nbsp;               "Stop":       \[  0,  0,  0,  0,  0 ]

# &nbsp;            },

# &nbsp;           "tagLayout": "Expanded"

# &nbsp;       },

# &nbsp;       "EKCH 22L/22R": {

# &nbsp;           "destinationAirports": \[ "EKCH" ],

# &nbsp;           "targetFixes": \[ "CH2LF", "CH2RF" ],

# &nbsp;           "viaFixes": \[  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],

# &nbsp;           "ArrivalScenarios": {

# &nbsp;               "Green":      \[  2,  2,  2,  2,  2 ],

# &nbsp;               "Yellow":     \[  4, 10,  8,  8, 10 ],

# &nbsp;               "Yellow-ER":  \[  3, 10,  6,  6,  6 ],

# &nbsp;               "Yellow-TI":  \[  4,  6,  6, 10,  6 ],

# &nbsp;               "Yellow-ERTI":\[  4,  6,  8,  8,  6 ],

# &nbsp;               "Red-ER":     \[  3, 10,  6, 10,  6 ],

# &nbsp;               "Red-TI":     \[  6,  6,  6, 10,  6 ],

# &nbsp;               "Red-ERTI":   \[  6,  6,  8,  6,  8 ],

# &nbsp;               "Priority-O": \[  3, 10, 12, 12, 10 ],

# &nbsp;               "Priority-W": \[  6, 10,  6,  6, 10 ],

# &nbsp;               "Stop":       \[  0,  0,  0,  0,  0 ]

# &nbsp;            },

# &nbsp;           "tagLayout": "Expanded"

# &nbsp;       },

# &nbsp;       "EKCH 12": {

# &nbsp;           "destinationAirports": \[ "EKCH" ],

# &nbsp;           "targetFixes": \[ "VECJA" ],

# &nbsp;           "viaFixes": \[  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],

# &nbsp;           "ArrivalScenarios": {

# &nbsp;               "Green":      \[  2,  2,  2,  2,  2 ],

# &nbsp;               "Yellow":     \[  4, 10,  8,  8, 10 ],

# &nbsp;               "Yellow-ER":  \[  3, 10,  6,  6,  6 ],

# &nbsp;               "Yellow-TI":  \[  4,  6,  6, 10,  6 ],

# &nbsp;               "Yellow-ERTI":\[  6,  6,  8,  6,  8 ],

# &nbsp;               "Red-ER":     \[  3, 10,  6, 10,  6 ],

# &nbsp;               "Red-TI":     \[  6,  6,  6, 10,  6 ],

# &nbsp;               "Red-ERTI":   \[  6,  6,  8,  6,  8 ],

# &nbsp;               "Priority-O": \[  3, 10, 12, 12, 10 ],

# &nbsp;               "Priority-W": \[  6, 10,  6,  6, 10 ],

# &nbsp;               "Stop":       \[  0,  0,  0,  0,  0 ]

# &nbsp;            },

# &nbsp;            "tagLayout": "Expanded"

# &nbsp;       },

# &nbsp;       "EKCH 30": {

# &nbsp;           "destinationAirports": \[ "EKCH" ],

# &nbsp;           "targetFixes": \[ "ORZIH" ],

# &nbsp;           "viaFixes": \[  "OLPIB", "TIDVU", "-----", "ROSBI", "LUGAS", "ERNOV", "-----"],

# &nbsp;           "ArrivalScenarios": {

# &nbsp;               "Green":      \[  2,  2,  2,  2,  2 ],

# &nbsp;               "Yellow":     \[  4, 10,  8,  8, 10 ],

# &nbsp;               "Yellow-ER":  \[  3, 10,  6,  6,  6 ],

# &nbsp;               "Yellow-TI":  \[  4,  6,  6, 10,  6 ],

# &nbsp;               "Yellow-ERTI":\[  6,  6,  8,  6,  8 ],

# &nbsp;               "Red-ER":     \[  3, 10,  6, 10,  6 ],

# &nbsp;               "Red-TI":     \[  6,  6,  6, 10,  6 ],

# &nbsp;               "Red-ERTI":   \[  6,  6,  8,  6,  8 ],

# &nbsp;               "Priority-O": \[  3, 10, 12, 12, 10 ],

# &nbsp;               "Priority-W": \[  6, 10,  6,  6, 10 ],

# &nbsp;               "Stop":       \[  0,  0,  0,  0,  0 ]

# &nbsp;            },

# &nbsp;           "tagLayout": "Expanded"

# &nbsp;       }

# &nbsp;   },

# &nbsp;   "tagLayouts": {

# &nbsp;       "Standard": \[

# &nbsp;           { "source": "assignedRunway", "width": 4 },

# &nbsp;           { "source": "callsign", "width": 8 },

# &nbsp;           { "source": "static", "defaultValue": " ", "width": 2, "isViaFixIndicator": true },

# &nbsp;           { "source": "aircraftType", "width": 5 },

# &nbsp;           { "source": "aircraftWtc", "width": 2 },

# &nbsp;           { "source": "timeBehindPreceeding", "width": 5, "rightAligned": true },

# &nbsp;           { "source": "remainingDistance", "width": 4, "rightAligned": true },

# &nbsp;           { "source": "static", "width": 1 },

# &nbsp;           { "source": "directRouting", "width": 5, "rightAligned": true, "defaultValue": "-----" },

# &nbsp;           { "source": "static", "width": 1 },

# &nbsp;           { "source": "scratchPad", "width": 4 }

# &nbsp;       ],

# &nbsp;       "Expanded": \[

# &nbsp;           { "source": "assignedRunway", "width": 4 },

# &nbsp;           { "source": "acno", "width": 3 },

# &nbsp;           { "source": "callsign", "width": 8 },

# &nbsp;           { "source": "static", "width": 1 },

# &nbsp;           { "source": "timeBehindPreceeding", "width": 5, "rightAligned": true },

# &nbsp;           { "source": "static", "width": 1 },

# &nbsp;           { "source": "targetFixEta", "width": 7, "rightAligned": true },

# &nbsp;           { "source": "targetFixATCTime", "width": 7, "rightAligned": true },

# &nbsp;           { "source": "remainingDistance", "width": 4, "rightAligned": true },

# &nbsp;           { "source": "static", "width": 1 },

# &nbsp;           { "source": "viaFix", "width": 7 },

# &nbsp;           { "source": "inboundGrouping", "width": 7 },

# &nbsp;           { "source": "holdingFix", "width": 5 },

# &nbsp;           { "source": "static", "width": 1 },

# &nbsp;           { "source": "directRouting", "width": 5, "rightAligned": true, "defaultValue": "-----" },

# &nbsp;           { "source": "static", "width": 1 },

# &nbsp;           { "source": "ATCShort", "width": 4 } 

# &nbsp;       ],

# &nbsp;       "Simple": \[

# &nbsp;           { "source": "assignedRunway", "width": 4, "isViaFixIndicator": true },

# &nbsp;           { "source": "callsign", "width": 8 }

# &nbsp;       ]

# &nbsp;   },

# &nbsp;   "TMACtr" : \[

# &nbsp;           { "lat": 55.64336491 },

# &nbsp;           { "lon": 12.24763954 },

# &nbsp;           { "radius": 29 }

# 

# &nbsp;   ],

# &nbsp;   "TMAPoly" : \[

# &nbsp;          { "lat": 55.24944444444444, "lon": 11.680833333333332 },

# &nbsp;          { "lat": 55.42722222222222, "lon": 11.41 },

# &nbsp;          { "lat": 55.84666666666667, "lon": 11.362777777777778 },

# &nbsp;          { "lat": 55.985, "lon": 11.825833333333334 },

# &nbsp;          { "lat": 56.156388888888884, "lon": 12.412777777777778 },

# &nbsp;          { "lat": 56.16416666666667, "lon": 12.44 },

# &nbsp;          { "lat": 56.032777777777774, "lon": 12.656944444444445 },

# &nbsp;          { "lat": 56.032777777777774, "lon": 12.679444444444444 },

# &nbsp;          { "lat": 55.99944444444444, "lon": 12.732222222222223 },

# &nbsp;          { "lat": 55.976111111111116, "lon": 12.865555555555556 },

# &nbsp;          { "lat": 55.73277777777778, "lon": 13.115555555555556 },

# &nbsp;          { "lat": 55.24944444444444, "lon": 12.998888888888889 },

# &nbsp;          { "lat": 55.195277777777775, "lon": 11.979444444444445 },

# &nbsp;          { "lat": 55.24944444444444, "lon": 11.680833333333332 }

# 

# &nbsp;   ]

# }

# 

# 

# ```

