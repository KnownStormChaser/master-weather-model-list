# ARPEGE Europe

## What this model is
ARPEGE Europe is the **regional 0.1° public distribution** of Météo-France's operational global ARPEGE model, covering Europe and North Africa.

It is not a separate model — the underlying forecasts come from the same global ARPEGE run on its stretched-grid spectral core, where native resolution is finest over France (~5 km). The Europe distribution interpolates that output onto a regional 0.1° regular latitude–longitude grid, providing a finer-resolution view of the same forecast over the European domain than the 0.25° global packages can offer.

For the parent system's full technical description — dynamical core, data assimilation, model configuration — see [ARPEGE](../../global/france/arpege-global.md).

> **This is not simply the global distribution at higher resolution.** The 0.1° packages differ from the 0.25° ones in package count, in which package a given parameter lands in, in vertical extent, and in temporal cadence. The differences are set out under *What it provides* and *Notes*; code written against the global packages will not work unmodified here.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** Europe and North Africa
- **Grid (live-verified):** **EURAT01** — `regular_ll`, **741 × 521**, 0.1° × 0.1°, **386,061 points per field**. First grid point 72.0°N / 328.0°E, last 20.0°N / 42.0°E; `iScansNegatively = 0`, `jScansPositively = 0` (north-to-south row order). Bounds are therefore **72°N–20°N, 32°W–42°E**, matching Météo-France's published EURAT01 definition. Verified identical across all 10,146 messages inspected in the 2026-08-09 12 UTC cycle.

> **Longitude is encoded on a 0–360° axis, and the domain crosses the prime meridian.** The first grid longitude is **328.0°E** (= 32°W) and the last is **42.0°E**, so the first value is numerically *larger* than the last. Readers that assume a monotonically increasing longitude axis, or that infer domain width by subtracting first from last, will mis-handle this grid. ecCodes and cfgrib resolve it correctly; hand-rolled parsers frequently do not.

---

## Basic details
- **Model type:** Global deterministic NWP, regional distribution (stretched-grid spectral parent)
- **Model system / core:** ARPEGE (shared IFS/ARPEGE code base with ECMWF); SURFEX surface modelling
- **Dynamical formulation:** Hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** No (deep convection is parameterized)
- **Native horizontal resolution:** Variable — approximately 5 km over France, ~24 km over the antipodes (TL1798 stretched spectral truncation)
- **Public distribution grid:** **0.1° (~10 km)** regular latitude–longitude over Europe and North Africa
- **Vertical levels:** 105 (model); the public distribution carries interpolated pressure and height-above-ground levels — see *Level sets* below
- **Model top:** ~0.1 hPa (~65 km); lowest model level ~10 m above ground
- **Forecast length (live-verified):** **102 hours for all four cycles.** Verified on 2026-08-08 across 00/06/12/18 UTC: every cycle publishes the full nine-range set terminating at `097H102H`.
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (live-verified):** **Mixed, and resolved per parameter rather than per package.** Everything is hourly through 48 h. Beyond 48 h, some parameters stay hourly while others drop to 3-hourly — including parameters sitting side by side in the same file. See *Temporal cadence splits after 48 h* under Notes.
- **Time step:** 240 s

---

## What it provides

The 0.1° distribution is split into **eight packages** — there is no SP3 — each published in **nine forecast-range files** (`000H012H`, `013H024H`, `025H036H`, `037H048H`, `049H060H`, `061H072H`, `073H084H`, `085H096H`, `097H102H`), giving **72 files per cycle**.

### Package inventory (live-verified, 2026-08-09 12 UTC, `000H012H` file)

