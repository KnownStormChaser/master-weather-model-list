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
- **Number of members:** 17 perturbed members + 1 control (per cycle)
- **Typical resolution:** ~20 km (global latitude–longitude grid)
- **Forecast length:**  
  - Up to ~198 hours (~8 days) historically  
  - Extended to ~246 hours (~10 days) for runs from late January 2026 onward
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:**  
  - Hourly out to 54 hours (or 132 hours, parameter-dependent)  
  - 3-hourly thereafter

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
MOGREPS-G runs four times daily and publishes a curated subset of operational ensemble parameters on AWS as NetCDF files. The dataset is offered on a non-operational, unsupported basis.

Data published from late January 2026 onward includes additional parameters, extended forecast range to 246 hours, and changes to existing parameters (for example, `height_asl_on_pressure_levels` was replaced by `geopotential_height_on_pressure_levels`).

MOGREPS-G's underlying data assimilation uses a hybrid 4D-EnVar system applied to a larger ensemble of 44 perturbed members; the 17 perturbed + 1 control figure above refers specifically to the forecast ensemble distributed per cycle. Ensemble output should be interpreted probabilistically rather than as a deterministic forecast.

---

## Official documentation
- https://www.metoffice.gov.uk/services/data
