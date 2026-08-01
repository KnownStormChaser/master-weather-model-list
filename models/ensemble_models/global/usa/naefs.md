# NAEFS (North American Ensemble Forecast System)

## What this model is
The North American Ensemble Forecast System (NAEFS) is a **global, multi-center bias-corrected ensemble** produced operationally as a joint project between three national meteorological services. It combines the U.S. NWS's [GEFS](./gefs.md) and Environment and Climate Change Canada's [Canadian GEPS](../canada/geps.md) into a bias-corrected "grand ensemble" providing probabilistic medium-range guidance that is seamless across the Canada–U.S.–Mexico borders.

NAEFS is not a forecast model in the usual sense — it runs no atmospheric integrations. It ingests ensemble members produced by the contributing centers, applies statistical bias correction against analyses, and derives combined probabilistic products. The operational rationale is that combining two independent, well-calibrated single-center ensembles produces probabilistic forecasts skill-superior to either parent alone, particularly in the medium range.

NAEFS was officially launched in November 2004 and is the longest-running operational multi-center ensemble in international NWP. The current operational version is **v7.0**, implemented 5 December 2023, which expanded the GEFS contribution from a 21-member subset to all 31 GEFS bias-corrected members.

> **What is actually distributed is narrower than the name suggests.** The `naefs.YYYYMMDD/` tree on NOMADS contains **no ensemble members** — only six derived statistics on two output grids, at **two cycles per day**, not four. The 52 bias-corrected members exist, but under the sibling `gefs.*` and `cmce.*` trees, and a user wanting member-level data must assemble them from those. See *What it provides* and *Reconstructing the 52-member ensemble*.

**Verification basis:** all structural, grid, step, and encoding claims below were verified live on **2026-08-01** against run `naefs.20260731` (00Z and 12Z), with cross-checks against `naefs.20260730`. GRIB2 headers were decoded with ecCodes 2.48.0. Claims not so grounded are labelled.

---

## Who runs it
- **Joint project between three organizations:**
  - **U.S. National Weather Service (NWS)** — operational production and primary data distribution via NCEP Central Operations (NCO)
  - **Meteorological Service of Canada (MSC)** / Environment and Climate Change Canada — supplies the bias-corrected Canadian GEPS contribution and produces derived chart and station products
  - **National Meteorological Service of Mexico (NMSM / SMN)** — joint partner since the 2004 launch
- **Country / region:** Multinational (Canada, United States, Mexico)
- **Operational distribution lead:** NCEP / NOAA (via NOMADS)
- **Operational since:** November 2004

---

## What area it covers
- **Coverage:** Global (0.5° streams); regional CONUS and Alaska (NDGD downscaled streams)
- **Primary use regions:** CONUS, Alaska, Canada, Mexico

Although NAEFS is technically global — both parents are global ensembles — its design intent and downscaled product suite focus on North America.

### Grids (verified)

| Stream | Grid type | Dimensions | Spacing | Projection parameters |
|---|---|---|---|---|
| `pgrb2ap5_bc`, `pgrb2ap5_an` | `regular_ll` | 720 × 361 (259,920 pts) | 0.5° × 0.5° | first point 90°N / 0°E, last −90° / 359.5°, scanningMode 0 |
| `ndgd_gb2` · `conus_ext_2p5` | `lambert` | 2145 × 1597 (3,425,565 pts) | 2539.703 m | LaD 25°N, LoV 265°E, Latin1 = Latin2 = 25°, first point 20.1919°N / 238.4460°E, scanningMode 64 |
| `ndgd_gb2` · `alaska_3p0` | `polar_stereographic` | 1649 × 1105 (1,822,145 pts) | 2976.563 m | LaD 60°N, first point 40.5301°N / 181.4290°E, scanningMode 64 |

`shapeOfTheEarth = 6` (spherical, 6,371,229 m) on all three grids.

> Note the CONUS grid is the **extended** NDGD 2.5 km domain (Nj = 1597), not the standard NDFD CONUS grid. Only these two domains are produced — Hawaii, Puerto Rico, Guam, and the standard `conus_2p5` grid all return HTTP 404.

