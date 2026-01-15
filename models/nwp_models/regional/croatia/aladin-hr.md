# ALADIN-HR (Croatian Meteorological and Hydrological Service)

## What this model is
ALADIN-HR is a high-resolution regional numerical weather prediction (NWP) system operated by the Croatian Meteorological and Hydrological Service (DHMZ).

The model is based on the ALADIN limited-area forecasting system developed within the international ALADIN/ACCORD collaboration and is used operationally for short-range weather forecasting over Croatia and the Adriatic region.

---

## Who runs it
- **Organization:** Croatian Meteorological and Hydrological Service (DHMZ)
- **Country / region:** Croatia

---

## What area it covers
- **Primary coverage:** Croatia
- **Extended coverage:** Adriatic Sea and surrounding regions (for specialized products)

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system:** ALADIN (ACCORD framework)
- **Horizontal resolution:**  
  - ~4 km for general meteorological fields  
  - ~2 km for high-resolution wind products
- **Forecast range:**  
  Short-range (up to several days)
- **Update frequency:**  
  - 1× daily (00 UTC) for publicly available NetCDF meteorological fields  
  - 2× daily (00, 12 UTC) for sailing-oriented wind products
- **Temporal output resolution:**  
  Hourly (selected fields)

---

## Publicly available products

### General meteorological data (NetCDF)
- Covers the **entire territory of Croatia**
- Produced from the **00 UTC model run**
- Includes standard meteorological variables
- **Format:** NetCDF

**Access link:**  
https://radar.dhz.hr/~aladinhr/opendata  

**Username:** `aladinhr`  
**Password:** `opendata_aladinhr2`

---

### Sailing and nautical wind products (GRIB)
- High-resolution wind fields at **10 m height**
- Includes **wind components** and **maximum hourly wind speed**
- Produced from a **2 km horizontal grid spacing**
- Covers three Adriatic domains:
  - **ADR** – entire Adriatic Sea  
  - **NA** – Northern Adriatic  
  - **SA** – Southern Adriatic
- Produced **twice daily** from the **00 and 12 UTC model runs**
- **Format:** GRIB

**Access link:**  
https://radar.dhz.hr/~aladinhr/opendata/sailing  

**Username:** `aladinhr`  
**Password:** `opendata_aladinhr24`

---

## What it provides
Deterministic high-resolution forecasts used for:
- National weather forecasting and warnings
- Civil protection and risk management
- Aviation, marine, and sailing applications
- Specialized post-processed products (e.g. meteograms, indices)

ALADIN-HR output forms the backbone of DHMZ’s short-range forecasting system.

---

## Data availability
- **Is the data free?** Yes (authentication required)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF, GRIB

---

## Notes
- Public access is limited to specific products and model runs.
- Additional high-resolution and specialized outputs are produced internally or for partner institutions.
- Model configuration and available products may evolve as part of DHMZ operational upgrades.

---

## Official documentation
- DHMZ brochure: *ALADIN-HR – Operational Numerical Weather Prediction* (2025)
