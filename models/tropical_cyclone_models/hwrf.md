# HWRF (Hurricane Weather Research and Forecasting Model)

## What this model is
The Hurricane Weather Research and Forecasting (HWRF) model is a **specialized tropical cyclone prediction model** used by NOAA for forecasting hurricane track, intensity, and structure.

It uses storm-centered moving nests to provide high-resolution forecasts focused on active tropical cyclones.

---

## Who runs it
- **Organization:** : National Centers for Environmental Prediction
- **Country / region:** United States

---

## When this model runs
HWRF runs **only when an active tropical cyclone exists**.

Each forecast is centered on an individual storm rather than a fixed geographic region.

---

## What area it covers
- **Coverage:** Storm-centered domains following active tropical cyclones  
  (Atlantic and Eastern/Central Pacific basins)

---

## Basic details
- **Model type:** Tropical cyclone NWP model
- **Core system:** WRF-based
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

---

## Relationship to other models
HWRF was one of NOAA’s primary operational hurricane models for many years.

While **HAFS is now NOAA’s primary next-generation tropical cyclone forecast system**, HWRF **continues to produce forecasts** and remains available for operational and reference use.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/hwrf/prod/

---

## Notes
HWRF output should be interpreted as **supplementary tropical cyclone guidance**, alongside newer systems such as HAFS.

---

## Official documentation
- https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/hwrf.php
