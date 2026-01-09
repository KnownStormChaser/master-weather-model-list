# GEFS (Global Ensemble Forecast System)

## What this model is
The Global Ensemble Forecast System (GEFS) is a global **ensemble weather forecast model** that produces multiple forecasts to represent uncertainty in future atmospheric conditions.

Instead of a single forecast, GEFS runs many slightly different simulations to show a range of possible outcomes.

---

## Who runs it
- **Organization:** : National Centers for Environmental Prediction
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global ensemble (atmospheric)
- **Number of members:** 30 perturbation members + 1 control
- **Typical resolution:** ~0.25° (varies by forecast range)
- **Forecast length:** Up to ~16 days
- **Update frequency:** 4× daily

---

## What it provides
- Probabilistic forecasts of weather variables such as:
  - temperature
  - wind
  - precipitation
  - pressure
- Ensemble mean and spread
- Inputs for downstream products (e.g. probabilistic guidance)

---

## Relationship to other models
GEFS is the ensemble companion to the **GFS** deterministic global model and uses the same core atmospheric modeling system with perturbed initial conditions.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  - https://nomads.ncep.noaa.gov/  
  - https://registry.opendata.aws/noaa-gefs/

---

## Notes
GEFS is widely used for medium-range forecasting, risk assessment, and uncertainty estimation.

It should be interpreted probabilistically rather than as a single deterministic forecast.

---

## Official documentation
- https://www.ncei.noaa.gov/products/weather-climate-models/global-ensemble-forecast
