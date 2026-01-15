# MFWAM (Météo-France Wave Model)

## What this model is
MFWAM is a **third-generation spectral ocean wave forecast model** derived from the WAM model and operated by Météo-France.

It provides deterministic analyses and forecasts of **sea state conditions**, including wind sea and swell components, and is forced by near-surface winds from atmospheric NWP models (ARPEGE and AROME). Satellite wave observations are assimilated in the global configuration.

---

## Who runs it
- **Organization:** Météo-France  
- **Country / region:** France

---

## What area it covers
MFWAM is operated on multiple grids:

- **Global grid:** **GLOB01**
  - Resolution: **0.1°**
  - Coverage: **Global (90°N–90°S, 0–359.5°E)**

- **Regional grid:** **FRANGP0025**
  - Resolution: **0.025° (~2.5 km)**
  - Coverage: **Western Europe**
    *(approx. 53°N–38°N, 8°W–12°E)*

---

## Basic details
- **Model type:** Spectral wave model (third-generation WAM)
- **Model system:** MFWAM
- **Forcing winds:** ARPEGE (global), AROME (regional)
- **Spectral resolution:** 24 directions × 30 frequencies
- **Forecast length:** Up to 102 hours (global grid)
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:**
  - 1-hourly from +1h to +48h
  - 3-hourly from +51h to +102h

---

## What it provides
Deterministic forecasts of sea state parameters including:
- significant wave height (total sea state)
- wave period and wave direction
- wind sea height, period, and direction
- primary and secondary swell height, period, and direction
- total swell characteristics
- peak wave period
- 10 m wind speed and direction

Most parameters are provided at the **sea surface**; winds are provided at **10 m height**.

---

## Data availability
- **Is the data free?** Yes  
- **Is the data downloadable?** Yes  
- **Data formats:** GRIB2 (compressed, `grid_ccsds`)  
- **Official download location:**  
  https://meteo.data.gouv.fr/datasets/65bd1a2957e1cc7c9625e7b5

---

## Notes
The global MFWAM configuration assimilates satellite altimeter and spectral observations and is regularly validated against buoy measurements.

The regional FRANGP0025 grid is nested within the global model and provides higher-resolution wave forecasts for Western Europe.

---

## Official documentation
- https://meteo.data.gouv.fr  
- https://donneespubliques.meteofrance.fr
