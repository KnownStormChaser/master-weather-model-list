# ECMWF EPS (Ensemble Prediction System)

## What this model is
The ECMWF Ensemble Prediction System (EPS) is a **global ensemble weather forecast system** designed to quantify uncertainty in medium-range weather forecasts.

It produces multiple forecasts from slightly different initial conditions to represent a range of possible atmospheric outcomes.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts
- **Country / region:** International (European consortium)

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global ensemble (atmospheric)
- **Number of members:** 50 perturbation members + 1 control  
- **Typical resolution:** ~0.5° (Open Data subset)
- **Forecast length:** Up to ~15 days
- **Update frequency:** 2× daily

---

## What it provides
- Probabilistic forecasts for:
  - temperature
  - wind
  - precipitation
  - pressure
- Ensemble mean and spread
- Inputs for risk-based and probabilistic forecasting

---

## Relationship to other models
ECMWF EPS is the ensemble companion to the **IFS** deterministic global model.

The Open Data version provides a reduced-resolution and reduced-field subset of the full operational EPS.

---

## Data availability
- **Is the data free?** Yes (Open Data subset)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  - https://www.ecmwf.int/en/forecasts/datasets/open-data

---

## Notes
ECMWF EPS is widely regarded as one of the highest-skill global ensemble forecast systems.

Users should note that the freely available Open Data represents a subset of the full operational EPS.

---

## Upcoming changes

### IFS Cycle 50r1 — operational May 12, 2026
ECMWF EPS is part of the IFS Cycle 50r1 upgrade scheduled for May 12, 2026. There is no change in resolution or forecast steps. Key changes relevant to ensemble users:
- Scale-selective ensemble recentering and revised stochastic physics (SPP) are intended to reduce over-dispersion in 10 m wind.
- ENS control forecasts move under the operational stream (archive/dissemination change; primarily affects MARS users).

### IFS Cycle 50r2 — full GRIB2 transition (tentative Q4 2026)
Cycle 50r2 completes ECMWF's migration to GRIB2-only parameter representation, affecting Open Data distribution of EPS products. See the IFS entry for migration details and test resource links.

## Official documentation
- https://www.ecmwf.int/en/forecasts/documentation-and-support
