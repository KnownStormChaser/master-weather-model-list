# AROME France

## What this model is
AROME France is the operational convection-permitting regional deterministic numerical weather prediction model run by Météo-France over metropolitan France and surrounding Western Europe.

It is designed for **short-range forecasting and nowcasting** of high-impact weather such as thunderstorms, heavy precipitation, fog, local wind effects, and convection over complex terrain. The system has been operational since December 2008, with the most recent major upgrade — a transition to a 3DEnVar data assimilation scheme — operationalized in October 2024.

> **Scope of this entry.** This entry covers the **0.025° distribution (EURW1S40)**, which Météo-France and most downstream users refer to simply as *AROME*. The same forecast is also published on a finer **0.01° grid (EURW1S100)**, marketed as *AROME HD*, in a much reduced form — four packages instead of eleven, hourly single-step files instead of range groups, and no pressure-level data at all. That distribution is documented separately in [AROME France HD](./arome-france-hd.md).

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** France and surrounding regions of Western Europe
- **Operational domain:** EURW1S40
- **Grid (live-verified):** `regular_ll`, **1121 × 717**, 0.025° × 0.025°, **803,757 points per field**. First grid point 55.4°N / 348.0°E, last 37.5°N / 16.0°E; `iScansNegatively = 0`, `jScansPositively = 0` (north-to-south row order). Bounds **55.4°N–37.5°N, 12°W–16°E**.
- **The native grid is trapezoidal, not rectangular** — see *Missing values* under Notes.

> **Longitude uses a 0–360° axis and the domain crosses the prime meridian.** The first grid longitude is **348.0°E** (= 12°W), numerically larger than the last (16.0°E). Same caveat as [ARPEGE Europe](./arpege-europe.md): readers assuming a monotonically increasing longitude axis will mis-handle this grid.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system:** AROME (spectral limited-area model, ALADIN-NH dynamical core); SURFEX surface modelling
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved at 1.3 km native resolution; shallow convection parameterized)
- **Native horizontal resolution:** ~1.3 km
- **Public distribution grid:** 0.025° (~2.5 km) regular latitude–longitude
- **Vertical levels:** 90 (lowest model level at ~5 m above ground; model top at 10 hPa)
- **Forecast length (live-verified):** **51 hours**, all cycles
- **Update frequency / cycles (live-verified):** **8× daily** (00, 03, 06, 09, 12, 15, 18, 21 UTC); all eight publish a complete 99-file set
- **Temporal output resolution (live-verified):** **Hourly throughout**, steps 0–51. Confirmed by decoding the `43H48H` and `49H51H` range files — unlike ARPEGE, there is no drop to 3-hourly at any lead time.
- **Data assimilation:** 3DEnVar in the OOPS framework, 1-hour cycle, with Incremental Analysis Update (IAU); operational since October 2024 (cy48t1_op1). Previously 3D-Var.
- **Time step:** 50 s
- **Numerical precision:** Uncycled forecasts run in single precision

Lateral boundary conditions are provided by the parent [ARPEGE global model](../../global/france/arpege-global.md).

> **No analysis fields.** As with ARPEGE, every message carries `typeOfGeneratingProcess = 2` (forecast) including step 0, despite data.gouv.fr describing the dataset as "données d'analyse et de prévision".

---

## What it provides

The 0.025° distribution is split into **eleven packages**, each published in **nine forecast-range files** (`00H06H`, `07H12H`, `13H18H`, `19H24H`, `25H30H`, `31H36H`, `37H42H`, `43H48H`, `49H51H`) — **99 files per cycle**.

### Package inventory (live-verified, 2026-08-09 15 UTC, `00H06H` file)

