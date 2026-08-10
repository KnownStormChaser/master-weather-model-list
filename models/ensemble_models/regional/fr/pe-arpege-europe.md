# PE-ARPEGE Europe (EURAT01)

## What this model is
PE-ARPEGE Europe is the **0.1° regional distribution** of Météo-France's operational global ensemble prediction system, covering Europe, the North Atlantic and North Africa.

It is not a separate ensemble. The members come from the same PE-ARPEGE integration as the global 0.25° distribution — same 35 members, same stretched-grid spectral core, same perturbation design, same 102-hour range, same four daily cycles. What differs is the published grid, which is finer (0.1°) and closer to the model's native resolution over Western Europe, and the published content, which is differently balanced rather than simply reduced.

For the parent system's full technical description — perturbation design, data assimilation, version history — see [PE-ARPEGE](../../global/fr/pe-arpege.md).

> **This distribution is richer than the global one below 48 h, and thinner in the near-surface profile.** EURAT01 publishes its full parameter set at **every hourly step through 48 h**, where GLOB025 alternates between a full and a reduced set. But EURAT01 omits the low-level temperature, humidity and condensate profiles that GLOB025 carries on height levels. See *Content tiers* and *What EURAT01 has that GLOB025 does not*.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** Europe, North Atlantic and North Africa
- **Grid (live-verified):** **EURAT01** — `regular_ll`, **741 × 521**, 0.1° × 0.1°, **386,061 points per field**. First grid point 72.0°N / 328.0°E, last 20.0°N / 42.0°E, north-to-south row order. Bounds **72°N–20°N, 32°W–42°E** — identical to the deterministic [ARPEGE Europe](../../../nwp_models/regional/france/arpege-europe.md) grid. `bitmapPresent = 0` — no missing values.

> **Longitude uses a 0–360° axis and the domain crosses the prime meridian.** The first grid longitude is **328.0°E** (= 32°W), numerically larger than the last (42.0°E). Readers assuming a monotonically increasing longitude axis will mis-handle this grid.

Each field carries **2.7× fewer** grid points than the GLOB025 equivalent (386,061 against 1,038,240), which is why EURAT01 is under half the global distribution's volume despite the finer mesh.

---

## Basic details
- **Model type:** Global ensemble NWP (stretched-grid spectral), regional distribution
- **Model system / core:** ARPEGE (shared IFS/ARPEGE code base with ECMWF); SURFEX surface modelling
- **Dynamical formulation:** Hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** No (deep convection parameterized; native ~5 km over Western Europe)
- **Ensemble size (live-verified):** **35 members — 1 control + 34 perturbed.** `perturbationNumber` runs **0 to 34**, member 0 being the control; `numberOfForecastsInEnsemble = 35` on every message, consistent with the member count.
- **Native horizontal resolution:** Variable — stretched spectral geometry TL1798C2.2, ~5 km over Western Europe
- **Vertical levels:** 105 in the model; this distribution publishes a small selected set of pressure, height, PV and isothermal levels — see *Level sets*
- **Model top:** ~0.1 hPa (~65 km)
- **Forecast length (live-verified):** **102 hours**, all cycles — steps `00` through `102`
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (live-verified):** **Hourly throughout** — 103 files per run, one per step, with content varying by step (see below)
- **Time step:** 240 s

---

## Data assimilation
- **Data assimilation:** No separate PE-ARPEGE analysis — initial states derive from the operational ARPEGE analysis and the ARPEGE ensemble of data assimilations (AEARP).
- **Method / cadence:** 35 of the 50 AEARP members drawn at random, combined with singular-vector perturbations.

---

## Perturbations and design
Identical to the global distribution — the two grids are outputs of one integration.

