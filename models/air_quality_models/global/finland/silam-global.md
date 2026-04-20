# SILAM Global (FMI Global Air Quality Forecast)

## What this model is
SILAM (System for Integrated modeLling of Atmospheric coMposition) is a global-to-mesoscale atmospheric composition and dispersion model developed and operated by the Finnish Meteorological Institute. It supports air quality forecasting, emergency response (volcanic ash, nuclear accident dispersion), and inverse dispersion problem solutions.

This entry describes **FMI's operational global SILAM air quality forecast**, which is distributed publicly through an AWS Open Data bucket. FMI publishes a 168-hour (7-day) global forecast of seven regulated atmospheric constituents at 20 km resolution — an unusually fine horizontal resolution for a publicly distributed global air quality model.

SILAM is also one of the 11 constituent models in the CAMS Regional European air quality ensemble (see [CAMS Regional](../../regional/eu/cams-regional.md)). The CAMS Regional contribution is a separate European-domain configuration; this entry documents FMI's independently distributed global forecast.

---

## Who runs it
- **Organization:** Finnish Meteorological Institute (FMI)
- **Country / region:** Finland
- **Distribution partner:** AWS Open Data

---

## What area it covers
- **Coverage:** Global
- **Vertical coverage:** Troposphere and stratosphere (3D concentration fields)

---

## Basic details
- **Model type:** Global atmospheric composition / air quality
- **Model system / core:** SILAM (multi-scale Eulerian / semi-Lagrangian dispersion model)
- **Model version in AWS distribution:** v5.6
- **Horizontal resolution:** ~20 km (global)
- **Forecast length:** 168 hours (7 days), of which **121 hours (5 days from D0 00Z onward)** are published in hourly steps through AWS
- **Update frequency:** 1× daily
- **Production cadence:** The forecast is initialized at **00Z of the previous day** (not the current day) in order to incorporate observed emissions of transient sources such as wildfires. The resulting forecast is published around **06:00 UTC** the following day.
- **Temporal output resolution:** Hourly

---

## Species and products distributed on AWS
FMI publishes **seven regulated atmospheric constituents** through the AWS Open Data bucket:
- Ozone (O3)
- Nitrogen dioxide (NO2)
- Sulfur dioxide (SO2)
- Carbon monoxide (CO)
- PM2.5
- PM10
- (Additional species; see AWS bucket listing for current file set)

Derived products include:
- 3D concentration fields (surface-level data in the main public bucket)
- Aerosol optical thickness (AOT)

Internally, SILAM simulates more than 100 species covering tropospheric and stratospheric chemistry; the AWS distribution is a curated subset focused on regulated air quality pollutants.

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

### File organization
Files are organized by model run date under `/global/<YYYYMMDD>/` with a naming convention of:
