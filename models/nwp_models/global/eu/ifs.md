# IFS (Integrated Forecasting System)

## What this model is
The Integrated Forecasting System (IFS) is ECMWF's flagship global numerical weather prediction system and the deterministic backbone of European medium-range forecasting.

IFS uses a hybrid spectral / reduced Gaussian-grid dynamical core and a 4D-Var data assimilation system, and is widely regarded as one of the most skillful operational global NWP systems in the world. It provides the analyses used to initialize ECMWF's other forecast products including IFS ENS (the physics ensemble), AIFS Single (the deterministic AI model), and AIFS ENS (the ensemble AI model).

ECMWF distributes IFS output through two routes: a free Open Data subset (CC-BY-4.0) and the full Real-time Catalogue (native 9 km, also under CC-BY-4.0 since 1 October 2025 but subject to service charges for high-volume delivery). Since **20 October 2026 (06 UTC run)** the free subset is published in **two resolution tiers** from the same forecast: the long-standing **0.25°** tier and a new **0.1°** tier that arrives **2 hours later**. Users who need the earliest possible data should stay on 0.25°; users who want the finer grid and can tolerate the delay can take 0.1°. This entry covers the IFS as a whole, with the Open Data details called out explicitly.

> **Note on naming after Cycle 50r1.** Since 12 May 2026 the separate HRES forecast no longer exists as a distinct product. ECMWF now calls this forecast the **IFS Medium-range Control Forecast**, and it doubles as the [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md) control member. In Open Data it is still distributed under `stream=oper, type=fc`. This entry documents that forecast.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts
- **Country / region:** International (European consortium of 23 Member States and 12 Co-operating States)

---

## What area it covers
- **Coverage:** Global
- **Open Data grids (verified):**
  - **0.25° tier:** 1440 × 721 regular latitude–longitude, 0.25° × 0.25°, 1,038,240 points per field
  - **0.1° tier:** 3600 × 1801 regular latitude–longitude, 0.1° × 0.1°, 6,483,600 points per field (6.25× the 0.25° point count)
- **Grid origin (verified, both tiers):** first grid point 90°N / 180°, last grid point 90°S / 179.75° (0.25°) or 179.9° (0.1°), scanning mode 0 (west→east, north→south). **The longitude axis begins at the dateline, not at 0° or −180°.** Decoders that normalize to −180…179.75 (as ecCodes does) will report the array as starting at −180°, but the raw GRIB header carries `longitudeOfFirstGridPointInDegrees = 180.0`. Code that assumes a Greenwich-first or −180-first raw layout will be shifted by half a globe.

---

## Basic details
- **Model type:** Global deterministic NWP
- **Model system / core:** IFS (hybrid spectral / reduced Gaussian-grid)
- **Dynamical formulation:** Hydrostatic, semi-Lagrangian, semi-implicit time integration
- **Convection-allowing:** No (deep convection is parameterized at 9 km native resolution)
- **Native horizontal resolution:** ~9 km (Tco1279 octahedral reduced Gaussian grid)
- **Open Data resolution:** two tiers — **0.25°** (~28 km) and **0.1°** (~11 km), both regular latitude–longitude. Both are interpolated from the same native ~9 km forecast; the 0.1° tier is a finer *distribution* grid, not a different model run, and is still slightly coarser than native.
- **Vertical levels:** 137 (model); **14 pressure levels in Open Data** (both tiers) — 10, 50, 100, 150, 200, 250, 300, 400, 500, 600, 700, 850, 925, 1000 hPa
- **Soil levels in Open Data:** 4 (`soilLayer` 1–4)
- **Model top:** 0.01 hPa (~80 km)
- **Forecast length (Open Data, verified):**
  - **360 h (15 days)** for 00 and 12 UTC cycles
  - **144 h (6 days)** for 06 and 18 UTC cycles
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (Open Data, verified):**
  - 00 / 12 UTC: 3-hourly to +144 h, then 6-hourly +150 h to +360 h — **85 steps**
  - 06 / 18 UTC: 3-hourly to +144 h — **49 steps**