| Package | Messages | Level type | Parameters |
|---|---:|---|---|
| **SP1** | 325 | surface, 2 m, 10 m | `prmsl`, `2t`, `2r`, `10u`, `10v`, `10si`, `10wdir`, total cloud cover, `tp`, `tsnowp`, `ssrd`, `max_i10fg`, `max_10efg`, `max_10nfg`, `cp`, `lsp`, `crr`, `lsrr`, `csfwe`, `lsfwe`, `tirf`, `sd`, 3 soil fields, 1 unresolved |
| **SP2** | 331 | surface, 2 m | `2d`, `2sh`, `sp`, surface `t`, `lcc`, `mcc`, `hcc`, `blh`, `CAPE_INS`, `h`, `min_2t`, `max_2t`, `sshf`, `slhf`, `ssr`, `str`, `strd`, `ssrc`, `strc`, `iews`, `inss`, `ptype` (avg and max), `max_clwc`, `min_vis`, + 3 unresolved |
| **HP1** | 2184 | height above ground (24) | `t`, `r`, `u`, `v`, `ws`, `wdir`, `pres` |
| **HP2** | 2184 | height above ground (24) | `dpt`, `q`, `tke`, `clwc`, `ciwc`, `cc`, `z` |
| **IP1** | 1560 | isobaric (24) | `t`, `r`, `u`, `v`, `z` |
| **IP2** | 1560 | isobaric (24) | `dpt`, `q`, `ws`, `wdir`, `w` |
| **IP3** | 1248 | isobaric (24) | `clwc`, `ciwc`, `cc`, `tke` |
| **IP4** | 754 | isobaric (24 / 20 / 4) + potential vorticity | `pv`, `absv`, `vo`, `papt`; `u`, `v`, `z` on PV surfaces |

### Level sets (live-verified)
- **Isobaric — 24 levels, uniform across IP1, IP2, IP3 and `pv` in IP4:** 100, 125, 150, 175, 200, 225, 250, 275, 300, 350, 400, 450, 500, 550, 600, 650, 700, 750, 800, 850, 900, 925, 950, 1000 hPa
- **Isobaric — `absv` and `vo` (IP4): only 4 levels** — 300, 500, 700, 850 hPa
- **Isobaric — `papt` (IP4): 20 levels** — 200 hPa through 1000 hPa
- **Height above ground (HP1, HP2) — 24 levels:** 20, 35, 50, 75, 100, 150, 200, 250, 375, 500, 625, 750, 875, 1000, 1125, 1250, 1375, 1500, 1750, 2000, 2250, 2500, 2750, 3000 m
- **Potential vorticity (IP4): two surfaces at 1.5 and 2.0 PVU** (encoded as 1500, 2000), carrying `u`, `v` and `z`

> **The 0.1° distribution has no stratosphere.** Isobaric fields stop at 100 hPa. The ten levels above that (1, 2, 3, 5, 7, 10, 20, 30, 50, 70 hPa) published on the 0.25° global grid are entirely absent here. Users needing stratospheric levels must use [ARPEGE Global](../../global/france/arpege-global.md) despite its coarser horizontal grid. This truncation is documented by Météo-France and is confirmed live.

---

## Data availability

- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence version 2.0** (Etalab v2.0; attribution required)
- **High-Value Dataset:** The data.gouv.fr dataset carries the **HVD badge** ("Données de forte valeur") under the EU Open Data Directive
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (edition 2, `grid_ccsds` packing)
- **Official download location:**
  https://www.data.gouv.fr/datasets/paquets-arpege-resolution-0-1deg

### Primary access — data.gouv.fr object storage (no authentication)

The GRIB packages are served from OVH-hosted S3-compatible object storage. **No account, no API key, no registration.**

- **URL pattern (live-verified):**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/{run}/arpege/01/{package}/arpege__01__{package}__{range}__{run}.grib2
  ```
  where `{run}` is ISO-8601 with literal colons (e.g. `2026-08-09T12:00:00Z`), `{package}` is one of `SP1 SP2 HP1 HP2 IP1 IP2 IP3 IP4`, and `{range}` is one of the nine listed above. Note the **double underscores** between filename fields, and that the grid token is `01` — the decimal point is dropped.

- **Example:**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/2026-08-09T12:00:00Z/arpege/01/HP1/arpege__01__HP1__000H012H__2026-08-09T12:00:00Z.grib2
  ```

- **The bucket is anonymously listable**, so runs and files can be discovered programmatically without scraping the data.gouv.fr pages:
  ```bash
  curl -s "https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=pnt/2026-08-09T12:00:00Z/arpege/01/&max-keys=1000"
  ```
  Listings paginate via `NextContinuationToken` (URL-encode it). Objects serve `Content-Length` and `Last-Modified` on `HEAD` and support HTTP Range requests.

- **Retention (live-verified):** **15 days rolling**, shared with every other model in the same bucket.