| Package | Messages | Level type | Parameters |
|---|---:|---|---|
| **SP1** | 97 | surface, 2 m, 10 m | `prmsl`, `2t`, `2r`, `10u`, `10v`, `10si`, `10wdir`, `max_i10fg`, `max_10efg`, `max_10nfg`, total cloud cover, `tp`, `tsnowp`, `tgrp`, `ssrd` |
| **SP2** | 79 | surface, 2 m | `h`, `sp`, surface `t`, `lcc`, `mcc`, `hcc`, `CAPE_INS`, `blh`, `tirf`, `min_2t`, `max_2t`, `2d`, `2sh` |
| **SP3** | 67 | surface | total column water vapour, evaporation, `slhf`, `sshf`, `strd`, `ssr`, `str`, `ssrc`, `strc`, `iews`, `inss` |
| **HP1** | 1225 | height above ground | `t`, `r`, `pres` (25 levels); `u`, `v`, `ws`, `wdir` (24 levels + separate 10 m fields) |
| **HP2** | 1550 | height above ground (25) | `z`, `q`, `dpt`, `tke`, `clwc`, `crwc`, `cswc`, `ciwc`, `cc` |
| **HP3** | 42 | height above ground (7) | radar reflectivity |
| **IP1** | 840 | isobaric (24) | `t`, `r`, `u`, `v`, `z` |
| **IP2** | 1008 | isobaric (24) | `clwc`, `crwc`, `cswc`, `ciwc`, `cc`, + 1 unresolved (see below) |
| **IP3** | 1176 | isobaric (24) | `dpt`, `q`, `ws`, `wdir`, `w`, `wz`, `pv` |
| **IP4** | 240 | isobaric (24 / 16) | `tke` (24 levels); radar reflectivity (16 levels) |
| **IP5** | 252 | isobaric (5 / 20) + potential vorticity | `absv`, `vo` (5 levels); `papt` (20 levels); `u`, `v`, `z` on PV surfaces |

### Level sets (live-verified)
- **Isobaric — 24 levels** (IP1, IP2, IP3, `tke` in IP4): 100, 125, 150, 175, 200, 225, 250, 275, 300, 350, 400, 450, 500, 550, 600, 650, 700, 750, 800, 850, 900, 925, 950, 1000 hPa
- **Isobaric — radar reflectivity (IP4), 16 levels:** 200 through 925 hPa (450 and 950/1000 omitted)
- **Isobaric — `absv`/`vo` (IP5), 5 levels:** 300, 500, 600, 700, 850 hPa
- **Isobaric — `papt` (IP5), 20 levels:** 200 through 1000 hPa
- **Height above ground — 25 levels** (HP1 `t`/`r`/`pres`, all of HP2): 10, 20, 35, 50, 75, 100, 150, 200, 250, 375, 500, 625, 750, 875, 1000, 1125, 1250, 1375, 1500, 1750, 2000, 2250, 2500, 2750, 3000 m
- **Height above ground — radar reflectivity (HP3), 7 levels:** 500, 750, 1000, 1500, 2000, 2500, 3000 m
- **Potential vorticity (IP5): two surfaces at 1.5 and 2.0 PVU** (encoded 1500, 2000), carrying `u`, `v`, `z`

> **Radar reflectivity is the standout product here.** AROME diagnoses reflectivity internally from its explicit microphysics and publishes it on both isobaric (IP4) and height (HP3) levels — something neither ARPEGE distribution offers. HP3 is also by far the cheapest package in the catalogue at ~50 MB per cycle.

---

## Data availability

- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence version 2.0** (Etalab v2.0; attribution required)
- **High-Value Dataset:** The data.gouv.fr dataset carries the **HVD badge** ("Données de forte valeur") under the EU Open Data Directive
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (edition 2, `grid_ccsds` packing)
- **Official download location:**
  https://www.data.gouv.fr/datasets/paquets-arome-resolution-0-025deg

### Primary access — data.gouv.fr object storage (no authentication)

Served from OVH-hosted S3-compatible object storage. **No account, no API key, no registration.**

- **URL pattern (live-verified):**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/{run}/arome/0025/{package}/arome__0025__{package}__{range}__{run}.grib2
  ```
  where `{run}` is ISO-8601 with literal colons (e.g. `2026-08-09T15:00:00Z`), `{package}` is one of `SP1 SP2 SP3 HP1 HP2 HP3 IP1 IP2 IP3 IP4 IP5`, and `{range}` is one of the nine groups above. Note the **double underscores** and the grid token `0025`.

- **Example:**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/2026-08-09T15:00:00Z/arome/0025/HP3/arome__0025__HP3__00H06H__2026-08-09T15:00:00Z.grib2
  ```

