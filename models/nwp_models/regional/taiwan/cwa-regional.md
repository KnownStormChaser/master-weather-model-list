# CWA Taiwan Regional WRF (15 km & 3 km)

## What this model is
A regional numerical weather prediction system run by Taiwan's Central Weather Administration on the WRF model, providing short-range forecasts of temperature, wind, precipitation, humidity, and pressure over East Asia and the western Pacific.

It is distributed in two configurations, each carrying its own CWA open-data product code: a broad **15 km** domain (`M-A0061`) spanning the tropics to the mid-latitudes across the western Pacific, and a higher-resolution **3 km** domain (`M-A0064`) focused on Taiwan and the surrounding seas. The 3 km configuration is convection-allowing and is particularly useful for typhoons, heavy rainfall, and complex terrain effects.

The two are **separate products sharing one S3 directory**, not two resolutions of a single product. Live-verified as carrying an identical parameter set, level set, projection family, cycle schedule, and forecast length — they differ only in grid spacing and domain extent. CWA publishes a product reference PDF for `M-A0061` but **not** for `M-A0064`.

---

## Who runs it
- **Organization:** Central Weather Administration (CWA)
- **Country / region:** Taiwan

---

## What area it covers
- **Coverage:** East Asia and the western Pacific — including the South China Sea, the Philippine Sea, and Japan-adjacent waters
- **Domain details:**
  - **15 km domain** — 661 × 385 grid points. Corner (1,1) at **5.693677°S, 78.02554°E** to corner (661,385) at **43.28705°N, 179.5461°W**; the domain spans the equator and crosses the dateline.
- **3 km domain** — 1158 × 673 grid points (779,334 points). Corner (1,1) at **14.02224°N, 105.2500°E** to corner (1158,673) at **32.12021°N, 140.91388°E** (Taiwan, the northern South China Sea, and the adjacent western Pacific).
  - Distributed on a **Lambert conformal** grid — GRIB2 Grid Definition Template **3.30**, live-verified in both products. Projection parameters are identical for the two domains:
    - **LoV** (orientation meridian): 120.0°E
    - **LaD** (reference latitude): 10.0°N
    - **Latin1 / Latin2** (standard parallels): 10.0°N / 40.0°N
    - **Dx / Dy:** exactly 15000 m (M-A0061) and 3000 m (M-A0064) — the "~15 km" and "~3 km" labels are exact, not nominal
    - **Scanning mode:** `0x40` — +i (west to east), +j (south to north)
  - Because the projection is Lambert conformal, the corner coordinates above do **not** imply constant degree spacing; reproject via the GRIB2 grid definition rather than interpolating between corners.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Product codes:** `M-A0061` (15 km), `M-A0064` (3 km)
- **Model system / core:** WRF (the specific dynamical core — ARW vs NMM — is not stated in the M-A0061 product reference)
- **Dynamical formulation:** Non-hydrostatic (WRF)
- **Convection-allowing:**
  - **15 km:** No (deep convection parameterized)
  - **3 km:** Yes (≤4 km grid; deep convection explicitly resolved)
- **Horizontal resolution:** ~15 km (broad domain) and ~3 km (high-resolution domain)
- **Grid dimensions:** 661 × 385 (15 km); 1158 × 673 (3 km) — see *What area it covers*
- **Vertical levels:** TBD (output is provided on 11 standard pressure levels: 1000, 925, 850, 700, 500, 400, 300, 250, 200, 150, 100 hPa)
- **Forecast length:** 84 hours (15 files per cycle, T+0 to T+84)
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 6-hourly (uniform across the whole range)
- **Messages per file:** 78, identical for both domains and at every step
- **File size:** ~59 MB per step (15 km, ~0.89 GB per cycle); ~179 MB per step (3 km, ~2.7 GB per cycle)
- **Target delivery time:** first steps appear ~T+4 h 50 m; the full 84 h set completes ~T+6 h. (Live-verified: the 2026-07-28 12 UTC cycle finished publishing at 18:03 UTC for 15 km and 18:06 UTC for 3 km.)
- **Archive availability:** **None** — see *Data availability*

---

## Initial and boundary conditions
- **Initial conditions:** TBD
- **Boundary conditions:** TBD (regional WRF configurations are typically driven by a global model; the driving model is not stated in the M-A0061 product reference)

---

## What it provides
Deterministic forecasts on **11 pressure levels (1000–100 hPa)** of:
- Temperature
- Geopotential height
- U / V horizontal wind components
- Vertical velocity (geometric, dz/dt)
- Relative humidity

Surface and near-surface fields:
- Surface (terrain) pressure and mean sea level pressure
- Total precipitation
- 2 m temperature, dew point, specific humidity, and relative humidity
- 10 m U / V wind components
- Skin (ground / terrain surface) temperature, and sea-level temperature (labelled "SST" in the product reference)
- Net shortwave (solar) flux at the surface (positive downward)

---

