# ALADIN Slovakia (ALADIN/SHMÚ)

## What this model is
ALADIN Slovakia is a **regional limited-area numerical weather prediction (NWP) system** operated by the Slovak Hydrometeorological Institute.

It is based on the **ALADIN modeling system** and uses the **ALARO Canonical Model Configuration**, optimized for short-range forecasting over complex Central European terrain, including mountainous regions.

---

## Who runs it
- **Organization:** Slovak Hydrometeorological Institute (SHMÚ)
- **Country / region:** Slovakia

---

## What area it covers
- **Coverage:** Slovakia and a large part of Central Europe
- **Model domain:** Continental Central Europe with Slovakia near the center

---

## Basic details
- **Model type:** Regional deterministic NWP (hydrostatic, limited-area)
- **Model system:** ALADIN (ALARO CMC)
- **Horizontal resolution:** **4.5 km**
- **Vertical levels:** **63**
- **Model top:** ~10 hPa
- **Forecast length:** Up to **72 hours**
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 1 hour

---

## Model configuration
- **Physics package:** ALARO-1vB
- **Radiation:** ACRANEB2
- **Turbulence:** TOUCANS scheme
- **Deep convection:** Unified ALARO mass-flux formulation

---

## Data assimilation and coupling
- **Upper-air initialization:** Spectral blending (digital filter technique)
- **Surface data assimilation:** CANARI optimal interpolation
- **Coupling model:** ARPEGE (3-hourly coupling frequency)

---

## What it provides
Deterministic forecasts of:
- temperature, humidity, and wind (3D atmosphere)
- surface and mean sea-level pressure
- hourly precipitation
- cloud cover and radiation variables
- derived parameters for warnings and hydrology

Outputs are used operationally for **weather forecasting, warnings, aviation, hydrology, and emergency management** in Slovakia.

---

## Data availability
- **Is the data free?** Yes  
- **Is the data downloadable?** Yes  
- **Data formats:** GRIB2  
- **Official download location:**  
  https://opendata.shmu.sk/meteorology/weather/nwp/aladin/

---

## Notes
The current ALADIN/SHMÚ configuration has been **fully operational since March 2017** and represents a major upgrade over earlier 9 km versions, with substantially improved performance in high-impact weather situations.

ALADIN/SHMÚ forms the backbone of Slovakia’s operational NWP system and is complemented by convection-permitting model experiments at higher resolution.

---

## Official documentation
- Derková et al., *Recent improvements in the ALADIN/SHMÚ operational system*, Meteorologický časopis, 2017
