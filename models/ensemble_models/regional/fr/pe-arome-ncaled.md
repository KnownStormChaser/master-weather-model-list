# PE-AROME New Caledonia (NCALED0025)

## What this model is
PE-AROME New Caledonia is the operational convection-permitting regional ensemble prediction system run by Météo-France over New Caledonia, providing short-range probabilistic guidance for tropical high-impact weather — deep convection, heavy rainfall, damaging wind, and tropical cyclones.

It is the New Caledonia domain of **PEAROME-OM** (Prévision d'Ensemble AROME Outre-Mer), the overseas ensemble counterpart to the deterministic [AROME Outre-Mer](../../../nwp_models/regional/france/arome-outre-mer.md), operational since 21 February 2023. It runs the same ALADIN-NH non-hydrostatic core and SURFEX surface scheme as its deterministic sibling, on the identical NCALED0025 grid, but at 2.5 km native resolution rather than 1.3 km and with 16 members instead of one.

> **Scope note: this is the only AROME ensemble on data.gouv.fr.** PEAROME-OM covers five overseas domains and a metropolitan PEAROME runs over EURW1S40, but **New Caledonia is the only AROME ensemble published to the open-data object storage** — `prod/data/arome/` in the `meteofrance-pe` bucket contains `ncaled0025/` and nothing else. There is no metropolitan PE-AROME dataset on data.gouv.fr, and no dataset for the Antilles, Guiana, Polynesia or Réunion–Mayotte ensembles. Those systems exist and are described in Météo-France's technical documentation, but are reachable only through the API portal. *(This entry replaces an earlier one that described a metropolitan PE-AROME open-data distribution; that distribution does not exist.)*

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France (New Caledonia)

---

## What area it covers
- **Coverage:** New Caledonia and surrounding southwest Pacific
- **Operational domain:** NCALED0025
- **Grid (live-verified):** `regular_ll`, **521 × 491**, 0.025° × 0.025°, **255,811 points per field**. First grid point 13.75°S / 158.5°E, last 26.0°S / 171.5°E, north-to-south row order. Bounds **13.75°S–26.0°S, 158.5°E–171.5°E** — **identical to the deterministic [AROME Outre-Mer](../../../nwp_models/regional/france/arome-outre-mer.md) New Caledonia grid**.
- **No missing values:** `bitmapPresent = 0`, as on the deterministic overseas domains and unlike metropolitan AROME.

---

## Basic details
- **Model type:** Regional ensemble NWP (non-hydrostatic, convection-permitting)
- **Model system / core:** AROME (spectral limited-area model, ALADIN-NH dynamical core); SURFEX surface modelling. Internal designation PAROTRO (AROME-Tropical).
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved; shallow convection parameterized)
- **Ensemble size (live-verified):** **16 members — 1 control + 15 perturbed.** `perturbationNumber` runs **0 to 15**, member 0 being the control; `numberOfForecastsInEnsemble = 16` on every message, consistent with the member count.
- **Native horizontal resolution:** ~2.5 km (the deterministic AROME-OM runs at ~1.3 km)
- **Public distribution grid:** 0.025° (~2.5 km) regular latitude–longitude
- **Vertical levels:** 90
- **Forecast length (live-verified):** **48 hours normally, extended to 78 hours when a tropical cyclone is present in the domain.** Both regimes were observed directly — see below.
- **Update frequency / cycles (live-verified):** **2× daily (06 and 18 UTC).** Confirmed against the bucket and the dataset description; this differs from the deterministic AROME-OM, which runs 4× daily on all five domains.
- **Temporal output resolution (live-verified):** Hourly throughout, one file per step
- **Time step:** 50 s

### The tropical-cyclone extension, observed live
Météo-France documents PEAROME-OM as running to 48 h, extended to 78 h when a tropical cyclone is present in the domain. **Both regimes were captured in the bucket during verification:**

| Run | Files | Max step |
|---|---:|---:|
| 2026-08-06 18 UTC | 79 | **78 h** |
| 2026-08-07 06 UTC | 79 | **78 h** |
| 2026-08-07 18 UTC | 79 | **78 h** |
| 2026-08-08 06 UTC | 49 | 48 h |
| 2026-08-08 18 UTC | 49 | 48 h |
| 2026-08-09 06 UTC | 49 | 48 h |
| 2026-08-09 18 UTC | 49 | 48 h |