---

## Basic details
- **Model type:** Multi-center bias-corrected global ensemble (statistical post-processing, not a forecast model)
- **Contributing systems:**
  - **NCEP [GEFS](./gefs.md):** 31 bias-corrected members (`gec00` + `gep01`–`gep30`), verified by file enumeration
  - **MSC [Canadian GEPS](../canada/geps.md):** 21 bias-corrected members (`perturbationNumber` 0–20), verified by file enumeration
- **Total members:** 52 (31 + 21) — **but see the encoding discrepancy below: the distributed GRIB2 declares 50.**
- **Horizontal resolution:** 0.5° global; 2.54 km CONUS extended and 2.98 km Alaska on the NDGD grids
- **Forecast length:** 384 hours (16 days)
- **Update frequency / cycles (verified):** **2× daily — 00 and 12 UTC.** The 06 and 18 UTC cycle directories exist and contain only the `dvrtma` analysis files; no NAEFS forecast product is produced at those cycles.
- **Temporal output resolution (verified):** 96 steps — **3-hourly f003–f192, then 6-hourly f198–f384. There is no f000 step** in any NAEFS-derived product.
- **Publication latency (observed, 12Z run of 2026-07-31):** `pgrb2ap5_*` complete by 18:40 UTC (T+6h40m); `ndgd_gb2` complete by 18:52 UTC (T+6h52m)
- **Retention on NOMADS (observed):** 2 days

> ⚠️ **The "4× daily" figure is wrong for the gridded products, and it comes from NOAA's own product description.** The NOMADS NAEFS description states the system "is run four times a day: 00Z, 06Z, 12Z, and 18Z. Each run produces forecast files every 3 hours from initial time out to 192 hours, and then every 6 hours out to 384 hours." Live enumeration of both 2026-07-30 and 2026-07-31 contradicts this on two counts: the 06 and 18 cycles carry no forecast files at all, and the 3-hourly sequence begins at f003, not at initial time. Whether NAEFS is *computed* four times daily and only published twice is unconfirmed (**TBD** — a question for NCO).

---

## Methodology

> *The template's "Data assimilation" and "Initial and boundary conditions" sections do not apply — NAEFS performs no assimilation and runs no integration. They are replaced by this section.*

### Bias correction
Each contributing ensemble's members are statistically bias-corrected against analyses before combination, computed and applied independently per center. The bias-corrected member sets are published in their own directories (`gefs.*/pgrb2ap5_bc/`, `cmce.*/pgrb2ap5_bc/`) and carry `typeOfGeneratingProcess = 11` (bias-corrected ensemble forecast) in their GRIB2 headers — as do all NAEFS-derived products.

### Downscaling reference
Each cycle's `ndgd_gb2/` directory carries `dvrtma.tCCz.{alaska_3p0,conus_ext_2p5}.grib2` — a downscaled RTMA analysis on the same two NDGD grids, present at **all four cycles** including 06 and 18. Six fields: `sp`, `2t`, `2d`, `2r`, `10u`, `10v`.

> The `dvrtma` files are encoded as **PDT 4.1 (individual ensemble forecast)** with `perturbationNumber = 0`, `typeOfProcessedData = cf`, `stepRange = 0`, and `numberOfForecastsInEnsemble = 30` — an analysis wearing control-member clothing. `typeOfGeneratingProcess = 7` and `generatingProcessIdentifier = 107`, both differing from the forecast products' 11 / 114. Treat the ensemble keys on these files as meaningless.

### Combination
After bias correction the two member sets are pooled and the derived statistics (mean, spread, mode, 10th/50th/90th percentiles, anomalies, EFI) are computed from the pooled membership.

### Why combine two ensembles?
The parents use different model cores (GEFS on FV3; Canadian GEPS on GEM with a Yin–Yang grid), different assimilation systems, and different perturbation strategies. Their errors are therefore partially independent, and the combination samples a broader range of model uncertainty than either alone.