- **Initial condition perturbations:** **Singular vectors** computed at TL95C1 on 65 levels over seven target regions (Europe: 16 vectors; Southern Hemisphere: 10; intertropical band: 28; complementary Northern Hemisphere domain: 10), with 18 h optimization for Europe/North Atlantic/tropics and 24 h elsewhere; moist static energy norm for extratropical boxes, kinetic energy norm for the tropics, where the four tropical boxes shift with the cyclone season. Combined with **35 of the 50 AEARP** ensemble-assimilation members.
- **Model/physics perturbations:** Random perturbation of physics parameters spanning turbulent kinetic energy, gravity-wave drag, microphysics, air–sea fluxes and solar radiation, combined with two deep-convection schemes split across the ensemble — **PCMT** and **Tiedtke–Bechtold** (the latter matching deterministic ARPEGE).

---

## What it provides

**Raw member forecasts only.** Every message is an individual ensemble member (`productDefinitionTemplateNumber = 1` or `11`). **No ensemble mean, spread, percentile or probability fields are published in this dataset.** The separate PEARP statistical-fields product covers Europe at 0.1° but is a distinct product and not part of this dataset.

### Content tiers by step (live-verified)

EURAT01 tiers differently from GLOB025, and more simply:

| Tier | Steps | Params | Messages/member | File size |
|---|---|---:|---:|---|
| **Analysis-time** | 0 only | 37 | 91 | ~930 MB |
| **Full (hourly)** | 1–48, all steps | 67 | 126 | ~1.4–1.5 GB |
| **Full (3-hourly)** | 51, 54, … 102 | 69 | 134 | ~1.4–1.6 GB |
| **Minimal** | non-multiples of 3 from 49 to 101 — 36 steps | **3** | **3** | ~34–35 MB |

The **minimal tier is identical to GLOB025's**: only `10u`, `10v` and `prmsl`, 105 messages total. Beyond 48 h, everything else exists only at 3-hourly resolution.

The **step-0 file** carries instantaneous fields only — no accumulations, gusts or time-extremes, which have no meaning at analysis time. It also carries two fields found nowhere else in the run: **sea-ice cover** (discipline 10, oceanographic) and surface orography (`h`).

The **3-hourly tier** adds `min_2t` and `max_2t` (3-hourly extremes) to the hourly set.

> **The key difference from GLOB025:** below 48 h, GLOB025 alternates between a 66-parameter set at multiples of 3 and a 58-parameter set elsewhere. EURAT01 publishes its full set at **every** hourly step. If you need hourly three-dimensional guidance over Europe, this distribution is the one that has it.

### Level sets (live-verified)
- **Isobaric (10 levels):** 200, 250, 300, 400, 500, 700, 800, 850, 925, 1000 hPa — `u`, `v`, `t`, `r`. **`z` adds a 50 hPa level** for 11 in total, the only field reaching the stratosphere.
- **Isobaric, sparse:** `papt` at 850 hPa; **`w` (vertical velocity) at 400 and 600 hPa**; **`d` (divergence) at 300 and 950 hPa**
- **Height above ground:** `u`, `v` at 50, 100, 500 m; standard 2 m and 10 m diagnostics
- **Potential vorticity: two surfaces at 1.5 and 2.0 PVU** (encoded 1500, 2000), carrying `u`, `v`, `z` and `pt`
- **Isothermal levels:** `h` (geometric height) at levels **273, 274 and 275** — the 0 °C, 1 °C and 2 °C isotherm heights. GLOB025 carries only 273 and 274.

### What EURAT01 has that GLOB025 does not
- **Vertical velocity** (`w`) and **divergence** (`d`) on pressure levels — absent from GLOB025 entirely
- **Clear-sky radiation** (`ssrc`, `strc`) and **top-of-atmosphere solar radiation** (`tsr`) — GLOB025 carries only top thermal radiation (`ttr`)
- **Surface stresses** (`iews`, `inss`) and **evaporation** (0/1/6)
- **A third isotherm height** (275 K)
- **Sea-ice cover**, at step 0 only

### What GLOB025 has that EURAT01 does not
- The **near-surface thermodynamic profile**: `t` at 10/20/50/100 m, `r` at 10/20/50 m, `clwc` and `ciwc` at 10/20 m. EURAT01 carries only wind on height levels.
- **Absolute vorticity** at 850 hPa
- The undocumented soil parameters (2/3/193) at surface and 2.5 m depth

