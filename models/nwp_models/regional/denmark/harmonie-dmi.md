# HARMONIE–AROME (DMI)

## What this model is
HARMONIE–AROME is a high-resolution regional numerical weather prediction (NWP) model operated by the Danish Meteorological Institute (DMI) for short-range forecasting over Northern Europe and the North Atlantic.

DMI runs two closely related operational configurations of the model — **DINI** and **IG** — both based on the HARMONIE–AROME system and using convection-permitting grid spacing.

---

## Who runs it
- **Organization:** Danish Meteorological Institute
- **Country / region:** Denmark

---

## Model configurations

### DINI
- **Coverage:** Denmark, Iceland, Netherlands, Ireland, and surrounding seas
- **Horizontal resolution:** ~2 km
- **Forecast length:** Up to ~60 hours
- **Primary use:** Short-range forecasting over Northwestern Europe

### IG
- **Coverage:** Iceland and Greenland
- **Horizontal resolution:** ~2 km
- **Forecast length:** Up to ~72 hours
- **Primary use:** High-resolution forecasting for North Atlantic and Arctic regions

Both configurations use lateral boundary conditions from the ECMWF global forecasting system.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system:** HARMONIE–AROME
- **Current cycle:** 43h
- **Horizontal resolution:** ~2 km
- **Update frequency / cycles:**  
  Internally runs hourly; deterministic output distributed every 3 hours  
  (00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Temporal output resolution:** 1 hour

---

## What it provides
High-resolution forecasts optimized for mesoscale and high-impact weather, including:
- Convective precipitation and cloudbursts
- Strong winds and rapidly evolving storm systems
- Near-surface temperature, wind, humidity, pressure, and cloud fields

HARMONIE–DMI output is also used as atmospheric forcing for DMI’s downstream marine models, including wave and storm-surge prediction systems.

---

## Coordinate system
- Uses a **Lambert conformal conic projection**
- Wind components are defined relative to the rotated model grid

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official open-data access:**  
  https://registry.opendata.aws/dmi-opendata/

---

## Notes
- DINI and IG share the same core model configuration but differ in domain extent and forecast length.
- Model resolution and configuration are optimized for convection-permitting short-range forecasts.
- Output availability and timing depend on operational constraints and upstream boundary data delivery.

---

## Official documentation
- DMI: *Forecast Data Weather Model (HARMONIE) for DINI and IG*  
  https://www.dmi.dk/friedata/dokumentation/data/forecast-data-weather-model-harmonie-for-dini-and-ig
- Bengtsson et al. (2017): *The HARMONIE–AROME Model Configuration*  
  https://doi.org/10.1175/MWR-D-16-0417.1
