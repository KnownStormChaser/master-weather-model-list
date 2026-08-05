# IFS (Integrated Forecasting System)

## What this model is
The Integrated Forecasting System (IFS) is ECMWF's flagship global numerical weather prediction system and the deterministic backbone of European medium-range forecasting.

IFS uses a hybrid spectral / reduced Gaussian-grid dynamical core and a 4D-Var data assimilation system, and is widely regarded as one of the most skillful operational global NWP systems in the world. It provides the analyses used to initialize ECMWF's other forecast products including IFS ENS (the physics ensemble), AIFS Single (the deterministic AI model), and AIFS ENS (the ensemble AI model).

ECMWF distributes IFS output through two channels: a free Open Data subset (0.25°, CC-BY-4.0) and the full Real-time Catalogue (native 9 km, also under CC-BY-4.0 since 1 October 2025 but subject to service charges for high-volume delivery). This entry covers the IFS as a whole, with the Open Data details called out explicitly.

> **Note on naming after Cycle 50r1.** Since 12 May 2026 the separate HRES forecast no longer exists as a distinct product. ECMWF now calls this forecast the **IFS Medium-range Control Forecast**, and it doubles as the [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md) control member. In Open Data it is still distributed under `stream=oper, type=fc`. This entry documents that forecast.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts
- **Country / region:** International (European consortium of 23 Member States and 12 Co-operating States)

---

## What area it covers
- **Coverage:** Global
- **Open Data grid:** 1440 × 721 regular latitude–longitude, 0.25° × 0.25°, 1,038,240 points per field
- **Grid origin (verified):** first grid point 90°N / 180°, last grid point 90°S / 179.75°, scanning mode 0 (west→east, north→south). **The longitude axis begins at the dateline, not at 0° or −180°.** Decoders that normalize to −180…179.75 (as ecCodes does) will report the array as starting at −180°, but the raw GRIB header carries `longitudeOfFirstGridPointInDegrees = 180.0`. Code that assumes a Greenwich-first or −180-first raw layout will be shifted by half a globe.

---

## Basic details
- **Model type:** Global deterministic NWP
- **Model system / core:** IFS (hybrid spectral / reduced Gaussian-grid)
- **Dynamical formulation:** Hydrostatic, semi-Lagrangian, semi-implicit time integration
- **Convection-allowing:** No (deep convection is parameterized at 9 km native resolution)
- **Native horizontal resolution:** ~9 km (Tco1279 octahedral reduced Gaussian grid)
- **Open Data resolution:** 0.25° (~28 km) regular latitude–longitude
- **Vertical levels:** 137 (model); **14 pressure levels in Open Data** — 10, 50, 100, 150, 200, 250, 300, 400, 500, 600, 700, 850, 925, 1000 hPa
- **Soil levels in Open Data:** 4 (`soilLayer` 1–4)
- **Model top:** 0.01 hPa (~80 km)
- **Forecast length (Open Data, verified):**
  - **360 h (15 days)** for 00 and 12 UTC cycles
  - **144 h (6 days)** for 06 and 18 UTC cycles
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (Open Data, verified):**
  - 00 / 12 UTC: 3-hourly to +144 h, then 6-hourly +150 h to +360 h — **85 steps**
  - 06 / 18 UTC: 3-hourly to +144 h — **49 steps**
- **Observed publication latency:** the complete run is published as a single batch (all steps share one `Last-Modified` timestamp), at approximately **T+7h34m** for 00/12 UTC and **T+6h27m** for 06 UTC. Measured 2026-07-30 across the 00z, 06z and 12z cycles, and confirmed identical for the 2026-07-29 00z cycle.

