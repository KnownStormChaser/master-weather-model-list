# ICON-2I-EPS (Italy – High Resolution Convection-Permitting Ensemble)

## What this model is
ICON-2I-EPS is the **convection-permitting ensemble prediction system** of Italy's high-resolution ICON-2I suite. It runs the ICON-2I model as a 20-member ensemble to represent forecast uncertainty at the convective scale, providing probabilistic guidance for weather forecasting and an assessment of forecast confidence.

By running many forecasts from slightly different starting conditions, the ensemble samples the range of plausible outcomes: agreement among members indicates higher confidence, while divergence highlights situations where the forecast is more uncertain. This makes it particularly useful for risk-informed decision-making around high-impact weather.

---

## Who runs it
- **Organization:** Agenzia ItaliaMeteo, in collaboration with Arpae Emilia-Romagna and Cineca (HPC infrastructure)
- **Country / region:** Italy
- **Model core developed by:** Deutscher Wetterdienst (DWD), within the ICON partnership (MPI-M, DWD, KIT, DKRZ, CSCS, COSMO, CLM)

Responsibility for national NWP passed from Arpae Emilia-Romagna to Agenzia ItaliaMeteo in 2025; under a collaboration agreement, the two agencies now jointly maintain and develop the system. ICON-2I-EPS is developed and maintained by ItaliaMeteo and Arpae.

---

## What area it covers
- **Coverage:** Entire Italian territory and surrounding areas (extends into neighbouring countries and the central Mediterranean)
- **Domain details:** Same domain as ICON-2I — single national domain, approximately 3°E–21°E and 35°N–47.5°N

---

## Basic details
- **Model type:** Ensemble NWP (regional, convection-permitting)
- **Model system / core:** ICON (ICOsahedral Nonhydrostatic) — same core as the deterministic ICON-2I
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** Yes — convection-permitting (deep convection resolved, shallow convection parameterized) at the 2.2 km convective grey-zone scale
- **Ensemble size:** 20 members
- **Horizontal resolution:** 2.2 km (same as ICON-2I)
- **Vertical levels:** 65 height-based terrain-following levels
- **Forecast length:** Up to 51 hours
- **Update frequency / cycles:** 1× daily, initialized at 21 UTC

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** Initial conditions are generated with the KENDA-LETKF (Local Ensemble Transform Kalman Filter) ensemble data assimilation system shared with the rest of the ICON-2I suite (40 members plus a deterministic run, hourly assimilation cycles with IAU and RTPS, assimilation up to ~200 hPa). Assimilated observations include SYNOP (wind and surface pressure), TEMP, aircraft (AIREP / AMDAR), radar reflectivity volumes and radial winds through KENDA, and radar-derived precipitation via Latent Heat Nudging (LHN).

---

## Initial and boundary conditions
- **Initial conditions:** ICON-2I KENDA-LETKF analyses
- **Boundary conditions:** ECMWF IFS (IFS-HRES)

---

## Perturbations and design
- **Initial condition perturbations:** Analysis perturbations from the LETKF ensemble data assimilation system
- **Model/physics perturbations:** None applied — ICON-2I-EPS perturbs only the analysis (initial conditions). This distinguishes it from the COSMO-LEPS system (also maintained by Arpae), which perturbs both the analysis and physical parameters.
- **Stochastic schemes:** TBD

---

## What it provides
Ensemble outputs and derived probabilistic products, including:
- ensemble member forecasts at 2.2 km resolution
- ensemble mean, minimum, maximum, and percentile fields (e.g. 90th percentile)
- probability maps (e.g. probability of precipitation exceeding given thresholds)
- summary products ("chess boards") aggregating exceedance probabilities over civil-protection warning areas

---

## Data availability
- **Is the data free?** Yes
- **License:** CC BY 4.0 (attribution required). See https://meteohub.agenziaitaliameteo.it/app/license
- **Is the data downloadable?** Yes, but access differs from the deterministic ICON-2I products (see note below)
- **Data formats:** GRIB2 (TBD — to be confirmed for the EPS products specifically)
- **Official download location:**
  - MeteoHub application (account required): https://meteohub.agenziaitaliameteo.it/app/data/datasets?network=ICON_2I_EPS
  - Open data catalog (dataset metadata): https://dati.agenziaitaliameteo.it

---

## Notes
- ICON-2I-EPS is the ensemble counterpart of the deterministic [ICON-2I](../../models/nwp_models/regional/italy/icon-2i.md) and its rapid-update sibling [ICON-2I-RUC](../../models/nwp_models/regional/italy/icon-2i-ruc.md) — same model core, domain, 2.2 km grid, vertical levels, and data assimilation system, run as a 20-member ensemble once daily at 21 UTC out to +51 h.
- **Access differs from the other ICON-2I products.** Unlike the deterministic ICON-2I and ICON-2I-RUC, which are published as open raw cycle archives under `meteohub.agenziaitaliameteo.it/nwp/`, ICON-2I-EPS does not appear under that path. It is instead available through the MeteoHub web application (`/app/`), which requires creating an account to download data. Whether programmatic/API access is offered is unconfirmed. The data remains licensed CC BY 4.0; the account requirement is an access mechanism, not a licensing restriction. **This should be verified by creating an account and checking the available access methods before publishing.**
- ICON-2I-EPS currently runs alongside **COSMO-LEPS** (a 20-member, 7 km ensemble developed by the COSMO Consortium and maintained by Arpae, perturbing both analysis and physical parameters). COSMO-LEPS is planned to be replaced by **ICON-LEPS**.
- Planned developments include enhancement of the ensemble component: additional perturbation methods, more ensemble runs, and expanded probabilistic products for forecasters.

---

## Official documentation
- ItaliaMeteo MeteoHub portal: https://meteohub.agenziaitaliameteo.it/
- ItaliaMeteo open data catalog: https://dati.agenziaitaliameteo.it
- ItaliaMeteo: https://www.agenziaitaliameteo.it/
