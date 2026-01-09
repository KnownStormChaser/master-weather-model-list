# HMON (Hurricane Multi-scale Ocean-coupled Non-hydrostatic Model)

## What this model is
The Hurricane Multi-scale Ocean-coupled Non-hydrostatic (HMON) model is a **specialized tropical cyclone forecast model** developed by NOAA to complement other hurricane prediction systems.

It emphasizes ocean–atmosphere coupling to better represent storm intensity changes.

---

## Who runs it
- **Organization:** : National Centers for Environmental Prediction
- **Country / region:** United States

---

## When this model runs
HMON runs **only when an active tropical cyclone exists** within its supported basins.

Forecasts are generated for individual storms using storm-centered moving domains.

---

## What area it covers
- **Coverage:** Storm-centered domains following active tropical cyclones  
  (Atlantic and Eastern/Central Pacific basins)

---

## Basic details
- **Model type:** Tropical cyclone NWP model
- **Core system:** FV3-based
- **Horizontal resolution:**  
  - Outer domain: ~13 km  
  - Inner moving nest(s): ~3 km
- **Forecast length:** Up to ~126 hours
- **Update frequency:** Every 6 hours (per active storm)

---

## What it predicts
- Tropical cyclone track
- Storm intensity (maximum winds, central pressure)
- Wind fields
- Precipitation
- Storm structure and evolution
- Ocean–atmosphere interaction effects

---

## Relationship to other models
HMON operated alongside HWRF as part of NOAA’s tropical cyclone modeling suite.

As of **31 October 2025**, no new HMON forecast runs have been released.  
It is **unclear whether this reflects model retirement or a lack of qualifying storms** during this period.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/hmon/prod/

---

## Notes
Users should verify the availability of HMON forecasts for specific storms and dates.

For current operational guidance, NOAA primarily relies on **HAFS**, but historical and archived HMON output remains accessible.

---

## Official documentation
- 