## Data availability
- **Is the data free?** Yes
- **License:** Taiwan Open Government Data License — https://data.gov.tw/en/license
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **File naming:** `M-A0061-{SSS}.grb2` and `M-A0064-{SSS}.grb2`, where `{SSS}` is the zero-padded forecast hour in 6-hour increments (`000`, `006`, … `084`). Each file has `.json` and `.xml` metadata sidecars carrying `InitialTime`, grid geometry, and the issue timestamp.
- **No archive — latest-only.** Every key is **fixed and overwritten in place** each cycle. There is no date partitioning, no cycle token in the path, and no S3 versioning. Building a hindcast or verification series requires the user to poll and retain their own copies.
- **Official download location:**
  - data.gov.tw dataset: https://data.gov.tw/en/datasets/58977
  - AWS Open Data Registry: https://registry.opendata.aws/cwa_opendata/
  - S3 bucket: `s3://cwaopendata/Model/`
  - AWS region: `ap-northeast-1`
  - CLI: `aws s3 ls --no-sign-request s3://cwaopendata/Model/`
  - Direct HTTPS: `https://cwaopendata.s3.ap-northeast-1.amazonaws.com/Model/M-A0061-000.grb2`

---

## Notes
- **Convection handling:** the 3 km configuration is convection-allowing and is particularly useful for typhoons, heavy rainfall, and complex terrain; the 15 km configuration relies on parameterized convection.
- **Grid scanning order:** data are ordered west-to-east along each row, then south-to-north (i.e. the GRIB2 scan starts at the south-west corner). Confirmed from the GRIB2 scanning-mode flag (`0x40`) as well as the sidecar `DataDirection` field.

- ⚠️ **Mixed-cycle hazard — check `InitialTime` on every file.** Because each step is a separate fixed key overwritten independently, the bucket routinely holds files from **two different cycles at once** while a run is publishing. Live-verified at 23:09 UTC on 2026-07-28: `M-A0061-000/006/012` carried the 18 UTC initialization while `M-A0061-018` through `-084` were still the previous 12 UTC run; the 3 km product had only `-000` updated. A naive "fetch all 15 files" therefore yields a forecast silently spliced from two runs, with a discontinuity at the join. Before use, verify that every file reports the same `dataDate`/`dataTime` (GRIB2 Section 1) or the same sidecar `InitialTime`, and re-poll until consistent.

- **Precipitation is accumulated from initialization, not per period.** The total precipitation field uses Product Definition Template **4.8** with `typeOfStatisticalProcessing = 1` (accumulation) and a time range spanning the whole forecast so far — live-verified as `stepRange = 0-12` at T+12 and `0-84` at T+84. To obtain 6-hourly amounts, difference consecutive files. This is a run-total field, so **differencing across a mixed-cycle boundary produces meaningless values** — see the hazard note above.

- **`shortName` does not resolve for precipitation.** With eccodes 2.48.0 the accumulation message decodes as `shortName`, `name`, and `units` all equal to `unknown`. Identify it by `discipline = 0`, `parameterCategory = 1`, `parameterNumber = 8` — WMO GRIB2 Code Table 4.2-0-1 entry 8, *Total precipitation* (kg m⁻²). All other 77 messages resolve normally.

- **Parameter sets are identical across domains and steps.** Live-verified: 78 messages in every file of both `M-A0061` and `M-A0064`, at every forecast hour — 11 pressure levels × 6 upper-air fields (`t`, `gh`, `u`, `v`, `r`, `wz`) plus 12 surface and near-surface fields (`sp`, `prmsl`, `2t`, `2d`, `2sh`, `2r`, `10u`, `10v`, `skt`, `t` at mean sea level, `snswrf`, and the unresolved precipitation message). Code written against one domain works unchanged on the other.
- **Plotting caveat:** because the 15 km domain spans the equator (and crosses the dateline), tools such as GrADS / OpenGrADS may misrender it; manual adjustment may be required.
- **No decoder provided:** CWA distributes raw GRIB2 and does not supply a decoder. NCEP's `wgrib2` is the usual choice — https://www.cpc.ncep.noaa.gov/products/wesley/wgrib2/
- CWA distributes operational model data through the AWS S3 bucket in `ap-northeast-1` as part of Taiwan's national open-data programme. Older references to `data.gov.tw` point to the same underlying CWA open-data system.
- Related entries: [GFS (CWA Redistribution – Taiwan)](./gfs-cwa.md) for CWA's GFS mirror in the same AWS region; [WRF-SMN Argentina](../argentina/wrf-smn.md) for a comparable national WRF system.

---

## Official documentation
- CWA product reference (M-A0061, 15 km domain): https://opendata.cwa.gov.tw/opendatadoc/Model/M-A0061.pdf
- CWA product reference (M-A0064, 3 km domain): **none published** — `https://opendata.cwa.gov.tw/opendatadoc/Model/M-A0064.pdf` returns 404. The M-A0061 reference is the closest available documentation; the 3 km specifics here are live-verified from the GRIB2 files and metadata sidecars.
- CWA developer documentation: https://opendata.cwa.gov.tw/devManual/insrtuction
- CWA open data portal: https://opendata.cwa.gov.tw/
