# Release Notes

See the GitHub Releases page for detailed change logs.

## 0.2.10 of 251215
##
- Config file format: 2
- Minimum compatible peer 0.2.9

### New features
- Automated EAT calculation
- Tag updating implemented in particular TAG_ITEM_GL_EAT_COMBI 

### Bug fixes
- Stability
### Known issues
- Aircraft are only cought if they have a STAR going to "targetFixes" as defined in the timeline. This means that aircraft without an (auto)assigned STAR will not show up on the timeline and not be part of the stats.



## 0.2.7 of 251027

### New features
- FMR claim and resign TX/RX
- AR set and TX/RX
- SEAT TX/RX

### Bug fixes
- Stability

### Known issues
- inTMA is a rough implementation based on ac passing the next fix after the holding Fix, For EKCH this is particularly important to understand for ERNOV arrivals.
- Currently the Controlpanel overlays the top of the ribon
- Aircraft are only cought if they have a STAR going to "targetFixes" as defined in the timeline. This means that aircraft without an (auto)assigned STAR will not show up on the timeline and not be part of the stats.



## 0.2.1e

### Known issues
- inTMA is a rough implementation based on ac passing the next fix after the holding Fix, For EKCH this is particularly important to understand for ERNOV arrivals.
- Currently the Controlpanel overlays the top of the ribon
- Aircraft are only cought if they have a STAR going to "targetFixes" as defined in the timeline. This means that aircraft without an (auto)assigned STAR will not show up on the timeline and not be part of the stats.


