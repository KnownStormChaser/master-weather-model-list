# RDWPS (Regional Deterministic Wave Prediction System)

## What this model is
The Regional Deterministic Wave Prediction System (RDWPS) is Canada’s operational regional wave forecasting system, designed to provide high-resolution deterministic wave guidance for coastal waters and inland seas.

RDWPS complements the global GDWPS by resolving regional and nearshore wave processes important for marine operations, coastal forecasting, and navigation.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Selected Canadian coastal waters and inland seas
- **Domains:**  
  - Great Lakes  
  - Northeastern Pacific  
  - Northwestern Atlantic

---

## Basic details
- **Model type:** Deterministic regional wave model
- **Wave model:** WAVEWATCH III
- **Forecast length:**  
  Up to ~51 hours
- **Update frequency / cycles:**  
  4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:**  
  Typically hourly to 3-hourly

---

## Domain configurations
RDWPS uses different grid strategies tailored to each region:

- **Great Lakes:**  
  Structured grid at ~1 km resolution
- **Northeastern Pacific:**  
  Unstructured grid with resolution ranging from ~5 km offshore to ~1 km near the coast
- **Northwestern Atlantic:**  
  Rotated structured grid at ~5 km resolution

---

## Forcing and inputs
- **Wind forcing:**  
  HRDPS near-surface winds  
  - 30-minute forcing during the first 24 forecast hours  
  - Hourly forcing thereafter
- **Sea ice forcing:**  
  - Great Lakes: WCPS ice analysis and forecast  
  - Ocean domains: RIOPS ice products
- **Boundary conditions:**  
  - Ocean domains receive lateral wave boundary conditions from GDWPS

---

## What it provides
Deterministic regional forecasts of:
- Significant wave height
- Wave period and direction
- Nearshore wave conditions
- Coastal and inland-sea wave guidance

RDWPS provides the primary short-range wave guidance for Canadian coastal waters and inland seas.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/readme_rdwps-datamart_en/

---

## Notes
- RDWPS depends on GDWPS for boundary conditions in ocean basins, ensuring consistency between global and regional wave forecasts.
- Higher-resolution wind forcing allows RDWPS to better resolve coastal wave growth and nearshore effects.
- As with all deterministic wave models, RDWPS output represents a single best-estimate realization of future wave conditions.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/readme_rdwps_en/