- **Observed publication latency:**
  - **0.25° tier:** the complete run is published as a single batch (all GRIB2 steps share one `Last-Modified` timestamp), at approximately **T+7h34m** for 00/12 UTC and **T+6h27m** for 06/18 UTC. Measured 2026-07-30 across the 00z, 06z and 12z cycles, and re-confirmed unchanged on 2026-10-20 12z, 2026-10-21 00z and 2026-10-21 06z.
  - **0.1° tier:** **exactly 2 hours after the 0.25° tier** — **T+9h34m** for 00/12 UTC and **T+8h27m** for 06/18 UTC, published within about one minute. Measured on the 2026-10-20 12z, 2026-10-21 00z and 2026-10-21 06z cycles. ECMWF describes this delay as scheduled, not late: the 0.1° files follow the end of the normal dissemination schedule and get their own WIS2 notifications.

> **"2-hour latency" means 2 hours behind the 0.25° data, not T+2h.** ECMWF's announcements described the 0.1° subset as having "a 2-hour latency", which is easy to read as two hours after initialisation. The live data shows it is two hours *on top of* the 0.25° latency — roughly T+9.5 h for the main runs. Third-party coverage of the launch (GribStream) reads it the same way.

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
Originally decoded from `20260730000000-0h-oper-fc.grib2` (187 GRIB messages, 50 distinct parameters at step 0; 184 messages / 48 parameters at all other steps). **Since the 2026-10-20 06z run there are 194 messages at step 0 and 191 at other steps**, after seven surface parameters were added (see below). **The 0.25° and 0.1° tiers carry exactly the same inventory** — verified by set comparison of the full `.index` files at steps 0 and 24 h on the 2026-10-20 12z run (194 = 194 and 191 = 191, no field in one tier missing from the other).

**Pressure levels** (10 parameters × 14 levels): `t`, `u`, `v`, `w`, `q`, `r`, `z`, `gh`, `d`, `vo`

**Height above ground:** `2t`, `2d`, `10u`, `10v`, `100u`, `100v`, `10fg` / `10fg3`, `mn2t3` / `mn2t6`, `mx2t3` / `mx2t6`

**Surface:** `sp`, `skt`, `lsm`, `z`, `sdor`, `slor`, `tp`, `tprate`, `sf`, `ptype`, `ro`, `sd`, `rsn`, `asn`, `ssr`, `ssrd`, `str`, `strd`, `ewss`, `nsss`, `sithick`, `zos`, `sve`, `svn`

**Other level types:** `msl` (mean sea level), `tcc`, `tcw`, `tcwv` (entire atmosphere), `ttr` (nominal top), `mucape` (most-unstable parcel), `sot` and `vsw` (soil layers 1–4)

**Added 2026-10-20 (06z run), in both tiers:** `cp` (convective precipitation), `lcc` / `mcc` / `hcc` (low, medium and high cloud cover), `tclw` / `tciw` / `tcrw` (total column cloud liquid water, cloud ice water and rain water). All seven are filed under `levtype=sfc` in the `.index` sidecar. They were absent from every 00z `oper` index sampled between 2026-07-30 and the 2026-10-20 00z run, and present from 2026-10-20 06z onwards — the same cycle the 0.1° tier launched.

Note the presence of **ocean and sea-ice fields in the atmospheric stream** — `zos` (sea surface height), `sithick` (sea ice thickness), `sve` / `svn` (surface sea water velocity components). These come from the coupled NEMO4-SI3 component and are delivered alongside the atmospheric fields rather than in a separate ocean stream.

#### Step-dependent parameter availability (verified)
The parameter set is **not constant across steps**. Three separate switchovers occur:

| Parameter | Steps 0–90 | Steps 93–144 | Steps 150–360 |
|---|---|---|---|
| Wind gust | `10fg` | `10fg3` | `10fg` |
| Min 2 m temperature | `mn2t3` | `mn2t3` | `mn2t6` |
| Max 2 m temperature | `mx2t3` | `mx2t3` | `mx2t6` |

