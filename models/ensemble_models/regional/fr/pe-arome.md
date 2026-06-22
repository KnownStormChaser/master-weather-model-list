# PE-AROME (Prévision d'Ensemble AROME)

## What this model is
PE-AROME is the operational convection-permitting regional ensemble prediction system run by Météo-France over metropolitan France and surrounding Western Europe, built on the same core as the deterministic [AROME France](../../../nwp_models/regional/france/arome-france.md).

It provides short-range probabilistic guidance for high-impact weather — thunderstorms, heavy precipitation, wind, fog — by running a set of perturbed AROME integrations that sample uncertainty in the initial conditions, the lateral boundary conditions, the surface state, and the model formulation itself. It is the convection-scale complement to the global [PE-ARPEGE](../../global/france/pe-arpege.md) ensemble, nested inside it for boundary forcing.

In Météo-France's open-data portal the system is branded **PE-AROME**; **PEAROME** is the equivalent name used in the technical descriptions. This entry covers the **metropolitan** PEAROME (EURW1S40 domain); the separate overseas suite **PEAROME-OM** (AROME Outre-Mer ensemble) is documented as its own entry.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** France and surrounding Western Europe
- **Operational domain:** EURW1S40
  - Approximate bounds: **55.4°N – 37.5°N**, **12°W – 16°E**
  - Native grid is trapezoidal (not strictly rectangular)

---

## Basic details
- **Model type:** Regional ensemble NWP (non-hydrostatic, convection-permitting)
- **Model system / core:** AROME (spectral limited-area model, ALADIN-NH dynamical core); SURFEX surface modelling
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved at 1.3 km native resolution; shallow convection parameterized)
- **Ensemble size:** 26 members (1 control + 25 perturbed) since the cy48t1_op1 suite (operational mid-October 2024); previously 17 members (1 control + 16 perturbed)
- **Native horizontal resolution:** ~1.3 km
- **Public distribution grid:** EURW1S40 — 0.025° (~2.5 km) regular latitude–longitude
- **Vertical levels:** 90 (same vertical grid as AROME; model top ~10 hPa)
- **Forecast length:** 51 hours (all cycles)
- **Update frequency / cycles:** 4× daily (03, 09, 15, 21 UTC)
- **Temporal output resolution:** Hourly
- **Time step:** 50 s

---

## Data assimilation
- **Data assimilation:** Yes — upper-air initial states draw on the AROME ensemble of data assimilations (AE-AROME), a 50-member ensemble running 3D-Var in the OOPS framework on a 3-hour cycle (50 members since cy48t1_op1, up from 25).
- **Method / cadence:** Perturbations are extracted around the AE-AROME ensemble mean from 3-hour AE-AROME forecasts and added to a synchronous 3D-Var analysis (see Perturbations and design).

---

## Initial and boundary conditions
- **Initial conditions (upper air):** Perturbed states formed by adding AE-AROME ensemble perturbations (taken around the AE-AROME mean, from 3-hour forecasts) to a synchronous 3DVAR analysis.
- **Initial conditions (surface):** Built by randomly perturbing the deterministic AROME-France surface analysis. Perturbations are applied to both physiographic variables (vegetation index, albedo, …) and prognostic variables (SST, soil temperature and moisture, …).
- **Boundary conditions:** Provided by [PE-ARPEGE](../../global/france/pe-arpege.md), with **hourly coupling**. Cycles are time-lagged against PE-ARPEGE: PE-ARPEGE 00 UTC → PE-AROME 03 UTC, 06 → 09, 12 → 15, 18 → 21. The perturbed PE-AROME members draw their lateral boundary conditions from PE-ARPEGE members selected by automatic classification (penalized Ward method) from PE-ARPEGE's 34 perturbed members; the control couples to deterministic ARPEGE.

---

## Perturbations and design
- **Initial condition perturbations:** AE-AROME-derived upper-air perturbations plus random surface-field perturbations (see above).
- **Boundary perturbations:** Inherited from the coupled PE-ARPEGE members (which themselves combine singular vectors and AEARP analysis perturbations).
- **Model/physics perturbations:** Stochastic physics — random multiplicative perturbations applied to the physical tendencies of wind, temperature, and water-vapour content.