> ⚠️ **Documentation discrepancy — forecast length and step ranges.** ECMWF's Open Data user documentation states that `stream=oper` runs to **240 h** at 00/12 UTC and to **90 h** at 06/18 UTC. Directory enumeration on 2026-07-30 shows **360 h** and **144 h** respectively, for both `oper` and `wave`. The published values are almost certainly the stale pre-50r1 figures: now that the deterministic forecast *is* the ENS control, it necessarily runs to the ensemble's 360 h horizon. The live archive is treated as authoritative here; the documentation has not been updated. Raised with ECMWF — see *Open questions* below.

---

## Data assimilation
- **Method:** 12-hour incremental 4D-Var with weak-constraint formulation
- **Background error covariances:** Flow-dependent, derived from an Ensemble of Data Assimilations (EDA)
- **Assimilated observations:** Conventional and satellite observations including radiances, GPS-RO, atmospheric motion vectors, scatterometer winds, and a wide range of additional sources

---

## What it provides
Deterministic global forecasts of:
- Temperature, wind, humidity, geopotential, and pressure on standard pressure levels
- Surface and near-surface fields (2 m temperature, 10 m wind, mean sea level pressure, etc.)
- Total precipitation, precipitation type, and convective indices
- Cloud and hydrometeor fields
- Heat and cold indices, mean radiant temperature, and globe temperature (added in Cycle 49r1)

The IFS analysis is also used to initialize ECMWF's [AIFS Single](./aifs-single.md), [AIFS ENS](../../../ensemble_models/global/eu/aifs-ens.md), [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md), and several downstream limited-area systems run by ECMWF Member States (including AROME, ALADIN, and HARMONIE-AROME family configurations).

### Open Data parameter inventory (verified)
Decoded from `20260730000000-0h-oper-fc.grib2` (187 GRIB messages, 50 distinct parameters at step 0; 184 messages / 48 parameters at all other steps).

**Pressure levels** (10 parameters × 14 levels): `t`, `u`, `v`, `w`, `q`, `r`, `z`, `gh`, `d`, `vo`

**Height above ground:** `2t`, `2d`, `10u`, `10v`, `100u`, `100v`, `10fg` / `10fg3`, `mn2t3` / `mn2t6`, `mx2t3` / `mx2t6`

**Surface:** `sp`, `skt`, `lsm`, `z`, `sdor`, `slor`, `tp`, `tprate`, `sf`, `ptype`, `ro`, `sd`, `rsn`, `asn`, `ssr`, `ssrd`, `str`, `strd`, `ewss`, `nsss`, `sithick`, `zos`, `sve`, `svn`

**Other level types:** `msl` (mean sea level), `tcc`, `tcw`, `tcwv` (entire atmosphere), `ttr` (nominal top), `mucape` (most-unstable parcel), `sot` and `vsw` (soil layers 1–4)

Note the presence of **ocean and sea-ice fields in the atmospheric stream** — `zos` (sea surface height), `sithick` (sea ice thickness), `sve` / `svn` (surface sea water velocity components). These come from the coupled NEMO4-SI3 component and are delivered alongside the atmospheric fields rather than in a separate ocean stream.

#### Step-dependent parameter availability (verified)
The parameter set is **not constant across steps**. Three separate switchovers occur:

| Parameter | Steps 0–90 | Steps 93–144 | Steps 150–360 |
|---|---|---|---|
| Wind gust | `10fg` | `10fg3` | `10fg` |
| Min 2 m temperature | `mn2t3` | `mn2t3` | `mn2t6` |
| Max 2 m temperature | `mx2t3` | `mx2t3` | `mx2t6` |

Additionally, three **static fields appear only at step 0** and are not repeated: `sdor` (standard deviation of sub-gridscale orography), `slor` (slope of sub-gridscale orography), and surface `z` (model orography as geopotential). Anyone needing the model orography must fetch step 0 specifically — this is the most common cause of a "missing orography" failure when building a workflow that starts at a later step.

Naive per-step parameter matching that assumes a fixed name list will silently drop gusts and temperature extremes at the boundaries. Match on step range, or accept both spellings.

---

