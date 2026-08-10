# PE-ARPEGE GLOB025

## What this model is
PE-ARPEGE is the operational global ensemble prediction system run by Météo-France, providing probabilistic medium-range guidance built on the same stretched-grid spectral core as the deterministic [ARPEGE](../../../nwp_models/global/france/arpege-global.md).

It is the ensemble counterpart to ARPEGE: a set of parallel ARPEGE integrations started from perturbed initial conditions whose spread is designed to sample analysis uncertainty, with additional model-error perturbations introduced for longer lead times. The control member is the (interpolated, unperturbed) ARPEGE run itself. Like ARPEGE, PE-ARPEGE runs on a stretched grid — finest over Western Europe and progressively coarser away from the central point — so a single global integration delivers higher effective resolution over the French and European domains.

> **Scope of this entry.** This entry covers the **GLOB025 global distribution at 0.25°**. The same ensemble is also published on a 0.1° Europe/Atlantic grid (EURAT01), documented separately in [PE-ARPEGE Europe (EURAT01)](../../regional/fr/pe-arpege-europe.md). Splitting them follows the convention already applied to [ARPEGE Global](../../../nwp_models/global/france/arpege-global.md) and [ARPEGE Europe](../../../nwp_models/regional/france/arpege-europe.md): the two grids are distributed as separate datasets from separate bucket prefixes with different volumes and content.

> **Naming.** Météo-France's open-data portal brands the system **PE-ARPEGE** (and **PEARPEGE** in API documentation); **PEARP** is the long-standing operational and scientific name used in the technical descriptions. Confusingly, the data.gouv.fr resource *titles* read `pearp_glob025_…` while the files they point at are named `arpege_glob025_…` — see *Notes*.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** Global, with variable native resolution (highest over Western Europe)
- **Grid (live-verified):** **GLOB025** — `regular_ll`, **1440 × 721**, 0.25° × 0.25°, **1,038,240 points per field**. First grid point 90.0°N / 0.0°E, last −90.0° / 359.75°E, north-to-south row order. `bitmapPresent = 0` — no missing values.

---

## Basic details
- **Model type:** Global ensemble NWP (stretched-grid spectral)
- **Model system / core:** ARPEGE (shared IFS/ARPEGE code base with ECMWF); SURFEX surface modelling
- **Dynamical formulation:** Hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** No (deep convection parameterized; native ~5 km over Western Europe)
- **Ensemble size (live-verified):** **35 members — 1 control + 34 perturbed.** `perturbationNumber` runs **0 to 34**, with member 0 the control; `numberOfForecastsInEnsemble = 35` on every message, consistent with the member count.
- **Native horizontal resolution:** Variable — stretched spectral geometry TL1798C2.2, ~5 km over Western Europe, coarsening toward the antipodes
- **Vertical levels:** 105 in the model (from ~10 m to 0.1 hPa); the GLOB025 distribution publishes a small selected set of pressure, height and PV levels — see *What it provides*
- **Model top:** ~0.1 hPa (~65 km)
- **Forecast length (live-verified):** **102 hours**, all cycles — steps `00` through `102`
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (live-verified):** **Hourly throughout** — 103 files per run, one per step. The *content* of those files varies sharply with step; see *Three content tiers* below.
- **Time step:** 240 s

---

## Data assimilation
- **Data assimilation:** No separate PE-ARPEGE analysis — initial states are derived from the operational ARPEGE analysis and the ARPEGE ensemble of data assimilations (AEARP).
- **Method / cadence:** 35 of the 50 AEARP members are drawn at random and combined with singular-vector perturbations (see below).

---

## Perturbations and design
- **Initial condition perturbations:** Two combined techniques —
  - **Singular vectors** computed at TL95C1 on 65 levels over seven target regions (Europe: 16 vectors; Southern Hemisphere: 10; intertropical band: 28; a complementary Northern Hemisphere domain: 10). Characteristic optimization time is 18 h for Europe/North Atlantic/tropics and 24 h elsewhere. A moist static energy norm is used for the extratropical boxes and a kinetic energy norm for the tropics, where the four tropical boxes shift with the cyclone season.
  - **AEARP ensemble:** 35 of the 50 ARPEGE ensemble-assimilation members, combined with the singular vectors across the seven regions.
- **Model/physics perturbations:** Random perturbation of physics parameters spanning turbulent kinetic energy (TKE), gravity-wave drag (GWD), microphysics, air–sea (oceanic) fluxes, and solar radiation, combined with two deep-convection schemes split across the ensemble — **PCMT** and **Tiedtke–Bechtold** (the latter matching deterministic ARPEGE). Surface processes use SURFEX with multiple surface types per grid cell, as in the deterministic system.

---

## What it provides