---

## What it provides
Probabilistic regional forecasts including:
- Individual ensemble member forecasts (1 control + 16 perturbed)
- Surface and upper-air fields: temperature, humidity, wind, pressure, precipitation, cloud, and standard diagnostics at convection-permitting scale
- Convective and aeronautical diagnostics (added in the June 2022 upgrade)

---

## Data availability
- **Is the data free?** Yes (Etalab Open Licence)
- **License:** Etalab Open Licence (attribution required)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (GeoTIFF also available via the WCS service)
- **Access:** Météo-France public API portal only (targeted/WCS single-field "API Ciblée PE Modèles"). Like PE-ARPEGE, PE-AROME is **not** mirrored on `object.data.gouv.fr` bulk storage or the community AWS mirror.
  - **Authentication:** Free API key required. Register at https://portail-api.meteofrance.fr
  - **Access granularity:** 1 model / 1 member / 1 run / 1 step / 1 level / 1 parameter per WCS request (single 2-D lat–lon field; no aggregation)
  - **Retention:** Rolling 5-day window (real-time only; no archive)
- **Official download location:** https://portail-api.meteofrance.fr/web/en/api/PE-AROME

---

## Notes
- **Deterministic counterpart:** PE-AROME is the ensemble version of [AROME France](../../../nwp_models/regional/france/arome-france.md), sharing the ALADIN-NH non-hydrostatic core, 90-level vertical grid, SURFEX surface scheme, and EURW1S40 domain. The control member is an AROME run.
- **Parent ensemble:** Lateral boundary conditions come from the global [PE-ARPEGE](../../global/france/pe-arpege.md); the two form a nested ensemble pair analogous to the deterministic [AROME France](../../../nwp_models/regional/france/arome-france.md) ← [ARPEGE](../../../nwp_models/global/france/arpege-global.md) chain.
- **Overseas sibling:** A separate **PEAROME-OM** (Prévision d'Ensemble AROME Outre-Mer) covers France's overseas domains at 2.5 km native resolution and is documented under its own entry; this entry is the metropolitan EURW1S40 system only.
- **Stretched/trapezoidal grid:** As with AROME France, the native grid is trapezoidal; GRIB files on the regular 0.025° lat–lon grid may contain missing values outside the effective domain, and the published 0.025° grid is interpolated from the finer 1.3 km native mesh.
- **Access model differs from deterministic AROME:** PE-AROME is distributed through the WCS-based ensemble API with a 5-day rolling retention and per-member/per-field granularity, rather than the bulk GRIB "Paquets" packages and unauthenticated data.gouv.fr / AWS channels used for deterministic AROME. Users wanting whole-field or whole-member bulk pulls should plan around the single-field WCS access pattern.
- **Forthcoming (cy49t1_op1; e-suite from autumn 2025, operational switch targeted for autumn 2026):** model-error representation to combine SPPT with random parameter perturbation (RPP; ~19 perturbed parameters); AROME-EDA to move to 3DEnVar+SDL. Subject to confirmation.

---

## Recent version history
- **cy48t1_op1 (operational mid-October 2024):** Ensemble enlarged from 17 to 26 members (16 → 25 perturbed). The driving AROME-EDA grew from 25 to 50 members and moved to the OOPS framework; deterministic AROME-France moved to 3DEnVar.
- **2022 (cy46t1-era upgrade):** Native resolution increased from 2.5 km to 1.3 km, matching deterministic AROME-France; ensemble reframed as 16 perturbed + control.
- **June 2022:** Added convective and aeronautical diagnostics to the distributed product.

---

## Official documentation
- Météo-France public API (PE-AROME): https://portail-api.meteofrance.fr/web/en/api/PE-AROME
- API Ciblée PE Modèles (EN) — Confluence: https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/853606545/
- Modèles et données de prévision — Confluence: https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- PEAROME technical description (PDF, Météo-France, version 30/10/2025): description-technique-pearome.pdf, linked from the Météo-France public data portal
