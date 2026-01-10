# AROME New Caledonia

## What this model is
AROME New Caledonia is a **high-resolution, convection-permitting regional deterministic numerical weather prediction (NWP) model** operated by Météo-France.

It is part of the **AROME Outre-Mer** suite and is optimized for **tropical and subtropical meteorology** in the southwest Pacific, including heavy rainfall, deep convection, and strong wind events.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France (New Caledonia)

---

## What area it covers
- **Coverage:** New Caledonia and surrounding regions of the southwest Pacific
- **Operational grid:** **NCALED0025**
  - Resolution: **0.025° (~2.5 km)**
  - Approximate bounds: **10°S – 30°S**, **156°E – 174°E**

---

## Basic details
- **Model type:** Regional deterministic NWP (non-hydrostatic, convection-permitting)
- **Model system:** AROME
- **Horizontal resolution:** ~2.5 km (0.025° grid)
- **Forecast length:** 0–48 hours
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 1 hour

---

## What it provides
Deterministic forecasts of atmospheric fields including:
- temperature, humidity, wind
- surface and mean sea-level pressure
- precipitation and hydrometeors
- cloud cover and cloud microphysics
- convective and boundary-layer diagnostics

Data are provided on:
- surface levels
- isobaric pressure levels (19 levels, 100–1000 hPa)
- height levels (14 levels, 20–3000 m)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (compressed, grid_ccsds)
- **Official download location:**  
  https://meteo.data.gouv.fr/datasets/65bd14cca6919e97e9699b09

---

## Notes
AROME New Caledonia uses the same **operational AROME forecasting chain** as mainland France, adapted for tropical conditions.

The native grid is trapezoidal; GRIB files may contain missing values outside the effective domain.

---

## Official documentation
- https://donneespubliques.meteofrance.fr  
- https://donneespubliques.meteofrance.fr/client/documentation/description-paquets-modele-aromeom.pdf
