# HGEFS (Hybrid Global Ensemble Forecast System)

## What this model is
The Hybrid Global Ensemble Forecast System (HGEFS) is a **global “grand ensemble” weather forecast system** that combines traditional physics-based ensemble forecasts with AI-based ensemble forecasts.

It merges the members of the physical GEFS with the members of the AI-based AIGEFS to create a larger ensemble designed to better represent forecast uncertainty.

---

## Who runs it
- **Organization:** : National Centers for Environmental Prediction
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global ensemble (hybrid physics + AI)
- **Number of members:** 62  
  - 31 members from GEFS (physics-based)  
  - 31 members from AIGEFS (AI-based)
- **Typical resolution:** ~0.25°
- **Forecast length:** Up to ~16 days
- **Update frequency:** 4× daily

---

## What it provides
- Probabilistic forecasts of atmospheric variables such as:
  - temperature
  - wind
  - precipitation
  - pressure
- Improved ensemble spread through hybridization
- A combined view of physics-based and AI-based uncertainty

---

## Relationship to other models
HGEFS is a **composite ensemble** built from:
- **GEFS** (traditional physics-based ensemble)
- **AIGEFS** (AI-based ensemble)

It does not replace either system, but instead provides a **larger, more diverse ensemble** for probabilistic guidance.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  - https://nomads.ncep.noaa.gov/pub/data/nccf/com/hgefs/prod/

---

## Notes
HGEFS reflects NOAA’s move toward **hybrid ensemble forecasting**, combining physical models and artificial intelligence to improve medium-range forecast skill.

It should be interpreted probabilistically and is particularly useful for risk-based decision making.

---

## Official documentation
- https://www.noaa.gov/news-release/noaa-deploys-new-generation-of-ai-driven-global-weather-models
