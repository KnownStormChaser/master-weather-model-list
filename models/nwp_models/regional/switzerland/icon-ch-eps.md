# ICON-CH-EPS (Switzerland)

## What this model is
ICON-CH-EPS is a **high-resolution regional ensemble numerical weather prediction (NWP) system** operated by MeteoSwiss.

It consists of two nested ensemble configurations, **ICON-CH1-EPS** and **ICON-CH2-EPS**, designed to predict atmospheric conditions over Switzerland and its surroundings while explicitly representing forecast uncertainty.

---

## Who runs it
- **Organization:** MeteoSwiss
- **Country / region:** Switzerland

---

## What area it covers
- **Coverage:** Switzerland and surrounding regions

---

## Basic details
- **Model type:** Regional ensemble NWP (convection-permitting)
- **Model system:** ICON

### ICON-CH1-EPS
- **Horizontal resolution:** ~1 km (native icosahedral grid)
- **Ensemble members:** 11
- **Forecast length:** 33 hours
- **Update frequency:** Every 3 hours
- **Temporal output resolution:** 1 hour

### ICON-CH2-EPS
- **Horizontal resolution:** ~2.1 km (native icosahedral grid)
- **Ensemble members:** 21
- **Forecast length:** 120 hours (up to 5 days)
- **Update frequency:** Every 6 hours
- **Temporal output resolution:** 1 hour

### Vertical grid
- **Vertical coordinate:** Height-based, terrain-following
- **Vertical levels:** 80 full levels (81 half levels)

---

## What it provides
- Probabilistic forecasts of:
  - temperature
  - wind
  - precipitation
  - pressure
  - humidity
  - cloud and hydrometeor fields
- Both **full ensemble members** and the **unperturbed control run** are available.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Availability window:** 24 hours after publication
- **Official download location:**  
  https://opendatadocs.meteoswiss.ch/e-forecast-data/e2-e3-numerical-weather-forecasting-model

---

## Notes
ICON-CH-EPS is MeteoSwiss’s primary **ensemble forecasting system** and is especially well-suited for complex terrain such as the Alps.

Analysis fields are not distributed directly; forecast data at **lead time 0** should be used as an analysis equivalent.

---

## Official documentation
- https://opendatadocs.meteoswiss.ch/e-forecast-data/e2-e3-numerical-weather-forecasting-model