- **The bucket is anonymously listable:**
  ```bash
  curl -s "https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=pnt/2026-08-09T15:00:00Z/arome/0025/&max-keys=1000"
  ```
  Paginate via `NextContinuationToken` (URL-encode it). Objects serve `Content-Length` and `Last-Modified` on `HEAD` and support HTTP Range requests.

- **Retention (live-verified):** **15 days rolling**, shared with every other model in the bucket. On 2026-08-09, `2026-07-26T12:00:00Z` held a complete 99-file set and `2026-07-25` was gone.

- **Publication latency (live-verified):** measured across all eight cycles of 2026-08-08 from object `Last-Modified` timestamps. **Latency varies far more than for ARPEGE, and not in a simple pattern:**

  | Cycle | First file | Last file |
  |---|---|---|
  | 00 UTC | T+1 h 58 m | T+2 h 55 m |
  | 03 UTC | T+1 h 53 m | T+2 h 45 m |
  | 06 UTC | T+4 h 20 m | T+5 h 12 m |
  | 09 UTC | T+3 h 28 m | T+4 h 15 m |
  | 12 UTC | T+3 h 09 m | T+4 h 14 m |
  | 15 UTC | T+3 h 05 m | T+3 h 56 m |
  | 18 UTC | T+4 h 17 m | T+5 h 06 m |
  | 21 UTC | T+3 h 23 m | T+4 h 14 m |

  The 00 and 03 UTC cycles land roughly **two and a half hours earlier** than the 06 and 18 UTC cycles. Anything with a fixed polling deadline should budget for the worst case (~T+5 h 15 m), not the average.

- **Volume (live-verified, 2026-08-09 15 UTC):** **~23.4 GB per cycle**, ~187 GB/day, roughly **2.8 TB** resident under the 15-day window. Per package per cycle: HP1 5.96 GB, IP3 5.74 GB, HP2 4.11 GB, IP1 3.63 GB, IP5 1.15 GB, IP2 1.00 GB, SP3 0.53 GB, IP4 0.46 GB, SP1 0.46 GB, SP2 0.33 GB, HP3 0.05 GB. Individual files range from 2.8 MB (HP3 `49H51H`) to 808 MB (HP1 `00H06H`).

  At eight cycles a day AROME is the heaviest single model in the bucket — more than four times ARPEGE Global's daily output despite covering a fraction of the globe.

### Companion static files

Published on the dataset page rather than in the object store:

- **`constant-eurw1s40.grib2`** ("Champs CONSTANT EURW1S40") — **live-verified**: 2 messages, orography (`h`, geometrical height above ground, m) and land–sea mask (`lsm`, 0–1) on the EURW1S40 grid. Encoded unlike the forecast data: `tablesVersion = 32` (against 15), `grid_second_order` packing (against `grid_ccsds`), `generatingProcessIdentifier = 255`, `typeOfGeneratingProcess = 0` — the same divergence seen in the ARPEGE constant files.
- **`descriptiontechnique-paquetsarome-donneespubliques-v4-20250401.pdf`** — official package description, ~209 KB, **version 01/04/2025**. Current and accurate; see *Documentation* below.
- **`description-parametres-modeles-arpege-arome-v2-185.pdf`** — parameter glossary shared with ARPEGE, ~107 KB. Plain-language definitions and units for every parameter name, with no GRIB code mapping.

### Secondary access — Météo-France API portal

Météo-France also exposes AROME through its developer portal, which requires free registration and an API key: https://portail-api.meteofrance.fr

This entry does not document the API routes. The object storage above carries the same GRIB2 packages with no authentication and no key rotation, and is the recommended access path.

---

## Notes

### Encoding conventions (live-verified)
- **All messages:** GRIB edition 2, `tablesVersion = 15`, `localTablesVersion = 0`, centre `lfpw` (Météo-France Toulouse), `subCentre = 0`, `typeOfGeneratingProcess = 2`.
- **`generatingProcessIdentifier = 204`** — distinct from ARPEGE's 211. This is the cleanest programmatic way to tell AROME and ARPEGE messages apart if they end up in the same archive.
- **Packing is `grid_ccsds`** (CCSDS/AEC) throughout.

