# NBM (National Blend of Models)

## What this model is
The National Blend of Models (NBM) is a statistically post-processed forecast system that blends and calibrates output from many different numerical weather prediction models into a single, nationally consistent set of deterministic and probabilistic forecast guidance.

Rather than running its own atmospheric simulation, the NBM applies bias correction, weighting, and blending techniques to both NWS and non-NWS model guidance to produce a highly skillful starting point for official National Weather Service forecasts and the National Digital Forecast Database (NDFD).

---

## Who runs it
- **Organization:** NOAA / National Weather Service (Meteorological Development Laboratory)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** United States and surrounding regions
- **Domain details:**  
  Multiple operational domains, including CONUS, Alaska, Hawaii, Puerto Rico, Guam, Oceanic regions, and a coarse global domain

---

## Basic details
- **Model type:** Statistical multi-model blend (post-processed guidance)
- **Horizontal resolution:**  
  - CONUS: ~2.5 km  
  - Alaska: ~3.0 km  
  - Hawaii: ~2.5 km  
  - Puerto Rico: ~1.25 km  
  - Oceanic: ~10 km  
  - Global: ~50 km
- **Forecast length:**  
  Up to ~264 hours (11 days), depending on variable
- **Update frequency / cycles:** Hourly (24× daily)
- **Temporal output resolution:**  
  - Hourly out to ~36 h  
  - 3-hourly through ~192 h  
  - 6-hourly thereafter

---

## What it provides
Statistically calibrated deterministic and probabilistic forecast guidance for:
- Near-surface temperature, dewpoint, wind, and pressure
- Precipitation amount and type
- Sky cover, ceilings, and visibility
- Wind gusts and hazardous weather parameters
- Probabilistic guidance (percentiles, thresholds, and risk-based products)

NBM guidance is the primary foundation for official NWS gridded forecasts in the National Digital Forecast Database.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  https://registry.opendata.aws/noaa-nbm/  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/blend/prod/

---

## Notes
- NBM blends input from a large and evolving set of deterministic and ensemble models, including global, regional, and convection-allowing systems.
- Bias correction methods include decaying averages, quantile mapping, and dynamically weighted model contributions.
- NBM produces both deterministic and probabilistic guidance.

---

## Official documentation
- https://vlab.noaa.gov/web/mdl/nbm
- https://vlab.noaa.gov/web/mdl/nbm-model-inputs
