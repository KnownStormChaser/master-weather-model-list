# SILAM Global (FMI Global Air Quality Forecast)

## What this model is
SILAM (System for Integrated modeLling of Atmospheric coMposition) is a global-to-mesoscale atmospheric composition and dispersion model developed and operated by the Finnish Meteorological Institute. It supports air quality forecasting, emergency response (volcanic ash, nuclear accident dispersion), and inverse dispersion problem solutions.

This entry describes **FMI's operational global SILAM air quality forecast**, which is distributed publicly through an AWS Open Data bucket. FMI publishes a global **surface** air-quality forecast of seven species at 0.2° (~20 km) resolution — an unusually fine horizontal resolution for a publicly distributed global air-quality model — as five forecast days (120 hourly steps) through an AWS Open Data bucket. The full multi-level, full-chemistry model output is not on AWS; it is distributed separately via FMI's THREDDS server (see Notes).

SILAM is also one of the 11 constituent models in the CAMS Regional European air quality ensemble (see [CAMS Regional](../../regional/eu/cams-regional.md)). The CAMS Regional contribution is a separate European-domain configuration; this entry documents FMI's independently distributed global forecast.

---

## Who runs it
- **Organization:** Finnish Meteorological Institute (FMI)
- **Country / region:** Finland
- **Distribution partner:** AWS Open Data

---

## What area it covers
- **Coverage:** Global
- **Vertical coverage:** Surface only in the AWS distribution. The public NetCDF/Zarr buckets contain single-level 2D fields (dimensions: time × lat × lon; verified July 2026). The full 3D output (troposphere and stratosphere) is served on the FMI THREDDS server, not AWS.

---

## Basic details
- **Model type:** Global atmospheric composition / air quality
- **Model system / core:** SILAM (multi-scale Eulerian / semi-Lagrangian dispersion model)
- **Model version in AWS distribution:** v6.1 (files named `silam_glob_v6_1_*`; NetCDF `source` attribute `silam_v6_1 SVN (r606254)`, verified July 2026)
- **Horizontal resolution:** 0.2° × 0.2° regular lat/lon (~22 km at the equator; commonly cited as ~20 km), global 1800 × 897 grid (−179.8° to 180.0°E, −89.6° to 89.6°N)
- **Forecast length (AWS):** **120 hourly steps (5 days)**, delivered as five daily files per species (`d0`–`d4`), 24 hourly steps each. (FMI's underlying run may extend further, but only these 5 days appear in the bucket.)
- **Update frequency:** 1× daily
- **Production cadence:** The forecast is initialized at **00Z of the previous day** (not the current day) in order to incorporate observed emissions of transient sources such as wildfires. The resulting forecast is published around **06:00 UTC** the following day.
- **Temporal output resolution:** Hourly

---

## Species and products distributed on AWS
FMI publishes **seven air-quality species** as separate per-species NetCDF files through the AWS Open Data bucket (verified July 2026, v6.1):
- Ozone (O3)
- Nitrogen dioxide (NO2)
- Nitric oxide (NO)
- Sulfur dioxide (SO2)
- Carbon monoxide (CO)
- PM2.5 (file token `PM25`)
- PM10

A supporting field, **air density** (`airdens`), is distributed alongside the species (used to convert between mixing ratio and mass concentration).

All fields are **surface-level 2D** (time × lat × lon), 24 hourly steps per daily file. **No aerosol-optical-thickness (AOT) file and no 3D fields are present** in the current v6.1 surface bucket, despite the AWS registry description mentioning 3D output and AOT — that description characterizes the full model, whose complete output is served on the FMI THREDDS server rather than AWS.

Internally, SILAM simulates more than 100 species across tropospheric and stratospheric chemistry; the AWS distribution is a curated **surface subset** focused on the main regulated air-quality pollutants.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF and Zarr (separate buckets)
- **Licence:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Official download locations:**
  - AWS Open Data Registry: https://registry.opendata.aws/silam/
  - **Surface data (NetCDF):** `s3://fmi-opendata-silam-surface-netcdf/`
    - Browse: http://fmi-opendata-silam-surface-netcdf.s3-website-eu-west-1.amazonaws.com/
    - CLI: `aws s3 ls --no-sign-request s3://fmi-opendata-silam-surface-netcdf/`
  - **Surface data (Zarr):** `s3://fmi-opendata-silam-surface-zarr/`
    - Browse: http://fmi-opendata-silam-surface-zarr.s3-website-eu-west-1.amazonaws.com/
    - CLI: `aws s3 ls --no-sign-request s3://fmi-opendata-silam-surface-zarr/`
- **AWS region:** `eu-west-1`
- **New-data notifications:** AWS SNS topics for both NetCDF and Zarr new-object events (see AWS registry page for ARNs)

### THREDDS access (full global output)
The AWS buckets carry only the curated seven-species surface subset. FMI's **THREDDS Data Server** distributes the *full* global model output — all chemistry variables and vertical levels — alongside a surface-only subset and older-version collections.

- **Server:** https://thredds.silam.fmi.fi/thredds/catalog/catalog.html
- **Global datasets:**
  - `silam_glob_v6_1` — global 0.2°, full chemistry (~528 variables), multi-level (3D)
  - `silam_glob_v6_1_sfc` — global 0.2°, surface-only (the THREDDS counterpart to the AWS surface data)
  - `silam_glob06_v5_8`, `daily_silam_glob_v6_1`, `daily_silam_glob06_v5_8` — older-version / daily global collections
- **Access methods** (verified live, July 2026; no login required):
  - **OPeNDAP** — base `/thredds/dodsC/` — remote subsetting without downloading whole files (e.g. `https://thredds.silam.fmi.fi/thredds/dodsC/silam_glob_v6_1/silam_glob_v6_1_best.ncd`)
  - **NetCDF Subset Service (NCSS)** — base `/thredds/ncss/grid/` — spatial/temporal/variable subset returned as NetCDF (dataset description at `.../thredds/ncss/grid/silam_glob_v6_1/silam_glob_v6_1_best.ncd/dataset.xml`)
  - **HTTPServer** — base `/thredds/fileServer/` — whole-file download of individual run files (~3.35 GB each; native naming `SILAM-AQ-glob_v6_1_<YYYYMMDDHH>_<NNN>.nc4`, sequence `001`–`005` per run)
- **Aggregations:** each dataset exposes a "Best Time Series" virtual file (`<dataset>_best.ncd`) aggregating all runs, plus per-run (`/runs/`) and per-file (`/files/`) catalogs.
- **Licence:** presumed to follow the same FMI CC BY 4.0 terms as the AWS distribution, but **not separately confirmed on the THREDDS server** — verify against the server's Info/licence page before relying on it (open access ≠ open licence).

### File organization

In the **NetCDF** bucket, files are organized by model run date under `/global/<YYYYMMDD>/`, one file per species per forecast day, with the naming convention:

```
silam_glob_v6_1_<YYYYMMDD>_<SPECIES>_d<N>.nc
```

- `<SPECIES>` ∈ `O3`, `NO2`, `NO`, `SO2`, `CO`, `PM25`, `PM10`, `airdens`
- `<N>` = forecast day, `0`–`4` (each daily file holds 24 hourly steps)

Example: `global/20260618/silam_glob_v6_1_20260618_O3_d0.nc` (~39 MB).

The archive is a rolling ~30-day window (e.g. `20260616`–`20260716` as of mid-July 2026). The **Zarr** bucket holds the same surface data as a consolidated store with a separate internal layout (not inspected here — verify before documenting its structure).