The extension is hourly throughout (steps 49–78 are all present, no thinning), and it roughly doubles the run's volume. **Consumers must not hard-code 49 files per run** — enumerate the prefix, or the extended steps will be silently dropped exactly when they matter most.

---

## Initial and boundary conditions
- **Initial conditions:** PEAROME-OM has no independent data assimilation cycle of its own; like the deterministic AROME-OM it is driven by larger-scale models, with the ensemble adding perturbations to the initial, surface, boundary and model state.
- **Boundary conditions:** Supplied by **PEARP** (the ARPEGE ensemble, i.e. [PE-ARPEGE](../../global/fr/pe-arpege.md)), **refreshed every 3 hours**. Fifteen selected PEARP members couple the fifteen perturbed members; the control couples to deterministic ARPEGE.

---

## Perturbations and design
- **Initial condition perturbations:** Applied to the initial atmospheric state.
- **Boundary perturbations:** Inherited from the coupled PEARP members, which themselves combine singular vectors and AEARP analysis perturbations.
- **Surface perturbations:** Applied to the surface state.
- **Model/physics perturbations:** Applied to the model formulation.

> Météo-France's published description of PEAROME-OM characterises the perturbation strategy at this level ("perturbing initial, boundary, surface, and model conditions") without naming the specific stochastic schemes or perturbed parameters. **TBD** — a more detailed breakdown is not available in the open documentation.

---

## What it provides

**Raw member forecasts only.** Every message is an individual ensemble member. **No ensemble mean, spread, percentile or probability fields are published in this dataset.**

Files are organised **one per forecast hour**, each containing all 16 members: **49 files per run** (steps 0–48), or **79** when the cyclone extension is active.

### Content (live-verified, step 24 of the 2026-08-09 18 UTC run)
- **3,184 messages**, 199 per member, 49 distinct parameter/level-type/step-type combinations
- **Product definition templates in use:** 1 (individual ensemble member), 11 (time-processed ensemble member), and **33 (individual ensemble member, synthetic satellite data)**

The step-0 file is thinner — 2,704 messages, 169 per member, 31 parameters — carrying instantaneous fields only. Accumulations, gust and 2 m extremes, cloud cover, and the reflectivity fields all begin at step 1.

### Level sets (live-verified)
- **Isobaric — 15 levels:** 100, 150, 200, 250, 300, 400, 500, 600, 700, 800, 850, 900, 925, 950, 1000 hPa — `u`, `v`, `t`, `r`, `z`, `w`, `wz`
- **Isobaric — `papt`, 13 levels:** 200 through 1000 hPa
- **Isobaric — 0/1/201 (unresolved), 14 levels:** 100, 150, 175, 200, 225, 250, 275, 300, 350, 400, 500, 600, 700, 800 hPa — note this level set is **not a subset of the 15-level set** (it includes 175, 225, 275 and 350 hPa, which the main set omits)
- **Isobaric — `tke`, 7 levels:** 700, 800, 850, 900, 925, 950, 1000 hPa
- **Isobaric — `absv`, 5 levels:** 300, 500, 600, 700, 850 hPa
- **Isobaric — `d` (divergence), 2 levels:** 300, 950 hPa
- **Isobaric — `vptmp` (virtual potential temperature), 1 level:** 1000 hPa
- **Height above ground — `u`, `v`, 5 levels:** 50, 100, 500, 1000, 1500 m
- **Height above ground — radar reflectivity (0/16/192), 7 levels:** 500, 750, 1000, 1500, 2000, 2500, 3000 m
- **Potential vorticity: a single surface at 0.7 PVU** (encoded 700), carrying `u`, `v`, `z`, `pt` and `absv`. The deterministic AROME-OM carries two PV surfaces (0.7 and 1.5); PE-ARPEGE carries 1.5 and 2.0.
- **Isothermal levels:** `h` (geometric height) at levels **263 and 273** — the −10 °C and 0 °C isotherm heights