### Notable content
As with the global distribution, EURAT01 carries a strong aviation- and hazard-oriented selection as 35-member ensembles: **ceiling** (`ceil`), **cloud base and cloud top pressure**, **cumulonimbus top pressure**, **minimum visibility**, **precipitation type** (time-average and time-maximum), **CAPE**, freezing-level heights, and lightning-related fields.

---

## Data availability

- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence version 2.0** (Etalab v2.0; attribution required)
- **High-Value Dataset:** **No.** Like its GLOB025 sibling, and unlike every deterministic Météo-France NWP dataset in this catalogue, this dataset carries **no HVD badge**.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (files use the `.grib` extension, not `.grib2`; `grid_second_order` packing)
- **Official download location:**
  https://www.data.gouv.fr/datasets/pe-arpege-eurat01

### Primary access — data.gouv.fr object storage (no authentication)

> **PE-ARPEGE lives in a different bucket from the deterministic models** — `meteofrance-pe`, not `meteofrance-pnt` — with a different path layout and a far shorter retention window.

- **URL pattern (live-verified):**
  ```
  https://meteofrance-pe.s3.rbx.io.cloud.ovh.net/prod/data/arpege/eurat01/{YYYYMMDDHH}/arpege_eurat01_{YYYYMMDDHH}_{step}:00.grib
  ```
  where `{YYYYMMDDHH}` is the run in compact form (e.g. `202608091800` — **not** the ISO-8601 form used by the `meteofrance-pnt` bucket) and `{step}` is the forecast hour **without zero-padding**, from `0` to `102`. Note the literal colon before `00`.

- **Examples:**
  ```
  .../prod/data/arpege/eurat01/202608091800/arpege_eurat01_202608091800_00:00.grib
  .../prod/data/arpege/eurat01/202608091800/arpege_eurat01_202608091800_102:00.grib
  ```

- **The bucket is anonymously listable:**
  ```bash
  # available runs
  curl -s "https://meteofrance-pe.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=prod/data/arpege/eurat01/&delimiter=/&max-keys=1000"

  # all files in one run
  curl -s "https://meteofrance-pe.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=prod/data/arpege/eurat01/202608091800/&max-keys=1000"
  ```

- **Retention (live-verified): approximately 36 hours — six cycles.** Observed directly during verification: a run present at 23:00 UTC (`202608081200`) had been deleted by 03:00 UTC the following morning, leaving `202608081800` as the oldest. This is **an order of magnitude shorter than the 15-day window** on the deterministic `meteofrance-pnt` bucket, and shorter than the 5-day rolling window the API portal offers for the same system.

- **Publication latency (live-verified)**, measured across five complete cycles from object `Last-Modified` timestamps:

  | Run | First file | Last file |
  |---|---|---|
  | 2026-08-08 18 UTC | T+4 h 05 m | T+5 h 49 m |
  | 2026-08-09 00 UTC | T+3 h 13 m | T+4 h 55 m |
  | 2026-08-09 06 UTC | T+4 h 07 m | T+5 h 48 m |
  | 2026-08-09 12 UTC | T+2 h 55 m | T+4 h 40 m |
  | 2026-08-09 18 UTC | T+4 h 01 m | T+5 h 57 m |

  The 00/12 UTC cycles run roughly an hour ahead of 06/18 UTC, the same pattern seen across the Météo-France products. Completion is T+4 h 40 m to T+5 h 57 m — within a few minutes of the GLOB025 timings, since both grids are written from the same integration. With ~36-hour retention, the usable window between completion and deletion is roughly 30 hours.

- **Volume (live-verified): ~103 GB per cycle, ~414 GB/day**, roughly **0.15 TB** resident under the ~36-hour window. Under half the GLOB025 footprint (~213 GB/cycle) despite the finer mesh, because the domain is far smaller. Files range from ~34 MB (minimal tier) to ~1.6 GB (3-hourly full tier).

  Combined with GLOB025, PE-ARPEGE writes **~316 GB per cycle, ~1.27 TB/day** — the largest production in the catalogue by a wide margin.

