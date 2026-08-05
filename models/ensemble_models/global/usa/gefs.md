# GEFS (Global Ensemble Forecast System)

## What this model is
The Global Ensemble Forecast System (GEFS) is the United States' primary global ensemble numerical weather prediction system, operated by NOAA's National Centers for Environmental Prediction (NCEP).

GEFS is the ensemble counterpart to the deterministic [GFS](../../../nwp_models/global/usa/gfs.md), built on the same FV3-based atmospheric model and the same Earth system framework, with 31 ensemble members (1 control + 30 perturbed) running 4× daily. It produces probabilistic medium-range forecasts for most cycles and extends to **35 days at the 00 UTC cycle**, providing the United States' operational sub-seasonal forecast guidance.

GEFS is also a contributing member to the [North American Ensemble Forecast System (NAEFS)](./naefs.md), the [557th WW GEPS](./557wg-geps.md) multi-model ensemble, and the [HGEFS](./hgefs.md) hybrid physics-AI grand ensemble — making it one of the most heavily-used ensemble forecast products in operational meteorology.

The current operational version is **GEFSv12** (operational since 23 September 2020), with **GEFSv13** under development for joint deployment alongside [GFSv17](../../../nwp_models/global/usa/gfs.md#upcoming-changes) (proposed for October 2026).

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global ensemble NWP
- **Model system / core:** Unified Forecast System (UFS) atmospheric model with **FV3** (Finite-Volume Cubed-Sphere) dynamical core
- **Dynamical formulation:** Non-hydrostatic, on a cubed-sphere finite-volume grid; semi-Lagrangian vertical coordinate
- **Convection-allowing:** No (deep convection is parameterized at ~25 km resolution)
- **Ensemble size:** 31 (1 control `gec00` + 30 perturbed `gep01`–`gep30`), plus derived `geavg` (mean) and `gespr` (spread) products. A **separate GEFS-Aerosols run** produces the `chem/` stream to 5 days with the GOCART aerosol chemistry component (one-way coupled) — see *Product inventory*.
- **Perturbation method:** Initial-condition perturbations from the EnKF ensemble, combined with stochastic physics (SPPT, SKEB, SHUM) for model-uncertainty representation
- **Native horizontal resolution:** ~25 km (C384 cubed-sphere grid)
- **Public output grids (live-verified 2026-07-31):**
  - **0.25°** — `pgrb2s` product only, and only to **+240 h**
  - **0.5°** — `pgrb2a` and `pgrb2b` products, full forecast length
- **Vertical levels:** 64 (hybrid sigma-pressure)
- **Forecast length:**
  - **35 days (840 h)** for the 00 UTC cycle (sub-seasonal range) — verified: all 33 prefixes (31 members + mean + spread) run the full 840 h, not a subset
  - **16 days (384 h)** for 06, 12, and 18 UTC cycles
- **Temporal output resolution:** 3-hourly to +240 h, then 6-hourly to the end of the run (verified uniform across `pgrb2a`/`pgrb2b`; `pgrb2s` is 3-hourly throughout its shorter 240 h span)
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Coupled wave model:** WAVEWATCH III ensemble, replacing the previous standalone Global Wave Ensemble — documented separately as [GEFS-Wave](../../../wave_models/global/usa/gefs-wave.md)

> ⚠️ **The 0.25° grid is not the primary output.** Earlier revisions of this entry described 0.25° as "primary output" and 0.5° as an "alternative resolution." Live enumeration shows the opposite emphasis: the 0.25° `pgrb2s` product carries only **38 records per step and stops at +240 h**, while the full-field `pgrb2a` (85 records) and `pgrb2b` (505 records) products are **0.5° only** and run the full length. `pgrb2s` is a small curated surface subset, not a high-resolution version of the full output.

---

## Initial conditions
GEFS is initialized from **6-hour EnKF forecasts** drawn from the **same 80-member EnKF ensemble** that informs the GFS hybrid 4DEnVar analyses (see [GDAS in the GFS entry](../../../nwp_models/global/usa/gfs.md#data-assimilation)).

The initial condition files are **publicly distributed** — see *The `init/` stream* below.

---

## What it provides
Probabilistic global forecasts of:
- Temperature, wind, pressure, and humidity (surface and upper-air)
- Precipitation amount, rate, and type, with probability-of-exceedance guidance derivable from the member spread
- Cloud cover, convective indices, and boundary-layer diagnostics
- Aerosol fields (from the separate GEFS-Aerosols `chem/` run)
- Wave height, period, and direction (from the coupled WAVEWATCH III ensemble)
- Ensemble mean (`geavg`) and spread (`gespr`) as pre-computed products alongside the raw members

### Product inventory

Live-verified against `gefs.20260731/` on 2026-07-31. "Extended" = 00 UTC (840 h); "standard" = 06/12/18 UTC (384 h).

**`atmos/` — the ensemble proper**

| Directory | Grid | Prefixes | Records/step | Steps (ext / std) | Size/step | Volume (ext cycle) |
|---|---|---|---|---|---|---|
| `pgrb2sp25` | 0.25° | 33 (31 members + `geavg` + `gespr`) | 38 | 81 / 81 (**capped at +240 h**) | 17.8 MB | 47.6 GB |
| `pgrb2ap5` | 0.5° | 33 | 85 | 181 / 105 | 12.3 MB | 88.4 GB |
| `pgrb2bp5` | 0.5° | **31 (members only — no mean or spread)** | 505 | 181 / 105 | 97.7 MB | 548.9 GB |
| `init` | native cubed-sphere | 31 members × 13 files | — | 1 (analysis) | — | 121.8 GB |

**`chem/` — GEFS-Aerosols, a single unperturbed run**

| Directory | Grid | Files | Records/step | Steps | Size/step |
|---|---|---|---|---|---|
| `pgrb2ap25` | 0.25° | `gefs.chem.tCCz.a2d_0p25.fFFF.grib2` | 32 | 41, 3-hourly to **+120 h** | 23.3 MB |
| `pgrb2ap5` | 0.5° | `gefs.chem.tCCz.a3d_0p25.fFFF.grib2` | — | 41, 3-hourly to +120 h | 179.8 MB |

`chem/` is **not** a 32nd ensemble member in any usable sense — it is a single deterministic aerosol run with no member dimension, its own file-naming scheme, a 5-day cap, and a different directory. Note the naming inconsistency: the file in `pgrb2ap5/` is named `a3d_0p25` despite sitting in the 0.5° directory.

**`wave/`** — `gridded/` and `station/`, documented in [GEFS-Wave](../../../wave_models/global/usa/gefs-wave.md).

**Total volume: ≈ 2.64 TB per day** (115,708 objects) — 864 GB for the 00 UTC extended cycle and ~593 GB for each of the other three. `pgrb2bp5` alone is 549 GB of the extended cycle. This makes GEFS by a wide margin the largest single dataset in this catalog, roughly 2.4× [HRRR](../../../nwp_models/regional/usa/hrrr.md#product-inventory)'s daily volume.

### The `init/` stream

`atmos/init/{c00,p01…p30}/` holds the **initial conditions for every member** as NetCDF: `gfs_ctrl.nc` (7.5 KB) plus `gfs_data.tile1–6.nc` and `sfc_data.tile1–6.nc` (580 MB each) on the native cubed-sphere tiles. 403 objects, **121.8 GB per cycle**, at every one of the four cycles.

This makes the full 31-member cubed-sphere initial state publicly available — unusual, and comparable to the [HRRRDAS ensemble backgrounds](../../../nwp_models/regional/usa/hrrr.md#5-the-nwges-guess-streams--hrrrdas-ensemble-backgrounds) in the HRRR buckets. It is DA/model-input machinery rather than forecast output, but it is gridded and openly distributed. Present on all three clouds.

---

## Relationship to other models
- **[GFS](../../../nwp_models/global/usa/gfs.md):** Deterministic counterpart, sharing the FV3 dynamical core and GDAS analysis infrastructure
- **[NAEFS](./naefs.md):** Multi-center ensemble combining GEFS with Canadian GEPS and (for the 557th WW GEPS) FNMOC NAVGEM ensemble; provides bias-corrected probabilistic guidance across North America
- **[557th WW GEPS](./557wg-geps.md):** U.S. Air Force statistical multi-model ensemble that uses GEFS as one of its three contributing single-center ensembles
- **[HGEFS](./hgefs.md):** Hybrid grand ensemble combining the 31 GEFS members with 31 [AIGEFS](./aigefs.md) AI-based members, providing a 62-member physics-plus-AI ensemble
- **[AIGEFS](./aigefs.md):** AI-based ensemble counterpart trained to emulate GEFS behavior
- **[GEFS-Wave](../../../wave_models/global/usa/gefs-wave.md):** The one-way-coupled WAVEWATCH III ensemble in the `wave/` subtree
- **[GFSv17](../../../nwp_models/global/usa/gfs.md#upcoming-changes) and GEFSv13:** Co-developed; GEFSv13 will be the ensemble counterpart to the proposed coupled Earth-system GFSv17

---

## Reforecasts
A **30-year GEFSv12 reforecast** dataset supports calibration and statistical post-processing, and is distributed from its own AWS bucket:

- **S3 bucket:** `s3://noaa-gefs-retrospective/` (region `us-east-1`)
- **Registry:** https://registry.opendata.aws/noaa-gefs-reforecast/
- **Layout:** `GEFSv12/reforecast/YYYY/YYYYMMDDHH/{c00,p01…p04}/{Days:1-10,Days:10-16}/<var>_<level>_<date>_<member>.grib2`

Live-verified structure (2026-07-31):
- **21 year-directories, 2000–2020**, one initialization per day at 00 UTC (366 dates in 2000, 365 in 2019)
- **5 members** per date (`c00` + `p01`–`p04`) — not the 31 of the real-time system
- Split into two lead-time blocks, `Days:1-10` and `Days:10-16`
- Organized **per-variable**, not per-step (e.g. `apcp_sfc_2000010100_c00.grib2`, 27 MB), each with a `.idx` sidecar

> ⚠️ **The `2020/` directory contains a single misfiled date.** It holds only `2010070100/` — a 2010 initialization sitting under the 2020 year prefix. Anyone enumerating by year will find 2020 effectively empty and may silently lose that date. Whether real 2020 reforecasts exist elsewhere is unconfirmed (**TBD**).

> Note the **colon in the `Days:1-10` path component**. This is legal in S3 keys but requires URL-encoding in HTTP requests and breaks naive local-filesystem mirroring on Windows.

The reforecast underpins many calibrated ensemble products including those in the [NAEFS](./naefs.md) bias-correction system.

---

## Data availability

- **Is the data free?** Yes (no registration, no API key)
- **License:** **Public domain (U.S. government work; CC0-equivalent).** Distributed via NOAA Open Data Dissemination (NODD): data are open to the public and may be used as desired. NOAA requests attribution, prohibits stating or implying NOAA endorsement or affiliation, and requires that modified data not be presented as unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 with `.idx` index sidecars (forecast fields); NetCDF (the `init/` stream); gzipped TAR and BUFR (soundings, NOMADS only)

### Distribution channels

**1. NOMADS (NCEP operational distribution) — real-time, 4-day rolling window**

```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/gens/prod/
  gefs.YYYYMMDD/{00,06,12,18}/{atmos,chem,wave}/
    atmos/{bufr,init,pgrb2ap5,pgrb2bp5,pgrb2sp25}/
```

Retention observed on 2026-08-01: **4 days** (`gefs.20260729` through `gefs.20260801`).

> **NOMADS carries a `bufr/` subtree that no cloud mirror has.** Under `atmos/bufr/` sit per-member `gec00/`, `p01/`–`p30/`, and `avg/` directories of exploded station soundings, alongside 32 bundled `ge{c00,p01…p30,avg}.tCCz.bufrsnd.tar.gz` files (~76 MB each) and an `ls-l` manifest. **None of it exists on AWS, Azure, or GCS** — all three show only `init/`, `pgrb2ap5/`, `pgrb2bp5/`, and `pgrb2sp25/` under `atmos/`, and `ls-l` returns HTTP 404 on each.
>
> With a 4-day NOMADS window and no cloud copy, **GEFS station soundings are effectively unarchived** — the same pattern documented for [RAP](../../../nwp_models/regional/usa/rap.md#distribution-channels), and the inverse of [HRRR](../../../nwp_models/regional/usa/hrrr.md#3-google-cloud-storage-nodd--real-time--identical-deep-archive), where GCS is the only archival route to them.

> Both the OPeNDAP (`/dods/`) and FTPPRD (`ftpprd.ncep.noaa.gov`, `ftp.ncep.noaa.gov`) routes were retired on **23 February 2026** under SCN 25-81 and SCN 25-82 respectively. This matters for [NAEFS](./naefs.md), whose entry still lists the `nomads.ncep.noaa.gov/dods/gens_bc` OPeNDAP endpoint for bias-corrected GEFS.

**2. AWS Open Data (NODD) — real-time + the deepest archive**

- **S3 bucket:** `s3://noaa-gefs-pds/` — ARN `arn:aws:s3:::noaa-gefs-pds`, region **`us-east-1`**
- **Browser access:** https://noaa-gefs-pds.s3.amazonaws.com/index.html
- **AWS CLI (no account required):** `aws s3 ls --no-sign-request s3://noaa-gefs-pds/`
- **SNS new-object notifications:** `arn:aws:sns:us-east-1:123901341784:NewGEFSObject`
- **Registry:** https://registry.opendata.aws/noaa-gefs/

Archive: `gefs.YYYYMMDD/` from **2017-01-01** to present — **3,500 directories with zero calendar gaps** across the full span. Note that the pre-2020-09-23 portion is GEFSv11 at a different resolution and directory layout.

**3. Google Cloud Storage (NODD) — real-time + GEFSv12-era archive**

- **Bucket:** `gs://gfs-ensemble-forecast-system/` — anonymous object read and JSON-API listing; storage class `STANDARD`
- **HTTPS object access:** `https://storage.googleapis.com/gfs-ensemble-forecast-system/gefs.YYYYMMDD/…`
- **Marketplace page:** https://console.cloud.google.com/marketplace/product/noaa-public/gfs-ensemble-forecast-system

Archive: **2,137 directories from 2020-09-25** — i.e. starting two days after the GEFSv12 implementation, so the GCS archive is **GEFSv12-only by construction**. That is arguably cleaner than AWS's mixed-version archive for anyone who wants a single-version dataset, but it means the 2017–2020 GEFSv11 period is AWS-only.

> ⚠️ **GCS is missing 315 `.idx` sidecars per cycle**, all in the `wave/gridded/` subtree — specifically the `mean`, `spread`, and `prob` derived products (105 each). Every GRIB2 data file is present, and the `atmos/` and `chem/` trees are complete. The same systematic-missing-`.idx` pattern documented for [RAP](../../../nwp_models/regional/usa/rap.md#4-google-cloud-storage-nodd--real-time--near-complete-archive) on GCS. Byte-range subsetting of the wave derived products will not work there.

**4. Microsoft Azure (NODD) — real-time, 90-day rolling window**

- **Blob container:** `https://noaagefs.blob.core.windows.net/gefs` — public, anonymous, no SAS token required
- **Read-only SAS token API (for BlobFuse mounts):** `https://planetarycomputer.microsoft.com/api/sas/v1/token/noaagefs/gefs`
- **Documentation:** https://microsoft.github.io/AIforEarthDataSets/data/noaa-gefs.html
- **Planetary Computer dataset page:** https://planetarycomputer.microsoft.com/dataset/storage/noaa-gefs

Live window on 2026-08-01: **92 days** (`gefs.20260502/` → `gefs.20260801/`) — the same ~90-day pattern as the Azure [GFS](../../../nwp_models/global/usa/gfs.md#data-availability), [RAP](../../../nwp_models/regional/usa/rap.md#3-microsoft-azure-nodd--real-time-90-day-rolling-window), and [CFSv2](../../../climate_models/global/usa/cfsv2.md#data-availability) containers.

> **Within its window, Azure is the most complete of the three mirrors** — object counts match AWS exactly (36,223 for the 00 UTC cycle), including all the `.idx` sidecars GCS lacks.

> ⚠️ **Azure enumeration gotcha.** As with the Azure GFS container, a single un-paginated list request for `prefix=gefs.` returns **zero results with a `NextMarker`** — it looks exactly like an absent stream. Follow `NextMarker` to exhaustion before concluding a prefix is empty.

**5. NCEI — long-term archive**

- https://www.ncei.noaa.gov/products/weather-climate-models/global-ensemble-forecast

### Cross-cloud equivalence

The GRIB2 forecast files are **byte-identical** across all three clouds. For `gefs.20260731/00/atmos/pgrb2sp25/geavg.t00z.pgrb2s.0p25.f003`:

| Cloud | Size | Checksum |
|---|---|---|
| AWS | 18,153,127 | ETag `9c2353fdc66b9acfdf2a8b46728358d7` |
| Azure | 18,153,127 | Content-MD5 `nCNT/cZrms/fKotGcoNY1w==` |
| GCS | 18,153,127 | md5Hash `nCNT/cZrms/fKotGcoNY1w==` |

All three serve **anonymous HTTP 206 byte-range requests** with `GRIB` magic at byte 0.

**Choose on:** AWS for the full 2017-onward archive or SNS notifications; GCS for a clean GEFSv12-only archive, provided you don't need wave-product indexes; Azure for the last ~90 days, where it is the most complete mirror.

> **Byte-range retrieval matters more here than almost anywhere else in the catalog.** A single `pgrb2b` step file is ~98 MB with 505 records, and a full 00 UTC cycle across all 31 members is 549 GB for that product alone. Pulling one parameter across all members via the `.idx` offsets is the difference between megabytes and hundreds of gigabytes.

---

## Notes
- GEFS uses the same FV3 dynamical core and physics suite as GFSv15, but at coarser resolution (C384 vs C768) and with stochastic physics added. The 64-level vertical structure is also coarser than the deterministic GFS's 127 levels.
- The 35-day forecast range at 00 UTC was a major v12 enhancement, motivated by NOAA's growing emphasis on sub-seasonal-to-seasonal (S2S) prediction. The other three cycles remain at 16 days because the additional sub-seasonal members are computationally expensive. Verified: at 00 UTC **all 33 prefixes** run to 840 h — the extension is not restricted to a member subset.
- GEFSv12 was the **first global-scale coupled forecast system at NCEP** under the Unified Forecast System (UFS) framework, deployed in September 2020 — predating the FV3 transition for the deterministic GFS by about nine months (GFSv16 transitioned in March 2021).
- **`pgrb2b` has no mean or spread.** Only `pgrb2a` and `pgrb2s` carry `geavg`/`gespr`. Users needing ensemble statistics for the extended `pgrb2b` parameter set must compute them from the 31 members themselves.
- **Grid and length are coupled to product choice, not freely selectable.** There is no 0.25° full-field product and no 0.5° product limited to 240 h. The three atmospheric products are three fixed (grid, field-set, length) combinations.
- **Version boundary in the archive.** The AWS bucket spans GEFSv11 (2017-01-01 → 2020-09-22) and GEFSv12 (2020-09-23 onward) with different resolutions, member configurations, and directory layouts. Anything crossing that boundary must handle both. GCS sidesteps this by starting after the transition.

---

## Recent version history

### GEFSv12 — operational 23 September 2020 (current)
- Transition to the **FV3 dynamical core** under the UFS framework — the first global-scale UFS-based system at NCEP
- Ensemble size increased to 31 members
- Horizontal resolution increased to C384 (~25 km); 64 vertical levels
- Forecast range extended to **35 days at the 00 UTC cycle** for sub-seasonal guidance
- Wave ensemble unified into GEFS as a one-way-coupled WW3 component, retiring the standalone Global Wave Ensemble System (GWES)
- GEFS-Aerosols introduced as a separate GOCART-coupled run
- Accompanied by the 30-year GEFSv12 reforecast dataset

### Upcoming — GEFSv13
Under development for joint deployment with [GFSv17](../../../nwp_models/global/usa/gfs.md#upcoming-changes) (proposed for October 2026). As with GFSv17, no formal Service Change Notice has been issued; product paths and the product set may change, and this entry's inventory should be re-verified if that upgrade goes operational.

---

## Official documentation
- GEFS documentation (EMC): https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/gefs.php
- GEFS product inventory (NCO): https://www.nco.ncep.noaa.gov/pmb/products/gens/
- NOMADS help and GRIB filter migration guide: https://nomads.ncep.noaa.gov/info.php?page=help
- NCEI archive: https://www.ncei.noaa.gov/products/weather-climate-models/global-ensemble-forecast
- AWS Open Data registry: https://registry.opendata.aws/noaa-gefs/
- AWS reforecast registry: https://registry.opendata.aws/noaa-gefs-reforecast/
- Azure / AIforEarthDataSets: https://microsoft.github.io/AIforEarthDataSets/data/noaa-gefs.html
- Google Cloud Marketplace: https://console.cloud.google.com/marketplace/product/noaa-public/gfs-ensemble-forecast-system
- Zhou et al. (2022), *The Development of the NCEP Global Ensemble Forecast System Version 12*, Wea. Forecasting: https://doi.org/10.1175/WAF-D-21-0112.1
