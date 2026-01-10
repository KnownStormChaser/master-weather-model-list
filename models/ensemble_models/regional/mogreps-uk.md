# MOGREPS-UK (Met Office Global and Regional Ensemble Prediction System – UK)

## What this model is
MOGREPS-UK is the **high-resolution regional ensemble weather prediction system** operated by the UK Met Office.

It is designed to represent forecast uncertainty for short-range, high-impact weather over the United Kingdom, including convection, heavy rainfall, wind, fog, and temperature extremes.

---

## Who runs it
- **Organization:** Met Office
- **Country / region:** United Kingdom

---

## What area it covers
- **Coverage:** United Kingdom  
- Boundary conditions are provided by **MOGREPS-G**, the Met Office global ensemble system.

---

## Basic details
- **Model type:** Regional ensemble NWP (convection-permitting)
- **Core system:** Unified Model (ensemble configuration)
- **Number of members:**  
  - Varies by cycle (see notes below)
- **Typical resolution:** ~2.2 km
- **Vertical levels:** 70
- **Forecast length:** Up to 126 hours (5 days)

---

## Update frequency and ensemble structure
- **Run frequency:** Hourly (24× per day)
- **Ensemble composition:**
  - 05, 11, 17, 23 UTC cycles:  
    - 1 control member + 2 perturbed members
  - All other cycles:  
    - 3 perturbed members

---

## What it provides
- Probabilistic forecasts for:
  - precipitation
  - wind
  - temperature
  - cloud, fog, and convection-related hazards
- Ensemble mean and spread
- Short-range uncertainty guidance for high-impact weather

---

## Relationship to other models
MOGREPS-UK is the **regional, convection-permitting counterpart** to **MOGREPS-G**.

It complements the Met Office deterministic UK model (UKV) by providing probabilistic guidance at local scales.

---

## Data availability
- **Is the data free?** Yes (Open Data subset)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-compliant)
- **Official download location:**  
  https://registry.opendata.aws/met-office-uk-ensemble/

---

## Notes
MOGREPS-UK runs hourly with a small ensemble size per cycle, optimized for short-range uncertainty guidance rather than medium-range probabilistic outlooks.

Only a subset of the full operational ensemble and parameters is publicly available.

---

## Official documentation
- https://www.metoffice.gov.uk/services/data
