# HREF (High-Resolution Ensemble Forecast)

> ⚠️ **Scheduled for retirement.** HREF is proposed for discontinuation and full replacement by [REFS](./refs.md) (the RRFS Ensemble Forecast System) per [NWS Public Information Statement 25-41](https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf) (June 26, 2025).

## What this model is
The High-Resolution Ensemble Forecast (HREF) is a **regional, convection-allowing ensemble weather forecast system** designed to capture uncertainty in short-range, high-impact weather such as thunderstorms, heavy rainfall, and severe weather.

HREF combines forecasts from multiple high-resolution numerical weather models to produce probabilistic guidance at convection-resolving scales.

---

## Who runs it
- **Organization:** National Centers for Environmental Prediction
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Continental United States (CONUS)

---

## Basic details
- **Model type:** Regional ensemble (convection-allowing)
- **Number of members:** Varies by cycle (typically 8–10 members)
- **Typical resolution:** ~3 km
- **Forecast length:** Up to ~48 hours
- **Update frequency:** 4× daily

---

## What it provides
- Probabilistic forecasts for:
  - precipitation
  - severe convection indicators
  - temperature
  - wind
- Ensemble mean and spread
- Neighborhood probabilities for high-impact weather

---

## Relationship to other models
HREF is a **multi-model regional ensemble**, combining members from several high-resolution deterministic models (such as HRRR, NAM Nest, and FV3-based regional models).

It complements single-model high-resolution forecasts by explicitly representing short-range forecast uncertainty.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/href/prod/

---

## Notes
HREF is optimized for short-range forecasting and severe weather applications.

Because it blends multiple high-resolution models, the exact member composition may evolve over time.

---

## Status
- Proposed for full retirement in NWS Public Information Statement 25-41 (June 26, 2025).
- Replaced by REFS, which provides probabilistic convection-allowing guidance across CONUS, Alaska, Hawaii, and Puerto Rico.
- Key differences from HREF:
  - REFS extends forecasts to 60 hours (HREF: 48 hours)
  - REFS provides 00, 06, 12, and 18 UTC cycles for all regions; HREF only ran 2× daily for non-CONUS domains
- Originally targeted for retirement in early 2026 alongside RRFSv1 operational implementation; timeline has slipped.

## Official documentation
- https://www.spc.noaa.gov/exper/href/
- https://www.emc.ncep.noaa.gov/users/meg/href/
- HREF-to-REFS product changes: https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/href_product_changes.txt
