# ICON-IL (ICON-LAM)

## What this model is
ICON-IL is the deterministic limited-area NWP model run by the Israel Meteorological Service (IMS) for southeast Europe. It is an ICON (ICOsahedral Nonhydrostatic) limited-area (ICON-LAM) configuration, run on ECMWF's HPC facility as the IMS contribution to the WMO South-East European Multi-Hazard Early Warning Advisory System (SEE-MHEWS-A). Its large domain extends well beyond Israel to resolve upstream synoptic systems. IMS develops it within the COSMO consortium.

---

## Who runs it
- **Organization:** Israel Meteorological Service (IMS)
- **Country / region:** Israel (run on ECMWF HPC; serves SEE-MHEWS-A countries across southeast Europe)

---

## What area it covers
- **Coverage:** Southeast Europe and the eastern Mediterranean
- **Domain details:** 4–45.5°E, 25.5–53°N, centered at 24.75°E, 39.25°N; ~41.5° × 27.5°; 1,783,780 triangular grid cells

---

## Basic details
- **Model type:** Regional deterministic limited-area NWP (convection-permitting)
- **Model system / core:** ICON-LAM
- **Dynamical formulation:** Non-hydrostatic (ICON)
- **Convection-allowing:** Yes — ~2.5 km grid spacing
- **Horizontal resolution:** ~2.5 km (R2B10)
- **Vertical levels:** 65
- **Model top:** ~23 km
- **Forecast length:** Up to 90 hours
- **Update frequency / cycles:** 2× daily (00/12 UTC)
- **Temporal output resolution:** Public maps shown at 3-hourly steps; full output resolution TBD

Map-update timing: first maps ~06:10 / 18:10 UTC; full 90-hour map sets ~07:45 / 19:45 UTC.

---

## Data assimilation
- **Data assimilation:** None currently performed (per the live IMS ICON-LAM page)

*Conflict to resolve:* ECMWF Newsletter 171 (2022) reported that OPERA (EUMETNET) radar assimilation via latent heat nudging became operational in ICON-IL2.5 in November 2021. The current IMS page states no DA is performed. The LHN scheme may since have been discontinued, or the public page may describe a configuration without it. Verify current status.

---

## Initial and boundary conditions
- **Initial conditions:** ECMWF deterministic IFS
- **Lateral boundary conditions:** ECMWF deterministic IFS
- **Boundary update frequency:** Every 3 hours

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
- **Is the data free?** Yes — open to the public, **upon signing a Terms of Use form** (see access requirements below)
- **License:** IMS Terms of Use ("Rights and terms of use" form on the Data Access page) — https://ims.gov.il/sites/default/files/2023-05/terms%20of%20use.pdf
- **Is the data downloadable?** Yes
- **Data formats:** TBD — not stated on the IMS Data Access page; ICON output is natively GRIB2. Verify.
- **Data retention:** Portal holds the **last month** of data; older data by request to ims@ims.gov.il
- **Official download location:**
  https://ims.gov.il/en/node/179 (Radar & Models Products)

### Access requirements
Access is "open to the public, upon signing term of use." Download the Terms of Use form, complete it, and send it to IMS (ims@ims.gov.il). See the repository scope note below regarding the permission step.

---

## Notes
- **SEE-MHEWS-A:** ICON-IL is the IMS deterministic contribution to the WMO SEE-MHEWS-A early-warning system. Forecasts are visualised on the project's Common Information Platform (CIP) — a separate, **registration-gated** professional platform (account sign-up required, apparently aimed at participating services' forecasters), not an open raw-data channel.
- **Wider distribution:** also distributed for Mediterranean cyclone-tracking research (COST Action CA19109) and for verification within the COSMO consortium.
- **Sibling model:** the IMS deterministic COSMO-IL (2.8 km local) and the ECMWF-HPC CO-IL2.5 / CO-IL-EPS suite cover the smaller Eastern Mediterranean domain (separate entries).
- **Aerosol coupling (research):** an ICON-IL2.5-CAMS variant coupling CAMS predicted 3D aerosols to ecRad was in development as of 2022; operational status unconfirmed.

---

## Official documentation
- IMS Radar & Models Products (data access): https://ims.gov.il/en/node/179
- IMS ICON-LAM page: https://ims.gov.il/en/ICON_LAM
- ECMWF Newsletter 171 — "Israel uses ECMWF supercomputer to advance regional forecasting" (2022): https://www.ecmwf.int/en/newsletter/171/earth-system-science/israel-uses-ecmwf-supercomputer-advance-regional-forecasting