The distribution is **raw member forecasts only** — every message is an individual ensemble member (`productDefinitionTemplateNumber = 1` or `11`). **No ensemble mean, spread, percentile or probability fields are published in this dataset.** Derived probabilistic products exist as a separate PEARP statistical-fields product distributed over Europe at 0.1°, and are not part of GLOB025.

### Three content tiers by step (live-verified)

This is the defining structural feature of the GLOB025 distribution, and it is not documented anywhere:

| Tier | Steps | Params | Messages/member | File size |
|---|---|---:|---:|---|
| **Full** | every multiple of 3 (0, 3, 6, … 102) — 34 steps | 66 | 135 | ~3.8–4.1 GB |
| **Reduced** | non-multiples of 3 up to 47 — 33 steps | 58 | 75 | ~2.1–2.9 GB |
| **Minimal** | non-multiples of 3 from 49 to 101 — 36 steps | **3** | **3** | ~80 MB |

The minimal tier carries **only `10u`, `10v` and `prmsl`** — 105 messages in total (3 fields × 35 members). Anything else you ask for beyond 48 h exists only at 3-hourly resolution.

The full tier adds, over the reduced tier: isobaric `u`, `v`, `t`, `r` and `z` on 10–11 pressure levels, `min_2t`/`max_2t`, and top-of-atmosphere thermal radiation (`ttr`).

Code that assumes uniform hourly content across the run will find fields silently missing at two-thirds of the steps beyond 48 h.

### Level sets (live-verified, full tier)
- **Isobaric (10 levels):** 200, 250, 300, 400, 500, 700, 800, 850, 925, 1000 hPa — `u`, `v`, `t`, `r`. **`z` adds a 50 hPa level** for 11 in total, the only field reaching the stratosphere.
- **Isobaric, single-level:** `papt` and `absv` at 850 hPa only
- **Height above ground:** `t` at 10/20/50/100 m; `r` at 10/20/50 m; `u`/`v` at 50/100/500 m; `clwc`/`ciwc` at 10/20 m; standard 2 m and 10 m diagnostics
- **Potential vorticity: two surfaces at 1.5 and 2.0 PVU** (encoded 1500, 2000), carrying `u`, `v`, `z` and `pt`
- **Isothermal levels:** `h` (geometric height) at levels 273 and 274 — the 0 °C and 1 °C isotherm heights, i.e. freezing-level products

### Notable content
Beyond the standard surface and upper-air set, GLOB025 carries a strong aviation- and hazard-oriented selection: **ceiling** (`ceil`), **cloud base and cloud top pressure** (`pcdb`, `pres` at `cloudBase`/`cloudTop`), **cumulonimbus top pressure** (`pres` at `cumulonimbusTop`), **minimum visibility**, **precipitation type** (both time-average and time-maximum), **CAPE**, freezing-level heights, and lightning-related fields — all as 35-member ensembles.

---

## Data availability

- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence version 2.0** (Etalab v2.0; attribution required)
- **High-Value Dataset:** **No.** Unlike every deterministic Météo-France NWP dataset in this catalogue, the PE-ARPEGE datasets carry **no HVD badge**.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (files use the `.grib` extension, not `.grib2`; `grid_second_order` packing)
- **Official download location:**
  https://www.data.gouv.fr/datasets/pe-arpege-glob025

### Primary access — data.gouv.fr object storage (no authentication)

> **PE-ARPEGE lives in a different bucket from the deterministic models.** The deterministic ARPEGE and AROME products are in `meteofrance-pnt`; the ensembles are in **`meteofrance-pe`**, with a different path layout and a much shorter retention window.

- **URL pattern (live-verified):**
  ```
  https://meteofrance-pe.s3.rbx.io.cloud.ovh.net/prod/data/arpege/glob025/{YYYYMMDDHH}/arpege_glob025_{YYYYMMDDHH}_{step}:00.grib
  ```
  where `{YYYYMMDDHH}` is the run in compact form (e.g. `202608091200` — **not** the ISO-8601 form used by the `meteofrance-pnt` bucket) and `{step}` is the forecast hour **without zero-padding**, from `0` to `102`. Note the literal colon before `00` in the filename.

- **Examples:**
  ```
  .../prod/data/arpege/glob025/202608091800/arpege_glob025_202608091800_00:00.grib
  .../prod/data/arpege/glob025/202608091200/arpege_glob025_202608091200_102:00.grib
  ```

- **The bucket is anonymously listable:**
  ```bash
  # available runs
  curl -s "https://meteofrance-pe.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=prod/data/arpege/glob025/&delimiter=/&max-keys=1000"

  # all files in one run
  curl -s "https://meteofrance-pe.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=prod/data/arpege/glob025/202608091200/&max-keys=1000"
  ```

