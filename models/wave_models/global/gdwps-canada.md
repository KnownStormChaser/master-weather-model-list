# GDWPS (Global Deterministic Wave Prediction System)

## What this model is
The Global Deterministic Wave Prediction System (GDWPS) is Canada’s **global operational wave forecast system**, used to predict ocean wave conditions worldwide.

It is based on the third-generation spectral wave model **WaveWatch III® (WW3)** and produces deterministic wave forecasts for marine applications.

---

## Who runs it
- **Organization:** Environment and Climate Change Canada (Canadian Meteorological Centre)
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global oceans

---

## Basic details
- **Model type:** Deterministic wave model
- **Core model:** WaveWatch III® (WW3)
- **Typical resolution:** ~0.25°
- **Forecast length:** Up to 240 hours (~10 days)
- **Update frequency:** 2× daily

---

## Forcing and coupling
GDWPS is forced by:
- **10 m winds** from the Global Deterministic Prediction System (GDPS)
- **Sea-ice concentration** from GDPS

Sea-ice concentration is used to:
- attenuate wave growth in regions with 25–75% ice coverage
- suppress wave growth in regions with ice concentration above 75%

---

## What it predicts
- Significant wave height
- Peak wave period
- Primary swell height, direction, and period
- Additional wind-wave and swell parameters (by product)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, NetCDF
- **Official documentation and access:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_gdwps/readme_gdwps-datamart_en/

---

## Notes
GDWPS provides global deterministic wave guidance and is widely used for marine forecasting in the North Atlantic, North Pacific, and Arctic regions.

It serves as the wave counterpart to Canada’s **GDPS** atmospheric forecasting system.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_gdwps/readme_gdwps_en/