## Relationship to other models
- **[IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md):** The ensemble counterpart, built on the same model core and analyses. Since Cycle 49r1 it runs at the same 9 km resolution as the deterministic IFS. **Since Cycle 50r1 the forecast documented in this entry is the ENS control member** — in Open Data, `enfo` carries only the 50 perturbed members and the control must be taken from `oper`.
- **[ECWAM](../../../wave_models/global/eu/ecwam.md):** The coupled wave model. Its deterministic output is the `wave` stream and its ensemble is the `waef` stream, both distributed through the same Open Data channels described below.
- **[AIFS Single](./aifs-single.md):** ECMWF's machine-learning deterministic model. Shares IFS analyses for initialization.
- **[AIFS ENS](../../../ensemble_models/global/eu/aifs-ens.md):** ECMWF's machine-learning ensemble. Also initialized from IFS analyses.

The deterministic IFS forecast (historically known as "HRES") and the ENS Control member became scientifically and computationally bit-identical with IFS Cycle 49r1 (12 November 2024). With Cycle 50r1 (12 May 2026), the separate HRES stopped being produced. See [Recent version history](#recent-version-history) below for migration details.

---

## Data availability

- **Is the data free?** Yes (Open Data subset); the full Real-time Catalogue is open-licensed but delivery may be charged
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0) plus the ECMWF Terms of Use; attribution required, commercial use and redistribution permitted
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 for all gridded fields (CCSDS compression — `grid_ccsds` packing, verified on all 187 messages), BUFR edition 4 for tropical cyclone trajectories, JSON-lines `.index` sidecars for metadata
- **Decoder requirement:** ecCodes **2.42.0 or newer** is recommended by ECMWF. CCSDS packing additionally requires `libaec` support compiled in — a plain ecCodes build without it will fail to unpack these files.

### Free Open Data subset
- **Resolution:** 0.25° only. As of 2026-07-30 the resolution directory contains `0p25` and nothing else; the announced 9 km subset has **not** yet appeared.
- **Streams available:** `oper` (deterministic atmospheric), `enfo` (ensemble atmospheric), `wave` (deterministic wave), `waef` (ensemble wave). A `mmsf` seasonal stream is documented with monthly steps (`1m`–`7m`) at 00 UTC; it was not observable in the current rolling window and is **TBD**.
- **Retention on the ECMWF portal:** rolling archive of the most recent 12 forecast runs (~2–3 days). Enumeration on 2026-07-30 found 13 cycles present (2026-07-27 12z through 2026-07-30 12z), consistent with 12 runs plus the newest arrival before the oldest is trimmed.
- **Connection limit:** the portal is capped at **500 simultaneous connections** to protect operational services. Parallel downloaders should throttle accordingly, or use a cloud mirror.

### File naming convention
```
[ROOT]/[yyyymmdd]/[HH]z/[model]/[resol]/[stream]/[yyyymmdd][HH]0000-[step][U]-[stream]-[type].[format]
```
Example: `20260730/00z/ifs/0p25/oper/20260730000000-24h-oper-fc.grib2`

### Access channels

All four channels serve the identical file tree. Verified byte-identical on 2026-07-30: the same `.index` object retrieved from the ECMWF portal, Azure and GCS produced matching MD5 checksums (`748d657229ab...`, 40,572 bytes).

| Channel | Endpoint | Anonymous? | Archive depth (verified) |
|---|---|---|---|
| **ECMWF portal** | `https://data.ecmwf.int/forecasts/` | Yes (500-connection cap) | Rolling ~12 runs |
| **AWS S3** | `s3://ecmwf-forecasts` (`eu-central-1`) | Yes — unsigned `ListObjectsV2` and `GetObject` | **2023-01-18 → present** |
| **Azure Blob** | `https://ai4edataeuwest.blob.core.windows.net/ecmwf` | **No — SAS token required** | **2022-01-21 → present** |
| **Google Cloud Storage** | `gs://ecmwf-open-data` (US multi-region) | Yes — fully unauthenticated | **2023-07-12 → present** |

