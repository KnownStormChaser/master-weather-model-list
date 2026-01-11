# RDPS (Regional Deterministic Prediction System)

## What this model is
The Regional Deterministic Prediction System (RDPS) provides higher-resolution short-range deterministic weather forecasts focused on North America.

As of recent operational upgrades, RDPS is no longer produced using a standalone limited-area model. Instead, it is generated from a global GEM configuration at approximately 10 km resolution, with forecast products distributed on a rotated regional grid covering North America.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** North America  
- **Domain details:**  
  Regional output extracted from a global 10 km GEM run and provided on a rotated latitude–longitude grid

---

## Basic details
- **Model type:** Deterministic NWP (regional-domain output from global system)
- **Model system / core:** GEM
- **Horizontal resolution:**  
  ~10 km
- **Vertical levels:** 84 hybrid staggered levels
- **Model top:** ~0.1 hPa
- **Forecast length:**  
  Up to ~84 hours
- **Update frequency / cycles:**  
  4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:**  
  Typically hourly to 3-hourly

---

## What it provides
Deterministic short-range forecasts of:
- Temperature, wind, humidity, and pressure
- Precipitation amount and type
- Cloud cover and near-surface conditions
- Aviation- and public-weather–relevant fields

RDPS provides the primary deterministic NWP guidance for forecast days 1 and 2 in Canada.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_rdps/readme_rdps-datamart_en/

---

## Notes
- RDPS shares the same modern GEM dynamical core and vertical structure as GDPS.
- The shift from a limited-area configuration to a global 10 km configuration simplifies system maintenance and improves consistency with global guidance.
- RDPS plays a role similar to the U.S. **NAM**, but with tighter integration into a global modeling framework.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_rdps/readme_rdps_en/
