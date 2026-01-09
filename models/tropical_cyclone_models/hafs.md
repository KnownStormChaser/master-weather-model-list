# HAFS (Hurricane Analysis and Forecast System)

## What this model is
The Hurricane Analysis and Forecast System (HAFS) is a **specialized numerical weather prediction model** designed specifically for forecasting tropical cyclones.

Unlike general weather models, HAFS uses **storm-centered moving nests** to provide high-resolution forecasts of hurricane structure, intensity, track, and associated hazards.

---

## Who runs it
- **Organization:** : National Centers for Environmental Prediction
- **Country / region:** United States

---

## When this model runs
HAFS **only runs when an active tropical cyclone exists**.

Each forecast is tied to a specific storm and forecast cycle rather than a fixed geographic domain.

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
- Related hazards (e.g. heavy rain, wind impacts)

---

## Relationship to other models
HAFS is NOAA’s **next-generation operational hurricane model**, replacing earlier systems such as HWRF and HMON.

It is designed to integrate more closely with NOAA’s global modeling framework.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/hafs/prod/

---

## Notes
HAFS forecasts should be interpreted in the context of tropical cyclone guidance and are not intended for general weather forecasting outside active storms.

Storm availability and output depend on basin activity and operational scheduling.

---

## Official documentation
- https://wpo.noaa.gov/the-hurricane-analysis-and-forecast-system-hafs/
