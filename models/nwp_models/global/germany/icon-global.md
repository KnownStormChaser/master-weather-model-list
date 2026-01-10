# ICON Global (Icosahedral Nonhydrostatic)

## What this model is
ICON Global is the **global deterministic numerical weather prediction (NWP) model** operated by the German Weather Service (**DWD**).

It predicts atmospheric conditions such as temperature, wind, precipitation, pressure, humidity, and clouds worldwide using a non-hydrostatic dynamical core on an icosahedral (triangular) grid.

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
- **Typical horizontal resolution:** ~13 km (native triangular grid)
- **Vertical levels:** 120 (operational setup), model top ~75 km 
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC) *(typical operational schedule)*  
- **Forecast length:** commonly up to ~180 hours for main cycles (varies by cycle/product)

---

## What it provides
- Deterministic forecasts of:
  - temperature
  - wind
  - precipitation
  - pressure
  - humidity
  - cloud and hydrometeor fields
- Boundary/forcing data for downstream applications and nested systems (e.g., regional ICON domains and wave models) 

---

## Data availability
- **Is the data free?** Yes (subset via Open Data)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://opendata.dwd.de/weather/nwp/icon/

---

## Notes
DWD documents that a subset of ICON analysis/forecast fields is publicly available via the DWD Open Data server. 

---

## Official documentation
- DWD model description page:
  https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
