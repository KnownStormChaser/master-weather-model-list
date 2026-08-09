# ARPEGE (Action de Recherche Petite Échelle Grande Échelle)

## What this model is
ARPEGE is the operational global deterministic numerical weather prediction system run by Météo-France.

It is the global member of Météo-France's NWP suite, used for medium-range forecasting worldwide and as the parent model providing initial and lateral boundary conditions for the regional AROME systems. ARPEGE is built on a stretched-grid spectral dynamical core, which gives it variable native resolution — finest over France and progressively coarser away from the central point. This design lets ARPEGE deliver high-resolution guidance over the French and European domains while still running globally.

ARPEGE is jointly developed with ECMWF as part of the IFS/ARPEGE shared code base, with Météo-France-specific operational configuration, physics, and data assimilation choices.

> **Scope of this entry.** This entry covers the **0.25° global distribution (GLOB025)**. The 0.1° Europe/North Africa distribution of the same forecast is documented separately in [ARPEGE Europe](../../regional/france/arpege-europe.md). The former 0.5° global packages are no longer published and have been removed from this entry.

---

## Who runs it
- **Organization:** Météo-France
- **Country / region:** France

---

## What area it covers
- **Coverage:** Global, with variable native resolution (highest over France)
- **Public distribution grid (live-verified):** **GLOB025** — `regular_ll`, **1440 × 721**, 0.25° × 0.25°, **1,038,240 points per field**. First grid point 90.0°N / 0.0°E, last −90.0° / 359.75°E; `iScansNegatively = 0`, `jScansPositively = 0` (north-to-south row order). Verified identical across all 21,824 messages inspected in the 2026-08-09 12 UTC cycle.

---

## Basic details
- **Model type:** Global deterministic NWP (stretched-grid spectral)
- **Model system / core:** ARPEGE (shared IFS/ARPEGE code base with ECMWF); SURFEX surface modelling
- **Dynamical formulation:** Hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** No (deep convection is parameterized at the native variable resolution, ~5 km over France)
- **Native horizontal resolution:** Variable — approximately 5 km over France, ~24 km over the antipodes (TL1798 stretched spectral truncation)
- **Public distribution grid:** 0.25° global (GLOB025); companion 0.1° Europe distribution documented separately
- **Vertical levels:** 105 (model); the public distribution carries interpolated pressure and height-above-ground levels, not model levels — see *Level sets* below
- **Model top:** ~0.1 hPa (~65 km); lowest model level ~10 m above ground
- **Forecast length (live-verified):** **102 hours for all four cycles.** Verified on 2026-08-08 across 00/06/12/18 UTC: every cycle terminates at the `073H102H` package and the final instantaneous step is 102.
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (live-verified):** **Hourly through 48 h, 3-hourly from 51 h to 102 h.** Confirmed by decoding all four range files of package SP1 in the 2026-08-09 12 UTC cycle.
- **Time step:** 240 s

> **Correction to a long-standing figure.** Older documentation (and previous versions of this entry) gave the 12 UTC cycle a **114-hour** range. That is not what the distribution contains: all four cycles stop at 102 h. Whether the internal operational run extends beyond 102 h cannot be determined from the public packages (**TBD**).

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** 4D-Var on a 6-hour cycle, run at two inner-loop resolutions (Tl224 c1 and Tl499 c1). Since the cy48t1_op1 suite (operational mid-October 2024), ARPEGE uses **Hybrid 4D-Var within the OOPS framework**, with a hybrid flow-dependent background-error (B) matrix drawn from the ARPEGE Ensemble of Data Assimilations (ARPEGE-EDA, 50 members). As of the cy49t1 development the hybrid B is described as anisotropic flow-dependent, combining ~50% climatological wavelet covariances with ~50% localised ensemble covariances.
- **Observations:** Conventional plus all-sky microwave (MHS, MWHS-2, GMI, AMSR2), ATMS, GOES ABI, IASI, GNSS-RO (GRACE-C, Sentinel-6, Spire), AMVs, and Mode-S, among others.