- **Retention (live-verified): approximately 36 hours — six cycles.** On 2026-08-09 at ~23:00 UTC the bucket held exactly six runs, from `202608081200` to `202608091800`. This is **an order of magnitude shorter than the 15-day window** on the deterministic `meteofrance-pnt` bucket, and shorter than the 5-day rolling window the API portal offers for the same system. Anyone wanting a PE-ARPEGE archive must pull within roughly a day and a half.

- **Publication latency (live-verified)**, measured across five complete cycles from object `Last-Modified` timestamps:

  | Run | First file | Last file |
  |---|---|---|
  | 2026-08-08 12 UTC | T+3 h 17 m | T+5 h 08 m |
  | 2026-08-08 18 UTC | T+4 h 05 m | T+5 h 51 m |
  | 2026-08-09 00 UTC | T+3 h 15 m | T+5 h 26 m |
  | 2026-08-09 06 UTC | T+4 h 08 m | T+5 h 53 m |
  | 2026-08-09 12 UTC | T+2 h 56 m | T+4 h 42 m |

  As with the deterministic products, the 00/12 UTC cycles run ahead of 06/18 UTC. Completion is T+4 h 42 m to T+5 h 53 m. Given the ~36-hour retention, the usable window between a cycle completing and its deletion is roughly 30 hours.

- **Volume (live-verified): ~213 GB per cycle, ~851 GB/day.** This is by a wide margin the largest single product in the catalogue — roughly five times the daily output of ARPEGE Global and nearly five times AROME's. Files range from ~80 MB (minimal tier) to ~4.1 GB (full tier). Bandwidth planning matters here: pulling one complete run takes longer than the retention window allows for casual retries.

### Secondary access — Météo-France API portal

Météo-France also exposes PE-ARPEGE through its developer portal via a targeted OGC WCS service ("API Ciblée PE Modèles"), returning one 2-D field per request (1 model / 1 member / 1 run / 1 step / 1 level / 1 parameter, no aggregation), with a 5-day rolling retention. Free registration and an API key required: https://portail-api.meteofrance.fr

This entry does not document the API routes. But note the trade-off is real here in a way it is not for the deterministic products: **the API's 5-day retention is longer than the object storage's ~36 hours**, and single-field requests avoid multi-gigabyte downloads. For anyone needing a handful of fields rather than whole members, the API is the better fit despite the key requirement.

---

## Notes

### Encoding conventions (live-verified)
- **All messages:** GRIB edition 2, `tablesVersion = 15`, centre `lfpw` (Météo-France Toulouse), `generatingProcessIdentifier = 211` (same as deterministic ARPEGE), **`typeOfGeneratingProcess = 4`** (ensemble forecast — this, not the process identifier, is what distinguishes PE-ARPEGE messages from deterministic ARPEGE ones).
- **`typeOfProcessedData = 'cp'`** (control and perturbed forecasts) on every message.
- **Packing is `grid_second_order`**, not the `grid_ccsds` used throughout the deterministic `meteofrance-pnt` products. Any pipeline tuned for CCSDS should be checked against these files.
- **Member packaging:** all 35 members for a given step are concatenated into **one file per step**, one GRIB message per member per field. There is no per-member file split.

### `typeOfEnsembleForecast` is unset
Every message carries **`typeOfEnsembleForecast = 255`** — the missing/undefined value. GRIB2 Code Table 4.6 would normally distinguish an unperturbed control forecast (0 or 1) from a perturbed member (2 or 3), and readers that select the control on that key will find nothing.

**The control is member `perturbationNumber = 0`**; perturbed members are 1–34. Select on `perturbationNumber`, not on `typeOfEnsembleForecast`.

### Filename mismatch between the portal and the files
The data.gouv.fr resource list displays titles of the form `pearp_glob025_202608091200_102:00.grib`, but every resource URL points at a file named `arpege_glob025_202608091200_102:00.grib`. The `pearp_` prefix does not exist in the bucket. Build URLs from the pattern above rather than from the displayed titles.

### The dataset page is a rolling file window, not a run
The GLOB025 dataset page lists exactly **103 resources at any moment, spanning two runs** — at the time of verification, 69 files from the 12 UTC run and 34 from the 18 UTC run then in progress. It is a rolling window over the most recently written objects, not a manifest of one cycle. Enumerate the bucket prefix for a specific run rather than scraping the dataset page.

### Parameters that do not decode
Several fields decode as `unknown` in ecCodes 2.48.0. Those resolvable from Météo-France's shared ARPEGE/AROME parameter glossary:

| Encoding | Identification |
|---|---|
| 0/6/1 | **Total cloud cover** (`NEBUL`) — WMO-standard encoding ecCodes leaves unresolved |
| 0/1/64 | **Total column water vapour** (`COLONNE_VAPO`) |
| 0/6/11 | **Cloud base** |
| 0/17/4 | **Lightning flash density**, accumulated |

