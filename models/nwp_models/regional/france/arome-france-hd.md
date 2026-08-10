# AROME France HD

## What this model is
AROME France HD is the **0.01° public distribution** of Météo-France's operational convection-permitting AROME model over metropolitan France and Western Europe.

It is not a separate model run. The forecasts come from the same AROME integration as the 0.025° distribution — same non-hydrostatic ALADIN-NH core, same 1.3 km native mesh, same 3DEnVar assimilation cycle, same eight daily cycles, same 51-hour range. What differs is the published grid, which is finer (0.01°, ~1.1 km) and therefore closer to the model's native resolution, and the published *content*, which is drastically reduced.

For the parent system's full technical description — dynamics, physics, data assimilation, version history — see [AROME France](./arome-france.md).

> **This is a thin surface and low-level product, not a high-resolution version of the full distribution.** Where the 0.025° grid publishes eleven packages spanning 24 pressure levels and 25 height levels, the 0.01° grid publishes **four packages** covering the surface and the lowest **four** height levels. **There are no pressure-level fields at all.** Anyone needing the three-dimensional atmospheric state must use the 0.025° distribution despite its coarser grid. See *What it provides*.

> **Naming.** Météo-France's data.gouv.fr dataset is titled simply "Paquets Arome - Résolution 0,01°" and the package description calls the grid EURW1S100; the label **AROME HD** comes from Météo-France's own product communication and is what most downstream services use. This entry uses AROME France HD to match the sibling entry's naming and common usage.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** France and surrounding regions of Western Europe — **identical domain to the 0.025° distribution**
- **Operational domain:** EURW1S100
- **Grid (live-verified):** `regular_ll`, **2801 × 1791**, 0.01° × 0.01°, **5,016,591 points per field**. First grid point 55.4°N / 348.0°E, last 37.5°N / 16.0°E; `iScansNegatively = 0`, `jScansPositively = 0` (north-to-south row order). Bounds **55.4°N–37.5°N, 12°W–16°E**.
- **The native grid is trapezoidal, not rectangular** — see *Missing values* under Notes.

Each field carries **6.2× as many grid points** as the 0.025° equivalent (5,016,591 against 803,757), which is why individual HD files are large despite the sparse parameter set.

> **Longitude uses a 0–360° axis and the domain crosses the prime meridian.** The first grid longitude is **348.0°E** (= 12°W), numerically larger than the last (16.0°E) — the same caveat that applies to [AROME France](./arome-france.md) and [ARPEGE Europe](./arpege-europe.md).

---

## Basic details
- **Model type:** Regional deterministic NWP, high-resolution distribution
- **Model system:** AROME (spectral limited-area model, ALADIN-NH dynamical core); SURFEX surface modelling
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved at 1.3 km native resolution; shallow convection parameterized)
- **Native horizontal resolution:** ~1.3 km
- **Public distribution grid:** **0.01° (~1.1 km)** regular latitude–longitude
- **Vertical levels:** 90 in the model; the public distribution carries **four height-above-ground levels only** (10, 20, 50, 100 m) plus surface fields
- **Forecast length (live-verified):** **51 hours**, all cycles
- **Update frequency / cycles (live-verified):** **8× daily** (00, 03, 06, 09, 12, 15, 18, 21 UTC); all eight publish a complete 208-file set
- **Temporal output resolution (live-verified):** **Hourly throughout**, steps 0–51
- **Data assimilation:** inherited from the parent run — 3DEnVar in the OOPS framework, 1-hour cycle, with Incremental Analysis Update; operational since October 2024 (cy48t1_op1)
- **Time step:** 50 s

Lateral boundary conditions are provided, via the parent AROME run, by [ARPEGE](../../global/france/arpege-global.md).

> **No analysis fields.** Every message carries `typeOfGeneratingProcess = 2` (forecast) including step 0, despite data.gouv.fr describing the dataset as "données d'analyse et de prévision".

---

## What it provides

The 0.01° distribution uses a **different file organisation from every other Météo-France NWP product in this catalogue**: instead of grouping forecast ranges into multi-step files, it publishes **one file per package per forecast hour** — **four packages × 52 steps (00H–51H) = 208 files per cycle**.

### Package inventory (live-verified, 2026-08-09 15 UTC)

| Package | Messages/step | Content |
|---|---:|---|
| **SP1** | 6 | `2t`, `2r`, `10u`, `10v`, `max_10efg`, `max_10nfg` |
| **SP2** | 9 | `sp`, `CAPE_INS`, `lcc`, `mcc`, `hcc`, `tirf`, `tsnowp`, `tgrp`, maximum radar reflectivity at the surface |
| **SP3** | 1 | synthetic satellite brightness temperature (+ orography at step 0 only) |
| **HP1** | 20 | `r` on 4 levels; `u`, `v`, `ws`, `wdir` on 20/50/100 m plus separate 10 m fields |