- **Publication latency (live-verified):** measured across six cycles (2026-08-08 00/06/12/18 UTC and 2026-08-09 00/06 UTC) from object `Last-Modified` timestamps:
  - First file of a cycle: **T+2 h 30 m to T+3 h 43 m**
  - Last file of a cycle: **T+3 h 09 m to T+4 h 19 m**
  - The same cycle split seen on the global grid applies: **00 and 12 UTC** begin publishing near **T+2 h 50 m**, **06 and 18 UTC** near **T+3 h 43 m**.
  - **ARPEGE Europe completes before ARPEGE Global.** Across the same six cycles the 0.1° set finished 30–50 minutes ahead of the 0.25° set, and its publication window is much tighter (~40 min, against 1 h 15 m to 1 h 35 m for the global grid). Users who only need the European domain get their data appreciably sooner.

- **Volume (live-verified, 2026-08-09 12 UTC):** **~14.8 GB per cycle**, ~59 GB/day, roughly **0.89 TB** resident under the 15-day window — about a third of the 0.25° footprint. Per package per cycle: HP1 4.02 GB, IP2 2.75 GB, IP1 2.40 GB, HP2 2.33 GB, IP4 1.51 GB, SP2 0.65 GB, SP1 0.63 GB, IP3 0.49 GB. Individual files range from 14 MB (IP3 `097H102H`) to 765 MB (HP1 `000H012H`) — all far smaller than the 0.25° equivalents, which reach 3.6 GB.

### Companion static files

Published on the dataset page rather than in the object store:

- **`constant-eurat01.grib2`** ("Champs CONSTANT EURAT01") — orography (`h`, geometrical height above ground, m) and land–sea mask (`lsm`, 0–1) on the EURAT01 grid, 2 messages, ~674 KB. Last updated 2025-02-17.
- **`description-paquets-modele-arpege.pdf`** — official package description covering both the 0.25° and 0.1° grids, ~90 KB, **version dated 02/01/2024**. See *Documentation defects* below before relying on it.

### Secondary access — Météo-France API portal

Météo-France also exposes ARPEGE Europe through its developer portal, which requires free registration and an API key: https://portail-api.meteofrance.fr

This entry does not document the API routes. The object storage above carries the same GRIB2 packages with no authentication and no key rotation, and is the recommended access path for the raw gridded data.

---

## Notes

### Encoding conventions (live-verified)
- **All messages:** GRIB edition 2, `tablesVersion = 15`, `localTablesVersion = 0`, centre `lfpw` (Météo-France Toulouse), `subCentre = 0`, `generatingProcessIdentifier = 211`, `typeOfGeneratingProcess = 2` — identical to the 0.25° distribution.
- **Packing is `grid_ccsds`** (CCSDS/AEC) throughout. The constant-fields file is the exception: like its GLOB025 counterpart it uses `tablesVersion = 32` and `grid_second_order` packing.
- **No analysis fields.** Every message is flagged as a forecast, including step 0, despite data.gouv.fr describing the dataset as "données d'analyse et de prévision".

### Package membership differs from the 0.25° grid
There is no SP3 at 0.1°. Its contents were not dropped — they were **redistributed into SP1 and SP2**, so the same parameter lives in a different package depending on which grid you fetch:

| Parameter | 0.25° package | 0.1° package |
|---|---|---|
| `cp`, `lsp`, `crr`, `lsrr`, `csfwe`, `lsfwe`, `tirf` | SP3 | **SP1** |
| soil fields (2/3/192, 2/3/193, 2/3/254, surface) | SP3 | **SP1** |
| `ssrc`, `strc` | SP3 | **SP2** |
| `ptype` (avg and max), `max_clwc`, `min_vis`, 0/19/201 | SP3 | **SP2** |
| `tsnowp` | SP1 *and* SP3 | **SP1** only |

Two parameters exist **only** at 0.1° and have no counterpart in any 0.25° package: **`sd`** (snow depth, 0/1/60) and an unresolved field encoded **0/7/199 at `atmosphere` level**, both in SP1.