Additionally, three **static fields appear only at step 0** and are not repeated: `sdor` (standard deviation of sub-gridscale orography), `slor` (slope of sub-gridscale orography), and surface `z` (model orography as geopotential). Anyone needing the model orography must fetch step 0 specifically — this is the most common cause of a "missing orography" failure when building a workflow that starts at a later step. In the 0.1° tier these three fields are in the step-0 **`-sfc`** file, not the `-pl` file.

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
- **Data formats:** GRIB2 for all gridded fields (CCSDS compression — `grid_ccsds` packing, verified on all 187 messages in July and on sampled messages from both tiers in October), BUFR edition 4 for tropical cyclone trajectories (**0.25° tier only**), JSON-lines `.index` sidecars for metadata
- **Decoder requirement:** ecCodes **2.42.0 or newer** is recommended by ECMWF. CCSDS packing additionally requires `libaec` support compiled in — a plain ecCodes build without it will fail to unpack these files.

### Free Open Data subset
- **Resolution:** two tiers, `0p25` and `0p1`, published side by side under `ifs/`. The 0.1° tier went live with the **2026-10-20 06z** run — verified absent from every cycle on the AWS mirror from 2026-10-15 through 2026-10-20 00z, and present on all four channels from 06z onwards. **The 0.25° tier was not replaced and continues unchanged in timing.** Only IFS has a 0.1° tier: `aifs-single/` and `aifs-ens/` still contain `0p25` only.
- **Streams available (both tiers):** `oper` (deterministic atmospheric), `enfo` (ensemble atmospheric), `wave` (deterministic wave), `waef` (ensemble wave). A `mmsf` seasonal stream is documented with monthly steps (`1m`–`7m`) at 00 UTC; it was not observable in the current rolling window and is **TBD**.
- **Retention on the ECMWF portal:** rolling archive of the most recent 12 forecast runs (~2–3 days). Enumeration on 2026-07-30 found 13 cycles present (2026-07-27 12z through 2026-07-30 12z), consistent with 12 runs plus the newest arrival before the oldest is trimmed.
- **Connection limit:** the portal is capped at **500 simultaneous connections** to protect operational services. Parallel downloaders should throttle accordingly, or use a cloud mirror.

### Choosing a tier — 0.25° or 0.1°

| | **0.25° tier** (`0p25`) | **0.1° tier** (`0p1`) |
|---|---|---|
| Grid | 1440 × 721 (~28 km) | 3600 × 1801 (~11 km) |
| Underlying forecast | Native ~9 km IFS | Same forecast |
| `oper` available (00/12 UTC) | **~T+7h34m** | ~T+9h34m |
| `oper` available (06/18 UTC) | **~T+6h27m** | ~T+8h27m |
| Parameters, levels, steps | Identical | Identical |
| Files per step | One combined file | **Two:** `-sfc` (surface + soil) and `-pl` (pressure levels) |
| `oper` +24 h step size (2026-10-21 00z) | 99.9 MB | 95.0 MB `-sfc` + 368.1 MB `-pl` = 463 MB |
| `oper` volume per day | ~27 GB | ~124 GB |
| Tropical cyclone BUFR (`-tf.bufr`) | Yes (`oper` and `enfo`) | **No** |
| Archive on cloud mirrors | 2022–2023 → present | **2026-10-20 06z → present** |
| Packing (bits per value) | Per parameter | Same per-parameter values as 0.25° |

**Pick 0.25° when timeliness matters** — it is the same forecast, two hours sooner, at a fifth of the volume. **Pick 0.1° when spatial detail matters more than the delay**: coastlines, orography-driven fields, convective precipitation structure, or initialising a limited-area model. Neither tier is native resolution; both are interpolations of the ~9 km octahedral grid.

