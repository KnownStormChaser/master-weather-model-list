# WRF-SMN Argentina

## What this model is
WRF-SMN Argentina is a **high-resolution, convection-permitting regional deterministic numerical weather prediction (NWP) model** operated by Argentina’s national weather service.

It is based on the **WRF-ARW (Advanced Research WRF)** dynamical core and is used operationally for short-range weather forecasting over Argentina and surrounding regions.

---

## Who runs it
- **Organization:** Servicio Meteorológico Nacional (SMN)
- **Country / region:** Argentina

---

## What area it covers
- **Coverage:** Argentina, adjacent South American regions, and nearby oceans
- **Map projection:** Lambert conformal
- **Grid center:** ~35°S, 65°W
- **Grid dimensions:** ~999 × 1249 grid points

---

## Basic details
- **Model type:** Regional deterministic NWP (non-hydrostatic, convection-permitting)
- **Model system:** WRF (ARW core, version 4.0)
- **Horizontal resolution:** ~4 km
- **Vertical levels:** 45 (model top at ~10 hPa)
- **Forecast length:** Up to 72 hours
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 1 hour (plus selected daily products)

---

## Model configuration
Key physical parameterizations include:
- **Microphysics:** WSM6
- **Longwave radiation:** RRTM
- **Shortwave radiation:** Dudhia
- **Planetary boundary layer:** MYJ
- **Land surface model:** NOAH (4-layer)

Initial and boundary conditions are provided by the **GFS** global model at 0.25° resolution.

---

## What it provides
Deterministic forecasts of near-surface and atmospheric fields including:
- temperature and humidity (2 m)
- wind speed and direction (10 m)
- surface pressure
- hourly and sub-hourly precipitation
- radiation fluxes
- soil temperature and soil moisture
- freezing level height
- daily minimum and maximum temperature products

Some surface variables are **statistically debiased** using in-situ observations.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-compliant)
- **Official download location:**  
  https://registry.opendata.aws/smn-ar-wrf-dataset/

---

## Notes
WRF-SMN Argentina explicitly resolves deep convection and is optimized for high-impact weather over southern South America.

Public datasets include deterministic forecasts with hourly, sub-hourly, and daily temporal aggregations, distributed via AWS Open Data.

---

## Official documentation
- https://odp-aws-smn.github.io/documentation_wrf_det/
