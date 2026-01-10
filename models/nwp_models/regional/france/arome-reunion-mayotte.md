# AROME Réunion–Mayotte

## What this model is
AROME Réunion–Mayotte is a **high-resolution, convection-permitting regional deterministic numerical weather prediction (NWP) model** operated by Météo-France.

It is part of the **AROME Outre-Mer** suite and is optimized for **tropical meteorology in the south-western Indian Ocean**, with a strong focus on deep convection, intense rainfall, cyclones, and complex island-scale wind patterns affecting Réunion and Mayotte.

---

## Who runs it
- **Organization:** Météo-France  
- **Country / region:** France (Réunion & Mayotte)

---

## What area it covers
- **Coverage:** Réunion, Mayotte, and surrounding south-western Indian Ocean regions  
- **Operational grid:** **INDIEN0025**
  - Resolution: **0.025° (~2.5 km)**
  - Approximate bounds: **7.25°S – 25.9°S**, **32.75°E – 67.6°E**

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
- precipitation, graupel, and hydrometeors
- cloud cover and cloud microphysics
- convective, boundary-layer, and turbulence diagnostics
- radar reflectivity fields

Data are provided on:
- surface levels
- isobaric pressure levels (19 levels, 100–1000 hPa)
- height levels (14 levels, 20–3000 m)

---

## Data availability
- **Is the data free?** Yes  
- **Is the data downloadable?** Yes  
- **Data formats:** GRIB2 (compressed, `grid_ccsds`)  
- **Official download location:**  
  https://meteo.data.gouv.fr

---

## Notes
AROME Réunion–Mayotte uses the same **operational AROME forecasting chain** as other overseas French territories, adapted for **tropical cyclone-prone maritime environments**.

Data are provided on a regular latitude/longitude grid interpolated from the native AROME grid. GRIB files may contain missing values outside the effective model domain.

---

## Official documentation
- https://donneespubliques.meteofrance.fr  
- https://donneespubliques.meteofrance.fr/client/documentation/description-paquets-modele-aromeom.pdf
