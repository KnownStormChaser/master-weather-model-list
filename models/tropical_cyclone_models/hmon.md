# HMON (Hurricanes in a Multi-scale Ocean-coupled Non-hydrostatic Model)

## What this model is
HMON (Hurricanes in a Multi-scale Ocean-coupled Non-hydrostatic Model) is a specialized tropical cyclone numerical weather prediction system developed by NOAA to forecast hurricane track and intensity.

HMON was introduced as a replacement for the legacy GFDL Hurricane Model and served as an intermediate step toward the unification of NOAA’s operational models within the NOAA Environmental Modeling System (NEMS) framework. 

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP Environmental Modeling Center)
- **Country / region:** United States

---

## When this model runs
HMON is run on demand for individual tropical cyclones when triggered by the National Hurricane Center (NHC).

Each forecast is storm-centered rather than tied to a fixed geographic domain.

---

## What area it covers
- **Coverage:** Storm-centered domains following active tropical cyclones
- **Basins:**  
  Atlantic, Eastern Pacific, and Central Pacific

---

## Basic details
- **Model type:** Tropical cyclone NWP (deterministic)
- **Model system / core:**  
  NMMB (Non-hydrostatic Multi-scale Model on a B grid)
- **Horizontal resolution:**  
  - Parent domain: ~13 km  
  - Inner moving nest(s): ~3 km
- **Vertical levels:** 43
- **Model top:** ~50 hPa
- **Forecast length:**  
  Up to 126 hours
- **Update frequency / cycles:**  
  4× daily (00, 06, 12, 18 UTC), per active storm
- **Temporal output resolution:**  
  Typically 3-hourly

---

## Ocean coupling
HMON includes atmosphere–ocean coupling:
- Coupled to the HYCOM ocean model for Eastern and Central Pacific basins
- Operates uncoupled in the Atlantic basin

This coupling was intended to improve representation of air–sea interaction and intensity change.

---

## What it provides
Deterministic storm-scale forecasts of:
- Tropical cyclone track
- Maximum winds and central pressure
- Storm structure and size
- Rainfall distribution
- Near-storm wind fields

HMON demonstrated skill improvements over the legacy GFDL Hurricane Model in track and intensity forecasts across multiple basins.

---

## Relationship to other models
HMON replaced the discontinued GFDL Hurricane Model and operated alongside HWRF as part of NOAA’s tropical cyclone modeling suite.

It has since been superseded by the Hurricane Analysis and Forecast System (HAFS), which incorporates newer infrastructure and modeling approaches.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/hmon/prod/

---

## Notes
- HMON does not include a dedicated data assimilation system.
- The model uses storm-following, two-way interactive nested grids.
- While no longer NOAA’s primary hurricane system, HMON remains an important transitional model in the evolution of U.S. operational tropical cyclone forecasting.

---

## Official documentation
- NOAA / NCEP HMON description