### Missing values: 17.2% of every field, and ecCodes will not flag them
Since 2019 the native AROME grid has been **trapezoidal**, but GRIB2 can only describe rectangular grids. The published files therefore mask the corners.

Live-verified on the IP2 `00H06H` file: **`bitmapPresent = 1`** and **138,076 of 803,757 points (17.2%) are masked** in every field. But ecCodes' default `missingValue` is **9999**, not NaN, so `codes_get_values()` returns masked points as the literal value **9999.0** — and `numpy.isnan()` finds nothing. Naive statistics are badly wrong as a result: the raw mean of the 850 hPa cloud-fraction field is 1718 for a quantity bounded by 0–1.

Set `missingValue` to NaN before reading, or mask on `value > 9998`, or crop to a rectangle inside the trapezoid. Météo-France additionally warns that **the masked set can differ between parameters**, and recommends splitting multi-parameter packages into single-parameter GRIB files before handling missing values, since some tools (CDO is named explicitly) do not handle multi-parameter GRIB cleanly.

### Cloud and turbulence fields skip step 0
Instantaneous fields generally carry 7 steps in a 6-hour range file (steps 0–6), but not all:
- **Total cloud cover, `lcc`, `mcc`, `hcc`** (SP1, SP2) and **`tke`** (HP2) carry only 6 — they start at step **1**.
- `h` (orography, SP2) appears **once per file** as a static field.
- Time-processed fields (accumulations, gust and 2 m extremes) carry 6, as expected for 6 one-hour intervals.

Code that assumes a uniform step axis across a package will mis-align cloud cover against temperature.

### 10 m winds carry different shortNames from the rest of the HP1 profile
In HP1, temperature, relative humidity and pressure are published on all **25** height levels including 10 m, decoding as plain `t`, `r`, `pres`. The wind fields are published on **24** levels (20–3000 m) plus separate 10 m entries that ecCodes resolves to the aliases `10u`, `10v`, `10si`, `10wdir` rather than `u`, `v`, `ws`, `wdir`.

The 10 m wind data is present, but selecting `ws` across levels silently misses it. Match on discipline/category/number and level rather than `shortName`.

### Accumulation convention
All time-processed fields in this distribution accumulate **from run start** (`0-1`, `0-2`, … `0-6`), including `tp`, `tsnowp`, `tgrp`, `tirf`, `ssrd` and the SP3 fluxes. The 2 m and gust extremes use the same run-start convention. This is simpler than ARPEGE, where three different interval conventions coexist — but it means hourly totals must be obtained by differencing successive messages.

### Parameters that do not decode
Four fields decode as `unknown` in ecCodes 2.48.0. Three are resolved by Météo-France's package description; one is not:

| Encoding | Package | Identification | Basis |
|---|---|---|---|
| 0/6/1 | SP1 | **Total cloud cover** (`NEBUL`) | Package description + parameter glossary; WMO-standard encoding that ecCodes leaves unresolved because its `tcc` is bound to the ECMWF-local 0/6/192 |
| 0/1/64 | SP3 | **Total column water vapour** (`COLONNE_VAPO`) | Package description + glossary |
| 0/1/6 | SP3 | **Evaporation** (`FLEVAP`), accumulated | Package description + glossary |
| 0/16/192 | IP4, HP3 | **Derived radar reflectivity** (`RFLCTVT`) | Package description + glossary; local parameter number used with `localTablesVersion = 0` |
| 0/1/201 | IP2 | **Undocumented — TBD** | Not in the package description, which lists only five IP2 parameters against six in the file |

On 0/1/201: the evidence points strongly to **specific graupel content**. IP2 is the condensate package and already carries liquid, rain, snow and ice species plus cloud fraction, with graupel the conspicuous absence — and AROME's microphysics carries a graupel species (`tgrp` is published at the surface in SP1). Sampled at 850 hPa the field has the right magnitude and shape for a condensate mixing ratio: max 6.4 × 10⁻⁴, mean 4.2 × 10⁻⁹, sitting between snow (2.6 × 10⁻⁴) and rain (1.3 × 10⁻³). Météo-France does not document it, so this remains inference.

