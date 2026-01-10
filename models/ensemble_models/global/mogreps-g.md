# MOGREPS-G (Met Office Global and Regional Ensemble Prediction System – Global)

## What this model is
MOGREPS-G is the **global ensemble numerical weather prediction system** operated by the UK Met Office.

It produces multiple forecast scenarios to represent uncertainty in medium-range weather prediction, rather than a single deterministic outcome.

---

## Who runs it
- **Organization:** Met Office
- **Country / region:** United Kingdom

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global ensemble NWP
- **Core system:** Unified Model (ensemble configuration)
- **Number of members:** 17 perturbed members + 1 control
- **Typical resolution:** ~20 km (global latitude–longitude grid)
- **Forecast length:** Up to 8 days
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)

---

## What it provides
- Probabilistic forecasts for:
  - temperature
  - wind
  - precipitation
  - pressure
- Ensemble mean and spread
- Medium-range forecast uncertainty guidance

---

## Relationship to other models
MOGREPS-G is the ensemble companion to the **UK Met Office Global Deterministic Model**.

The openly available data represents a **subset of the full operational ensemble**, with reduced parameter availability.

---

## Data availability
- **Is the data free?** Yes (Open Data subset)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-compliant)
- **Official download location:**  
  https://registry.opendata.aws/met-office-global-ensemble/

---

## Notes
MOGREPS-G runs four times daily, with all cycles producing forecasts out to 8 days.

Ensemble output should be interpreted probabilistically rather than as deterministic forecasts.

---

## Official documentation
- https://www.metoffice.gov.uk/services/data