Still unresolved (**TBD**): **0/6/2** (surface, instantaneous — convective cloud cover on the WMO table, unconfirmed), **0/7/7** and **0/7/200** (thermodynamic-stability category; 0/7/7 is CIN on the WMO table, 0/7/200 is local), **0/19/201** (local, time-minimum), and **2/3/193** at both surface and 3 (2.5 m) `depthBelowLand` — the same undocumented soil parameters seen in the deterministic ARPEGE SP3 package.

`CAPE_INS` again has no resolvable level type, as elsewhere in the Météo-France distributions.

### Access channels that no longer work
- **`object.data.gouv.fr` is dead** for all Météo-France products; the `meteo.data.gouv.fr/datasets/{id}` landing-page IDs previously listed in this entry are superseded by the `www.data.gouv.fr` slugs above.
- **The community AWS mirror is gone** (`mf-models-on-aws.s3.amazonaws.com` returns `NoSuchBucket`). It never carried PE-ARPEGE in any case.

### Model family and relationships
- **Deterministic counterpart:** [ARPEGE](../../../nwp_models/global/france/arpege-global.md), sharing the stretched-grid spectral core, SURFEX surface scheme and physics. ARPEGE (interpolated, unperturbed) is PE-ARPEGE's control member.
- **Regional distribution of this ensemble:** [PE-ARPEGE Europe (EURAT01)](../../regional/fr/pe-arpege-europe.md) — same 35 members at 0.1° over Europe/Atlantic, ~103 GB per cycle against GLOB025's ~213 GB. EURAT01 publishes its full parameter set at every hourly step below 48 h, where GLOB025 alternates; GLOB025 carries the near-surface thermodynamic profile that EURAT01 omits.
- **Regional sibling ensembles:** the convection-permitting AROME ensembles, coupled to PE-ARPEGE for their lateral boundary conditions via PEARP. Only the overseas New Caledonia domain is on open data — see [PE-AROME New Caledonia](../../regional/fr/pe-arome-ncaled.md); the metropolitan PEAROME and the four other overseas domains are API-only.
- **Stretched grid:** As with ARPEGE, the published 0.25° grid interpolates a model running at finer native resolution over Western Europe; the regridded resolution does not reflect where the integration is actually finest.
- **Also in this bucket:** `prod/data/arome/ncaled0025/` — the New Caledonia PEAROME-OM ensemble, 16 members, documented in [PE-AROME New Caledonia](../../regional/fr/pe-arome-ncaled.md). It is the only AROME ensemble published to object storage, and gets ~3.5 days of retention against PE-ARPEGE's ~36 hours in the same bucket.

### Forthcoming
- **cy49t1_op1** (e-suite from May 2025, operational switch targeted for autumn 2026): singular vectors to be removed, with initial-condition perturbations of the near-surface and oceanic state (2 m temperature, 2 m humidity, and the whole ocean water column) drawn from ARPEGE-EDA instead; ARPEGE-EDA itself moving to random parameter perturbation on ~30 model parameters; uncycled ARPEGE-EPS forecasts to run in single precision. Subject to confirmation.

---

## Recent version history
- **2022 upgrade (chaîne 2021-01, 29 June 2022):** Resolution aligned with ARPEGE (7.5 → 5 km over Europe); ARPEGE became the PE-ARPEGE control member; model-error representation revised (perturbed physics parameters + addition of the PCMT convection scheme); new GLOB025 global grid introduced for 3-D fields; all four cycles unified to 102 h.

---

## Official documentation
- data.gouv.fr dataset — PE Arpege GLOB025: https://www.data.gouv.fr/datasets/pe-arpege-glob025
- data.gouv.fr dataset — PE Arpege EURAT01: https://www.data.gouv.fr/datasets/pe-arpege-eurat01
- Météo-France open data portal: https://meteo.data.gouv.fr
- API Ciblée PE Modèles (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/853606545/
- Modèles et données de prévision (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- ARPEGE/AROME parameter glossary (PDF): `description-parametres-modeles-arpege-arome-v2-185.pdf`, linked from the deterministic ARPEGE and AROME dataset pages
- Météo-France API portal (registration required): https://portail-api.meteofrance.fr
- Etalab Open Licence v2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live verification performed 2026-08-09 against the 2026-08-09 12:00 UTC cycle (full 103-file inventory; steps 01, 03 and 97 decoded in full — 2,625, 4,725 and 105 GRIB messages respectively — with ecCodes 2.48.0), with supporting checks on the 2026-08-08 12/18 UTC and 2026-08-09 00/06/18 UTC cycles for retention, publication timing and volume.*