### Notable content
- **Synthetic satellite brightness temperature** as a 16-member ensemble (PDT 33) on **two channels: 10.40 µm infrared window and 6.20 µm water vapour** — matching the deterministic AROME-OM New Caledonia channels exactly. 32 messages per step (2 channels × 16 members).
- **Three reflectivity products:** 0/16/192 on seven height levels, plus **0/16/193** and **0/16/195** at the surface. The deterministic AROME-OM publishes 0/16/192 and the WMO-standard `rare` on height levels; this ensemble publishes a different mix, including 0/16/195 which appears nowhere else in the Météo-France distributions covered by this catalogue.
- **Precipitation partition** (`tirf`, `tsnowp`, `tgrp`) and lightning flash density, as 16-member ensembles.
- **CAPE, CIN-candidate and stability diagnostics**, `min_vis`-family fields, cloud layers, and 10 m gust maxima.

---

## Data availability

- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence version 2.0** (Etalab v2.0; attribution required)
- **High-Value Dataset:** **No.** Like the PE-ARPEGE datasets, and unlike every deterministic Météo-France NWP dataset in this catalogue, this dataset carries **no HVD badge**.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (files use the `.grib` extension, not `.grib2`)
- **Official download location:**
  https://www.data.gouv.fr/datasets/pe-arome-ncaled0025

### Primary access — data.gouv.fr object storage (no authentication)

> **The ensembles live in the `meteofrance-pe` bucket**, not the `meteofrance-pnt` bucket used for the deterministic products, with a different path layout and different retention.

- **URL pattern (live-verified):**
  ```
  https://meteofrance-pe.s3.rbx.io.cloud.ovh.net/prod/data/arome/ncaled0025/{YYYYMMDDHH}/arome_ncaled0025_{YYYYMMDDHH}_{step}:00.grib
  ```
  where `{YYYYMMDDHH}` is the run in compact form (e.g. `202608091800`) and `{step}` is the forecast hour **without zero-padding**, from `0` to `48` (or `78` under the cyclone extension). Note the literal colon before `00`, and that the filename prefix is `arome_`, not `pearome_`.

- **Examples:**
  ```
  .../prod/data/arome/ncaled0025/202608091800/arome_ncaled0025_202608091800_00:00.grib
  .../prod/data/arome/ncaled0025/202608091800/arome_ncaled0025_202608091800_48:00.grib
  ```

- **The bucket is anonymously listable:**
  ```bash
  # available runs
  curl -s "https://meteofrance-pe.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=prod/data/arome/ncaled0025/&delimiter=/&max-keys=1000"

  # all files in one run
  curl -s "https://meteofrance-pe.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=prod/data/arome/ncaled0025/202608091800/&max-keys=1000"
  ```

- **Retention (live-verified): approximately 3.5 days — seven cycles.** On 2026-08-10 the bucket held runs from `202608061800` to `202608091800`. This is substantially longer than the ~36 hours the [PE-ARPEGE](../../global/fr/pe-arpege.md) products get in the same bucket — retention appears to be counted in cycles rather than in time, and at two cycles a day that buys considerably more wall-clock coverage.

- **Publication latency (live-verified)**, measured across all seven cycles present:

  | Run | First file | Last file |
  |---|---|---|
  | 2026-08-06 18 UTC (78 h) | T+6 h 10 m | T+7 h 23 m |
  | 2026-08-07 06 UTC (78 h) | T+6 h 10 m | T+7 h 22 m |
  | 2026-08-07 18 UTC (78 h) | T+6 h 10 m | T+7 h 22 m |
  | 2026-08-08 06 UTC | T+6 h 10 m | T+6 h 55 m |
  | 2026-08-08 18 UTC | T+6 h 10 m | T+6 h 54 m |
  | 2026-08-09 06 UTC | T+6 h 10 m | T+6 h 52 m |
  | 2026-08-09 18 UTC | T+6 h 10 m | T+6 h 52 m |

  **The first file lands at T+6 h 10 m on every single cycle**, within a 44-second spread across seven runs — by far the most consistent publication schedule of any Météo-France product in this catalogue. Completion is T+6 h 52 m for a 48 h run and T+7 h 23 m for a 78 h run.