---

## What it provides

### 1. Global 0.5° derived statistics — `naefs.YYYYMMDD/CC/pgrb2ap5_bc/`

Six files per step, 96 steps, no `.idx` sidecars:

```
naefs_{ge10pt,ge50pt,ge90pt,geavg,gemode,gespr}.tCCz.pgrb2a.0p50_bcfFFF
```

Each carries **49 GRIB2 messages**: `gh`, `t`, `u`, `v` on 10 pressure levels (1000, 925, 850, 700, 500, 250, 200, 100, 50, 10 hPa), plus `sp`, `prmsl`, `2t`, `2d`, `2r`, `10u`, `10v`, `10si`, and `w` at 850 hPa. Packing is `grid_complex_spatial_differencing`, `tablesVersion = 2`, `centre = kwbc`, `subCentre = 2`.

> **There is no precipitation field in this stream.** Bias-corrected precipitation lives under the sibling `gefs.YYYYMMDD/CC/prcp_bc_gb2/` and `ndgd_prcp_gb2/` directories, not under `naefs.*`.

### 2. Global 0.5° anomaly and index products — `naefs.YYYYMMDD/CC/pgrb2ap5_an/`

Only three file families, 96 steps each:

| File | Messages | Fields |
|---|---|---|
| `naefs_geavg.tCCz.pgrb2a.0p50_anfFFF` | 18 | `gh` @1000/700/500/250, `u`/`v`/`t` @850/500/250, `prmsl`, `10u`, `10v`, `2t`, `10si` |
| `naefs_geavg.tCCz.pgrb2a.0p50_anvfFFF` | 18 | same field set, different `derivedForecast` code |
| `naefs_geefi.tCCz.pgrb2a.0p50_bcfFFF` | 3 | `prmsl`, `2t`, `10si` (Extreme Forecast Index) |

> **Filing quirk:** the EFI file carries a `_bcf` suffix but sits in the `_an` directory. Anyone globbing `*_bcf*` across the cycle will pick it up alongside the `pgrb2ap5_bc` files despite it being a different product class.

> The precise distinction between `_anf` and `_anvf` is not documented in any source located, and the two are distinguishable only by their local-use `derivedForecast` codes (197 vs 198). Plausibly anomaly vs. normalized/variance-scaled anomaly, but this is **TBD** pending confirmation from NCO.

### 3. Downscaled NDGD surface products — `naefs.YYYYMMDD/CC/ndgd_gb2/`

Same six statistics × two domains × 96 steps (1,152 files), plus the two `dvrtma` analyses:

```
naefs.tCCz.{ge10pt,ge50pt,ge90pt,geavg,gemode,gespr}.fFFF.{alaska_3p0,conus_ext_2p5}.grib2
```

Eight surface fields each: `sp`, `2t`, `2d`, `2r`, `10u`, `10v`, `10si`, `10wdir`. No pressure levels, no precipitation, no `.idx` sidecars.

### Ensemble statistic encoding (verified)

All derived products use **PDT 4.2** (derived forecast from all ensemble members) and distinguish the statistic by the `derivedForecast` key. Most of the values used are **local-use codes outside GRIB2 Code Table 4.7**:

| `derivedForecast` | Product | File prefix | Standard? |
|---|---|---|---|
| 0 | Unweighted ensemble mean | `geavg` (`_bcf`) | Table 4.7 |
| 4 | Ensemble spread | `gespr` | Table 4.7 |
| 192 | Mode | `gemode` | local use |
| 193 | 10th percentile | `ge10pt` | local use |
| 194 | 50th percentile | `ge50pt` | local use |
| 195 | 90th percentile | `ge90pt` | local use |
| 197 | Anomaly | `geavg` (`_anf`) | local use |
| 198 | Anomaly variant | `geavg` (`_anvf`) | local use |
| 199 | Extreme Forecast Index | `geefi` | local use |

Code 196 was not observed in any file and its meaning is unknown (**TBD**).

