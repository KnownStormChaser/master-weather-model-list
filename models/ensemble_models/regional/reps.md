# REPS (Regional Ensemble Prediction System)

## What this model is
The Regional Ensemble Prediction System (REPS) is Canada’s **regional ensemble weather forecast system**, designed to produce probabilistic forecasts and quantify uncertainty at regional scales.

It generates multiple forecast scenarios to represent the uncertainty inherent in short-range weather prediction.

---

## Who runs it
- **Organization:** Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Canada and the United States (regional North American domain)

---

## Basic details
- **Model type:** Regional ensemble (atmospheric)
- **Number of members:** 20 perturbation members + 1 control
- **Typical resolution:** ~10 km
- **Vertical levels:** 24
- **Forecast length:** Up to 3 days (~72 hours)
- **Update frequency:** 4× daily

---

## What it provides
- Probabilistic forecasts for:
  - temperature
  - wind
  - precipitation
  - cloud cover
  - humidity
- Ensemble mean and spread
- Short-range regional uncertainty guidance

---

## Ensemble methodology
REPS ensemble members differ through perturbations to:
- initial conditions
- boundary conditions
- physical tendencies

A control member without perturbations is also produced.

---

## Relationship to other models
REPS is the ensemble companion to Canada’s **RDPS** deterministic regional forecast system.

It complements **RDPS** and **GEPS** by providing probabilistic guidance at regional scales.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official documentation and access:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_reps/readme_reps-datamart_en/

---

## Notes
REPS is used operationally by Environment and Climate Change Canada for short-range regional probabilistic forecasting.

As with all ensemble systems, REPS output should be interpreted probabilistically rather than as deterministic forecasts.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_reps/readme_reps_en/