- **Volume (live-verified): ~27 GB per 48 h cycle, ~48 GB per 78 h cycle** — roughly **54 GB/day** under normal operation. Files run 420–585 MB and grow monotonically with lead time (441 MB at step 0, 585 MB at step 48).

### Secondary access — Météo-France API portal

Météo-France also exposes the AROME ensembles through its developer portal via a targeted OGC WCS service ("API Ciblée PE Modèles"), returning one 2-D field per request (1 model / 1 member / 1 run / 1 step / 1 level / 1 parameter, no aggregation), with a 5-day rolling retention. Free registration and an API key required: https://portail-api.meteofrance.fr

This entry does not document the API routes. Note, though, that **the API is the only route to the metropolitan PEAROME and to the four other PEAROME-OM domains**, none of which are published to object storage.

---

## Notes

### Encoding conventions (live-verified)
- **All messages:** GRIB edition 2, `tablesVersion = 15`, centre `lfpw` (Météo-France Toulouse), **`typeOfGeneratingProcess = 4`** (ensemble forecast).
- **`generatingProcessIdentifier = 248`** — distinct from every other Météo-France system in this catalogue: deterministic AROME-OM uses 121, metropolitan AROME 204, ARPEGE and PE-ARPEGE 211. This is the cleanest way to identify PE-AROME New Caledonia messages in a mixed archive.
- **`typeOfProcessedData = 'cp'`** (control and perturbed forecasts) on every message.
- **Packing is mixed within a single file** — most messages use `grid_second_order`, but ~7% use `grid_simple` (229 of 3,184 at step 24). Readers must not assume a uniform packing type per file.
- **Member packaging:** all 16 members for a given step are concatenated into **one file per step**, one GRIB message per member per field. There is no per-member file split.

### `typeOfEnsembleForecast` is unset
Every message carries **`typeOfEnsembleForecast = 255`** — the missing/undefined value, exactly as in the PE-ARPEGE distributions. GRIB2 Code Table 4.6 would normally distinguish an unperturbed control forecast from a perturbed member, and readers selecting the control on that key will find nothing.

**The control is member `perturbationNumber = 0`**; perturbed members are 1–15. Select on `perturbationNumber`.

### Filename mismatch between the portal and the files
The data.gouv.fr resource list displays titles of the form `pearome_ncaled0025_202608091800_00:00.grib`, but every resource URL points at a file named `arome_ncaled0025_202608091800_00:00.grib`. The `pearome_` prefix does not exist in the bucket. This is the same pattern as PE-ARPEGE, where displayed `pearp_` titles map to `arpege_` files. Build URLs from the pattern above rather than from the displayed titles.

Unlike the PE-ARPEGE dataset pages, this one lists exactly one run's worth of resources (49 files, all from the same cycle) rather than a rolling two-run window — a consequence of the smaller file count per cycle.

### Parameters that do not decode
Several fields decode as `unknown` in ecCodes 2.48.0. Those resolvable from Météo-France's shared ARPEGE/AROME parameter glossary:

| Encoding | Identification |
|---|---|
| 0/6/1 | **Total cloud cover** (`NEBUL`) — WMO-standard encoding ecCodes leaves unresolved |
| 0/1/64 | **Total column water vapour** (`COLONNE_VAPO`) |
| 0/16/192 | **Derived radar reflectivity** (`RFLCTVT`) |
| 0/16/193 | **Maximum radar reflectivity at the surface** (`RFLCTVT_MAX`) |
| 0/17/4 | **Lightning flash density**, accumulated |
| 0/5/7 (PDT 33) | **Synthetic satellite brightness temperature** (`BT`) |

Still unresolved (**TBD**): **0/1/201** on 14 isobaric levels — the same undocumented parameter seen in metropolitan AROME's IP2 and AROME-OM's HP2, where the evidence points to specific graupel content; **0/16/195** at the surface (local, reflectivity category, no counterpart elsewhere in the Météo-France distributions); **0/1/199** at the surface; **0/7/7** and **0/7/200** (thermodynamic-stability category; 0/7/7 is CIN on the WMO table, 0/7/200 is local); and **0/19/201** (local, time-minimum).