> **The cloud mirrors are full archives, not mirrors of the rolling window.** This is the single most consequential fact about ECMWF Open Data access and it is easy to miss: the ECMWF portal keeps ~3 days, but AWS, Azure and GCS retain everything they have ever received. Azure reaches furthest back, to January 2022. For any retrospective or backtesting use, the mirrors — not the portal — are the access route.

#### AWS
Anonymous S3 REST works without credentials:
```
https://ecmwf-forecasts.s3.amazonaws.com/?list-type=2&prefix=20260730/00z/ifs/0p25/oper/
```
The bucket is in `eu-central-1`; the `ecmwf-opendata` client addresses it as `https://ecmwf-forecasts.s3.eu-central-1.amazonaws.com`.

#### Azure — SAS token required
**Anonymous access to the Azure mirror was withdrawn and now returns `PublicAccessNotPermitted`.** A container listing without a token returns that error; a direct blob `GET` returns HTTP 409. Access is still free and requires no account, but a short-lived SAS token must first be requested from the Planetary Computer token endpoint:

```bash
TOK=$(curl -s https://planetarycomputer.microsoft.com/api/sas/v1/token/ai4edataeuwest/ecmwf \
      | python3 -c "import sys,json;print(json.load(sys.stdin)['token'])")
curl "https://ai4edataeuwest.blob.core.windows.net/ecmwf/20260730/00z/ifs/0p25/oper/20260730000000-0h-oper-fc.grib2?$TOK"
```
Verified working on 2026-07-30. The token is read+list scoped and expired **45 minutes** after issue in the observed case, so long-running jobs must refresh it. Note that the token must be appended with `?`, not `&`, when no other query string is present — appending with `&` returns HTTP 409.

The Planetary Computer dataset page (`planetarycomputer.microsoft.com/dataset/ecmwf-forecast`) is the catalog front-end for this same container.

#### Google Cloud — which link is the right one
ECMWF's documentation cites the **BigQuery marketplace listing** as its Google location, but the actual raw-GRIB mirror that the `ecmwf-opendata` client uses is a plain, fully anonymous GCS bucket that appears in neither of the commonly-cited pages:

```
https://storage.googleapis.com/ecmwf-open-data/20260730/00z/ifs/0p25/oper/20260730000000-0h-oper-fc.grib2
```
JSON API listing also works unauthenticated:
```
https://storage.googleapis.com/storage/v1/b/ecmwf-open-data/o?delimiter=/&prefix=20260730/
```
Bucket metadata: US multi-region, `STANDARD` class, created 2023-05-03. **This bucket is the in-scope Google channel for this repository** — same GRIB2 files, same layout, no authentication.

> **Out of scope — Earth Engine.** The `ECMWF/NRT_FORECAST/IFS/OPER` Earth Engine ImageCollection is a *derived* product, not a mirror: fields are re-ingested as Earth Engine raster assets with renamed bands and converted units (temperatures in °C rather than K), it updates twice daily rather than four times, coverage begins only at Cycle 49r1 (2024-11-12), and access requires Earth Engine registration and platform-side compute rather than file download. It falls outside this repository's raw-gridded-file scope for the same reason other platform-native raster catalogs do. **It also appears stalled:** the catalog's stated availability ends 2026-07-07, roughly three weeks before this check. Flagged rather than catalogued.

#### Access mechanics — index files and byte-range requests
Every `.grib2` file has a sibling `.index` file at the same URL with the extension substituted. Each line is a JSON record describing one GRIB message in MARS query language, with two extra keys giving its position:

```json
{"domain": "g", "date": "20260730", "time": "0000", "expver": "0001", "class": "od",
 "type": "fc", "stream": "oper", "levtype": "sfc", "step": "0", "param": "2t",
 "_offset": 17459800, "_length": 609046}
```

