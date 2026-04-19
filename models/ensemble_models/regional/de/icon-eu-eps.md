# ICON-EU-EPS (ICON European Regional Ensemble Prediction System)

## What this model is
ICON-EU-EPS is DWD's operational regional ensemble prediction system for Europe. It is the European-nest portion of ICON-EPS (DWD's global ensemble) — the 40 ensemble members are produced by the two-way-nested European subdomain embedded in the global ICON ensemble, and then distributed separately as a regional ensemble product.

ICON-EU-EPS is the probabilistic counterpart of DWD's deterministic ICON-EU model. It provides 5-day probabilistic forecasts over the European domain with a horizontal resolution of approximately 20 km.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany / Europe

---

## What area it covers
- **Coverage:** European nest of the global ICON-EPS — the full ICON-EU-EPS domain extends 23.5° W to 62.5° E, 29.5° N to 70.5° N
- **Element-package domain:** Restricted to 23.5° W to 45.0° E, 29.5° N to 70.5° N on the DWD Open Data Server

---

## Basic details
- **Model type:** Regional ensemble prediction system (regional subset of a global ensemble)
- **Underlying core:** ICON (Icosahedral Nonhydrostatic) — same model core as the deterministic ICON-EU
- **Number of ensemble members:** 40 (inherited from ICON-EPS)
- **Horizontal resolution:** ~20 km (R2B07 icosahedral grid)
- **Vertical levels:** 60 levels to a height of 22.5 km
- **Forecast length:** Up to +120 hours (5 days) for the main runs
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly up to +78 hours, 3-hourly thereafter

---

## How the ensemble is generated
ICON-EU-EPS members are the regional-nest portions of the 40 ICON-EPS global members. Because it is a two-way nested part of the global ensemble (not a separately initialized regional ensemble), it inherits:

- **Initial conditions** from the global ICON LETKF-based Ensemble Data Assimilation
- **Physics parameter perturbations** from the same set applied to ICON-EPS members
- **Boundary conditions** implicitly, since the European nest is embedded in the global ensemble

This makes ICON-EU-EPS structurally different from regional ensembles like MOGREPS-UK or COSMO-based systems, which are driven by separate regional data assimilation.

---

## What it provides
- Full set of surface and upper-air fields (temperature, wind, humidity, pressure, precipitation, cloud, etc.) at 20 km resolution over Europe
- Probabilistic products derived from the 40-member ensemble
- Ensemble statistics, exceedance probabilities, and spread information

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data format:** GRIB2
- **Primary access:** DWD Open Data Server
  - ICON-EU-EPS: https://opendata.dwd.de/weather/nwp/icon-eu-eps/grib/
- **Grids available:** Native icosahedral grid (triangular); the DWD Open Data Server also provides pre-interpolated regular lat-lon grids for many element packages
- **Licence:** DWD Open Data licence (CC-BY 4.0-compatible; attribution required)
- **Retention note:** DWD retains GRIB2 files on the open data server for a short period only (typically 24 hours).

---

## Notes
- ICON-EU-EPS is the ensemble counterpart of the deterministic ICON-EU model — see the ICON-EU entry for the shared model core, physics, and operational context.
- Users should be aware that ICON-EU-EPS shares initial conditions and physics perturbations with the global ICON-EPS; it is not an independent regional ensemble.
- For convection-allowing probabilistic forecasts over central Europe, see the separate ICON-D2-EPS entry.

---

## Official documentation
- DWD ICON-EU model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
- DWD ensemble prediction overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/04_ensemble_methods/ensemble_prediction/ensemble_prediction_node.html
- DWD-Geoportal ICON entry: https://dwd-geoportal.de/products/G_EJM/
- DWD Open Data Server root: https://opendata.dwd.de/weather/nwp/
