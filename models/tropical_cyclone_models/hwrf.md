# HWRF (Hurricane Weather Research and Forecasting Model)

## What this model is
The Hurricane Weather Research and Forecasting (HWRF) model is a specialized tropical cyclone numerical weather prediction system developed by NOAA to forecast hurricane track, intensity, structure, and rainfall.

HWRF is a storm-centered, moving-nest model that runs only when an active tropical cyclone exists. It was designed specifically to resolve the inner-core structure of tropical cyclones and their interaction with the surrounding atmosphere and ocean.

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP Environmental Modeling Center)
- **Country / region:** United States

---

## When this model runs
HWRF is run on demand for individual tropical cyclones when triggered by:
- National Hurricane Center (NHC)
- Central Pacific Hurricane Center (CPHC)
- Joint Typhoon Warning Center (JTWC)

Each run is centered on a specific storm rather than a fixed geographic domain.

---

## What area it covers
- **Coverage:** Storm-centered, basin-dependent domains
- **Basins:**  
  Atlantic, Eastern & Central Pacific, Western Pacific, North Indian Ocean, and Southern Hemisphere basins

---

## Basic details
- **Model type:** Tropical cyclone NWP (deterministic)
- **Model system / core:**  
  WRF-NMM (Non-hydrostatic Mesoscale Model)
- **Horizontal resolution:**  
  - Parent domain: ~13.5 km  
  - Intermediate moving nest: ~4.5 km  
  - Inner moving nest: ~1.5 km
- **Vertical levels:** 75
- **Model top:** ~10 hPa
- **Forecast length:**  
  Up to 126 hours
- **Update frequency / cycles:**  
  4× daily (00, 06, 12, 18 UTC), per active storm
- **Temporal output resolution:**  
  3-hourly

---

## Data assimilation and initialization
HWRF employs a specialized hurricane initialization and data assimilation system that includes:
- Vortex relocation and intensity correction
- GSI-based hybrid ensemble–variational data assimilation (HDAS)
- Assimilation of conventional observations, satellite radiances, aircraft data, and Tail Doppler Radar (TDR) when available

Initial and boundary conditions are provided by the GFS and GDAS systems. 

---

## Coupling
HWRF is an atmosphere–ocean coupled system:
- Coupled to MPIPOM-TC or HYCOM, depending on basin
- One-way coupling to WAVEWATCH III for selected basins in operations
- Designed to represent air–sea interaction critical for tropical cyclone intensity prediction 

---

## What it provides
Deterministic storm-scale forecasts of:
- Tropical cyclone track
- Maximum winds and central pressure
- Storm structure and size
- Rainfall distribution
- Near-storm wind and precipitation fields

HWRF output has historically been a major input to operational hurricane forecasting and research.

---

## Relationship to other models
HWRF was NOAA’s primary operational hurricane prediction system for many years.

It has since been superseded by the Hurricane Analysis and Forecast System (HAFS), which incorporates newer infrastructure and modeling approaches. HWRF continues to be run operationally for reference, legacy comparison, and research applications.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/hwrf/prod/

---

## Notes
- HWRF uses storm-following moving nests rather than a fixed domain.
- The system includes tightly integrated atmosphere, ocean, data assimilation, and vortex-tracking components.
- While no longer NOAA’s primary hurricane system, HWRF remains an important reference model in tropical cyclone forecasting history and evaluation.

---

## Official documentation
- https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/hwrf.php
- Biswas et al. (2018), *HWRF Scientific Documentation*
