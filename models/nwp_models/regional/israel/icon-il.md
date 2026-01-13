# ICON-IL (ICON-LAM Israel)

## What this model is
ICON-IL is a **regional deterministic limited-area numerical weather prediction (NWP) model** operated by the Israel Meteorological Service (IMS).

It is based on the **ICON (ICOsahedral Nonhydrostatic) modeling system** and is run as a limited-area configuration (ICON-LAM) with a very large domain extending well beyond Israel to capture upstream weather systems.

---

## Who runs it
- **Organization:** Israel Meteorological Service (IMS)
- **Country / region:** Israel

---

## What area it covers
- **Coverage:** Large regional domain spanning much of Europe and the Middle East  
  - Approximate bounds: **4°E – 45.5°E**, **25.5°N – 53°N**
  - Domain size: ~41.5° × 27.5°  
  - ~1.78 million triangular grid cells

---

## Basic details
- **Model type:** Regional deterministic limited-area NWP
- **Model system:** ICON-LAM
- **Horizontal resolution:** ~2.5 km (R2B10 grid)
- **Vertical levels:** 65
- **Model top:** ~23 km
- **Forecast length:** Up to 90 hours
- **Update frequency:** 2× daily (00Z, 12Z)

---

## Initial and boundary conditions
- **Initial conditions:** ECMWF deterministic IFS
- **Lateral boundary conditions:** ECMWF deterministic IFS
- **Boundary update frequency:** Every 3 hours
- **Data assimilation:** None (no local DA cycle)

---

## What it provides
Deterministic forecasts of:
- temperature
- wind
- precipitation
- pressure
- humidity
- cloud and hydrometeor fields

---

## Data availability
- **Is the data free?** Yes (upon request)
- **Is the data downloadable?** Yes
- **Data formats:** N/A
- **Official download location:**  
  https://ims.gov.il/en/node/179

---

## Access requirements
Users who wish to gain access to IMS open model data must **sign the official Terms of Use PDF** and send the completed form to:

**ims@ims.gov.il**

- **Terms of Use (PDF):**  
  https://ims.gov.il/sites/default/files/2023-05/terms%20of%20use.pdf

---

## Notes
ICON-IL is run on **ECMWF computing infrastructure** as part of the **SEE-MHEWS project**.

The unusually large regional domain allows the model to explicitly resolve upstream synoptic-scale features that influence weather over Israel.

---

## Official documentation
- https://ims.gov.il/en/node/179
