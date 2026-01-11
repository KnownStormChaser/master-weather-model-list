# GFS (IDEAM Operational Global Forecast)

## What this model is
IDEAM provides operational global weather forecast guidance for Colombia based on the Global Forecast System (GFS) using the FV3 dynamical core.

The GFS output is obtained from international sources and distributed locally by IDEAM, forming the backbone of several national forecast products, including precipitation, wind circulation, and synoptic-scale guidance.

---

## Who runs it
- **Organization:** Instituto de Hidrología, Meteorología y Estudios Ambientales
- **Country / region:** Colombia

---

## What area it covers
- **Coverage:** Global
- **Primary area of use:** Colombia and surrounding regions

---

## Basic details
- **Model type:** Deterministic global NWP (locally distributed)
- **Dynamical core:** FV3 (Finite-Volume Cubed-Sphere)
- **Horizontal resolution:**  
  - 0.25° (selected cycles)  
  - 0.5° (selected cycles)
- **Forecast length:**  
  - Up to ~7 days (0.5° products)  
  - Up to ~15 days (0.25° products)
- **Update frequency / cycles:**  
  4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:**  
  6–9 hours, depending on cycle

---

## What it provides
Deterministic global forecast fields used by IDEAM for:
- Precipitation forecasting
- Wind circulation at multiple pressure levels
- Synoptic-scale atmospheric analysis
- Boundary and contextual guidance for regional modeling

The GFS products also serve as inputs to IDEAM’s regional WRF modeling system.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download portal:**  
  https://bart.ideam.gov.co/wrfideam/new_modelo/

---

## Notes
- Update times may vary depending on the availability and download speed of upstream GFS data.
- IDEAM distributes selected GFS cycles and resolutions tailored to national forecasting needs.

---

## Official documentation
- IDEAM, *Guía rápida de usuario para consultar el portal de modelación numérica de tiempo y clima* (2025)
