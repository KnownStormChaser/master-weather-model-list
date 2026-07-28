# GFS (CWA Redistribution – Taiwan)

## What this model is
The Central Weather Administration (CWA) of Taiwan redistributes NOAA's Global Forecast System (GFS) through its national open data programme. The data itself is produced by NOAA/NCEP; CWA mirrors it on an AWS S3 bucket hosted in the `ap-northeast-1` region for use within Taiwan and by regional downstream users.

CWA distributes it under the open-data product code **M-A0060**. Note that this is a **reduced subset** of NOAA's operational 0.25° product, not a full mirror — see *What is actually carried* below.

This entry documents the redistribution. For model configuration, physics, and authoritative documentation, see the primary [GFS entry](../../global/usa/gfs.md).

---

## Who runs it
- **Source model operator:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Redistributor:** Central Weather Administration
- **Country / region:** Taiwan (redistribution); United States (source)

---

## What area it covers
- **Coverage:** Global
- **Primary area of use:** Taiwan and surrounding regions of East Asia

---

## Basic details
- **Model type:** Deterministic global NWP (redistributed)
- **Product code:** `M-A0060`
- **Horizontal resolution:** 0.25° (1440 × 721, regular latitude–longitude, GRIB2 Grid Definition Template 3.0)
- **Grid orientation:** first point 90.0°N / 0.0°E, scanning mode `0x00` — west to east, then **north to south**. This is NOAA's native orientation and is the **opposite** meridional order from CWA's own WRF products, which scan south to north.
- **Forecast length:** 384 hours (16 days), 52 files per cycle
- **Temporal output resolution:** **6-hourly to T+240, then 12-hourly to T+384.** The **T+252 step is absent** — the sequence jumps 240 → 264. (Live-verified against the bucket listing; the previously documented uniform 6-hourly cadence was incorrect.)
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC), inherited from NOAA GFS
- **Messages per file:** 94
- **File size:** ~61 MB per step, ~3.1 GB per cycle
- **Target delivery time:** ~T+4 h 40 m for the earliest steps (live-verified: the 2026-07-28 18 UTC cycle began publishing at 22:41 UTC)
- **Archive availability:** **None** — see *Data availability*

For the full technical specification of the source model, see the [GFS entry](../../global/usa/gfs.md).

---

## What is actually carried

CWA does **not** mirror NOAA's full `pgrb2.0p25` product. Each file holds **94 messages at ~61 MB**, against roughly 700 messages and ~500 MB in the NOAA original — a small, forecaster-oriented subset. Live-verified inventory (T+24, 2026-07-28 18 UTC):

**16 pressure levels** — 1000, 925, 850, 700, 500, 400, 300, 250, 200, 150, 100, 70, 50, 30, 20, 10 hPa — carrying:
- Temperature (`t`)
- Geopotential height (`gh`)
- Relative humidity (`r`)
- U / V wind components (`u`, `v`)

**Surface and near-surface:**
- Mean sea level pressure (`msl`)
- Surface (skin) temperature
- 2 m temperature and relative humidity (`2t`, `2r`)
- 10 m U / V wind (`10u`, `10v`) and relative humidity at 10 m
- Low cloud cover (`lcc`)
- Total precipitation — **two messages**, a run-total accumulation (`stepRange = 0-24`) *and* a 6-hour bucket (`stepRange = 18-24`)

**Lowest hybrid model level (level 1):** temperature, relative humidity, U / V wind

Notably **absent** relative to the NOAA product: vertical velocity, specific humidity, CAPE/CIN and other convective diagnostics, total and mid/high cloud cover, radiation fluxes, soil fields, and precipitation type. Users needing any of these must go to NOAA directly.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **File naming:** `M-A0060-{SSS}.grb2`, where `{SSS}` is the zero-padded forecast hour (`000`, `006`, … `240`, `264`, `276`, … `384`). Each file has `.json` and `.xml` metadata sidecars carrying `InitialTime` and grid geometry.
- **No archive — latest-only.** Every key is **fixed and overwritten in place** each cycle; no date partitioning, no cycle token in the path, no S3 versioning. NOAA's own `s3://noaa-gfs-bdp-pds/` is date-partitioned and should be preferred by anyone needing more than the current run.
- **Official download location:**
  - AWS Open Data Registry: https://registry.opendata.aws/cwa_opendata/
  - S3 bucket: `s3://cwaopendata/Model/`
  - AWS region: `ap-northeast-1`
  - CLI: `aws s3 ls --no-sign-request s3://cwaopendata/Model/`
  - Direct HTTPS: `https://cwaopendata.s3.ap-northeast-1.amazonaws.com/Model/M-A0060-000.grb2`
- **License:** Taiwan Open Government Data License — https://data.gov.tw/en/license. Note this is CWA's licence on the redistribution; the underlying GFS output is a U.S. Government work in the public domain.

---

## Notes
- This is a **redistribution** of NOAA GFS, not an independently run global model. CWA does not operate its own global NWP system.
- For users outside Taiwan, it is usually preferable to access GFS directly from NOAA NOMADS or the primary NOAA AWS bucket (`s3://noaa-gfs-bdp-pds/`). The case for going to NOAA is stronger than region alone: the CWA copy is a 94-message subset with no archive, while NOAA's is the complete product with full date partitioning.
- The CWA mirror is most useful for users in the Asia-Pacific region who want to co-locate global and CWA regional (WRF) model access in the same AWS region (`ap-northeast-1`), and whose variable needs are covered by the subset above.

- ⚠️ **Mixed-cycle hazard — check `InitialTime` on every file.** Each step is an independently overwritten fixed key, so while a run is publishing the bucket holds files from **two different cycles simultaneously**. Live-verified at 23:09 UTC on 2026-07-28: `M-A0060-000` through `-096` carried the 18 UTC initialization while `-144` through `-384` were still the previous 12 UTC run. Verify that every file reports the same `dataDate`/`dataTime` (GRIB2 Section 1) or the same sidecar `InitialTime` before use, and re-poll until consistent. This applies identically to CWA's WRF products — see [CWA Taiwan Regional WRF](./cwa-regional.md).

- **`shortName` does not resolve for precipitation.** With eccodes 2.48.0 both accumulation messages decode as `shortName`, `name`, and `units` equal to `unknown`. Identify them by `discipline = 0`, `parameterCategory = 1`, `parameterNumber = 8` (WMO GRIB2 Code Table 4.2-0-1 entry 8, *Total precipitation*, kg m⁻²), then separate the run-total from the 6-hour bucket using `stepRange` / `lengthOfTimeRange`.
- Comparable redistribution entries in this repository: [GFS (IDEAM, Colombia)](../colombia/gfs-ideam.md).
- Other CWA products in the same bucket and under the same licence: [CWA Taiwan Regional WRF](./cwa-regional.md) (`M-A0061` / `M-A0064`, GRIB2) and [CWA OCM](../../../ocean_models/regional/taiwan/cwa-ocm.md) (`M-B0071`, NetCDF). The latest-only retention and mixed-cycle caveats apply across all of them.

---

## Official documentation
- CWA developer documentation: https://opendata.cwa.gov.tw/devManual/insrtuction
- Primary GFS documentation: see the [GFS entry](../../global/usa/gfs.md)