Combined with HTTP range requests this allows single-field retrieval without downloading the whole step file — a meaningful saving, since a single 00z `oper` step file is ~136 MB while one field is under 1 MB:

```bash
curl --range 17459800-18068845 [ROOT]/.../20260730000000-0h-oper-fc.grib2 -o 2t.grib2
```

Offsets change every run and must be re-read from the index each time. Multipart byte ranges are **not** reliably supported across all four channels — issue one range request per field.

#### Volume across the 0.25° tree
Measured 2026-07-30 from `Content-Length` on the 00z step-24 file of each stream, extrapolated over each stream's step count. Per-step forecast files only — the small `-ep` and `-tf` products are excluded.

| Stream | MB/step | GB per 00/12 cycle | GB per 06/18 cycle | GB/day |
|---|---|---|---|---|
| [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md) `enfo` (50 pf) | 6,633.5 | 563.8 | 325.0 | **1,778** |
| [AIFS ENS](../../../ensemble_models/global/eu/aifs-ens.md) `enfo` (cf + pf) | 4,570.9 | 278.8 | 278.8 | **1,115** |
| [ENS-WAM](../../../wave_models/global/eu/ecwam-ens.md) `waef` (50 pf) | 529.3 | 45.0 | 25.9 | **142** |
| AIFS ENS `waef` (cf + pf) | 425.2 | 25.9 | 25.9 | **104** |
| **IFS `oper` (this entry)** | 144.7 | 12.3 | 7.1 | **39** |
| [AIFS Single](./aifs-single.md) `oper` | 84.9 | 5.2 | 5.2 | **21** |
| [ECWAM](../../../wave_models/global/eu/ecwam.md) `wave` | 10.6 | 0.9 | 0.5 | **3** |
| AIFS Single `wave` | 8.1 | 0.5 | 0.5 | **2** |
| **Whole tree** | | | | **~3,200 (3.2 TB)** |

Roughly 2,950 GB/day of that is atmospheric and 250 GB/day wave. Two observations that matter when planning retrieval:

- **The ensembles are 96% of the tree.** `enfo` alone across both models is ~2.9 TB/day. Deterministic streams together are under 65 GB/day — a full day of IFS `oper` plus AIFS Single `oper` plus both deterministic wave streams is smaller than *one* 06z IFS ENS step file.
- **The IFS streams are front-loaded onto 00/12 UTC** (85 steps against 49), while both AIFS streams run 61 steps at every cycle. AIFS ENS therefore delivers a constant daily load where IFS ENS spikes.

Note that CCSDS compression is content-dependent, so per-step sizes vary with the meteorology — a stormy field compresses worse than a quiet one. These are single-sample measurements; treat the totals as approximate and the ranking as robust.

### Full ECMWF Real-time Catalogue
Since **1 October 2025**, the entire ECMWF Real-time Catalogue is licensed under CC-BY-4.0 — data costs have been removed and full-resolution 9 km IFS output can be redistributed and used commercially with attribution. However, *delivery* of high-volume data may still incur service charges to cover infrastructure costs, and full-catalogue access typically requires a Real-time Dissemination Service Agreement.

The authenticated dissemination endpoint `xdiss.ecmwf.int/ecpds/home/opendata` referenced by the client library returns HTTP 401 without credentials and is **not** a public access route.

### Tooling
The `ecmwf-opendata` Python client provides a consistent interface for retrieving Open Data from the portal, AWS, Azure, or GCS, handles the Azure SAS-token exchange automatically, and is the recommended programmatic access path:
- https://github.com/ecmwf/ecmwf-opendata

---