> ⚠️ **Percentiles are not encoded as percentiles.** NAEFS does not use GRIB2 product definition template 4.6 (percentile forecast), so the `percentileValue` key is absent from every message. A decoder reading the GRIB2 alone **cannot distinguish the 10th, 50th, and 90th percentile files** — they are identical in every standard header field, and only the filename and the non-standard `derivedForecast` value separate them. Any pipeline that indexes on standard GRIB keys and discards filenames will silently collapse the three percentile products into one. This is the single most consequential encoding hazard in the dataset.

---

## Reconstructing the 52-member ensemble

The member-level data lives in sibling directories under the same `naefs/prod` root:

| Tree | Contents | Naming | Steps |
|---|---|---|---|
| `gefs.YYYYMMDD/CC/pgrb2ap5_bc/` | 31 bias-corrected GEFS members | `ge{c00,p01…p30}.tCCz.pgrb2a.0p50_bcfFFF` (+ `.idx`) | 97: **f000**–f192 3-hourly, f198–f384 6-hourly |
| `cmce.YYYYMMDD/CC/pgrb2ap5_bc/` | 21 bias-corrected Canadian members | `YYYYMMDDCC_CMC_naefsbc_hr_latlon0p5x0p5_P{step:03d}_{member:03d}.grib2` | 97, same cadence |
| `cmce.YYYYMMDD/CC/pgrb2ap5/` | Raw Canadian ensemble | `cmc_ge{c00,p01…,avg}.tCCz.pgrb2a.0p50.fFFF` (+ `.idx`) | — |
| `fens.YYYYMMDD/CC/pgrb2ap5/`, `pgrb2a/` | FNMOC ensemble (raw only, no `_bc`) | — | — |

Three practical hazards:

1. **The two member sets use unrelated naming schemes.** GEFS follows the NCEP `geXNN.tCCz.…` convention; the Canadian files follow ECCC's `YYYYMMDDCC_CMC_naefsbc_…_P{step}_{member}` convention, with step and member both zero-padded to three digits in a single filename. Sorting lexically will not interleave them.

2. **The member sets carry different fields.** GEFS bias-corrected members hold 49 messages; Canadian members hold **45**. The four GEFS-only fields are **`10si`, `2d`, `2r`, and `w` @850 hPa**. Any statistic computed for these four fields cannot have drawn on the Canadian membership.

3. **Member files include f000; the derived products do not.** Reconstruction and comparison against the published statistics must drop step 0.

Per-member GRIB2 headers differ by center: Canadian members are `centre = cwao`, `generatingProcessIdentifier = 70`, `tablesVersion = 4`, `packingType = grid_jpeg`, `numberOfForecastsInEnsemble = 21`. GEFS members are `centre = kwbc`, `generatingProcessIdentifier = 107`, `tablesVersion = 2`, `grid_complex_spatial_differencing`, `numberOfForecastsInEnsemble = 30`. Both use PDT 4.1 with `typeOfGeneratingProcess = 11`.

### ⚠️ Ensemble size is misdeclared in the derived products

Every NAEFS-derived message declares **`numberOfForecastsInEnsemble = 50`**, not the 52 members that demonstrably exist as files (31 + 21). The value plausibly reflects perturbed members only (30 + 20), but this is not stated anywhere and the controls are certainly present in the member directories (**TBD**).

The one exception is more revealing: within `naefs_geavg.tCCz.pgrb2a.0p50_bcfFFF`, **48 of the 49 messages declare 50 while `w` @850 hPa alone declares 30** — matching the GEFS perturbed count and consistent with vertical velocity being unavailable from the Canadian side. But `10si`, `2d`, and `2r` are equally absent from the Canadian members and still declare 50. The header is therefore internally inconsistent: it is honest about the member count for one GEFS-only field and wrong about it for three others. **Do not use `numberOfForecastsInEnsemble` from this dataset to determine how many members contributed to any given field.**

---

## Relationship to other models

