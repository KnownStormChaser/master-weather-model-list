# AROME France

## What this model is
AROME France is a **high-resolution, convection-permitting regional deterministic numerical weather prediction (NWP) model** operated by Météo-France.

It is designed for **short-range forecasting and nowcasting**, with strong performance for thunderstorms, heavy precipitation, fog, local wind effects, and complex terrain.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** France and nearby regions of Western Europe  
- **Operational domain:** EURW1S40  
  - Approximate bounds: **55.4°N – 37.5°N**, **12°W – 16°E**
  - Note: native grid is **trapezoidal**, not strictly rectangular

---

## Basic details
- **Model type:** Regional deterministic NWP (non-hydrostatic, convection-permitting)
- **Model system:** AROME
- **Horizontal resolution:** ~2.5 km (0.025° grid)
- **Forecast length:** 0–51 hours
- **Update frequency:** 8× daily (00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Temporal output resolution:** 1 hour

---

## What it provides
Deterministic forecasts of a wide range of atmospheric fields, including:
- temperature, humidity, wind
- surface and mean sea-level pressure
- precipitation (rain, snow, graupel)
- cloud cover and cloud microphysics
- convective indices (e.g. CAPE)
- boundary-layer and radiation parameters

Data are provided on:
- surface levels
- isobaric pressure levels
- selected height levels above ground

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (compressed)
- **Official download location:**  
  https://meteo.data.gouv.fr/datasets/65bd1247a6238f16e864fa80

---

## Notes
Météo-France distributes AROME outputs as **parameter group packages**, following WMO GFNC naming conventions.

Because the native AROME grid is trapezoidal, GRIB files may contain **missing values** outside the effective domain; users may need to crop or mask data when post-processing.

---

## Official documentation
- https://donneespubliques.meteofrance.fr
- https://donneespubliques.meteofrance.fr/client/documentation/descriptiontechnique-paquetsarome-donneespubliques-v4-20250401.pdf