> ⚠️ **Documentation discrepancy — "additional parameters" at 0.1°.** ECMWF's *what's new at 0.1°* page states that the 0.1° tier "carries extra fields beyond the 0.25° core set" for Forecast in a Box and limited-area model initialisation, including model-level fields. **The live data does not show this.** Full `.index` comparison of the two tiers for `oper` (steps 0 and 24 h) and `enfo` (step 24 h) on the 2026-10-20 12z run found identical inventories — no extra parameters, no extra pressure levels, and no model-level (`ml`) fields; only the `pl`, `sfc` and `sol` level types appear. The seven fields added on 2026-10-20 (`cp`, `lcc`, `mcc`, `hcc`, `tclw`, `tciw`, `tcrw`) were added to **both** tiers. **TBD:** whether the extra 0.1° fields are still to be phased in, or whether the page is in error. Live observation recorded as authoritative.

> ⚠️ **Precision reduction in the 0.25° tier from 2026-10-20 06z.** The same cycle that launched the 0.1° tier also **lowered the bits per value of most 0.25° fields**, cutting file sizes by roughly a third (`oper` +24 h step: ~146 MB → ~100 MB; `enfo`: ~6.7 GB → ~4.4 GB). The two tiers now use identical per-parameter bits per value. ECMWF's *what's new at 0.1°* page treats the 0.25° reduction as already in place ("its bits-per-value reduction has already brought the volume down") without dating it; the archive shows it arriving on the same cycle as the 0.1° tier. Measured on the `oper` +24 h step, 2026-10-20 00z (before) vs 2026-10-21 00z (after):
>
> | Field | Bits per value before → after | Approx. quantisation step before → after |
> |---|---|---|
> | `2t` | 12 → **9** | 0.03 K → **0.25 K** |
> | `t` 500 hPa | 12 → **9** | 0.016 K → **0.125 K** |
> | `tp` | 16 → **12** | 0.008 mm → **0.12 mm** |
> | `10u`, `u` 250 hPa | 12 → 11 | 0.016 → 0.031 m/s |
> | `q` 500 hPa, `skt`, `tcwv` | 12 → 9 | — |
> | `vo` 500 hPa, `w` 500 hPa | 16 → 12 | — |
> | `r` 850 hPa | 8 → 7 | — |
> | `msl`, `sp`, `gh` 500 hPa | 12 → 12 (unchanged) | `msl` 4 Pa |
> | `z` 500 hPa | 16 → 16 (unchanged) | 0.25 m²/s² |
>
> The quantisation step depends on each message's value range, so the steps above are those of this sample, not fixed constants. The change matters for anything that differences small quantities — light-precipitation thresholds, accumulated-precipitation de-accumulation, or near-surface temperature verification at sub-kelvin precision. **Archive users should note that 0.25° data before and after 2026-10-20 06z is packed differently.** The AIFS streams in the same bucket also shrank at the same point (AIFS ENS `enfo` ~1,115 GB/day in July → ~738 GB/day now), so the reduction is not limited to IFS.

### File naming convention
```
0.25°: [ROOT]/[yyyymmdd]/[HH]z/[model]/0p25/[stream]/[yyyymmdd][HH]0000-[step][U]-[stream]-[type].[format]
0.1°:  [ROOT]/[yyyymmdd]/[HH]z/ifs/0p1/[stream]/[yyyymmdd][HH]0000-[step][U]-[stream]-[type]-[sfc|pl].[format]
```
Examples:
- `20260730/00z/ifs/0p25/oper/20260730000000-24h-oper-fc.grib2`
- `20261021/00z/ifs/0p1/oper/20261021000000-24h-oper-fc-sfc.grib2`
- `20261021/00z/ifs/0p1/oper/20261021000000-24h-oper-fc-pl.grib2`

In the 0.1° tier, **soil fields (`sot`, `vsw`) are in the `-sfc` file**, and the wave streams have a `-sfc` file only. Each split file has its own `.index` sidecar. ECMWF's stated reason for the split is to let users in low-bandwidth regions take surface fields alone: the 0.1° `oper` `-pl` file is about four times the size of its `-sfc` file. Code that builds filenames from `[stream]-[type].grib2` will not find any 0.1° files.

