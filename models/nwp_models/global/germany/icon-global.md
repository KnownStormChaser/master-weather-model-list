# ICON Global (Icosahedral Nonhydrostatic)

## What this model is
ICON Global is the **global deterministic numerical weather prediction (NWP) model** operated by the German Weather Service (DWD).

It predicts atmospheric conditions such as temperature, wind, precipitation, pressure, humidity, and clouds on a global scale using a modern non-hydrostatic dynamical core and an icosahedral grid.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst
- **Country / region:** Germany

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global deterministic NWP
- **Core system:** ICON (Icosahedral Nonhydrostatic)
- **Typical horizontal resolution:** ~13 km
- **Vertical levels:** 90 (model top ~75 km)
- **Forecast length:**
  - 00 & 12 UTC cycles: up to 180 hours (~7.5 days)
  - 06 & 18 UTC cycles: up to 120 hours (5 days)
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)

---

## Data assimilation
ICON Global uses a **3-hourly data assimilation cycle** based on a 3D-Var approach to generate initial conditions for each forecast run.

---

## What it provides
- Deterministic forecasts of:
  - temperature
  - wind
  - precipitation
  - pressure
  - humidity
  - cloud and hydrometeor fields
- Lateral boundary conditions for DWD regional models (ICON-EU, ICON-D2)
- Input forcing for downstream applications such as wave and dispersion models

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://opendata.dwd.de/weather/nwp/icon/

---

## Notes
ICON uses a global **icosahedral triangular grid**, avoiding the pole singularities of traditional latitude–longitude grids and providing nearly uniform global resolution.

Forecast output is written hourly during the early part of the forecast to support regional nesting, then reduced to 3-hourly output later in the run.

---

## Official documentation
- https://www.dwd.de/EN/ourservices/opendata/opendata.html
- https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