- **Height-above-ground levels (live-verified), 4:** 10, 20, 50, 100 m
- **No isobaric levels, no model levels.**

### Step 0 is a reduced set
Three of the four packages publish fewer fields at step 0 than at steps 1–51:

| Package | Step 0 | Steps 1–51 |
|---|---|---|
| **SP1** | 4 fields — `2t`, `2r`, `10u`, `10v` | 6 — adds `max_10efg`, `max_10nfg` |
| **SP2** | 2 fields — `sp`, `CAPE_INS` | 9 — adds cloud cover, precipitation, reflectivity |
| **SP3** | 2 fields — brightness temperature **plus orography** | 1 — brightness temperature only |
| **HP1** | 20 (unchanged) | 20 |

The gust maxima and precipitation accumulations are hour-interval quantities and cannot exist at step 0, which is expected. **Cloud cover and surface reflectivity being absent at step 0 is less obvious**, and matches the same behaviour on the 0.025° grid. The orography field in SP3 is the reverse case — a static field published once, at step 0 only, and absent from every later file.

### Notable content
- **Maximum radar reflectivity at the surface** (`RFLCTVT_MAX`, SP2) — a column-maximum reflectivity field with no counterpart in the 0.025° packages, which publish reflectivity on isobaric and height levels but not as a surface maximum.
- **Synthetic satellite brightness temperature** (SP3) at **~10.8 µm** (`scaledValueOfCentralWaveNumber = 9259259`, scale factor 2), infrared window. Verified values span 212–304 K. As with the ARPEGE synthetic satellite fields, `satelliteSeries`, `satelliteNumber` and `instrumentType` are **all zero**, so the emulated platform cannot be determined from the file. Météo-France's package description labels this "BT (niveau CANAUX 108)".
- **Graupel, snow and rain accumulations** (`tgrp`, `tsnowp`, `tirf`) are present, but **total precipitation is not** — unlike the 0.025° SP1, which carries `tp`. Users needing a total must sum the three components (Météo-France's glossary defines AROME's PRECIP as EAU + NEIGE + GRAUPEL).

---

## Data availability

- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence version 2.0** (Etalab v2.0; attribution required)
- **High-Value Dataset:** The data.gouv.fr dataset carries the **HVD badge** ("Données de forte valeur") under the EU Open Data Directive
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (edition 2, `grid_ccsds` packing)
- **Official download location:**
  https://www.data.gouv.fr/datasets/paquets-arome-resolution-0-01deg

### Primary access — data.gouv.fr object storage (no authentication)

Served from OVH-hosted S3-compatible object storage. **No account, no API key, no registration.**

- **URL pattern (live-verified):**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/{run}/arome/001/{package}/arome__001__{package}__{step}H__{run}.grib2
  ```
  where `{run}` is ISO-8601 with literal colons (e.g. `2026-08-09T15:00:00Z`), `{package}` is one of `SP1 SP2 SP3 HP1`, and `{step}` is a **zero-padded two-digit forecast hour** from `00` to `51`. Note the **double underscores** and the grid token `001`.

- **Example:**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/2026-08-09T15:00:00Z/arome/001/SP2/arome__001__SP2__06H__2026-08-09T15:00:00Z.grib2
  ```

- **The per-hour layout is the practical advantage of this distribution.** Fetching a single lead time costs one small file rather than a multi-hundred-megabyte range group — a 06 h surface forecast is a 14 MB SP2 download here against a 43 MB `00H06H` SP2 file on the 0.025° grid that also contains five steps you did not ask for. For step-targeted or nowcasting-style polling, HD is the cheaper feed despite its finer grid.

- **The bucket is anonymously listable:**
  ```bash
  curl -s "https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=pnt/2026-08-09T15:00:00Z/arome/001/&max-keys=1000"
  ```
  Paginate via `NextContinuationToken` (URL-encode it). Objects serve `Content-Length` and `Last-Modified` on `HEAD` and support HTTP Range requests.

- **Retention (live-verified):** **15 days rolling**, shared with every other model in the bucket. On 2026-08-09, `2026-07-26T12:00:00Z` held a complete 208-file set and `2026-07-25` was gone.