### Access channels

All four channels serve the identical file tree, for both tiers. Verified byte-identical on 2026-07-30 for 0.25°: the same `.index` object retrieved from the ECMWF portal, Azure and GCS produced matching MD5 checksums (`748d657229ab...`, 40,572 bytes). Verified on 2026-10-21 for 0.1°: `20261021000000-24h-oper-fc-sfc.index` returned matching MD5 (`6c920c0213c7...`, 10,643 bytes) from **all four** channels.

| Channel | Endpoint | Anonymous? | Archive depth, 0.25° (verified) | Archive depth, 0.1° (verified) |
|---|---|---|---|---|
| **ECMWF portal** | `https://data.ecmwf.int/forecasts/` | Yes (500-connection cap) | Rolling ~12 runs | Rolling ~12 runs |
| **AWS S3** | `s3://ecmwf-forecasts` (`eu-central-1`) | Yes — unsigned `ListObjectsV2` and `GetObject` | **2023-01-18 → present** | **2026-10-20 06z → present** |
| **Azure Blob** | `https://ai4edataeuwest.blob.core.windows.net/ecmwf` | **No — SAS token required** | **2022-01-21 → present** | **2026-10-20 06z → present** |
| **Google Cloud Storage** | `gs://ecmwf-open-data` (US multi-region) | Yes — fully unauthenticated | **2023-07-12 → present** | **2026-10-20 06z → present** |

> **The cloud mirrors can lag the portal for the 0.1° ensembles.** File counts for every 0.1° cycle matched across all four channels once copying finished, but the large ensemble streams take hours to arrive on some mirrors. At 03:30 UTC on 2026-10-21 the 2026-10-20 18z run was complete on the portal and AWS, while GCS had 23 of 98 `enfo` files and 0 of 49 `waef` files, and Azure had 66 and 0. At 16:45 UTC the 2026-10-21 06z run showed the same pattern on GCS only (92 of 98 `enfo`, 0 of 49 `waef`). The deterministic `oper` and `wave` streams were complete on every channel in both checks. **GCS was the slowest mirror both times.** Pipelines triggered on a fixed schedule should check that the expected file count is present before reading, rather than assuming a run is complete when its first file appears.

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

Combined with HTTP range requests this allows single-field retrieval without downloading the whole step file — a meaningful saving, since a single 00z `oper` step file is ~100 MB at 0.25° (~136–146 MB before the 2026-10-20 packing change) while one field is under 1 MB. At 0.1° a single `2t` field is ~1.4 MB against a 95 MB `-sfc` file. The `.index` format is identical in both tiers:

```bash
curl --range 17459800-18068845 [ROOT]/.../20260730000000-0h-oper-fc.grib2 -o 2t.grib2
```

Offsets change every run and must be re-read from the index each time. Multipart byte ranges are **not** reliably supported across all four channels — issue one range request per field. **Expect intermittent HTTP 503 responses on `.index` and range requests from AWS** — seen repeatedly on 2026-10-20 and 2026-10-21 for both tiers, with retries succeeding; implement backoff.

<a id="volume-across-the-025-tree"></a>

#### Volume across the Open Data tree
**Re-measured 2026-10-21** by summing the actual GRIB2 object sizes (forecast and probability files; `.index` and BUFR excluded) from AWS listings over four consecutive cycles — 2026-10-20 12z and 18z, 2026-10-21 00z and 06z — so the totals are a real day of output rather than an extrapolation. All figures are after the 2026-10-20 packing change.