## Notes
- The operational IFS runs on a roughly annual major cycle upgrade schedule. Each cycle can shift skill characteristics, biases, and variable distributions — users running long backtests or climatologies should split evaluation windows around cycle transition dates.
- The freely-distributed Open Data is a **curated subset** of the operational IFS at reduced resolution. For the full-resolution native 9 km output and the complete parameter list, users should consult ECMWF's dissemination services or wait for the 9 km Open Data subset still planned for later in 2026.
- IFS is run as a coupled system with ocean, sea ice, and wave components. The atmospheric component is the focus of this entry; the coupled ocean surface fields that reach Open Data are listed above, and the wave model is documented separately in [ECWAM](../../../wave_models/global/eu/ecwam.md). Both the wave and ocean/sea-ice components were upgraded materially in Cycle 50r1.
- Heat and cold indices, mean radiant temperature, and globe temperature were added as standard IFS output parameters in Cycle 49r1 (November 2024). They are **not** part of the 0.25° Open Data subset.
- The current IFS cycle is **50r1** (operational since 12 May 2026).

### Archive path-schema changes (verified against the AWS mirror)
Because the cloud mirrors retain data back to 2022–2023 but the path layout has changed three times, any crawler walking the historical archive must handle several schemas. Transition dates below were established by bisecting the AWS bucket:

| Period | Path schema | Notes |
|---|---|---|
| → 2024-01-31 | `<date>/<HH>z/0p4-beta/` | No `[model]` component at all |
| 2024-02-01 → 2024-02-28 | `<date>/<HH>z/0p25/` and `.../0p4-beta/` | 0.25° first appears **2024-02-01** |
| 2024-02-29 → | `<date>/<HH>z/ifs/` and `.../aifs/` | `[model]` component inserted; AIFS appears same day |
| ~2025-03 → | `0p4-beta` gone | Last observed between 2025-02-15 and 2025-03-01 |
| ~2025-03 → | `aifs/` renamed `aifs-single/` | Between 2025-02-01 and 2025-04-01, tracking AIFS Single v1 operational status |
| 2025-07/08 → | `aifs-ens/` added | Between 2025-07-01 and 2025-08-01 |
| 2026-05-12 → | `scda/` and `scwv/` gone | Folded into `oper` and `wave` at Cycle 50r1 |

> **The 0.25° switchover date is commonly misreported as March 2024.** That is when ECMWF announced it; the data itself is present in the archive from **1 February 2024**. Entries citing the announcement date will mismatch the archive by a month.

Historical files retain their original stream names — pre-50r1 data on the mirrors still uses `scda` and `scwv` paths and filenames, and pre-50r1 `enfo` files contain **51** members rather than 50. Cross-cycle workflows must branch on date.

---

## Upcoming changes

### 9 km Open Data subset — announced for "later in 2026", not yet live
ECMWF has stated that the free and open subset will be extended to include native 9 km forecasts with a 2-hour latency. As of 2026-07-30 the resolution directory contains only `0p25`; no 9 km path exists. **Status: announced, not delivered.**

### IFS Cycle 50r2 — completion of the GRIB2 parameter migration (tentative Q4 2026)
Cycle 50r2 completes ECMWF's migration to GRIB2-native parameter encoding.

> **Correction to a common framing.** This migration is often described as moving Open Data files "from mixed GRIB1/GRIB2 to GRIB2 only." That is not what the files show. **All 187 messages in the verified `oper` step file are already GRIB edition 2** (`tablesVersion` 32, `grid_ccsds` packing). The migration concerns *parameter encoding conventions inside* GRIB2 containers — specifically, parameters still described via ECMWF local tables rather than WMO-standard discipline/category/number triplets.

Measured on the 2026-07-30 00z `oper` step-0 file: **181 of 187 messages already use WMO-standard encoding**; exactly **six** still carry `localTablesVersion = 1`:

| shortName | paramId | discipline / category / number | Name |
|---|---|---|---|
| `tp` | 228 | 0 / 1 / 193 | Total precipitation |
| `sf` | 144 | 0 / 1 / 198 | Snowfall |
| `sd` | 141 | 0 / 1 / 254 | Snow depth |
| `tcc` | 164 | 0 / 6 / 192 | Total cloud cover |
| `ro` | 205 | 2 / 0 / 201 | Runoff |
| `asn` | 32 | 0 / 19 / 192 | Snow albedo |