`CAPE_INS` again has no resolvable level type.

### How this ensemble differs from its deterministic sibling
| | [AROME Outre-Mer](../../../nwp_models/regional/france/arome-outre-mer.md) NCALED | PE-AROME NCALED (this entry) |
|---|---|---|
| Members | 1 | 16 (1 control + 15 perturbed) |
| Native resolution | ~1.3 km | ~2.5 km |
| Cycles | 4× daily (00/06/12/18) | 2× daily (06/18) |
| Forecast length | 48 h | 48 h, or 78 h with a cyclone in the domain |
| File organisation | 11 packages × 49 steps = 537 files | 1 file per step = 49 files |
| Bucket | `meteofrance-pnt` | `meteofrance-pe` |
| Retention | 15 days | ~3.5 days |
| Volume per cycle | 4.5 GB | 27 GB |
| `generatingProcessIdentifier` | 121 | 248 |
| PV surfaces | 0.7 and 1.5 PVU | 0.7 PVU only |
| Boundary conditions | IFS + ARPEGE | PEARP (3-hourly) |

Note the inversion in file organisation: the deterministic feed splits each step across eleven thematic packages, while the ensemble puts everything for a step — all 16 members, all parameters — in one file. Fetching a single deterministic parameter is cheap; fetching a single ensemble parameter means pulling a ~500 MB file.

### Access channels that no longer work
- **`object.data.gouv.fr` is dead** for all Météo-France products.
- **The community AWS mirror is gone** (`mf-models-on-aws.s3.amazonaws.com` returns `NoSuchBucket`). It never carried any Météo-France ensemble.

### Model family and relationships
- **Deterministic counterpart:** [AROME Outre-Mer](../../../nwp_models/regional/france/arome-outre-mer.md), which covers New Caledonia and four other overseas domains. This ensemble shares its grid exactly.
- **Driving ensemble:** [PE-ARPEGE](../../global/fr/pe-arpege.md) supplies lateral boundary conditions via PEARP, refreshed 3-hourly. See also [PE-ARPEGE Europe (EURAT01)](./pe-arpege-europe.md).
- **Sibling ensembles not on open data:** the metropolitan PEAROME (EURW1S40, 26 members since cy48t1_op1, 4× daily at 03/09/15/21 UTC, 51 h range) and the four other PEAROME-OM domains — Antilles and Guiana at 00/12 UTC, Réunion at 00/18 UTC, Polynesia at 06/18 UTC. All API-only. **TBD** — whether any of these will follow New Caledonia onto data.gouv.fr.
- **Metropolitan deterministic relatives:** [AROME France](../../../nwp_models/regional/france/arome-france.md) and [AROME France HD](../../../nwp_models/regional/france/arome-france-hd.md).

---

## Recent version history
- **21 February 2023:** PEAROME-OM became operational across the five overseas domains, with 16 members (1 control + 15 perturbed) at 2.5 km native resolution.

---

## Official documentation
- data.gouv.fr dataset — PE Arome NCALED0025: https://www.data.gouv.fr/datasets/pe-arome-ncaled0025
- Météo-France open data portal: https://meteo.data.gouv.fr
- API Ciblée PE Modèles (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/853606545/
- Modèles et données de prévision (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- PEAROME technical description (PDF, Météo-France, version 30/10/2025): `description-technique-pearome.pdf`, linked from the Météo-France public data portal
- ARPEGE/AROME parameter glossary (PDF): `description-parametres-modeles-arpege-arome-v2-185.pdf`, linked from the deterministic ARPEGE and AROME dataset pages. No PE-AROME-specific package description is published.
- Météo-France API portal (registration required): https://portail-api.meteofrance.fr
- Etalab Open Licence v2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live verification performed 2026-08-10 against the 2026-08-09 18:00 UTC cycle (full 49-file inventory; steps 00 and 24 decoded in full — 2,704 and 3,184 GRIB messages respectively — with ecCodes 2.48.0), with supporting checks on all seven cycles present in the bucket (2026-08-06 18 UTC through 2026-08-09 18 UTC) for retention, publication timing, volume, and the tropical-cyclone forecast-length extension.*