| Stream | GB per 00/12 cycle | GB per 06/18 cycle | GB/day |
|---|---|---|---|
| **0.1° tier** | | | |
| [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md) `enfo` (50 pf + `-ep`) | 1,701 | 972 | **5,348** |
| [ENS-WAM](../../../wave_models/global/eu/ecwam-ens.md) `waef` (50 pf + `-ep`) | 112.5 | 65.0 | **355** |
| **IFS `oper` (this entry)** | 39.0 | 22.7 | **124** |
| [ECWAM](../../../wave_models/global/eu/ecwam.md) `wave` | 2.2 | 1.3 | **7** |
| *0.1° subtotal* | | | ***~5,830 (5.8 TB)*** |
| **0.25° tier** | | | |
| [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md) `enfo` (50 pf + `-ep`) | 375 | 214 | **1,178** |
| [AIFS ENS](../../../ensemble_models/global/eu/aifs-ens.md) `enfo` | 184 | 184 | **738** |
| [ENS-WAM](../../../wave_models/global/eu/ecwam-ens.md) `waef` | 22.9 | 13.2 | **72** |
| AIFS ENS `waef` | 12.3 | 12.3 | **49** |
| **IFS `oper` (this entry)** | 8.5 | 4.9 | **27** |
| [AIFS Single](./aifs-single.md) `oper` | 3.5 | 3.5 | **14** |
| [ECWAM](../../../wave_models/global/eu/ecwam.md) `wave` | 0.5 | 0.3 | **1.4** |
| AIFS Single `wave` | 0.2 | 0.2 | **0.9** |
| *0.25° subtotal* | | | ***~2,080 (2.1 TB)*** |
| **Whole tree** | | | **~7,900 (7.9 TB)** |

Observations that matter when planning retrieval:

- **The 0.1° tier is nearly three times the size of the whole 0.25° tree**, and 0.1° IFS ENS alone is ~5.3 TB/day — more than double the whole 0.25° tree. The size ratio per stream is about 4.5–5× rather than the 6.25× implied by the point count — presumably because CCSDS compresses the smoother, more finely sampled fields more efficiently.
- **The ensembles still dominate.** At either tier, the deterministic streams are a few percent of the total. A full day of 0.1° `oper` (~124 GB) is about the size of six 0.1° `enfo` steps (~20 GB per step, `-sfc` + `-pl` combined).
- **The 0.25° tree shrank by about a third on 2026-10-20** (from ~3.2 TB/day measured in July). The July figures in previous versions of this entry are no longer valid for current data, though they still describe the archive before that date.
- **The IFS streams are front-loaded onto 00/12 UTC** (85 steps against 49), while both AIFS streams run 61 steps at every cycle. AIFS ENS therefore delivers a constant daily load where IFS ENS spikes.

Note that CCSDS compression is content-dependent, so per-step sizes vary with the meteorology — a stormy field compresses worse than a quiet one. Treat the totals as approximate and the ranking as robust.

### Full ECMWF Real-time Catalogue
Since **1 October 2025**, the entire ECMWF Real-time Catalogue is licensed under CC-BY-4.0 — data costs have been removed and full-resolution 9 km IFS output can be redistributed and used commercially with attribution. However, *delivery* of high-volume data may still incur service charges to cover infrastructure costs, and full-catalogue access typically requires a Real-time Dissemination Service Agreement.

The authenticated dissemination endpoint `xdiss.ecmwf.int/ecpds/home/opendata` referenced by the client library returns HTTP 401 without credentials and is **not** a public access route.

### Tooling
The `ecmwf-opendata` Python client provides a consistent interface for retrieving Open Data from the portal, AWS, Azure, or GCS, handles the Azure SAS-token exchange automatically, and is the recommended programmatic access path:
- https://github.com/ecmwf/ecmwf-opendata

---