The practical scope of 50r2 for Open Data users is therefore narrow but sharply pointed: **`tp` — the most widely consumed parameter in the entire subset — is one of the six.** Anything matching precipitation on local-table coordinates will break. Matching on `shortName` or `paramId` should survive; matching on raw `parameterNumber` will not.

Other stated changes: some level-type handling changes, and legacy GRIB1-style parameter references (e.g. `165.128`) moving to GRIB2-native identifiers. GRIB2 output continues to use CCSDS compression.

ECMWF migration resources:
- Static test dataset: available since September 2025
- Migration documentation: https://confluence.ecmwf.int/display/MTG2US/Migration+to+GRIB+edition+2+Information+page
- GRIB2 encoding changes: https://confluence.ecmwf.int/display/MTG2US/Migration+to+GRIB2+-+changes+to+encoding+of+parameters

---

## Recent version history

### Cycle 50r1 — operational 12 May 2026 (current)
A major upgrade affecting the atmospheric, wave, and ocean/sea-ice components, deployed jointly with AIFS Single v2 and AIFS ENS v2 on the same day. The Release Candidate Phase ran from 19 February 2026 through the go-live; test data was available from MARS with experiment version (expver) 0080.

**Key atmospheric changes:**
- **No change in horizontal resolution, vertical resolution, or forecast steps.**
- Coupled data assimilation became more central: outer-loop coupling between atmosphere and ocean, with a 12-hour ocean analysis running alongside the atmospheric analysis. Microwave imagers and geostationary infrared data now contribute to coupled atmosphere/ocean increments.
- Weak-constraint 4D-Var formulation extended to the boundary layer, allowing assimilation of 2 m temperature observations across the full 12-hour window (vs the first 6 hours previously).
- Convection and cloud-microphysics changes addressing stationary precipitation and improving how precipitation propagates inland from the coast.
- New glacier parametrisation in the ecLand component (four-layer land-ice scheme replacing the previous binary glacier mask).
- Improved QBO, stratospheric winds, and stratospheric humidity (radiosonde humidity assimilation reintroduced up to 60 hPa).
- Solar eclipse effects now represented via accurate astronomical computations.
- Single-precision computation in the coupled atmosphere–ocean trajectory and reduced-resolution EDA inner loop yields ~40% computational savings.
- Underlying ocean/sea-ice core moved from NEMO 3.4 + LIM2 to NEMO4-SI3, with fully active coupling.

**Stream and archive changes — these DID materially affect Open Data users:**
- The separate HRES forecast is no longer produced. It is now the **IFS Medium-range Control Forecast**, which also serves as the ENS control member.
- **`stream=enfo, type=cf` is deprecated.** Users who consumed the ensemble control from `enfo` must now take it from **`stream=oper, type=fc`**. Verified: `enfo` index files at steps 0 and 240 h contain `type=pf` only, members 1–50, with no `cf` record anywhere.
- **`stream=scda` is deprecated** — 06/18 UTC high-resolution atmospheric data moved to `stream=oper`.
- **`stream=scwv` is deprecated** — 06/18 UTC high-resolution wave data moved to `stream=wave`.
- The same control-member removal applies to the wave ensemble: `waef` now carries 50 perturbed members only.
- Tropical cyclone ensemble products drop from 52 to 51 members.
- Vegetation fraction difference (`vegdiff`) is discontinued and replaced by Urban cover.

> **Corrects a previous version of this entry**, which stated that "Open Data users were not directly affected by the stream/archive changes." They were: ensemble member counts changed from 51 to 50, two stream directories disappeared from the path layout, and the control forecast moved files. Any workflow written against the pre-50r1 layout breaks at the cycle boundary. The earlier entry also rendered the migration arrow as `stream=oper, type=fc` → `stream=enfo, type=cf`, which is the wrong direction, and advised migrating 06/18 UTC users to `scda` — the stream that 50r1 deprecated.