### Parent ensembles
- **U.S. contribution:** [GEFS](./gefs.md) (NCEP) — 31 members
- **Canadian contribution:** [Canadian GEPS](../canada/geps.md) (MSC / ECCC) — 21 members

### Related multi-center systems
- **[557th WW GEPS](./557wg-geps.md)** (U.S. Air Force Global Ensemble Prediction **Suite**): a separate multi-center statistical ensemble adding a third member set from FNMOC's NAVGEM-based ensemble. Conceptually extends NAEFS's approach; both run operationally side by side. Note the acronym collision with the Canadian GEPS — see the disambiguation note in either entry.
- **[FNMOC Ensemble](./fnmoc-ensemble.md):** distributed under the same `naefs/prod` root as `fens.YYYYMMDD/`, but **raw only — there is no `pgrb2ap5_bc/` directory in the `fens.*` tree**, confirming the Navy ensemble is not bias-corrected into NAEFS. Its presence here is co-location, not participation.
- **NUOPC ensemble framework:** the broader multi-center framework under which 557th WW GEPS operates. NAEFS predates it and operates independently.

### Distinction from contributing parents
NAEFS does not replace its parents. GEFS and Canadian GEPS continue to run independently and are distributed separately through their national channels. NAEFS is the bias-corrected combined layer on top.

---

## Data availability

- **Is the data free?** Yes (no registration, no API key)
- **License:**
  - **NOMADS gridded products:** Public domain (U.S. government work; CC0-equivalent), distributed under NOAA Open Data Dissemination terms. NOAA requests attribution, prohibits stating or implying endorsement, and requires that modified data not be presented as unaltered NOAA data.
  - **MSC Datamart XML:** Environment and Climate Change Canada Data Servers End-use Licence, version 2.1 (September 2022) — worldwide, royalty-free, perpetual, non-exclusive, commercial use permitted, attribution required.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (gridded); bzip2-compressed XML (MSC station products)

### Primary channel — NCEP NOMADS

```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/naefs/prod/
  naefs.YYYYMMDD/{00,12}/{ndgd_gb2,pgrb2ap5_an,pgrb2ap5_bc}/
  naefs.YYYYMMDD/{06,18}/ndgd_gb2/            <- dvrtma analyses only
  gefs.YYYYMMDD/CC/{pgrb2ap5_bc,pgrb2ap5_an,prcp_bc_gb2,prcp_gb2,ndgd_gb2,ndgd_prcp_gb2}/
  cmce.YYYYMMDD/CC/{pgrb2ap5,pgrb2ap5_bc}/
  fens.YYYYMMDD/CC/{pgrb2a,pgrb2ap5}/
```

Retention observed 2026-08-01: `naefs.*` and `cmce.*`/`fens.*` two days each, `gefs.*` two days. There is **no cloud mirror** — NAEFS is not on NODD's AWS, Azure, or GCS buckets, so the two-day NOMADS window is the entire public archive.

### Secondary channels

- **GRIB filter (`gensbc`):** https://nomads.ncep.noaa.gov/gribfilter.php?ds=gensbc — **live and working**, but it exposes only `gefs.YYYYMMDD` subdirectories, i.e. the bias-corrected GEFS members and GEFS-only statistics. It does **not** serve the combined `naefs.*` products. Its page title reads "GFS Ensemble Bias-Corrected Forecasts (1 degree grid)" while the underlying files are 0.5° — the title appears stale (**TBD**).
- **MSC Datamart XML:** see below.

### Retired and non-existent endpoints

- **OPeNDAP `https://nomads.ncep.noaa.gov/dods/gens_bc` — retired.** Verified live 2026-08-01: the server returns a notice stating the OPeNDAP format has been retired, citing Service Change Notice 25-81. Previous versions of this entry listed it as an access route.
- **`https://www.ftp.ncep.noaa.gov/data/nccf/com/naefs/prod/` and `ftp://ftp.ncep.noaa.gov/...` — retired** per SCN 25-82 (effective 23 February 2026). Both returned HTTP 503 from the test host, which is consistent with retirement but not by itself conclusive, since the failure mode was a proxy-level connection timeout rather than a server response (**verification incomplete**).
- **Parallel feed `https://nomads.ncep.noaa.gov/pub/data/nccf/com/naefs/para/` — does not exist** (HTTP 403, the response NOMADS gives for absent directories). Previously listed here; removed.