- **Publication latency (live-verified):** measured across all eight cycles of 2026-08-08 from object `Last-Modified` timestamps. The same wide, irregular cycle-to-cycle spread seen on the 0.025° grid applies:

  | Cycle | First file | Last file |
  |---|---|---|
  | 00 UTC | T+1 h 56 m | T+2 h 47 m |
  | 03 UTC | T+1 h 47 m | T+2 h 38 m |
  | 06 UTC | T+4 h 17 m | T+5 h 02 m |
  | 09 UTC | T+3 h 23 m | T+4 h 11 m |
  | 12 UTC | T+3 h 01 m | T+3 h 55 m |
  | 15 UTC | T+3 h 01 m | T+3 h 50 m |
  | 18 UTC | T+4 h 11 m | T+5 h 02 m |
  | 21 UTC | T+3 h 17 m | T+4 h 08 m |

  HD completes **5–10 minutes ahead of** the 0.025° set on every cycle. The 00 and 03 UTC cycles land roughly two and a half hours earlier than 06 and 18 UTC; budget for the worst case (~T+5 h) rather than the average.

- **Volume (live-verified, 2026-08-09 15 UTC):** **~5.9 GB per cycle**, ~47 GB/day, roughly **0.71 TB** resident under the 15-day window — about a quarter of the 0.025° footprint despite the 6.2× finer grid, because the parameter set is so much smaller. Per package per cycle: HP1 3.80 GB, SP1 1.18 GB, SP2 0.76 GB, SP3 0.18 GB. Individual files range from 3.0 MB (SP3) to 78 MB (HP1).

### Companion static files

Published on the dataset page rather than in the object store:

- **`constant-eurw1s100.grib2`** ("Champs CONSTANT EURW1S100") — **live-verified**: 2 messages, orography (`h`, geometrical height above ground, m) and land–sea mask (`lsm`, 0–1) on the EURW1S100 grid, ~6.8 MB. Encoded unlike the forecast data: `tablesVersion = 32` (against 15), `grid_second_order` packing (against `grid_ccsds`), `generatingProcessIdentifier = 255`, `typeOfGeneratingProcess = 0`. Last updated 2025-02-17.
- **`descriptiontechnique-paquetsarome-donneespubliques-v4-20250401.pdf`** — official package description covering both AROME grids, ~204 KB, version 01/04/2025.
- **`description-parametres-modeles-arpege-arome-v2-185.pdf`** — parameter glossary shared with ARPEGE, ~104 KB.

Note that SP3 also carries orography as a step-0 field, so the constant file is not strictly required for the terrain — but it is the only source of the land–sea mask.

### Secondary access — Météo-France API portal

Météo-France also exposes AROME HD through its developer portal, which requires free registration and an API key: https://portail-api.meteofrance.fr

This entry does not document the API routes. The object storage above carries the same GRIB2 files with no authentication and no key rotation, and is the recommended access path.

---

## Notes

### Encoding conventions (live-verified)
- **All forecast messages:** GRIB edition 2, `tablesVersion = 15`, `localTablesVersion = 0`, centre `lfpw` (Météo-France Toulouse), `subCentre = 0`, `generatingProcessIdentifier = 204` (same as 0.025° AROME; ARPEGE uses 211), `typeOfGeneratingProcess = 2`.
- **Packing is `grid_ccsds`** (CCSDS/AEC) throughout the forecast files.

### Missing values: 17.1% of every field, and ecCodes will not flag them
The native AROME grid is trapezoidal while GRIB2 can only describe rectangular grids, so the published files mask the corners — the same problem as the 0.025° distribution, at the same proportion.

Live-verified on the SP3 `06H` file: **`bitmapPresent = 1`** and **856,072 of 5,016,591 points (17.1%) are masked**. ecCodes' default `missingValue` is **9999**, not NaN, so `codes_get_values()` returns masked points as the literal 9999.0 and `numpy.isnan()` finds nothing. Note also that `numberOfValues` reports **4,160,519** — the coded (unmasked) count, not the grid size; code that sizes arrays from `numberOfValues` rather than `Ni × Nj` will allocate wrongly.

Set `missingValue` to NaN before reading, mask on `value > 9998`, or crop to a rectangle inside the trapezoid. Météo-France warns that the masked set can differ between parameters and recommends splitting multi-parameter packages into single-parameter GRIB files first, since some tools (CDO is named explicitly) do not handle multi-parameter GRIB cleanly.

### 10 m winds carry different shortNames from the rest of the HP1 profile
As on the 0.025° grid, HP1 publishes relative humidity on all four levels as plain `r`, but the wind fields appear on 20/50/100 m as `u`, `v`, `ws`, `wdir` with the 10 m values encoded separately and resolved by ecCodes to the aliases `10u`, `10v`, `10si`, `10wdir`. The 10 m wind data is present, but selecting `ws` across levels silently misses it. Match on discipline/category/number and level rather than `shortName`.

### Parameters that do not decode
| Encoding | Package | Identification | Basis |
|---|---|---|---|
| 0/16/193 | SP2 | **Maximum radar reflectivity at the surface** (`RFLCTVT_MAX`) | Package description; local parameter number used with `localTablesVersion = 0` |
| 0/5/7, PDT 32 | SP3 | **Synthetic satellite brightness temperature** (`BT`) | Package description; ecCodes reports no `typeOfLevel` for PDT 32 messages |

