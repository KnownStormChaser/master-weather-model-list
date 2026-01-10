# RDWPS (Regional Deterministic Wave Prediction System)

## What this model is
The Regional Deterministic Wave Prediction System (RDWPS) is Canada’s **operational regional wave forecast system**, designed to provide high-resolution wave guidance over coastal waters and the Great Lakes.

It is based on the third-generation spectral wave model **WaveWatch III® (WW3)** and produces deterministic short-range wave forecasts.

---

## Who runs it
- **Organization:** Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Regional marine domains of Canada, including:
  - Lake Superior  
  - Lake Huron–Michigan  
  - Lake Erie  
  - Lake Ontario  
  - Atlantic North-West  
  - Pacific North-East

---

## Basic details
- **Model type:** Deterministic wave model (regional)
- **Core model:** WaveWatch III® (WW3)
- **Typical resolution:** ~0.1° (varies by domain)
- **Forecast length:** Up to 48 hours
- **Update frequency:** 2× daily

---

## Forcing and coupling
RDWPS is forced by:
- **10 m winds** from the High-Resolution Deterministic Prediction System (HRDPS)

Ice forecasts are used to modify wave growth:
- **Great Lakes:** Ice forecasts from the Water Cycle Prediction System of the Great Lakes (WCPS)  
  - Wave growth attenuated for 25–75% ice concentration  
  - Wave growth suppressed above 75%
- **Ocean domains:** Ice forecasts from the Regional Ice Ocean Prediction System (RIOPS)  
  - Northeast Pacific: waves propagate freely below 50% ice concentration, no propagation above  
  - Northwest Atlantic: same ice–wave logic as the Great Lakes

---

## What it predicts
- Significant wave height
- Peak wave period
- Partitioned wave parameters
- Additional wind-wave and swell characteristics (by domain)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, NetCDF
- **Official documentation and access:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/readme_rdwps-datamart_en/

---

## Notes
RDWPS provides higher-resolution regional and coastal wave guidance than **GDWPS**, particularly for the Great Lakes and Canadian coastal waters.

It serves as the regional counterpart to Canada’s global wave prediction system.

---

## Official documentation
- https://eccc-msc.github.io/open-data/msc-data/nwp_rdwps/readme_rdwps_en/