### MSC Datamart (Canada) — station XML, **out of catalog scope**

```
https://dd.weather.gc.ca/YYYYMMDD/WXO-DD/ensemble/naefs/xml/YYYYMMDD/{00,12}/<VARIABLE>/raw/
  YYYYMMDDCC_GEPS-NAEFS-RAW_<STATION>_<PROV>_<COUNTRY>_<VARIABLE>_000-384.xml.bz2
```

This is ECCC's **only** NAEFS data distribution — the readme lists no GRIB channel, and the four "products" it links are rendered web charts. **The XML is per-station point data, not gridded**, and therefore falls outside this repository's gridded-data scope alongside the CWA JSON-wrapped products. It is documented here rather than given a distribution role; the repository's storm-surge station-data exception does not extend to it. Characteristics verified 2026-08-01 against 2026-07-31:

- **540 stations** per variable per cycle, covering Canada, the U.S., Mexico, and selected South American and Pacific sites
- **10 variables:** `APCP-SFC`, `HGT-500HPA`, `LAYER-1000-500HPA`, `MSLP`, `RELH-SFC`, `TCDC`, `TMP-SFC`, `WDIR-SFC`, `WIND-200HPA`, `WIND-SFC`
- **2 cycles** (00, 12 UTC) — matching the NOMADS gridded cadence
- **64 forecast steps, 6-hourly f006–f384**, no f000
- 10,800 files per day; roughly 5–6 KB compressed per file
- Archive depth ~31 days on the dated `dd.weather.gc.ca/YYYYMMDD/` paths
- Only a `raw/` subdirectory exists under each variable — no bias-corrected XML is published

> ⚠️ **The XML carries a 21-member NCEP subset, not 31.** The `<model_description>` block enumerates **43 streams: 21 CMC GEM members (1 control + 20), 21 NCEP members (1 control + 20), and 1 CMC deterministic run**, all tagged `data_type="RAW"`. This is the pre-v7.0 membership. Either the ECCC XML product was never updated for NAEFS v7.0, or it is intentionally a different (raw, symmetric) product that merely shares the NAEFS name. **TBD** — worth an outreach question to ECCC.

> ⚠️ **The `today/` alias is unreliable for this product.** `https://dd.weather.gc.ca/today/ensemble/naefs/` returned HTTP 404 at 04:28 UTC on 2026-08-01 while `geps/`, `reps/`, and `cansips/` were all present. The 2026-07-31 NAEFS subtree first appeared at 06:43 UTC, so the alias lacks a `naefs/` directory entirely for roughly the first seven hours of each UTC day. Use the dated `dd.weather.gc.ca/YYYYMMDD/WXO-DD/…` path.

### Access notes

- **NOMADS directory listings can fail with HTTP 502 while file requests succeed.** Appending a sort query string (`?C=N&O=A`) reliably returned listings from the same host that 502'd on the bare URL. Absent directories return **403**, not 404; missing files inside an existing directory return 404. This distinction is a usable existence test.
- **NOMADS rate-limits concurrent requests aggressively, and the failure mode is a spurious 404.** A 12-way threaded step enumeration reported ragged gaps in the `ndgd_gb2` series (54 of 96 steps present for CONUS, 70 for Alaska); a serial re-probe with retries returned the full 96 for both. Any enumeration of this tree must be serialized or retried, or it will record archive gaps that do not exist.

---

## Version history

