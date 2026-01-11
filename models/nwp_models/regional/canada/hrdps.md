# HRDPS (High-Resolution Deterministic Prediction System)

## What this model is
The High-Resolution Deterministic Prediction System (HRDPS) is Canada’s convection-permitting numerical weather prediction system, designed to forecast small-scale and rapidly evolving weather phenomena such as thunderstorms, localized wind patterns, and heavy precipitation.

HRDPS provides high-resolution deterministic guidance to complement Canada’s global (GDPS) and regional (RDPS) forecast systems.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Most of Canada
- **Domain details:**  
  High-resolution regional domains focused on populated and meteorologically active areas

---

## Basic details
- **Model type:** Deterministic NWP (convection-permitting)
- **Model system / core:** GEM
- **Horizontal resolution:**  
  ~2.5 km
- **Vertical levels:** 84 hybrid staggered levels
- **Model top:** ~0.1 hPa
- **Forecast length:**  
  Up to ~48 hours
- **Update frequency / cycles:**  
  Multiple daily cycles (up to 4× daily, depending on domain)
- **Temporal output resolution:**  
  Typically hourly

---

## What it provides
Deterministic high-resolution forecasts of:
- Convective precipitation and thunderstorms
- Near-surface wind, temperature, and humidity
- Localized hazardous weather
- Boundary-layer and terrain-driven effects

HRDPS is Canada’s highest-resolution operational deterministic forecast system and is used for short-range, local-scale forecasting.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/readme_hrdps-datamart_en/

---

## Notes
- HRDPS operates in the convection-permitting regime and does not rely on deep convection parameterization.
- It complements RDPS by resolving smaller-scale features at the expense of forecast range.
- HRDPS plays a role broadly comparable to the U.S. **HRRR**, though with different update frequency and domain strategy.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/readme_hrdps_en/