> **The distribution contains no analysis fields as such.** data.gouv.fr advertises the dataset as "données d'analyse et de prévision", but **every message in every package carries `typeOfGeneratingProcess = 2` (forecast)**, including step 0. Users expecting an analysis-flagged step 0 will not find one; step 0 is encoded as a zero-hour forecast.

---

## What it provides

The 0.25° distribution is split into **nine packages** grouped by level type, each published in **four forecast-range files** (`000H024H`, `025H048H`, `049H072H`, `073H102H`) — **36 files per cycle**.

### Package inventory (live-verified, 2026-08-09 12 UTC, `000H024H` file)

| Package | Messages | Level type | Parameters |
|---|---:|---|---|
| **SP1** | 344 | surface, 2 m, 10 m | `prmsl`, `2t`, `2r`, `10u`, `10v`, `10si`, `10wdir`, total cloud cover, `tp`, `tsnowp`, `ssrd`, `max_i10fg`, `max_10efg`, `max_10nfg` |
| **SP2** | 459 | surface, 2 m | `2d`, `2sh`, `sp`, surface `t`, `lcc`, `mcc`, `hcc`, `blh`, `CAPE_INS`, `min_2t`, `max_2t`, `sshf`, `slhf`, `ssr`, `str`, `strd`, `iews`, `inss`, `h`, + 2 unresolved (see below) |
| **SP3** | 678 | surface, soil, satellite | `cp`, `lsp`, `crr`, `lsrr`, `csfwe`, `lsfwe`, `tsnowp`, `tirf`, `ssrc`, `strc`, `ceil`, `min_vis`, `ptype` (avg and max), `max_clwc`, 6 soil fields, synthetic satellite brightness temperature, + 4 unresolved |
| **HP1** | 4200 | height above ground (24) | `t`, `r`, `u`, `v`, `ws`, `wdir`, `pres` |
| **HP2** | 4200 | height above ground (24) | `dpt`, `q`, `tke`, `clwc`, `ciwc`, `cc`, `z` |
| **IP1** | 3450 | isobaric (34 / 24) | `t`, `r`, `u`, `v`, `z` |
| **IP2** | 3450 | isobaric (34 / 24) | `dpt`, `q`, `ws`, `wdir`, `w` |
| **IP3** | 2400 | isobaric (24) | `clwc`, `ciwc`, `cc`, `tke` |
| **IP4** | 2643 | isobaric (26 / 24 / 20) + potential vorticity | `pv`, `absv`, `vo`, `papt`; `u`, `v`, `z` on PV surfaces |

### Level sets (live-verified)
- **Isobaric (IP1, IP2 — full set, 34 levels):** 1, 2, 3, 5, 7, 10, 20, 30, 50, 70, 100, 125, 150, 175, 200, 225, 250, 275, 300, 350, 400, 450, 500, 550, 600, 650, 700, 750, 800, 850, 900, 925, 950, 1000 hPa
- **Isobaric (IP3, and hourly steps of IP1/IP2 — 24 levels):** 100 hPa through 1000 hPa (the ten levels above 100 hPa dropped)
- **Isobaric (IP4 `pv`, `absv`, `vo` — 26 levels):** as above plus 50 and 70 hPa
- **Isobaric (IP4 `papt` — 20 levels):** 200 hPa through 1000 hPa
- **Height above ground (HP1, HP2 — 24 levels):** 20, 35, 50, 75, 100, 150, 200, 250, 375, 500, 625, 750, 875, 1000, 1125, 1250, 1375, 1500, 1750, 2000, 2250, 2500, 2750, 3000 m
- **Potential vorticity (IP4):** three surfaces at **0.7, 1.5 and 2.0 PVU** (encoded as 700, 1500, 2000), carrying `u`, `v` and `z` — i.e. dynamic-tropopause fields

ARPEGE serves as a primary initial- and boundary-condition source for the AROME regional configurations: [AROME France](../../regional/france/arome-france.md), AROME France HD, and [AROME Outre-Mer](../../regional/france/arome-outre-mer.md) (which spans five overseas domains — Antilles, French Guiana, New Caledonia, French Polynesia, and Réunion–Mayotte).