### 5 December 2023 — NAEFS v7.0 (current)
- Expanded the GEFS contribution from 21 to all 31 bias-corrected members (v6.1 used a 21-member subset)
- Probabilistic forecast skill extended by approximately 3–4 forecast hours
- Overall improvement in calibrated precipitation forecasts
- New bias-corrected files for GEFS members 21–30 under `gefs.YYYYMMDD/CC/pgrb2ap5_bc/` and `pgrb2ap5_an/`
- GEFS members 21–30 added to the bias-corrected precipitation files (`prcp_bc_gb2`) and the NDGD 2.5 km CONUS precipitation files (`ndgd_prcp_gb2`)
- The `gensbc` GRIB filter and the (since-retired) `gens_bc` OPeNDAP product both expanded to include the additional members

### Earlier versions
- **v6.1 and earlier:** 21-member GEFS subset alongside the Canadian contribution
- **November 2004:** NAEFS launched as a joint MSC / NWS / NMSM project

NAEFS upgrades generally track upgrades to the parent ensembles; each major GEFS or Canadian GEPS revision typically prompts a NAEFS revision to fold the parent improvements into the bias correction.

---

## Notes

- **NAEFS is a post-processing system, not a forecast model.** Compute cost is minimal relative to the parents; the operational expense is dominated by GEFS and the Canadian GEPS.

- **Why this is filed under `usa/` despite being trinational:** NCEP NCO operates the bias correction and combination, and NOMADS is the only channel carrying the gridded product. ECCC and SMN are full project partners with genuinely shared governance and costs, but ECCC's own distribution is station XML only (see above), and Mexico distributes nothing.

- **The 2004 launch is a notable milestone in operational NWP.** NAEFS was the first major operational multi-center ensemble combining independently developed national systems, predating both the NUOPC framework and 557th WW GEPS by roughly a decade.

- **The 31 + 21 = 52 count is asymmetric** because the parents differ in size. v7.0's expansion to all 31 GEFS members reflects GEFS's current configuration; the Canadian contribution remains at 21 (control + 20), matching the operational Canadian GEPS.

- **`naefs.*` carries no `.idx` sidecars, while `gefs.*` and `cmce.*` do.** Byte-range subsetting of the combined statistics therefore requires downloading whole files or building indexes locally — a meaningful cost given the 8–24 MB per-file sizes and 1,440 files per cycle.

- **Mexico's role (NMSM / SMN):** a full project partner contributing to research, development, and operational direction, but it does not operate a contributing global ensemble and does not redistribute NAEFS data.

- **As with all ensemble systems**, NAEFS output should be interpreted probabilistically. Its value is in calibrated probabilities and spread, not in any single-member view — which is just as well, given that no members are distributed in the `naefs.*` tree.

### Open questions for NCO / ECCC
1. Is NAEFS computed at 06 and 18 UTC and merely not published, or genuinely run twice daily? The NOMADS description says four.
2. What distinguishes `_anf` from `_anvf` (`derivedForecast` 197 vs 198)?
3. What does `derivedForecast = 196` denote, and is it used in any product?
4. Why does `numberOfForecastsInEnsemble` report 50 when 52 member files exist, and why does `w` @850 alone report 30?
5. Is there any reason percentiles are not encoded with PDT 4.6, which would make them self-describing?
6. (ECCC) Does the NAEFS station XML still use a 21-member NCEP subset by design, or has it not been updated for v7.0?

---

## Official documentation
- NCEP/EMC NAEFS v7.0 page: https://www.emc.ncep.noaa.gov/users/meg/naefsv7/
- NWS Service Change Notice 23-104 (NAEFS v7.0): https://www.weather.gov/media/notification/pdf_2023_24/scn23-104_naefs_v7.0.pdf
- ECCC NAEFS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_naefs/readme_naefs_en/
- ECCC NAEFS Datamart XML page: https://eccc-msc.github.io/open-data/msc-data/nwp_naefs/readme_naefs-datamartxml_en/
- ECCC Data Servers End-use Licence: https://eccc-msc.github.io/open-data/licence/readme_en/
- NOMADS NAEFS product directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/naefs/prod/
- COMET MetEd Introduction to NAEFS (account required): https://learn.meted.ucar.edu/#/online-courses/d336c069-8e91-425a-9d10-5b8a89009609
