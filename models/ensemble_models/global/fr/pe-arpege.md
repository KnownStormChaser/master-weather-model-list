# PE-ARPEGE (Prévision d'Ensemble ARPEGE)

## What this model is
PEARP is the operational global ensemble prediction system run by Météo-France, providing probabilistic medium-range guidance built on the same stretched-grid spectral core as the deterministic [ARPEGE](../../../nwp_models/global/france/arpege-global.md).

It is the ensemble counterpart to ARPEGE: a set of parallel ARPEGE integrations started from perturbed initial conditions whose spread is designed to sample analysis uncertainty, with additional model-error perturbations introduced for longer lead times. The control member is the (interpolated, unperturbed) ARPEGE run itself. Like ARPEGE, PEARP runs on a stretched grid — finest over Western Europe and progressively coarser away from the central point — so a single global integration delivers higher effective resolution over the French and European domains.

In Météo-France's open-data portal the system is branded **PE-ARPEGE** (and **PEARPEGE** in the API documentation and technical PDF filename); **PEARP** is the long-standing operational and scientific name used in the technical descriptions.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** Global, with variable native resolution (highest over Western Europe)
- **Public distribution grids:**
  - **GLOB025** — 0.25° regular lat–lon, global (90°S–90°N, 0°–359.75°E)
  - **EURAT01** — 0.1° regular lat–lon, Europe/Atlantic (20°N–72°N, 32°W–42°E)

---

## Basic details
- **Model type:** Global ensemble NWP (stretched-grid spectral)
- **Model system / core:** ARPEGE (shared IFS/ARPEGE code base with ECMWF); SURFEX surface modelling
- **Dynamical formulation:** Hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** No (deep convection parameterized; native ~5 km over Western Europe)
- **Ensemble size:** 35 members (1 control + 34 perturbed)
- **Native horizontal resolution:** Variable — stretched spectral geometry TL1798C2.2, ~5 km over Western Europe, coarsening toward the antipodes
- **Vertical levels:** 105 (from ~10 m to 0.1 hPa)
- **Model top:** ~0.1 hPa (~65 km)
- **Forecast length:** 102 hours (4 days 6 hours), all cycles
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 1-hourly or 3-hourly depending on domain
- **Time step:** 240 s

---

## Data assimilation
- **Data assimilation:** No separate PEARP analysis — initial states are derived from the operational ARPEGE analysis and the ARPEGE ensemble of data assimilations (AEARP).
- **Method / cadence:** 35 of the 50 AEARP members are drawn at random and combined with singular-vector perturbations (see below).

---

## Perturbations and design
- **Initial condition perturbations:** Two combined techniques —
  - **Singular vectors** computed at TL95C1 on 65 levels over seven target regions (Europe: 16 vectors; Southern Hemisphere: 10; intertropical band: 28; a complementary Northern Hemisphere domain: 10). Characteristic optimization time is 18 h for Europe/North Atlantic/tropics and 24 h elsewhere. A moist static energy norm is used for the extratropical boxes and a kinetic energy norm for the tropics, where the four tropical boxes shift with the cyclone season.
  - **AEARP ensemble:** 35 of the 50 ARPEGE ensemble-assimilation members, combined with the singular vectors across the seven regions.
- **Model/physics perturbations:** Random perturbation of physics parameters spanning turbulent kinetic energy (TKE), gravity-wave drag (GWD), microphysics, air–sea (oceanic) fluxes, and solar radiation, combined with two deep-convection schemes split across the ensemble — **PCMT** and **Tiedtke–Bechtold** (the latter matching deterministic ARPEGE). Surface processes use SURFEX with multiple surface types per grid cell, as in the deterministic system.

---

## What it provides
Probabilistic global forecasts including:
- Individual ensemble member forecasts (1 control + 34 perturbed)
- Surface and upper-air fields: temperature, wind, humidity, pressure, precipitation, cloud cover, and standard diagnostics
- A companion **statistical-fields product** derived from PEARP (ensemble mean, standard deviation, threshold-exceedance probabilities, and anomaly-from-mean) distributed separately over Europe at 0.1° in GRIB2, daily, at ranges 12–84 h (06 UTC run) and 12–96 h (18 UTC run)

