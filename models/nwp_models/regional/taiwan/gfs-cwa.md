# GFS (CWA Redistribution – Taiwan)

## What this model is
The Central Weather Administration (CWA) of Taiwan redistributes NOAA's Global Forecast System (GFS) through its national open data programme. The data itself is produced by NOAA/NCEP; CWA mirrors it on an AWS S3 bucket hosted in the `ap-northeast-1` region for use within Taiwan and by regional downstream users.

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
- **Horizontal resolution:** 0.25°
- **Forecast length:** Up to 384 hours (16 days)
- **Temporal output resolution:** 6-hourly
- **Update frequency:** 4× daily (inherited from NOAA GFS)

For the full technical specification, see the [GFS entry](../../global/usa/gfs.md).

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  - AWS Open Data Registry: https://registry.opendata.aws/cwa_opendata/
  - S3 bucket: `s3://cwaopendata/Model/`
  - AWS region: `ap-northeast-1`
  - CLI: `aws s3 ls --no-sign-request s3://cwaopendata/Model/`
- **License:** https://data.gov.tw/en/license

---

## Notes
- This is a **redistribution** of NOAA GFS, not an independently run global model. CWA does not operate its own global NWP system.
- For users outside Taiwan, it is usually preferable to access GFS directly from NOAA NOMADS or the primary NOAA AWS bucket (`s3://noaa-gfs-bdp-pds/`).
- The CWA mirror is most useful for users in the Asia-Pacific region who want to co-locate global and CWA regional (WRF) model access in the same AWS region (`ap-northeast-1`).
- Comparable redistribution entries in this repository: [GFS (IDEAM, Colombia)](../colombia/gfs-ideam.md).

---

## Official documentation
- CWA developer documentation: https://opendata.cwa.gov.tw/devManual/insrtuction
- Primary GFS documentation: see the [GFS entry](../../global/usa/gfs.md)
