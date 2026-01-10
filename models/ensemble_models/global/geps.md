# GEPS (Global Ensemble Prediction System)

## What this model is
The Global Ensemble Prediction System (GEPS) is Canada’s **global ensemble weather forecast system**, designed to produce probabilistic forecasts and quantify uncertainty in medium- to extended-range weather prediction.

It generates multiple forecast scenarios to represent the nonlinear (chaotic) behaviour of the atmosphere.

---

## Who runs it
- **Organization:** Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global ensemble (atmospheric)
- **Number of members:** 20 perturbation members + 1 control
- **Typical resolution:** ~0.5° (~39 km)
- **Vertical levels:** ~15
- **Forecast length:**  
  - Up to 16 days (operational)  
  - Up to ~39 days (twice weekly at 00 UTC for anomaly guidance)
- **Update frequency:** 2× daily

---

## What it provides
- Probabilistic forecasts for:
  - temperature
  - wind
  - precipitation
  - cloud cover
  - humidity
- Ensemble mean and spread
- Extended-range anomaly guidance (limited cycles)

---

## Ensemble methodology
GEPS ensemble members differ through:
- perturbations to initial conditions
- stochastic physics parameter perturbations (SPP)
- stochastic kinetic energy perturbations

A control member without perturbations is also produced.

---

## Relationship to other models
GEPS is the ensemble companion to Canada’s **GDPS (GEM Global)** deterministic forecast system.

It complements deterministic guidance by explicitly representing forecast uncertainty.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Grid:** Global latitude–longitude grid (0.5°)
- **Official download location:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_geps/readme_geps-datamart_en/

---

## Notes
GEPS is used operationally in Canada for medium-range probabilistic forecasting and contributes to multi-model ensemble systems.

As with all ensemble systems, GEPS output should be interpreted probabilistically rather than as a single forecast.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_geps/readme_geps_en/