---

## Data availability

PEARP is openly licensed under the Etalab Open Licence v2.0 and is available through two parallel channels.

### 1. data.gouv.fr open-data portal
Bulk GRIB files, one set per run, with no authentication required. Distributed as two separate datasets by grid:
- **PE Arpege GLOB025** (0.25° global): https://meteo.data.gouv.fr/datasets/67ac5dffb832ee7684ed1c40 — mirror: https://www.data.gouv.fr/datasets/pe-arpege-glob025
- **PE Arpege EURAT01** (0.1° Europe/Atlantic): https://meteo.data.gouv.fr/datasets/67ac5e277e258045630bf687 — mirror: https://www.data.gouv.fr/datasets/pe-arpege-eurat01
- **Authentication:** None
- **Use case:** Best for unauthenticated bulk access (full-run GRIB files; GLOB025 packages run ~2–4 GB each, EURAT01 ~1–1.5 GB).

### 2. Météo-France public API portal (targeted WCS)
Single-field retrieval via the OGC WCS "API Ciblée PE Modèles", returning one 2-D lat–lon field per request.
- **Authentication:** Free API key required. Register at https://portail-api.meteofrance.fr
- **Access granularity:** 1 model / 1 member / 1 run / 1 step / 1 level / 1 parameter per request (no aggregation)
- **Retention:** Rolling 5-day window (real-time only; no archive)
- **Landing page:** https://portail-api.meteofrance.fr/web/en/api/PE-ARPEGE

### Summary
- **Is the data free?** Yes (Etalab Open Licence v2.0)
- **License:** Etalab Open Licence v2.0 (attribution required)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (an ASCII variant is also referenced in Météo-France's data.gouv listing; GeoTIFF is available via the WCS service)

---

## Notes
- **Deterministic counterpart:** PEARP is the ensemble version of [ARPEGE](../../../nwp_models/global/france/arpege-global.md), sharing the stretched-grid spectral core, SURFEX surface scheme, and the ARPEGE physics. ARPEGE (interpolated, unperturbed) is PEARP's control member.
- **Regional sibling:** [PEAROME](../../regional/france/pearome.md), the convection-permitting AROME ensemble over Western Europe, is coupled to PEARP for its lateral boundary conditions.
- **Stretched grid:** As with ARPEGE, the published 0.25°/0.1° grids interpolate a model running at finer native resolution over Western Europe; the regridded resolution does not reflect where the integration is actually finest.
- **Two access channels:** PEARP is distributed both as **bulk GRIB files on data.gouv.fr** (no authentication, one dataset per grid — GLOB025 and EURAT01) and via the **targeted WCS ensemble API** (single field per request, 5-day rolling retention, API key required). This parallels deterministic ARPEGE's open access, though PEARP does **not** appear on the community AWS mirror that carries the deterministic packages.
- **2022 upgrade (chaîne 2021-01, 29 June 2022):** Resolution aligned with ARPEGE (7.5 → 5 km over Europe); ARPEGE became the PEARP control member; model-error representation revised (perturbed physics parameters + addition of the PCMT convection scheme); new GLOB025 global grid for 3-D fields; all four cycles unified to 102 h.
- **Forthcoming (cy49t1_op1; e-suite from May 2025, operational switch targeted for autumn 2026):** singular vectors to be removed, with initial-condition perturbations of the near-surface and oceanic state (2 m temperature, 2 m humidity, and the whole ocean water column) drawn from ARPEGE-EDA instead; ARPEGE-EDA itself moving to random parameter perturbation on ~30 model parameters; uncycled ARPEGE-EPS forecasts to run in single precision. Subject to confirmation.

---

## Official documentation
- Météo-France public API (PE-ARPEGE): https://portail-api.meteofrance.fr/web/en/api/PE-ARPEGE
- API Ciblée PE Modèles (EN) — Confluence: https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/853606545/
- Modèles et données de prévision — Confluence: https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- PEARP technical description (PDF, Météo-France, version 30/10/2025): description-technique-pearpege.pdf, linked from the Météo-France public data portal
- PEARP statistical-fields technical description (PDF): description-technique-modele-stats-pearpege.pdf, linked from the same portal page
