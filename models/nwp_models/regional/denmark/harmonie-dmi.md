# HARMONIE – DMI (NEA)

## What this model is
HARMONIE–DMI is a **high-resolution regional numerical weather prediction (NWP) model** operated by the Danish Meteorological Institute.

It is based on the **HARMONIE-AROME modeling system** and is optimized for short-range forecasting of mesoscale weather phenomena such as convective precipitation, rapidly deepening lows, and strong winds over Northern Europe and the North Atlantic.

---

## Who runs it
- **Organization:** Danish Meteorological Institute (DMI)
- **Country / region:** Denmark

---

## What area it covers
- **Domain name:** NEA (North-East Atlantic)
- **Coverage:** Northwestern Europe and the North Atlantic, including Denmark, surrounding European regions, Iceland, and adjacent seas
- **Geographic extent (approx.):**
  - Longitude: 28°W – 37°E
  - Latitude: 44°N – 73°N

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system:** HARMONIE-AROME
- **Horizontal resolution:** **~2.5 km** (1/40°)
- **Vertical levels:** **65**
- **Forecast length:** **~54–56 hours**
- **Update frequency:** 8× daily  
  *(deterministic forecasts distributed every 3 hours: 00, 03, 06, 09, 12, 15, 18, 21 UTC)*
- **Temporal output resolution:** 1 hour

---

## Model characteristics
- Designed for **small-scale, high-impact weather**
- Strong performance for:
  - convective precipitation and cloudbursts
  - rapidly evolving storm systems
- Provides atmospheric forcing for DMI’s downstream marine models:
  - **WAM** (wave model)
  - **DKSS** (storm surge model)

---

## Coordinate system
- Uses a **rotated pole latitude/longitude grid**
- South pole located at approximately **26.5°E, 40°S**
- Coordinates must be rotated to standard lat/lon before use in many GIS tools

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (API access also available)
- **Official download location:**  
  https://www.dmi.dk/frie-data/

---

## Notes
Although HARMONIE runs internally every hour, DMI distributes only selected deterministic runs. The NEA configuration replaces older domains (e.g. IGB) and represents DMI’s primary regional atmospheric forecast model.

---

## Official documentation
- https://www.dmi.dk/frie-data/prognosedata  
- https://www.dmi.dk/frie-data/dokumentation/data/forecast-data-weather-model-harmonie