## Notes
- The operational IFS runs on a roughly annual major cycle upgrade schedule. Each cycle can shift skill characteristics, biases, and variable distributions — users running long backtests or climatologies should split evaluation windows around cycle transition dates.
- The freely-distributed Open Data is a **curated subset** of the operational IFS, interpolated to 0.25° or 0.1°. For native-grid (Tco1279) output and the complete parameter list, users should consult ECMWF's dissemination services. The 0.1° tier narrows the resolution gap but does not change the parameter subset.
- IFS is run as a coupled system with ocean, sea ice, and wave components. The atmospheric component is the focus of this entry; the coupled ocean surface fields that reach Open Data are listed above, and the wave model is documented separately in [ECWAM](../../../wave_models/global/eu/ecwam.md). Both the wave and ocean/sea-ice components were upgraded materially in Cycle 50r1.
- Heat and cold indices, mean radiant temperature, and globe temperature were added as standard IFS output parameters in Cycle 49r1 (November 2024). They are **not** part of the Open Data subset at either resolution.
- **Tropical cyclone tracks are 0.25°-only.** The `-tf.bufr` trajectory files appear in the 0.25° `oper` and `enfo` directories at every cycle (`360h` at 00/12 UTC, `144h` at 06/18 UTC). The 0.1° directories contain no BUFR files at all — verified across the 2026-10-20 12z and 2026-10-21 00z and 06z runs. Users of the 0.1° tier who need tracks must take them from the 0.25° tree; since tracks are point data, nothing is lost by doing so.
- The current IFS cycle is **50r1** (operational since 12 May 2026).

### Archive path-schema changes (verified against the AWS mirror)
Because the cloud mirrors retain data back to 2022–2023 but the path layout has changed several times, any crawler walking the historical archive must handle several schemas. Transition dates below were established by bisecting the AWS bucket:

| Period | Path schema | Notes |
|---|---|---|
| → 2024-01-31 | `<date>/<HH>z/0p4-beta/` | No `[model]` component at all |
| 2024-02-01 → 2024-02-28 | `<date>/<HH>z/0p25/` and `.../0p4-beta/` | 0.25° first appears **2024-02-01** |
| 2024-02-29 → | `<date>/<HH>z/ifs/` and `.../aifs/` | `[model]` component inserted; AIFS appears same day |
| ~2025-03 → | `0p4-beta` gone | Last observed between 2025-02-15 and 2025-03-01 |
| ~2025-03 → | `aifs/` renamed `aifs-single/` | Between 2025-02-01 and 2025-04-01, tracking AIFS Single v1 operational status |
| 2025-07/08 → | `aifs-ens/` added | Between 2025-07-01 and 2025-08-01 |
| 2026-05-12 → | `scda/` and `scwv/` gone | Folded into `oper` and `wave` at Cycle 50r1 |
| 2026-10-20 06z → | `ifs/0p1/` added alongside `ifs/0p25/` | 0.1° files use `-sfc` / `-pl` filename suffixes; no `[model]` other than `ifs` has a `0p1` directory |
| 2026-10-20 06z → | No path change in `0p25/` | **Content change:** lower bits per value on most fields, and seven surface parameters added (`cp`, `lcc`, `mcc`, `hcc`, `tclw`, `tciw`, `tcrw`) |

> **The 0.25° switchover date is commonly misreported as March 2024.** That is when ECMWF announced it; the data itself is present in the archive from **1 February 2024**. Entries citing the announcement date will mismatch the archive by a month.

Historical files retain their original stream names — pre-50r1 data on the mirrors still uses `scda` and `scwv` paths and filenames, and pre-50r1 `enfo` files contain **51** members rather than 50. Cross-cycle workflows must branch on date. The same applies to 2026-10-20: 0.25° files from before the 06z run have a smaller parameter set and higher-precision packing than those after it.

---

## Upcoming changes

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

The 0.1° tier inherits the same encoding: `tp` in the 0.1° `-sfc` file was verified on 2026-10-21 to still carry `localTablesVersion = 1`, so the 50r2 migration affects both tiers equally.

The practical scope of 50r2 for Open Data users is therefore narrow but sharply pointed: **`tp` — the most widely consumed parameter in the entire subset — is one of the six.** Anything matching precipitation on local-table coordinates will break. Matching on `shortName` or `paramId` should survive; matching on raw `parameterNumber` will not.

Other stated changes: some level-type handling changes, and legacy GRIB1-style parameter references (e.g. `165.128`) moving to GRIB2-native identifiers. GRIB2 output continues to use CCSDS compression.

