# COSMO-IL

## What this model is
COSMO-IL is a **high-resolution regional deterministic numerical weather prediction (NWP) model** operated by the Israel Meteorological Service (IMS).

It is based on the **COSMO (Consortium for Small-scale Modeling)** system and is specifically adapted for the **East Mediterranean / Levant region**, with a strong focus on short-range and high-impact weather.

---

## Who runs it
- **Organization:** Israel Meteorological Service (IMS)
- **Country / region:** Israel

---

## What area it covers
- **Coverage:** Israel and surrounding regions of the Eastern Mediterranean and Levant

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system:** COSMO
- **Horizontal resolution:** ~2.8 km
- **Vertical levels:** 60
- **Model top:** ~23 km
- **Forecast length:**  
  - Up to 90 hours (main operational runs)
  - Up to 12 hours (rapid-update runs during active weather)
- **Update frequency:**  
  - 2× daily (00Z, 12Z) for full-range forecasts  
  - Hourly short-range runs during rainy / high-impact events

---

## Initial and boundary conditions
- **Boundary conditions:** ECMWF deterministic IFS
- **Data assimilation:** Yes (real-time)

Assimilated observations include:
- weather radar
- surface observations
- radiosondes
- aircraft data
- ship observations

---

## What it provides
Deterministic forecasts of:
- temperature
- relative humidity
- wind
- surface and mean sea level pressure
- precipitation
- cloudiness
- snow accumulation
- additional diagnostic fields

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://ims.gov.il/en/node/179

---

## Notes
COSMO-IL is actively developed and verified by IMS, with special attention given to performance during rainfall and severe weather events.

Unlike ICON-IL, COSMO-IL uses **local data assimilation** and **rapid-update cycling** to improve short-range forecast skill.

---

## Official documentation
- https://ims.gov.il/en/node/179
