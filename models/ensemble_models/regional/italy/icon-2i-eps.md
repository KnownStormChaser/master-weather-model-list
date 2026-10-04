# ICON-2I-EPS (Italy – High Resolution Convection-Permitting Ensemble)

## What this model is
ICON-2I-EPS is the **convection-permitting ensemble prediction system** of Italy's high-resolution ICON-2I suite. It runs the ICON-2I model as a 20-member ensemble to represent forecast uncertainty at the convective scale, providing probabilistic guidance for weather forecasting and an assessment of forecast confidence.

By running many forecasts from slightly different starting conditions, the ensemble samples the range of plausible outcomes: agreement among members indicates higher confidence, while divergence highlights situations where the forecast is more uncertain. This makes it particularly useful for risk-informed decision-making around high-impact weather.

---

## Who runs it
- **Organization:** Agenzia ItaliaMeteo, in collaboration with Arpae Emilia-Romagna and Cineca (HPC infrastructure)
- **Country / region:** Italy
- **Model core developed by:** Deutscher Wetterdienst (DWD), within the ICON partnership (MPI-M, DWD, KIT, DKRZ, CSCS, COSMO, CLM)

Responsibility for national NWP passed from Arpae Emilia-Romagna to Agenzia ItaliaMeteo in 2025; under a collaboration agreement, the two agencies now jointly maintain and develop the system. ICON-2I-EPS is developed and maintained by ItaliaMeteo and Arpae. The suites are financed by ItaliaMeteo; the ensemble (and the RUC) are operated by Arpae personnel, while the main deterministic ICON-2I run is operated by Cineca personnel. Handover of suite management to Cineca is in progress, with development remaining under Arpae (Arpae contribution to the Italian national report, COSMO General Meeting 2026).

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
- **Boundary conditions:** ECMWF IFS (IFS-HRES). *Flag:* Arpae's 2026 suite diagram shows a single "ECMWF IFS HRES + ENS" boundary source feeding KENDA, ICON-2I-EPS, ICON-2I and ICON-2I-RUC. Whether the ensemble members take IFS ENS boundaries (the usual design for a LAM ensemble) is not stated — TBD.

---

## Perturbations and design
- **Initial condition perturbations:** Analysis perturbations from the LETKF ensemble data assimilation system
- **Model/physics perturbations:** None applied — ICON-2I-EPS perturbs only the analysis (initial conditions). This distinguishes it from the COSMO-LEPS system (also maintained by Arpae), which perturbs both the analysis and physical parameters. Arpae is testing alternative perturbation approaches for ICON ensembles, including SPP, within the COSMO priority project KAOS (COSMO GM 2026); none is reported as operational in ICON-2I-EPS.
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
- ICON-2I-EPS is the ensemble counterpart of the deterministic [ICON-2I](../../../nwp_models/regional/italy/icon-2i.md) and its rapid-update sibling [ICON-2I-RUC](../../../nwp_models/regional/italy/icon-2i-ruc.md) — same model core, domain, 2.2 km grid, vertical levels, and data assimilation system, run as a 20-member ensemble once daily at 21 UTC out to +51 h.
- **Access differs from the other ICON-2I products.** Unlike the deterministic ICON-2I and ICON-2I-RUC, which are published as open raw cycle archives under `meteohub.agenziaitaliameteo.it/nwp/`, ICON-2I-EPS does not appear under that path (re-checked 2026-10-04: the `/nwp/` root lists `ICON-2I_SURFACE_PRESSURE_LEVELS`, `ICON_2I_RUC`, `MOLOCH_AIM`, `SEASONAL`, `WRF`, `shyfem` and `ww3` only). It is instead available through the MeteoHub web application (`/app/`), which requires creating an account to download data. Whether programmatic/API access is offered is unconfirmed. The data remains licensed CC BY 4.0; the account requirement is an access mechanism, not a licensing restriction. **This should be verified by creating an account and checking the available access methods before publishing.**
- ICON-2I-EPS currently runs alongside **COSMO-LEPS** (a 20-member, 7 km ensemble developed by the COSMO Consortium and maintained by Arpae, perturbing both analysis and physical parameters). COSMO-LEPS is planned to be replaced by **ICON-LEPS**, which Arpae reports is now in a regular testing phase ahead of that replacement (COSMO GM 2026).
- Planned developments include enhancement of the ensemble component: additional perturbation methods, more ensemble runs, and expanded probabilistic products for forecasters.
- **Planned expansion (COSMO GM 2026):** Arpae plans to run the ensemble **twice a day and out to +72 h by the end of 2026**, up from once daily (21 UTC) to +51 h. Update Basic details when the change appears in MeteoHub.

---

## Recent version history
- No dated configuration changes recorded yet. The twice-daily / +72 h extension announced at COSMO GM 2026 should be entered here with its effective date once it goes live.

---

## Official documentation
- ItaliaMeteo MeteoHub portal: https://meteohub.agenziaitaliameteo.it/
- ItaliaMeteo open data catalog: https://dati.agenziaitaliameteo.it
- ItaliaMeteo: https://www.agenziaitaliameteo.it/