`CAPE_INS` also has no resolvable level type, as elsewhere in the Météo-France distributions.

### Documentation
The AROME package description (v. 01/04/2025) covers the 0.01° grid on page 3 and is accurate on the package list, the per-hour file organisation, the grid definition and the level set. One gap: **HP1 is listed as carrying only HU, U and V**, whereas the live files also contain **DD and FF** (`wdir`, `ws`) on the same levels. The PDF also documents the trapezoidal-grid missing-value problem in full.

### Access channels that no longer work
- **`object.data.gouv.fr/meteofrance-pnt/…` is dead.** The host resolves and recognises the bucket name but returns `NoSuchKey` for every object.
- **The community AWS mirror is gone.** `mf-models-on-aws.s3.amazonaws.com` returns `NoSuchBucket` and `mf-models-on-aws.org` no longer resolves; only the stale [AWS Open Data Registry listing](https://registry.opendata.aws/meteo-france-models/) remains.

### Choosing between the two AROME distributions

| | 0.025° ([AROME France](./arome-france.md)) | 0.01° (this entry) |
|---|---|---|
| Grid points per field | 803,757 | 5,016,591 |
| Packages | 11 | 4 |
| Isobaric levels | 24 | **none** |
| Height levels | 25 (10–3000 m) | 4 (10–100 m) |
| File organisation | 9 range groups | 52 hourly files |
| Files per cycle | 99 | 208 |
| Volume per cycle | ~23.4 GB | ~5.9 GB |
| Smallest single-step fetch | a whole range group | one hour |

Use **HD** for near-surface detail over complex terrain, for surface reflectivity and synthetic satellite imagery, and wherever you need one specific lead time cheaply. Use the **0.025°** distribution for anything above 100 m, for pressure-level data, for total precipitation, for the full cloud-microphysics profile, and for the isobaric and height-level reflectivity fields.

### Model family and relationships
- **Parent distribution:** [AROME France](./arome-france.md) — same forecast, coarser grid, far richer content. Cross-linked in both directions.
- **Global parent:** [ARPEGE](../../global/france/arpege-global.md) supplies lateral boundary conditions; [ARPEGE Europe](./arpege-europe.md) is its 0.1° regional distribution.
- **Ensemble counterpart:** PEAROME — 26 members over the same EURW1S40 domain, reachable only through the WCS ensemble API and **not published to data.gouv.fr**. **There is no 0.01° ensemble distribution.** The only AROME ensemble on open data is the New Caledonia overseas domain — see [PE-AROME New Caledonia](../../../ensemble_models/regional/fr/pe-arome-ncaled.md).
- **Overseas sibling:** [AROME Outre-Mer](./arome-outre-mer.md) covers five overseas domains at 0.025° only.

---

## Recent version history
AROME HD is a distribution of the parent AROME run and has no independent version history. For model upgrades — the October 2024 move to 3DEnVar, the 2015 resolution and vertical-level increase, and the December 2008 first implementation — see [AROME France](./arome-france.md).

---

## Official documentation
- data.gouv.fr dataset — Paquets Arome, résolution 0,01° : https://www.data.gouv.fr/datasets/paquets-arome-resolution-0-01deg
- data.gouv.fr dataset — Paquets Arome, résolution 0,025° : https://www.data.gouv.fr/datasets/paquets-arome-resolution-0-025deg
- Météo-France open data portal: https://meteo.data.gouv.fr
- Modèles et données de prévision (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- AROME package description (PDF, v. 01/04/2025): `descriptiontechnique-paquetsarome-donneespubliques-v4-20250401.pdf`, linked from the dataset pages above
- ARPEGE/AROME parameter glossary (PDF): `description-parametres-modeles-arpege-arome-v2-185.pdf`, linked from the dataset pages above
- Météo-France local GRIB definitions (for local parameter numbers): linked from the Confluence page above
- Météo-France API portal (registration required): https://portail-api.meteofrance.fr
- Etalab Open Licence v2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

### Key references
- Seity et al. (2011), *The AROME-France Convective-Scale Operational Model*, Monthly Weather Review 139(3): 976–991.
- Brousseau et al. (2016), *Improvement of the forecast of convective activity from the AROME-France system*, Quarterly Journal of the Royal Meteorological Society 142(699): 2231–2243.

---

*Live verification performed 2026-08-09 against the 2026-08-09 15:00 UTC cycle (all four 0.01° packages at steps 00H, 01H, 06H and 51H, decoded with ecCodes 2.48.0), with supporting checks on all eight cycles of 2026-08-08 for publication timing and volume, on the 2026-07-25/26 boundary for retention, and on the EURW1S100 constant-fields file.*