### Cycle 49r1 — operational 12 November 2024
- HRES and ENS Control made scientifically and computationally bit-identical
- HRES extended from 10 to 15 days at 00/12 UTC, and runs to 6 days at 06/18 UTC
- New land surface model upgrades including urban tiles
- Heat indices, mean radiant temperature, and globe temperature added as output parameters
- Multi-layer snow scheme integrated into reforecasts via offline land DA reanalysis

### Cycle 48r1 — operational 27 June 2023
- ENS horizontal resolution increased from 18 km to 9 km, matching HRES
- Extended-range ensemble (now "sub-seasonal") restructured: separate system running daily out to 46 days at 36 km with 101 members
- GRIB2 output began using CCSDS compression

### Cycle 41r2 — operational 8 March 2016
- HRES grid resolution upgraded to 9 km on the new octahedral reduced Gaussian grid (Tco1279); ENS to 18 km

---

## Verification record
All quantitative claims marked "verified" in this entry were established on **2026-07-30** by direct inspection rather than from documentation:
- Directory enumeration of `data.ecmwf.int/forecasts/` across all 13 available cycles for archive depth, stream inventory, and step lists
- ecCodes 2.48.0 decode of `20260730000000-0h-oper-fc.grib2` (136 MB, 187 messages) for grid geometry, packing, editions, level types, parameter inventory, and local-table encoding
- `.index` sidecar parsing across steps 0, 3, 87, 90, 93, 96, 144, 150, 240 and 360 for step-dependent parameter availability
- Anonymous S3 `ListObjectsV2` against `ecmwf-forecasts` for mirror archive depth and path-schema bisection
- Planetary Computer SAS token exchange and authenticated blob retrieval against `ai4edataeuwest/ecmwf`
- Anonymous GCS JSON API and object retrieval against `ecmwf-open-data`
- MD5 comparison of the same object across three channels
- `Last-Modified` header sampling across the 00z, 06z and 12z cycles for publication latency

Where live observation and ECMWF documentation disagree, the live observation is recorded and the disagreement flagged rather than silently resolved.

---

## Official documentation
- Open Data dataset page: https://www.ecmwf.int/en/forecasts/datasets/open-data
- Open Data user documentation (access, naming, index files): https://confluence.ecmwf.int/display/DAC/ECMWF+open+data%3A+real-time+forecasts+from+IFS+and+AIFS
- Dissemination schedule: https://confluence.ecmwf.int/display/DAC/Dissemination+schedule
- Changes to the forecasting system: https://confluence.ecmwf.int/display/FCST/Changes+to+the+forecasting+system
- Planned model changes: https://www.ecmwf.int/en/forecasts/documentation-and-support/changes-ecmwf-model
- IFS Cycle 50r1 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+IFS+Cycle+50r1
- IFS Cycle 49r1 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+IFS+Cycle+49r1
- ECMWF Newsletter 185 (Cycle 50r1 overview): https://www.ecmwf.int/en/newsletter/185/earth-system-science/upgrade-ifs-cycle-50r1
- ECMWF Open Data Python client: https://github.com/ecmwf/ecmwf-opendata
- ECMWF Open Data community forum: https://forum.ecmwf.int/c/open-data
- AWS Open Data Registry entry: https://registry.opendata.aws/ecmwf-forecasts/
- Planetary Computer dataset page: https://planetarycomputer.microsoft.com/dataset/ecmwf-forecast
- Planetary Computer SAS token concepts: https://planetarycomputer.microsoft.com/docs/concepts/sas/
- Google Cloud marketplace listing: https://console.cloud.google.com/marketplace/product/bigquery-public-data/open-data-ecmwf
- Open-ECPDS (portal software): https://ecmwf.github.io/open-ecpds/
