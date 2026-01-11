# GEPS (Global Ensemble Prediction System)

## What this model is
The Global Ensemble Prediction System (GEPS) is Canada’s global ensemble numerical weather prediction system, designed to provide probabilistic weather forecasts and quantify forecast uncertainty from the medium range into the extended range.

GEPS generates multiple realizations of future atmospheric states using perturbed initial conditions and stochastic model physics, allowing users to assess forecast confidence and risk rather than relying on a single deterministic solution.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global ensemble NWP
- **Model system / core:** GEM (Global Environmental Multiscale)
- **Number of members:** 20 perturbed members + 1 control
- **Horizontal resolution:**  
  ~25 km (quasi-uniform Yin–Yang grid)
- **Vertical levels:** 84 hybrid staggered levels
- **Model top:** ~0.1 hPa
- **Forecast length:**  
  - Up to ~16 days (operational medium range)  
  - Extended-range guidance produced on selected cycles
- **Update frequency / cycles:**  
  2× daily (00, 12 UTC)
- **Temporal output resolution:**  
  Typically 3- to 6-hourly

---

## Ensemble methodology
GEPS represents forecast uncertainty through:
- Perturbations to initial conditions derived from an ensemble data assimilation system
- Recentering of ensemble members around the GDPS deterministic analysis
- Stochastic physics perturbations during model integration

The ensemble is designed to remain statistically consistent with the deterministic GDPS while explicitly sampling uncertainty.

---

## What it provides
Probabilistic global forecasts of:
- Temperature, wind, humidity, and pressure
- Precipitation amount and type
- Large-scale circulation patterns
- Ensemble mean, spread, and percentile-based guidance

GEPS output is used operationally for medium-range probabilistic forecasting and as input to multi-model ensemble and post-processing systems.

---

## Relationship to other models
- GEPS is the ensemble counterpart to Canada’s **GDPS** deterministic global system.
- It provides uncertainty information that complements deterministic guidance.
- GEPS also supplies ensemble-based information used by downstream systems, including **REPS** initialization.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_geps/readme_geps-datamart_en/

---

## Notes
- GEPS is part of Canada’s modernized IC-4 prediction framework.
- The system is coupled within an Earth-system context, with interactions between atmosphere, ocean, and sea ice represented at global scales.
- As with all ensemble systems, GEPS output should be interpreted probabilistically rather than as a single forecast.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_geps/readme_geps_en/