Conversely, eight parameter/level-type combinations present at 0.25° are **absent at 0.1°**, all of them SP3 members: `ceil` (ceiling), cloud base (0/6/11), lightning flash density (0/17/4), 0/4/198, the synthetic satellite brightness temperatures (PDT 32, 10.8 µm and 6.2 µm), and the three soil fields at 2.5 m `depthBelowLand`. **The 0.1° distribution carries no synthetic satellite imagery and no ceiling field** — a meaningful gap for aviation use, where the higher-resolution grid would otherwise be the natural choice.

### Temporal cadence splits after 48 h — per parameter, not per package
Météo-France's dataset page states the cadence as "1h pour certains paramètres / 1h jusqu'à l'échéance 48 puis 3h pour les paramètres restant" but does not say which parameters fall on which side. Verified against the `049H060H` files of the 2026-08-09 12 UTC cycle:

| Package | Hourly beyond 48 h | 3-hourly beyond 48 h |
|---|---|---|
| **SP1** | all core surface fields, precipitation and gusts (21 params) | `sd`, the three soil fields, 0/7/199 |
| **SP2** | `2d`, `lcc`, `mcc`, `hcc`, `strd` | everything else, incl. `sp`, `t`, `2sh`, `blh`, `CAPE_INS`, all remaining fluxes, `min_2t`, `max_2t` |
| **HP1** | `wdir`, `ws`, `u`, `v` | `t`, `r`, `pres` |
| **HP2, IP1, IP2, IP3, IP4** | — | all parameters |

The HP1 case is the sharpest trap: **wind fields stay hourly while temperature, humidity and pressure on the same 24 height levels drop to 3-hourly, inside the same file.** Any code that derives a time axis from one variable and applies it across a package will mis-align beyond step 48. Read `stepRange` per message.

Below 48 h everything is hourly (`min_2t` and `max_2t` excepted — those are inherently 3-hourly interval extremes throughout).

### `ssr` is emitted twice at every step
In **SP2**, surface net short-wave radiation (`ssr`, 0/4/9) appears **exactly twice per step** in every range file. The duplicates are **bit-identical**: same `stepRange`, `lengthOfTimeRange`, `typeOfStatisticalProcessing`, `bitsPerValue` and `numberOfValues`, and `numpy.array_equal` on the decoded values returns true. Verified across three range files and three separate cycles (2026-08-09 12 UTC `000H012H` and `049H060H`; 2026-08-09 00 UTC; 2026-08-08 18 UTC) — this is systematic, not a one-off corruption.

Harmless for tools that index by key, but it inflates `ssr` message counts by a factor of two and can trip readers that build arrays by counting messages or that reject duplicate coordinate keys (cfgrib will warn or drop). No equivalent duplication occurs in the 0.25° packages.

### Parameters that do not decode
The same ecCodes gaps as on the global grid apply, plus a few specific to this distribution:

| Encoding | Package | Status |
|---|---|---|
| 0/6/1 | SP1 | **Total cloud cover** (`NEBUL` in Météo-France's documentation). WMO-standard encoding that ecCodes 2.48.0 leaves as `unknown`, because its `tcc` is bound to the ECMWF-local 0/6/192. Low/mid/high cloud resolve normally. |
| 0/1/64 | SP2 | **Total column water vapour** (`COLONNE_VAPO`). Decodes as `unknown`. |
| 0/1/6 | SP2 | **Evaporation flux** (`FLEVAP`). Decodes as `unknown`. |
| 2/3/192, 2/3/193, 2/3/254 | SP1 | Soil fields. Local parameter numbers used with `localTablesVersion = 0`; undocumented. **TBD** |
| 0/19/201 | SP2 | Local number, time-minimum; undocumented. **TBD** |
| 0/7/199 | SP1 | Local number at `atmosphere` level; 0.1°-only and undocumented. **TBD** |

Resolving the remaining four requires Météo-France's local GRIB definitions, linked from the Confluence page below.

Two further oddities carried over from the global distribution: **`CAPE_INS` has no resolvable level type**, and **`iews`/`inss`** are named "Instantaneous … turbulent surface stress" by ecCodes while encoded as `accum` — they are time-integrated.

### Documentation defects
`description-paquets-modele-arpege.pdf` (version 02/01/2024) is the only official parameter listing, and it has drifted badly from the live data. Verified discrepancies affecting this grid:

- **The IP4 vorticity level counts are swapped between the two grids.** The PDF gives `TA`/`TB` (`absv`/`vo`) as 26 levels (50–1000 hPa) for EURAT01 and 4 levels (300, 500, 700, 850 hPa) for GLOB025. The live files are the exact reverse: **4 levels at 0.1°, 26 levels at 0.25°.**
- **`THETAPW` (`papt`) is described for EURAT01 as "24 niveaux 20 à 3000 hPa"** — wrong on both counts. The live field has **20 levels spanning 200–1000 hPa**; the quoted "20 à 3000" appears to be the height-level range accidentally pasted from the HP section.
- **SP1 and SP2 are substantially under-documented.** The PDF lists 14 parameters for SP1 and 23 for SP2; the live files carry **26** and **28** distinct parameter/level-type/step-type combinations respectively. The precipitation-partition fields, soil fields, `sd`, `ptype`, `max_clwc`, `min_vis` and the unresolved local parameters are all absent from the PDF.
- **The PDF gives no cadence statement for EURAT01 at all** — the mixed hourly/3-hourly behaviour appears only in the data.gouv.fr dataset description, and neither source names the affected parameters.

The PDF is correct on the points that matter most for planning: the EURAT01 bounds (72N 20N 32W 42E), the nine range groups, the 24-level isobaric truncation, and the HP1/HP2/IP1/IP2/IP3 parameter lists.

### Access channels that no longer work
- **`object.data.gouv.fr/meteofrance-pnt/…` is dead.** The host resolves and recognises the bucket name but returns `NoSuchKey` for every object. This pattern still circulates widely in third-party tooling.
- **The community AWS mirror is gone.** `mf-models-on-aws.s3.amazonaws.com` returns `NoSuchBucket` and `mf-models-on-aws.org` no longer resolves; only the stale [AWS Open Data Registry listing](https://registry.opendata.aws/meteo-france-models/) remains.

### Model family and relationships
- **Not a separate model run.** ARPEGE Europe is a regional output grid from the same global ARPEGE forecast as the 0.25° packages. The 0.1° resolution is meaningful because the stretched grid runs at native resolutions finer than 0.1° over Europe, so this distribution preserves detail the global grid discards.
- **Choosing between the two distributions:** prefer 0.1° for horizontal detail, faster availability, hourly surface fields to 102 h, and smaller files. Use [ARPEGE Global](../../global/france/arpege-global.md) when you need stratospheric levels, ceiling, lightning, synthetic satellite imagery, deep-soil fields, the 0.7 PVU surface, or coverage outside 72°N–20°N / 32°W–42°E.
- **Ensemble counterpart:** [PE-ARPEGE](../../../ensemble_models/global/fr/pe-arpege.md) provides 35-member probabilistic guidance and includes a matching EURAT01 0.1° distribution.
- **Higher-resolution sibling:** [AROME France](./arome-france.md) (1.3 km native, 0.025° public) is nested inside ARPEGE for its lateral boundary conditions; ARPEGE Europe sits between ARPEGE Global and AROME in both resolution and domain extent. See also [AROME Outre-Mer](./arome-outre-mer.md).

---

## Official documentation
- data.gouv.fr dataset — Paquets Arpège, résolution 0,1° : https://www.data.gouv.fr/datasets/paquets-arpege-resolution-0-1deg
- data.gouv.fr dataset — Paquets Arpège, résolution 0,25° : https://www.data.gouv.fr/datasets/paquets-arpege-resolution-0-25deg
- Météo-France open data portal: https://meteo.data.gouv.fr
- Modèles et données de prévision (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- Météo-France local GRIB definitions (for local parameter numbers): linked from the Confluence page above
- ARPEGE package technical description (PDF, v. 02/01/2024): `description-paquets-modele-arpege.pdf`, linked from the dataset pages above
- Météo-France API portal (registration required): https://portail-api.meteofrance.fr
- Etalab Open Licence v2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live verification performed 2026-08-09 against the 2026-08-09 12:00 UTC cycle (all eight 0.1° packages, `000H012H` files, 10,146 GRIB2 messages decoded with ecCodes 2.48.0), with supporting checks on the `049H060H` and `097H102H` ranges for cadence splits, on the 2026-08-08 00/06/12/18 UTC and 2026-08-09 00/06 UTC cycles for forecast length and publication timing, and on three separate cycles for the `ssr` duplication.*
