# CWA Taiwan Regional WRF (15 km & 3 km)

## What this model is
A regional numerical weather prediction system run by Taiwan's Central Weather Administration on the WRF model, providing short-range forecasts of temperature, wind, precipitation, humidity, and pressure over East Asia and the western Pacific.

It is distributed in two configurations: a broad **15 km** domain spanning the tropics to the mid-latitudes across the western Pacific, and a higher-resolution **3 km** domain focused on Taiwan and the surrounding seas. The 3 km configuration is convection-allowing and is particularly useful for typhoons, heavy rainfall, and complex terrain effects.

---

## Who runs it
- **Organization:** Central Weather Administration (CWA)
- **Country / region:** Taiwan

---

## What area it covers
- **Coverage:** East Asia and the western Pacific — including the South China Sea, the Philippine Sea, and Japan-adjacent waters
- **Domain details:**
  - **15 km domain** — 661 × 385 grid points. Corner (1,1) at **5.693677°S, 78.02554°E** to corner (661,385) at **43.28705°N, 179.5461°W**; the domain spans the equator and crosses the dateline.
  - **3 km domain** — 1158 × 673 grid points. Corner (1,1) at **14.02224°N, 105.2500°E** to corner (1158,673) at **32.12021°N, 140.91388°E** (Taiwan, the northern South China Sea, and the adjacent western Pacific).
  - Distributed on a Lambert conformal grid.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** WRF (the specific dynamical core — ARW vs NMM — is not stated in the M-A0061 product reference)
- **Dynamical formulation:** Non-hydrostatic (WRF)
- **Convection-allowing:**
  - **15 km:** No (deep convection parameterized)
  - **3 km:** Yes (≤4 km grid; deep convection explicitly resolved)
- **Horizontal resolution:** ~15 km (broad domain) and ~3 km (high-resolution domain)
- **Grid dimensions:** 661 × 385 (15 km); 1158 × 673 (3 km) — see *What area it covers*
- **Vertical levels:** TBD (output is provided on 11 standard pressure levels: 1000, 925, 850, 700, 500, 400, 300, 250, 200, 150, 100 hPa)
- **Forecast length:** Up to 84 hours
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 6-hourly

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
- **Official download location:**
  - data.gov.tw dataset: https://data.gov.tw/en/datasets/58977
  - AWS Open Data Registry: https://registry.opendata.aws/cwa_opendata/
  - S3 bucket: `s3://cwaopendata/Model/`
  - AWS region: `ap-northeast-1`
  - CLI: `aws s3 ls --no-sign-request s3://cwaopendata/Model/`

---

## Notes
- **Convection handling:** the 3 km configuration is convection-allowing and is particularly useful for typhoons, heavy rainfall, and complex terrain; the 15 km configuration relies on parameterized convection.
- **Grid scanning order:** data are ordered west-to-east along each row, then south-to-north (i.e. the GRIB2 scan starts at the south-west corner).
- **Plotting caveat:** because the 15 km domain spans the equator (and crosses the dateline), tools such as GrADS / OpenGrADS may misrender it; manual adjustment may be required.
- **No decoder provided:** CWA distributes raw GRIB2 and does not supply a decoder. NCEP's `wgrib2` is the usual choice — https://www.cpc.ncep.noaa.gov/products/wesley/wgrib2/
- CWA distributes operational model data through the AWS S3 bucket in `ap-northeast-1` as part of Taiwan's national open-data programme. Older references to `data.gov.tw` point to the same underlying CWA open-data system.
- Related entries: [GFS (CWA Redistribution – Taiwan)](./gfs-cwa.md) for CWA's GFS mirror in the same AWS region; [WRF-SMN Argentina](../argentina/wrf-smn.md) for a comparable national WRF system.

---

## Official documentation
- CWA product reference (M-A0061, this model): https://opendata.cwa.gov.tw/opendatadoc/Model/M-A0061.pdf
- CWA developer documentation: https://opendata.cwa.gov.tw/devManual/insrtuction
- CWA open data portal: https://opendata.cwa.gov.tw/
