# GDWPS (Global Deterministic Wave Prediction System)

## What this model is
The Global Deterministic Wave Prediction System (GDWPS) is Canada’s operational global wave forecasting system, designed to predict ocean surface wave conditions worldwide.

GDWPS provides deterministic forecasts of wave height, period, direction, and related parameters and serves as the primary source of global wave guidance and boundary conditions for Canada’s regional wave models.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global oceans

---

## Basic details
- **Model type:** Deterministic global wave model
- **Wave model:** WAVEWATCH III
- **Horizontal resolution:**  
  ~0.25° (global), implemented using multiple overlapping grids
- **Forecast length:**  
  Up to ~243 hours (~10 days)
- **Update frequency / cycles:**  
  - Pseudo-analysis cycles: 4× daily (00, 06, 12, 18 UTC)  
  - Forecast cycles: 2× daily (00, 12 UTC)
- **Temporal output resolution:**  
  Typically 3-hourly

---

## Model configuration
GDWPS uses three overlapping global grids to provide consistent coverage across different latitude bands. This multi-grid configuration improves numerical stability and accuracy at high latitudes.

The system includes a short pseudo-analysis phase prior to forecast integration, allowing the wave state to adjust dynamically to the most recent wind and ice forcing.

---

## Forcing and inputs
- **Wind forcing:**  
  GDPS near-surface winds
- **Sea ice forcing:**  
  GDPS sea-ice concentration and extent
- **Ice treatment:**  
  Wave growth and propagation are progressively attenuated in partially ice-covered waters and suppressed in areas of high ice concentration.

---

## What it provides
Deterministic global forecasts of:
- Significant wave height
- Wave period and direction
- Wind wave and swell components
- Wave energy spectra (for selected products)

GDWPS output is used directly for marine forecasting and as boundary conditions for Canada’s regional wave prediction systems.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_gdwps/readme_gdwps-datamart_en/

---

## Notes
- GDWPS is tightly coupled to Canada’s global atmospheric prediction systems through wind and ice forcing.
- The system plays a central role in Canada’s marine forecasting and wave modeling hierarchy.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_gdwps/readme_gdwps_en/
