# ICON-EPS (ICON Global Ensemble Prediction System)

## What this model is
ICON-EPS is DWD's operational global ensemble prediction system. It is the ensemble counterpart of DWD's deterministic ICON global model and uses the same model core, produced operationally since January 2018.

ICON-EPS generates 40 ensemble members on a global grid with a two-way-nested higher-resolution subdomain over Europe — the European nest portion is distributed separately as ICON-EU-EPS (see that entry). The system provides probabilistic forecasts of short- to medium-range weather for up to 7.5 days, with uncertainty captured through both ensemble data assimilation (initial conditions) and physics parameter perturbations (model uncertainty).

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country:** Germany

---

## What area it covers
- **Coverage:** Global
- **Nested subdomain:** European nest at higher resolution (see ICON-EU-EPS entry for the regional ensemble)

---

## Basic details
- **Model type:** Global ensemble prediction system
- **Underlying core:** ICON (Icosahedral Nonhydrostatic) model — same as the deterministic ICON
- **Number of ensemble members:** 40
- **Horizontal resolution:** ~40 km (R2B06 icosahedral grid) globally; ~20 km (R2B07) over the European nest
- **Vertical levels:** 90 levels to a height of 75 km
- **Forecast length:** +180 hours (7.5 days) for 00 and 12 UTC runs, +120 hours for 06 and 18 UTC runs, shorter for intermediate runs
- **Update frequency:** 8× daily (every 3 hours: 00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Temporal output resolution:**
  - Hourly up to +78 hours
  - 3-hourly from +81 to +120 hours
  - 6-hourly or 12-hourly for selected fields from +120 to +180 hours

---

## How the ensemble is generated

### Initial conditions
ICON-EPS is initialized by a **Local Ensemble Transform Kalman Filter (LETKF)**-based Ensemble Data Assimilation (EDA) system. The EDA operates on a 3-hour assimilation cycle, producing 40 slightly different analysis states at the end of each cycle that become the initial conditions for the next ensemble forecast.

### Model uncertainty
Model uncertainty is represented through **physics parameter perturbations**: before each model run, a set of defined parameter perturbations is randomly assigned to the ensemble members. A constraint ensures each forecast run perturbs the same number of parameters, but the distribution across members varies run-to-run. Parameters are not varied spatially or temporally during a forecast.

### Use as boundary conditions
ICON-EPS provides boundary conditions for DWD's regional ensembles (ICON-EU-EPS for the European nest, ICON-D2-EPS via forecasts from members 1 to 20).

---

## What it provides
- Full set of surface and upper-air fields (temperature, wind, humidity, pressure, precipitation, cloud, etc.)
- Probabilistic products derived from the 40-member ensemble
- Ensemble statistics, exceedance probabilities, and spread information

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data format:** GRIB2
- **Primary access:** DWD Open Data Server
  - ICON-EPS: https://opendata.dwd.de/weather/nwp/icon-eps/grib/
- **Licence:** DWD Open Data licence (CC-BY 4.0-compatible; attribution required)
- **Retention note:** DWD retains GRIB2 files on the open data server for a short period only (typically 24 hours). Users needing longer-term archives must set up their own retention or use third-party archives.

---

## Notes
- ICON-EPS is the ensemble counterpart of the deterministic ICON global model — see the ICON entry for the shared model core, physics, and domain details.
- The European nest portion of ICON-EPS is distributed separately as ICON-EU-EPS.
- As of v2.6.4 (late 2024), ICON uses 90 vertical levels. Earlier ICON-EPS versions used 60 levels.

---

## Official documentation
- DWD ICON model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
- DWD ensemble prediction overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/04_ensemble_methods/ensemble_prediction/ensemble_prediction_node.html
- DWD Open Data Server root: https://opendata.dwd.de/weather/nwp/