ECMWF migration resources:
- Static test dataset: available since September 2025
- Migration documentation: https://confluence.ecmwf.int/display/MTG2US/Migration+to+GRIB+edition+2+Information+page
- GRIB2 encoding changes: https://confluence.ecmwf.int/display/MTG2US/Migration+to+GRIB2+-+changes+to+encoding+of+parameters

---

## Recent version history

### Open Data 0.1° tier — live 20 October 2026, 06 UTC run
Not a model cycle change — the forecast itself is unchanged — but the largest change to the free distribution since the 0.25° upgrade in 2024.
- **New 0.1° tier** (`ifs/0p1/`) for `oper`, `enfo`, `wave` and `waef`, published 2 hours after the 0.25° tier. First present on the 06z run; all four cycles (00, 06, 12, 18 UTC) confirmed by 2026-10-21.
- **0.25° tier continues**, with unchanged timing and paths.
- **AIFS is not included** — `aifs-single` and `aifs-ens` remain 0.25° only.
- **Surface and pressure-level fields split** into separate `-sfc` and `-pl` files per step in the 0.1° tier only.
- **Seven surface parameters added to both tiers:** `cp`, `lcc`, `mcc`, `hcc`, `tclw`, `tciw`, `tcrw`.
- **Bits per value reduced in the 0.25° tier** on most fields, roughly one-third smaller files; both tiers now share per-parameter packing.
- No tropical cyclone BUFR in the 0.1° tier.
- This delivers ECMWF's 1 October 2025 commitment to extend the free subset to its "9 km resolution forecasts with a 2-hour latency" in 2026 — with the clarification that the distributed grid is 0.1° (~11 km), not the native octahedral grid, and the latency is relative to the 0.25° data.
- **Corrects the previous version of this entry**, which listed the 9 km subset under *Upcoming changes* as "announced, not delivered".

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

The 0.1° tier and the 2026-10-20 changes were verified on **2026-10-20 and 2026-10-21**:
- Directory listing of all four channels (portal, AWS, Azure with SAS token, GCS) for every cycle from 2026-10-20 06z to 2026-10-21 06z, with per-stream GRIB2 file counts; AWS scan back to 2026-10-15 confirming no earlier `0p1` directory
- ecCodes 2.50.0 decode of byte-range-extracted messages from the 0.1° tier (`2t`, `tp`, `10u`, `msl`, `t`/`z`/`q` at 500 hPa) for grid geometry, packing, bits per value and local-table status
- Full `.index` set comparison between tiers for `oper` (steps 0 and 24 h) and `enfo` (step 24 h)
- `.index` comparison of 0.25° `oper` across 00z runs from 2026-07-30 to 2026-10-21 to date the seven-parameter addition
- `Content-Length` sampling of the 0.25° `oper` +24 h file across dates and the four 2026-10-20 cycles to date the packing change, plus bits-per-value decode before and after
- Summed AWS listing sizes over four consecutive cycles for the volume table
- AWS `LastModified` sampling for both tiers on the 2026-10-20 12z, 2026-10-21 00z and 2026-10-21 06z runs for publication latency
- MD5 comparison of one 0.1° `.index` object across all four channels

Where live observation and ECMWF documentation disagree, the live observation is recorded and the disagreement flagged rather than silently resolved.

---

## Official documentation
- Open Data dataset page: https://www.ecmwf.int/en/forecasts/datasets/open-data
- Open Data user documentation (access, naming, index files): https://confluence.ecmwf.int/display/DAC/ECMWF+open+data%3A+real-time+forecasts+from+IFS+and+AIFS
- ECMWF open data: what's new at 0.1°: https://confluence.ecmwf.int/spaces/DAC/pages/738041087/ECMWF+open+data+what+s+new+at+0.1%C2%B0
- ECMWF announcement of the full Real-time Catalogue opening and the planned 9 km free subset (1 October 2025): https://www.ecmwf.int/node/29497
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
