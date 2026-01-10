# AROME French Polynesia

## What this model is
AROME French Polynesia is a **high-resolution, convection-permitting regional deterministic numerical weather prediction (NWP) model** operated by Météo-France.

It is part of the **AROME Outre-Mer** suite and is optimized for **tropical meteorology** across the widely dispersed island chains of French Polynesia, including deep convection, heavy rainfall, and local wind effects.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France (French Polynesia)

---

## What area it covers
- **Coverage:** French Polynesia and surrounding South Pacific regions
- **Operational grid:** **POLYN0025**
  - Resolution: **0.025° (~2.5 km)**
  - Approximate bounds: **12.6°S – 25.25°S**, **157.5°W – 144.5°W**

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
  https://meteo.data.gouv.fr/datasets/65bd1509cc112e6a1458ab95

---

## Notes
AROME French Polynesia uses the same **operational AROME forecasting chain** as mainland France, adapted for tropical conditions and large maritime domains.

The native grid is trapezoidal; GRIB files may contain missing values outside the effective domain.

---

## Official documentation
- https://donneespubliques.meteofrance.fr  
- https://donneespubliques.meteofrance.fr/client/documentation/description-paquets-modele-aromeom.pdf
