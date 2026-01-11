# HRRR (High-Resolution Rapid Refresh)

## What this model is
The High-Resolution Rapid Refresh (HRRR) is a convection-allowing, high-resolution regional numerical weather prediction model designed for very short-range forecasting of rapidly evolving weather such as thunderstorms, fog, low clouds, wind shifts, and precipitation.

It is optimized for near-surface and storm-scale prediction and is widely used in severe weather, aviation, fire weather, and short-term decision support across the United States.

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** United States (CONUS)
- **Domain details:** Separate operational domains also exist for Alaska, Hawaii, and Puerto Rico.

---

## Basic details
- **Model type:** Deterministic NWP
- **Model system / core:** WRF-ARW
- **Horizontal resolution:** ~3 km
- **Vertical levels:** 50
- **Model top:** ~15–20 mb (hybrid sigma–isobaric)
- **Forecast length:**  
  - Up to 48 h for 00/06/12/18 UTC cycles  
  - Shorter forecasts for intermediate cycles
- **Update frequency / cycles:** Hourly (24× daily)
- **Temporal output resolution:**  
  - 15-minute output during early forecast hours  
  - Hourly output at longer lead times

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:**  
  - Hourly cycling Gridpoint Statistical Interpolation (GSI)  
  - Intensive radar reflectivity and radial velocity assimilation  
  - Cloud and hydrometeor data assimilation during a 1-hour pre-forecast spinup 

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** RAP analysis
- **Boundary conditions:** RAP, updated hourly

---

## What it provides
Deterministic short-range forecasts of:
- Near-surface temperature, humidity, wind, and pressure
- Convection and severe storm evolution
- Precipitation type and intensity
- Low clouds, ceilings, visibility, fog
- Fire-weather-relevant fields (wind, humidity, smoke variants)

HRRR is primarily intended for surface, boundary-layer, and storm-scale analysis rather than synoptic-scale upper-air diagnostics.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/hrrr/prod/  
  https://registry.opendata.aws/noaa-hrrr-pds/

---

## Notes
- HRRR is a convection-allowing model and does not use a cumulus parameterization.
- It is tightly coupled with RAP, which supplies its initial and boundary conditions.
- Experimental variants exist (e.g., HRRR-Smoke, HRRR-DAS), but this entry describes the primary deterministic operational system.

---

## Official documentation
- https://rapidrefresh.noaa.gov/hrrr/
