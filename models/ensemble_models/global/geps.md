# GEPS (Global Ensemble Prediction System)

## What this model is
The Global Ensemble Prediction System (GEPS) is Canada’s **global ensemble weather forecast model**, designed to represent uncertainty in medium-range weather forecasts.

It produces multiple forecasts from perturbed initial conditions to provide probabilistic guidance rather than a single deterministic outcome.

---

## Who runs it
- **Organization:** : Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global ensemble (atmospheric)
- **Number of members:** 20 perturbation members + 1 control
- **Typical resolution:** ~0.5° (varies by forecast range)
- **Forecast length:** Up to ~16 days
- **Update frequency:** 2× daily

---

## What it provides
- Probabilistic forecasts for:
  - temperature
  - wind
  - precipitation
  - pressure
- Ensemble mean and spread
- Inputs for probabilistic weather guidance and risk assessment

---

## Relationship to other models
GEPS is the ensemble companion to Canada’s **GEM Global** deterministic weather model.

It complements deterministic forecasts by quantifying forecast uncertainty.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_geps/readme_geps-datamart_en/

---

## Notes
GEPS is widely used in Canada for medium-range forecasting and contributes to multi-model ensemble systems.

As with all ensemble systems, GEPS outputs should be interpreted probabilistically rather than as a single forecast.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_geps/readme_geps_en/
