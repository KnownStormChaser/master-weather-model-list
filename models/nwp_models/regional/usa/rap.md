# RAP (Rapid Refresh)

## What this model is
The Rapid Refresh (RAP) is a high-frequency regional numerical weather prediction model designed for short-range forecasting of rapidly evolving weather, including fronts, wind shifts, temperature changes, and aviation-relevant conditions.

It serves as both a standalone forecast system and as the parent model providing initial and boundary conditions for the HRRR. 

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** North America

---

## Basic details
- **Model type:** Deterministic NWP
- **Model system / core:** WRF-ARW
- **Horizontal resolution:** ~13 km
- **Vertical levels:** 50
- **Model top:** ~10 mb (hybrid sigma–isobaric)
- **Forecast length:**  
  - Up to 51 h for 03/09/15/21 UTC cycles  
  - Up to ~21 h for other cycles
- **Update frequency / cycles:** Hourly (24× daily)
- **Temporal output resolution:** Hourly

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:**  
  - Hourly cycling Gridpoint Statistical Interpolation (GSI)  
  - Assimilation of conventional, aircraft, satellite, cloud, and hydrometeor observations to improve short-range cloud and precipitation forecasts 

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** RAP cycling analysis
- **Boundary conditions:** GFS, updated hourly

---

## What it provides
Deterministic short-range forecasts of:
- Temperature, wind, humidity, and pressure
- Cloud cover, ceilings, and visibility
- Precipitation and precipitation type
- Aviation-relevant weather fields

RAP is commonly used for aviation forecasting and mesoscale analysis and complements HRRR by providing broader-scale context at slightly lower resolution.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/rap/prod/

---

## Notes
- RAP and HRRR share much of the same modeling and assimilation infrastructure.
- RAP provides the initial and lateral boundary conditions for HRRR.
- Unlike HRRR, RAP uses a cumulus parameterization and is intended to retain synoptic-scale structure.

---

## Official documentation
- https://www.ncei.noaa.gov/products/weather-climate-models/rapid-refresh-update
