# WRF (IDEAM Regional Forecast System)

## What this model is
IDEAM operates a regional numerical weather prediction system based on the Weather Research and Forecasting (WRF) model to provide high-resolution short-range forecasts for Colombia and surrounding regions.

The system uses multiple nested domains to dynamically downscale global model guidance and better represent regional topography, convection, and local-scale weather phenomena.

---

## Who runs it
- **Organization:** Instituto de Hidrología, Meteorología y Estudios Ambientales
- **Country / region:** Colombia

---

## What area it covers
- **Coverage:** Colombia and surrounding regions
- **Domains:**  
  Multiple nested regional domains, including:
  - National Colombia domain  
  - Caribbean region  
  - Central Colombia  
  - Cundinamarca  
  - Bogotá metropolitan area

---

## Basic details
- **Model type:** Deterministic regional NWP
- **Model system / core:** WRF v4.0
- **Horizontal resolution:**  
  - Colombia: ~10 km  
  - Caribbean: ~20 km  
  - Central Colombia: ~15 km  
  - Cundinamarca: ~5 km  
  - Bogotá: ~1.6 km
- **Forecast length:**  
  Up to ~7 days
- **Update frequency / cycles:**  
  Primarily 00 UTC (selected domains), with additional 12 UTC cycles for some regions
- **Temporal output resolution:**  
  1–5 hours, depending on domain

---

## What it provides
High-resolution deterministic forecasts of:
- Precipitation
- Near-surface wind, temperature, and humidity
- Cloud cover and atmospheric circulation
- Sector-specific variables derived from WRF output

WRF products form the basis for IDEAM’s national and subnational weather forecast guidance.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:**  
  - NetCDF (full model output)  
  - GeoTIFF (derived forecast products)
- **Official download portal:**  
  https://bart.ideam.gov.co/wrfideam/new_modelo/

---

## Notes
- Some WRF domains are nested within parent domains that are not publicly distributed.
- Update timing depends on upstream global model availability and local processing.
- The WRF system is also used for climate downscaling and sectoral applications at IDEAM.

---

## Official documentation
- IDEAM, *Guía rápida de usuario para consultar el portal de modelación numérica de tiempo y clima* (2025)
