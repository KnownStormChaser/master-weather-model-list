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
- **Temporal output resolution (NBM v5.0, effective April 15, 2026):**  
  - Hourly out to 48 h for most parameters  
  - 3-hourly through ~192 h  
  - 6-hourly thereafter  
  - Some aviation and severe-weather fields (ceiling, visibility, cloud base, cloud layers, low-level wind shear, turbulence, Ellrod Index, solar radiation, thunderstorm coverage, thunderstorm probability, Craven-Wiedenfeld Aggregate Severe Parameter) continue to use the pre-v5.0 hourly-to-36 h cadence

---

## What it provides
Statistically calibrated deterministic and probabilistic forecast guidance for:
- Near-surface temperature, dewpoint, apparent temperature, wind, and pressure
- Precipitation amount, type, and probability-matched mean (PMM) QPF (24h, CONUS/AK)
- Snow and ice guidance, including PMM snow/ice, calibrated snow exceedance (24h/48h/72h, CONUS), and deterministic snow depth (CONUS/AK)
- Sky cover, ceilings, and visibility
- Wind gusts and hazardous weather parameters
- Fire weather joint probabilities (wind speed × relative humidity combinations)
- Deterministic and probabilistic precipitable water
- Probabilistic surface-based CAPE (10th/50th/90th percentiles, CONUS)
- Wet bulb globe temperature in NBM text products (NBH/NBS/NBE/NBX)
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
- Bias correction methods include decaying averages, quantile mapping (QM), and dynamically weighted model contributions. NBM v5.0 expands QM usage to instantaneous temperature, dew point, apparent temperature, relative humidity, 12-hour max/min RH, and significant wave height — replacing decaying-average logic for those fields.
- NBM v5.0 introduces a new "percentile picking" method for deterministic wind speed and gust forecasts, modulating away from the mean QM value based on where the mean sits relative to the model distribution and climatology.
- NBM produces both deterministic and probabilistic guidance, and v5.0 increases consistency between them by applying shared input weighting and derivation methods.
- The Haines Index is no longer produced in NBM v5.0 (CONUS, AK, HI, PR domains).

---

## Version history

### NBM v5.0 (operational April 15, 2026)
Effective with the 13 UTC cycle on April 15, 2026, via NWS Service Change Notice 26-24 (March 12, 2026).

Key changes:
- Hourly guidance extended from 36 h to 48 h for most parameters
- New products: 24h PMM QPF and snow/ice (CO/AK), joint fire weather probabilities, quantile-mapped 10 m wind and gust for AK/HI/PR/GU/OC, precipitable water guidance, calibrated snow exceedance (CO), deterministic snow depth (CO/AK), apparent temperature, CAPE weighted percentiles (CO)
- Removed: Haines Index
- Methodology: Expanded QM usage, new percentile-picking wind/gust method, shared input weighting for deterministic and probabilistic outputs
- Ceiling and visibility guidance for Hawaii replaced with gridded LAMP "Meld" approach

Input changes in v5.0:
- Higher-resolution ECMWF (0.10°) and ECMWF Ensemble (0.20°), replacing 0.25° and 0.50°
- Higher-resolution GDPS (15 km) and REPS (10 km), replacing 25 km and 15 km
- Added ECAIFS (ECMWF AI/IFS) and AIGFS as inputs for temperature, wind speed, and QPF (all domains except no QPF over GU)
- Higher-resolution GEFS surface guidance
- WPC MMEBC QPF added as input (CO)
- SREF input usage eliminated
- Significant wave height bias correction now uses ~120 ensemble member inputs (up from 13 ensemble means)

## Official documentation
- https://vlab.noaa.gov/web/mdl/nbm
- https://vlab.noaa.gov/web/mdl/nbm-model-inputs
- NBM version history: https://vlab.noaa.gov/web/mdl/nbm-versions
- NWS SCN 26-24 (NBM v5.0 implementation): https://www.weather.gov/media/notification/pdf_2026/scn26-24NBM_V5.0.pdf
