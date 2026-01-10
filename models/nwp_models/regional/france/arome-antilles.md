# AROME Antilles

## What this model is
AROME Antilles is a **high-resolution, convection-permitting regional deterministic numerical weather prediction (NWP) model** operated by Météo-France for the French Antilles.

It is designed for **short-range forecasting and nowcasting** in a tropical environment, with particular strength in predicting heavy rainfall, convective storms, and local wind effects.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France (French Antilles)

---

## What area it covers
- **Coverage:** French Antilles and surrounding Caribbean region
- **Operational grid:** ANTIL0025 / CARAIB0025  
  - Approximate bounds: **22.45°N – 10.4°N**, **67.8°W – 52.2°W**

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
- precipitation (rain, snow, graupel)
- cloud cover and cloud microphysics
- convective and boundary-layer parameters

Data are provided on:
- surface levels
- isobaric pressure levels (100–1000 hPa)
- height levels (20–3000 m)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (compressed, grid_ccsds)
- **Official download location:**  
  https://meteo.data.gouv.fr/datasets/65bd162b9dc0d31edfabc2b9

---

## Notes
AROME Antilles is part of Météo-France’s **AROME Outre-Mer** suite, which also includes domains for French Guiana, New Caledonia, Polynesia, and the Indian Ocean.

The native grid is trapezoidal; GRIB files may contain missing values outside the effective domain.

---

## Official documentation
- https://donneespubliques.meteofrance.fr  
- https://donneespubliques.meteofrance.fr/client/documentation/description-paquets-modele-aromeom.pdf
