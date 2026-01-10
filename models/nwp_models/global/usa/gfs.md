# GFS (Global Forecast System)

## What this model is
The Global Forecast System (GFS) is a **global deterministic numerical weather prediction (NWP) model** operated by the U.S. National Centers for Environmental Prediction (NCEP).

It produces medium- to extended-range forecasts of atmospheric, land, and near-surface conditions worldwide.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global deterministic NWP
- **Dynamical core:** FV3 (Finite-Volume Cubed-Sphere)
- **Data assimilation:** GSI (Grid-Point Statistical Interpolation)
- **Native horizontal resolution:** ~13 km (C768 cubed-sphere grid)
- **Public output grids:**  
  - ~0.25° (~28 km) for most standard products  
  - ~0.5° (~50–70 km) for extended-range products
- **Vertical levels:** 127 (surface to mesopause)
- **Forecast length:** Up to 16 days
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)

---

## What it provides
Deterministic forecasts of:
- temperature, wind, pressure, humidity
- precipitation and cloud fields
- land and soil variables (e.g. soil moisture, surface fluxes)
- additional atmospheric tracers (e.g. ozone, by product)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://www.ncei.noaa.gov/products/weather-climate-models/global-forecast  
  https://nomads.ncep.noaa.gov/

---

## Notes
The operational GFS uses **warm-start initial conditions** derived from the Global Data Assimilation System (GDAS).

GFS serves as the deterministic counterpart to the **Global Ensemble Forecast System (GEFS)**.

---

## Official documentation
- https://www.weather.gov/media/notification/pdfs/scn20-99gfs_v16_implementation.pdf
- https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/gfs.php