---

## Data availability

- **Is the data free?** Yes
- **License:** **Licence Ouverte / Open Licence version 2.0** (Etalab v2.0; attribution required)
- **High-Value Dataset:** The data.gouv.fr dataset carries the **HVD badge** ("Données de forte valeur") under the EU Open Data Directive
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (edition 2, `grid_ccsds` packing — see *Notes*)
- **Official download location:**
  https://www.data.gouv.fr/datasets/paquets-arpege-resolution-0-25deg

### Primary access — data.gouv.fr object storage (no authentication)

The GRIB packages are served from OVH-hosted S3-compatible object storage. **No account, no API key, no registration.**

- **URL pattern (live-verified):**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/{run}/arpege/025/{package}/arpege__025__{package}__{range}__{run}.grib2
  ```
  where `{run}` is ISO-8601 with literal colons (e.g. `2026-08-09T12:00:00Z`), `{package}` is one of `SP1 SP2 SP3 HP1 HP2 IP1 IP2 IP3 IP4`, and `{range}` is one of `000H024H 025H048H 049H072H 073H102H`. Note the **double underscores** between filename fields.

- **Example:**
  ```
  https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/pnt/2026-08-09T12:00:00Z/arpege/025/SP1/arpege__025__SP1__000H024H__2026-08-09T12:00:00Z.grib2
  ```

- **The bucket is anonymously listable**, which makes programmatic discovery straightforward — no need to scrape the data.gouv.fr HTML pages:
  ```bash
  # available run directories
  curl -s "https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=pnt/&delimiter=/&max-keys=1000"

  # all ARPEGE files in one cycle (with sizes and Last-Modified)
  curl -s "https://meteofrance-pnt.s3.rbx.io.cloud.ovh.net/?list-type=2&prefix=pnt/2026-08-09T12:00:00Z/arpege/025/&max-keys=1000"
  ```
  Listings are paginated; follow `NextContinuationToken` (URL-encode it) for prefixes exceeding `max-keys`. Objects also serve `Content-Length` and `Last-Modified` on `HEAD`, and support HTTP Range requests.

- **Retention (live-verified):** **15 days rolling.** On 2026-08-09, `2026-07-26T12:00:00Z` was present with a complete 36-file set and `2026-07-25T12:00:00Z` was absent.

- **Publication latency (live-verified):** measured across six cycles (2026-08-08 00/06/12/18 UTC and 2026-08-09 00/06 UTC) from object `Last-Modified` timestamps:
  - First file of a cycle: **T+2 h 42 m to T+3 h 57 m**
  - Last file of a cycle: **T+3 h 57 m to T+4 h 59 m**
  - A consistent split is visible: the **00 and 12 UTC** cycles begin publishing around **T+3 h**, the **06 and 18 UTC** cycles around **T+3 h 55 m**.
  - Files do not appear in package or range order — later ranges of one package frequently land before earlier ranges of another. Poll for the specific keys you need rather than assuming sequential completion.

- **Volume (live-verified, 2026-08-09 12 UTC):** **~43 GB per cycle**, ~172 GB/day, implying roughly **2.6 TB** resident under the 15-day window for the 0.25° grid alone. Per package per cycle: HP1 9.7 GB, IP2 8.1 GB, IP4 7.2 GB, IP1 7.0 GB, HP2 6.1 GB, IP3 1.9 GB, SP3 1.2 GB, SP2 1.2 GB, SP1 0.7 GB. Individual files range from 90 MB (SP1 `049H072H`) to 3.6 GB (HP1 `000H024H`).

### Companion static files

Published on the same dataset page rather than in the object store:

- **`constant-glob025.grib2`** — orography (`h`, geometrical height above ground, m) and land–sea mask (`lsm`, 0–1) on the GLOB025 grid, 2 messages, ~1.2 MB. Last updated 2025-03-10.
- **`description-paquets-modele-arpege.pdf`** — official package/parameter description covering both the 0.25° and 0.1° grids, ~90 KB, **version dated 02/01/2024** (data.gouv.fr resource last updated 2024-02-29). See *Documentation defects* below before relying on it.

### Secondary access — Météo-France API portal

Météo-France also exposes ARPEGE through its developer portal, which requires free registration and an API key: https://portail-api.meteofrance.fr

This entry does not document the API routes. The object storage above carries the same GRIB2 packages with no authentication, no key rotation, and a longer retention window than the API's rolling archive, and is the recommended access path for anyone wanting the raw gridded data.

---

## Notes

### Encoding conventions (live-verified)
- **All messages:** GRIB edition 2, `tablesVersion = 15`, `localTablesVersion = 0`, centre `lfpw` (Météo-France Toulouse), `subCentre = 0`, `generatingProcessIdentifier = 211`, `typeOfGeneratingProcess = 2`.
- **Packing is `grid_ccsds`** (CCSDS/AEC compression) throughout. Readers built only for simple or JPEG2000 packing will fail; ecCodes and recent wgrib2 handle it.

### Level count varies with forecast step — per parameter, not per package
In **IP1** and **IP2**, the full 34-level pressure set appears **only at 3-hourly steps** (0, 3, 6, … 24). At the intervening hourly steps only **24 levels** (100–1000 hPa) are published — the ten levels above 100 hPa (1, 2, 3, 5, 7, 10, 20, 30, 50, 70 hPa) are dropped. The same truncation applies to `pv` in **IP4** (26 levels at 3-hourly steps, 24 at hourly steps).

**But it is not a package-wide rule.** `absv` and `vo` sit on the identical 26-level set inside the same IP4 file and carry **all** levels at **every** step, as does `papt` on its 20-level set and every parameter in IP3, HP1 and HP2. Code that infers a level set from one parameter and applies it across the file will silently drop or mis-align data.

Verified counts (per parameter, `000H024H` file): IP1/IP2 690 = 9 × 34 + 16 × 24; IP4 `pv` 618 = 9 × 26 + 16 × 24; IP4 `absv`/`vo` 650 = 26 × 25; IP4 `papt` 500 = 20 × 25; IP3 600 = 24 × 25; HP1/HP2 600 = 24 × 25.

### Range filenames overstate their contents
Because output drops to 3-hourly after 48 h, the last two range files begin later than their names suggest:
- `049H072H` — first instantaneous step is **51**, not 49
- `073H102H` — first instantaneous step is **75**, not 73

This is by design rather than a gap: Météo-France's package description states the cadence as hourly from 0 h to 48 h and 3-hourly **from 51 h** to 102 h. The range-group names simply do not reflect it.

### Three different accumulation conventions coexist
Within a single cycle, time-processed fields (`productDefinitionTemplateNumber = 8`) use three incompatible interval conventions:
- **Accumulated from run start** (`0-1`, `0-2`, … `0-24`): `tp`, `tsnowp`, `ssrd`, and the SP2 flux fields
- **Hourly intervals** (`0-1`, `1-2`, … `23-24`): `max_i10fg`, `max_10efg`, `max_10nfg`
- **3-hourly intervals** (`0-3`, `3-6`, … `21-24`): `min_2t`, `max_2t`

Differencing successive `tp` messages yields hourly totals; differencing successive gust maxima does not. Read `stepRange` per message rather than assuming a package-wide convention.

### Total cloud cover does not decode
Météo-France encodes total cloud cover with the **WMO-standard** triplet **discipline 0 / category 6 / number 1**. **ecCodes 2.48.0 does not resolve this**, returning `shortName = unknown`, `paramId = 0`, `units = unknown` — because ecCodes' `tcc` is bound to the ECMWF-*local* number 0/6/192. Values (0–100, mean ~66% in the verified file) confirm the field is total cloud cover in percent.

This is a single-parameter gap and easy to miss: low, medium and high cloud (0/6/3, 0/6/4, 0/6/5) resolve normally as `lcc`, `mcc`, `hcc` in the same cycle. Match total cloud cover on discipline/category/number, not on `shortName`.

### Local parameter numbers used with `localTablesVersion = 0`
Several fields use parameter numbers in the local range (≥ 192) while declaring `localTablesVersion = 0`, which formally leaves them unresolvable — no local table is declared for a reader to consult:

| Package | Encoding | Level type | Notes |
|---|---|---|---|
| SP3 | 2/3/192, 2/3/193, 2/3/254 | surface | Soil fields; ranges 0–4.6, 0–10, 0–10 |
| SP3 | 2/3/192, 2/3/193, 2/3/254 | depthBelowLand | Same three at **2.5 m** depth; ranges 0–150, 0–8000, 0–8000 |
| SP3 | 0/4/198 | surface | Short-wave radiation category, accumulated |
| SP3 | 0/19/201 | surface | Physical-atmospheric-properties category, time-minimum |

Identifying these requires Météo-France's local GRIB definitions, linked from the Confluence page below. **TBD** — the official package description does not help here: **SP3 does not appear in it at all**, for either grid (see *Documentation defects*).

Five further fields use **WMO-standard** numbers that ecCodes 2.48.0 nonetheless leaves as `unknown`. The first three are confirmed against Météo-France's package description; the last two are WMO Table 4.2 readings only, since SP3 is undocumented:

| Package | Encoding | Identification | Basis |
|---|---|---|---|
| SP1 | 0/6/1 | Total cloud cover | `NEBUL` in the package description; value range 0–100 % |
| SP2 | 0/1/64 | Total column water vapour | `COLONNE_VAPO` in the package description |
| SP2 | 0/1/6 | Evaporation flux | `FLEVAP` in the package description |
| SP3 | 0/6/11 | Cloud base | WMO Table 4.2 only — **TBD** |
| SP3 | 0/17/4 | Lightning flash density | WMO Table 4.2 only — **TBD** |

### Soil depth is reported inconsistently by ecCodes
The `depthBelowLand` soil fields encode `scaledValueOfFirstFixedSurface = 250`, `scaleFactorOfFirstFixedSurface = 2` — a depth of **2.5 m**. ecCodes' integer `level` key rounds this to **3**. Use `scaledValueOfFirstFixedSurface` and `scaleFactorOfFirstFixedSurface` rather than `level` for these fields.

### Synthetic satellite imagery is present but unidentified
SP3 carries **synthetic (simulated) satellite brightness temperatures** under `productDefinitionTemplateNumber = 32`, 48 messages per range file — two channels at 24 steps:
- **~10.8 µm** infrared window (`scaledValueOfCentralWaveNumber = 9259259`, scale factor 2)
- **~6.2 µm** water vapour (`scaledValueOfCentralWaveNumber = 16129032`, scale factor 2)

However `satelliteSeries`, `satelliteNumber` and `instrumentType` are **all zero**, so the emulated platform and sensor cannot be determined from the file. The two channels are distinguishable only by central wavenumber, and ecCodes reports no `typeOfLevel` for them.

### Other encoding oddities
- **`CAPE_INS` has no resolvable level type** — ecCodes reports `typeOfLevel = unknown` while the field decodes normally otherwise.
- **`iews` and `inss`** carry ecCodes names beginning "Instantaneous … turbulent surface stress" but are encoded with `stepType = accum`. They are **time-integrated** stresses (N m⁻² s), not instantaneous values; the shortName is misleading.
- **The constant-fields file is encoded unlike the data it accompanies**: `constant-glob025.grib2` uses `tablesVersion = 32` and `grid_second_order` packing, against `tablesVersion = 15` and `grid_ccsds` in the operational packages. It also carries `generatingProcessIdentifier = 255` and `typeOfGeneratingProcess = 0`.

### Documentation defects
`description-paquets-modele-arpege.pdf` (version 02/01/2024) is the only official parameter listing, and it has drifted from the live data. Verified discrepancies affecting this grid:

- **The stated GLOB025 bounds are wrong.** The PDF gives the grid as "GLOB025 0.25 dg ( 53N 38N 8W 12E )" — a box over France — for what is a global product. Live files are 90°N–90°S, 0–359.75°E.
- **The IP4 vorticity level counts are swapped between the two grids.** The PDF gives `TA`/`TB` (`absv`/`vo`) as 4 levels (300, 500, 700, 850 hPa) for GLOB025 and 26 levels (50–1000 hPa) for EURAT01. The live files are the exact reverse: **26 levels at 0.25°, 4 levels at 0.1°.**
- **The 0.7 PVU surface is undocumented.** The PDF lists `u`, `v`, `z` on two `ISO_TP` surfaces (2000 and 1500). The 0.25° files carry **three** — 700, 1500 and 2000 (0.7, 1.5 and 2.0 PVU).
- **SP3 is absent from the PDF entirely**, for both grids. All 26 of its parameter/level-type/step-type combinations — including the aviation diagnostics, soil fields and synthetic satellite channels — are undocumented.
- **The step-dependent level truncation is undocumented.** The PDF describes IP1 and IP2 as "34 niveaux (1 à 1000 hPa)" with no mention that the ten levels above 100 hPa appear only at 3-hourly steps.

The PDF is correct on the range groups, the cadence (hourly to 48 h, 3-hourly from 51 h), the packing format, and the HP and IP1/IP2/IP3 parameter lists.

### Access channels that no longer work
- **`object.data.gouv.fr/meteofrance-pnt/…` is dead.** The host still resolves and still recognises the `meteofrance-pnt` bucket name, but every key returns `NoSuchKey`. This URL pattern appears in a good deal of third-party documentation and tooling; use the OVH endpoint above instead.
- **The community AWS mirror is gone.** `mf-models-on-aws.s3.amazonaws.com` returns `NoSuchBucket` and `mf-models-on-aws.org` no longer resolves. The [AWS Open Data Registry listing](https://registry.opendata.aws/meteo-france-models/) is still live but points at a bucket that no longer exists.

### Model family and relationships
- **Stretched grid:** ARPEGE's native resolution varies smoothly across the globe. The published 0.25° grid interpolates that output onto a regular latitude–longitude mesh, so the distributed resolution does not reflect where the model is actually running finest — the high-resolution detail over France is present in the global file, just regridded.
- **Regional distribution:** [ARPEGE Europe](../../regional/france/arpege-europe.md) publishes the same forecast at 0.1° over Europe and North Africa. It uses **eight** packages (no SP3) in **nine** range files, and is the better choice for users whose domain of interest lies inside it.
- **Ensemble counterpart:** [PE-ARPEGE](../../../ensemble_models/global/fr/pe-arpege.md) provides probabilistic guidance from 35 members on the same stretched-grid core, with matching GLOB025 and EURAT01 distributions.
- **Nested regional models:** [AROME France](../../regional/france/arome-france.md) and [AROME Outre-Mer](../../regional/france/arome-outre-mer.md) take initial and/or boundary conditions from ARPEGE.

---

## Official documentation
- data.gouv.fr dataset — Paquets Arpège, résolution 0,25° : https://www.data.gouv.fr/datasets/paquets-arpege-resolution-0-25deg
- data.gouv.fr dataset — Paquets Arpège, résolution 0,1° : https://www.data.gouv.fr/datasets/paquets-arpege-resolution-0-1deg
- Météo-France open data portal: https://meteo.data.gouv.fr
- Modèles et données de prévision (Confluence): https://confluence-meteofrance.atlassian.net/wiki/spaces/OpenDataMeteoFrance/pages/621019138/
- Météo-France local GRIB definitions (for local parameter numbers): linked from the Confluence page above
- ARPEGE package description (PDF, v. 02/01/2024): `description-paquets-modele-arpege.pdf`, linked from the dataset pages above
- Météo-France API portal (registration required): https://portail-api.meteofrance.fr
- Etalab Open Licence v2.0: https://www.etalab.gouv.fr/licence-ouverte-open-licence

---

*Live verification performed 2026-08-09 against the 2026-08-09 12:00 UTC cycle (all nine 0.25° packages, `000H024H` files, 21,824 GRIB2 messages decoded with ecCodes 2.48.0), with supporting checks on the 2026-08-08 00/06/12/18 UTC and 2026-08-09 00/06 UTC cycles for forecast length, publication timing and retention.*