Two carried-over oddities: **`CAPE_INS` has no resolvable level type**, and **`iews`/`inss`** are named "Instantaneous … turbulent surface stress" by ecCodes while encoded as `accum` — they are time-integrated.

### Documentation
Unlike the ARPEGE package description (still at version 02/01/2024 and substantially wrong), **the AROME package description is current — version 01/04/2025 — and accurate.** Package membership, level counts, range groups, cycle list and forecast length all match the live files exactly. Two small discrepancies:

- **HP1 is listed as carrying `Z`.** It does not; geopotential height on height levels is in HP2, where the PDF also lists it. The HP1 listing has a spurious entry.
- **HP2's `Z` is described as "24 niveaux (100 à 1000)"** — isobaric wording pasted into a height-level package. The live field is on the 25 height levels from 10 m to 3000 m.
- IP2 lists five parameters against six in the file (see 0/1/201 above).

The PDF also documents the trapezoidal-grid missing-value problem and the CDO caveat in full — worth reading before writing an ingest pipeline.

### Access channels that no longer work
- **`object.data.gouv.fr/meteofrance-pnt/…` is dead.** The host resolves and recognises the bucket name but returns `NoSuchKey` for every object.
- **The community AWS mirror is gone.** `mf-models-on-aws.s3.amazonaws.com` returns `NoSuchBucket` and `mf-models-on-aws.org` no longer resolves; only the stale [AWS Open Data Registry listing](https://registry.opendata.aws/meteo-france-models/) remains. The previously documented caveat that the mirror served only 42 hours of range is therefore moot.

### Model family and relationships
- **Higher-resolution distribution:** [AROME France HD](./arome-france-hd.md) publishes the same forecast on a 0.01° grid over the identical domain, but as a reduced four-package set with no pressure levels. Use it for surface and low-level detail; use this entry's 0.025° packages for anything needing the full three-dimensional state.
- **Parent model:** [ARPEGE](../../global/france/arpege-global.md) supplies lateral boundary conditions. See also [ARPEGE Europe](./arpege-europe.md), the 0.1° regional distribution of the same global forecast.
- **Ensemble counterpart:** PEAROME — 26 members over the same EURW1S40 domain since cy48t1_op1, 4× daily at 03/09/15/21 UTC, 51 h range. It is **not published to data.gouv.fr** and is reachable only through the WCS ensemble API. The only AROME ensemble on open data is the New Caledonia overseas domain — see [PE-AROME New Caledonia](../../../ensemble_models/regional/fr/pe-arome-ncaled.md).
- **Overseas sibling:** [AROME Outre-Mer](./arome-outre-mer.md) runs the same core on five overseas domains, without its own data assimilation and with a coarser output level set (19 isobaric, 14 height, against 24 and 25 here).
- **Rapid-update variants:** Météo-France also runs 15-minute output configurations (AROME PI 15min and HD 15min) providing 6-hour forecasts updated hourly for nowcasting. These are **not** present in the `arome/` tree of the open-data bucket — only the `0025` and `001` grids appear — so their open-data availability is **TBD**.

---

## Recent version history
- **October 2024 (cy48t1_op1):** transition from 3D-Var to **3DEnVar** data assimilation in the OOPS framework, with Incremental Analysis Update.
- **2022:** additional refinements (cy46t1-era).
- **2015:** native resolution increased from 2.5 km to 1.3 km; vertical levels from 60 to 90; hourly data-assimilation cycling introduced.
- **December 2008:** first operational implementation.

---

## Official documentation
- data.gouv.fr dataset — Paquets Arome, résolution 0,025° : https://www.data.gouv.fr/datasets/paquets-arome-resolution-0-025deg
- data.gouv.fr dataset — Paquets Arome, résolution 0,01° : https://www.data.gouv.fr/datasets/paquets-arome-resolution-0-01deg
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

*Live verification performed 2026-08-09 against the 2026-08-09 15:00 UTC cycle (all eleven 0.025° packages, `00H06H` files, 6,576 GRIB2 messages decoded with ecCodes 2.48.0), with supporting checks on the `43H48H` and `49H51H` ranges for step spacing, on all eight cycles of 2026-08-08 for publication timing and volume, and on the 2026-07-25/26 boundary for retention.*
