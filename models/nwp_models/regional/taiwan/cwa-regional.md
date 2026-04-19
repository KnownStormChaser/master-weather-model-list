# CWA Taiwan Regional Models (15 km & 3 km)

## What this model is
A set of regional weather forecast models used to predict weather conditions such as temperature, wind, precipitation, and pressure over Taiwan and surrounding areas.

They are designed for short-range forecasting and provide higher detail than global models, especially for typhoons and heavy rainfall.

---

## Who runs it
- **Organization:** Central Weather Administration
- **Country / region:** Taiwan

---

## What area it covers
- **Coverage:** Taiwan and nearby regions of East Asia

---

## Basic details
- **Model type:** Regional
- **Model system / core:** WRF
- **Typical resolution:**
  - **~15 km** (larger regional domain)
  - **~3 km** (high-resolution nested domain)
- **Forecast length:** Up to 84 hours
- **Temporal output resolution:** 6-hourly
- **Update frequency:** 4× daily

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, NetCDF
- **Official download location:**
  - AWS Open Data Registry: https://registry.opendata.aws/cwa_opendata/
  - S3 bucket: `s3://cwaopendata/Model/`
  - AWS region: `ap-northeast-1`
  - CLI: `aws s3 ls --no-sign-request s3://cwaopendata/Model/`
- **License:** https://data.gov.tw/en/license

---

## Notes
The higher-resolution 3 km configuration is convection-allowing and is particularly useful for forecasting typhoons, heavy rainfall, and complex terrain effects.

CWA distributes operational model data through an AWS S3 bucket in the `ap-northeast-1` region as part of Taiwan's national open data programme. Older references to `data.gov.tw` point to the same underlying CWA open data system.

---

## Official documentation
- CWA developer documentation: https://opendata.cwa.gov.tw/devManual/insrtuction
- https://www.cwa.gov.tw/eng/