- **Download reliability:** long transfers from this bucket truncated silently during verification (a 1.4 GB file returned 5 MB with a success status, producing a `PrematureEndOfFileError` on decode). Use `curl -C -` with a size check and retry loop, and verify against the `Content-Length` from the listing before decoding.

### Secondary access — Météo-France API portal

Météo-France also exposes PE-ARPEGE through its developer portal via a targeted OGC WCS service ("API Ciblée PE Modèles"), returning one 2-D field per request (1 model / 1 member / 1 run / 1 step / 1 level / 1 parameter, no aggregation), with a 5-day rolling retention. Free registration and an API key required: https://portail-api.meteofrance.fr

This entry does not document the API routes, but the trade-off is genuine here as for GLOB025: **the API's 5-day retention is longer than the object storage's ~36 hours**, and single-field requests avoid multi-gigabyte downloads. For a handful of fields rather than whole members, the API is the better fit despite the key requirement.

---

## Notes

### Encoding conventions (live-verified)
- **All messages:** GRIB edition 2, `tablesVersion = 15`, centre `lfpw` (Météo-France Toulouse), `generatingProcessIdentifier = 211` (same as deterministic ARPEGE), **`typeOfGeneratingProcess = 4`** (ensemble forecast — this, not the process identifier, distinguishes PE-ARPEGE messages from deterministic ARPEGE ones).
- **`typeOfProcessedData = 'cp'`** (control and perturbed forecasts) on every message.
- **Packing is `grid_second_order`**, not the `grid_ccsds` used throughout the deterministic `meteofrance-pnt` products.
- **Member packaging:** all 35 members for a given step are concatenated into **one file per step**, one GRIB message per member per field. There is no per-member file split.

### `typeOfEnsembleForecast` is unset
Every message carries **`typeOfEnsembleForecast = 255`** — the missing/undefined value. GRIB2 Code Table 4.6 would normally distinguish an unperturbed control forecast from a perturbed member, and readers selecting the control on that key will find nothing.

**The control is member `perturbationNumber = 0`**; perturbed members are 1–34. Select on `perturbationNumber`.

### Filename mismatch between the portal and the files
The data.gouv.fr resource list displays titles of the form `pearp_eurat01_202608091800_102:00.grib`, but every resource URL points at a file named `arpege_eurat01_202608091800_102:00.grib`. The `pearp_` prefix does not exist in the bucket. Build URLs from the pattern above rather than from the displayed titles.

### The dataset page is a rolling file window, not a run
Like GLOB025, the EURAT01 dataset page lists exactly **103 resources at any moment, spanning two runs** — a rolling window over the most recently written objects, not a manifest of one cycle. Enumerate the bucket prefix for a specific run rather than scraping the dataset page.

data.gouv.fr's own metadata-quality panel flags this dataset for **"Documentation des fichiers manquante"** and **"Couverture temporelle non renseignée"** — there is no package-description PDF for PE-ARPEGE comparable to those published for ARPEGE and AROME, which is why the tier structure documented above had to be established by decoding.

### Parameters that do not decode
Several fields decode as `unknown` in ecCodes 2.48.0. Those resolvable from Météo-France's shared ARPEGE/AROME parameter glossary:

| Encoding | Identification |
|---|---|
| 0/6/1 | **Total cloud cover** (`NEBUL`) — WMO-standard encoding ecCodes leaves unresolved |
| 0/1/64 | **Total column water vapour** (`COLONNE_VAPO`) |
| 0/1/6 | **Evaporation** (`FLEVAP`), accumulated |
| 0/6/11 | **Cloud base** |
| 0/17/4 | **Lightning flash density**, accumulated |

Still unresolved (**TBD**): **0/6/2** (surface, instantaneous — convective cloud cover on the WMO table, unconfirmed), **0/7/7** and **0/7/200** (thermodynamic-stability category; 0/7/7 is CIN on the WMO table, 0/7/200 is local), **0/7/199** at `atmosphere` level (local), **0/4/198** (local, accumulated), **0/19/201** (local, time-minimum), and **10/2/0** at `meanSea` in the step-0 file (discipline 10 is oceanographic, category 2 ice — sea-ice cover on the WMO table, unconfirmed).

`CAPE_INS` again has no resolvable level type, as elsewhere in the Météo-France distributions.

### Access channels that no longer work
- **`object.data.gouv.fr` is dead** for all Météo-France products; the `meteo.data.gouv.fr/datasets/{id}` landing-page IDs previously used for PE-ARPEGE are superseded by the `www.data.gouv.fr` slugs above.
- **The community AWS mirror is gone** (`mf-models-on-aws.s3.amazonaws.com` returns `NoSuchBucket`). It never carried PE-ARPEGE.

### Model family and relationships
- **Global distribution of this ensemble:** [PE-ARPEGE](../../global/fr/pe-arpege.md) — same 35 members at 0.25° globally, ~213 GB per cycle. Choose GLOB025 for global coverage, the near-surface thermodynamic profile, or absolute vorticity; choose EURAT01 for finer horizontal detail over Europe, full hourly content below 48 h, vertical velocity and divergence, and less than half the volume.
- **Deterministic counterpart:** [ARPEGE Europe](../../../nwp_models/regional/france/arpege-europe.md), which publishes the deterministic forecast on the identical EURAT01 grid. Note the contrast in retention — 15 days there against ~36 hours here.
- **Parent deterministic model:** [ARPEGE](../../../nwp_models/global/france/arpege-global.md); ARPEGE (interpolated, unperturbed) is PE-ARPEGE's control member.
- **Regional sibling ensembles:** the convection-permitting AROME ensembles, coupled to PE-ARPEGE for their lateral boundary conditions via PEARP. Only the overseas New Caledonia domain is on open data — see [PE-AROME New Caledonia](./pe-arome-ncaled.md); the metropolitan PEAROME and the four other overseas domains are API-only.
- **Also in this bucket:** `prod/data/arome/ncaled0025/` — a New Caledonia AROME ensemble, presumably part of PEAROME-OM. **TBD** — see the PE-AROME entry.

### Forthcoming
- **cy49t1_op1** (e-suite from May 2025, operational switch targeted for autumn 2026): singular vectors to be removed, with initial-condition perturbations of the near-surface and oceanic state (2 m temperature, 2 m humidity, and the whole ocean water column) drawn from ARPEGE-EDA instead; ARPEGE-EDA itself moving to random parameter perturbation on ~30 model parameters; uncycled ARPEGE-EPS forecasts to run in single precision. Subject to confirmation.

---

## Recent version history
- **2022 upgrade (chaîne 2021-01, 29 June 2022):** Resolution aligned with ARPEGE (7.5 → 5 km over Europe); ARPEGE became the PE-ARPEGE control member; model-error representation revised (perturbed physics parameters + addition of the PCMT convection scheme); GLOB025 global grid introduced for 3-D fields; all four cycles unified to 102 h.

---

## Official documentation
- data.gouv.fr dataset — PE Arpege EURAT01: https://www.data.gouv.fr/datasets/pe-arpege-eurat01
- data.gouv.fr dataset — PE Arpege GLOB025: https://www.data.gouv.fr/datasets/pe-arpege-glob025
- Météo-France open data portal: https://meteo.data.gouv.fr
- API Ciblée PE Modèles (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/853606545/
- Modèles et données de prévision (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- ARPEGE/AROME parameter glossary (PDF): `description-parametres-modeles-arpege-arome-v2-185.pdf`, linked from the deterministic ARPEGE and AROME dataset pages. No PE-ARPEGE-specific package description is published.
- Météo-France API portal (registration required): https://portail-api.meteofrance.fr
- Etalab Open Licence v2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live verification performed 2026-08-09/10 against the 2026-08-09 12:00 UTC cycle (full 103-file inventory; steps 00, 01, 03 and 97 decoded in full — 3,185, 4,410, 4,690 and 105 GRIB messages respectively — with ecCodes 2.48.0), with supporting checks on the 2026-08-08 18 UTC and 2026-08-09 00/06/18 UTC cycles for publication timing and volume, and direct observation of a run being deleted from the bucket for retention.*
