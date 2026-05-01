# NAQFC (National Air Quality Forecast Capability)

## What this is
The National Air Quality Forecast Capability (NAQFC) is NOAA/NWS's operational air quality forecast programme for the United States. It has provided operational air quality forecast guidance since 2004 and supports daily air quality alerts issued by state and local air quality agencies.

NAQFC is **not a single model** but a forecasting capability composed of three distinct prediction systems, each handling a different set of pollutants:

| Pollutant | Component model | Entry |
|---|---|---|
| Ozone (O3) and PM2.5 | AQM v7 (UFS-CMAQ online-coupled) | [aqm.md](./aqm.md) |
| Wildfire smoke | RAP-Smoke (integrated in operational RAP since June 2022) | [rap.md](../../../nwp_models/regional/usa/rap.md) |
| Atmospheric dust | HYSPLIT-Dust | [hysplit-dust.md](./hysplit-dust.md) |

The three components share infrastructure — a single AWS distribution bucket, a single AirNow / public viewer pipeline, and a single NCEP operational change-management process — but are otherwise distinct prediction systems with different model cores, different forecast lengths, different update frequencies, and different domain coverage.

This umbrella entry covers what spans all three. For technical details on any one component, see its dedicated entry.

---

## Who runs it
- **Lead organization:** National Weather Service (NWS) / NOAA, with modeling centres at the National Centers for Environmental Prediction (NCEP) / Environmental Modeling Center (EMC) and the NOAA Air Resources Laboratory (ARL)
- **Programme partner:** U.S. Environmental Protection Agency (EPA), through the AirNow air quality alert system
- **Country / region:** United States

---

## Coverage and forecast lengths at a glance

| Component | CONUS | Alaska | Hawaii | Forecast length | Update frequency |
|---|---|---|---|---|---|
| AQM (O3, PM2.5) | ✓ ~5 km | ✓ ~6 km | ✓ ~2.5 km | 72 hours | 2× daily (06, 12 UTC) |
| RAP-Smoke | ✓ | ✓ | ✓ | 51 hours | 1× daily (03 UTC for the smoke product cycle within NAQFC) |
| HYSPLIT-Dust | ✓ | — | — | 48 hours | 2× daily (06, 12 UTC) |

Native AQM forecasts since AQMv7 (May 2024) are run on a single unified North American domain at 13 km, with output regridded to the three product grids above. Earlier AQM versions used three separate domains.

---

## Component summary

### AQM (Air Quality Model) — O3 and PM2.5
The primary chemistry component, currently AQMv7 (operational 14 May 2024). UFS-based online-coupled atmosphere-chemistry system embedding CMAQ v5.2.1. Provides hourly forecasts of ozone and fine particulate matter, with raw and KFAN-bias-corrected outputs. Feeds directly into AirNow public AQI alerts.

→ Full details: [aqm.md](./aqm.md)

### RAP-Smoke — wildfire smoke
Operational smoke forecasting within NAQFC has been provided by the [RAP](../../../nwp_models/regional/usa/rap.md) model since 28 June 2022, when it replaced HYSPLIT in this role. RAP-Smoke is integrated into the operational RAP forecast (since RAPv5, December 2020), with smoke aerosols feeding back on radiation and the atmospheric forecast. NAQFC distributes RAP-Smoke output alongside its other component products.

→ Full details: [rap.md](../../../nwp_models/regional/usa/rap.md)

### HYSPLIT-Dust — atmospheric dust
Operational dust forecasting uses NOAA ARL's HYSPLIT Lagrangian transport-and-dispersion model. CONUS-only (no Alaska or Hawaii dust forecasts). Until 28 June 2022, HYSPLIT also handled smoke forecasting within NAQFC; that role has since transitioned to RAP.

→ Full details: [hysplit-dust.md](./hysplit-dust.md)

---

## Shared infrastructure

### Distribution
All three NAQFC component products are distributed through a single shared AWS Open Data bucket:

- **Bucket:** `noaa-nws-naqfc-pds`
- **Region:** `us-east-1`
- **Browse:** https://noaa-nws-naqfc-pds.s3.amazonaws.com/index.html
- **CLI:** `aws s3 ls --no-sign-request s3://noaa-nws-naqfc-pds/`
- **New-data notifications:** AWS SNS topic `arn:aws:sns:us-east-1:709902155096:NewNWSAirQualityObject` (Lambda and SQS protocols)
- **Format:** GRIB2

### Public viewer and AirNow integration
- **Operational forecast viewer:** https://airquality.weather.gov/
- **AirNow:** AQM-derived O3 and PM2.5 forecasts feed into the EPA AirNow system, which produces the public-facing Air Quality Index (AQI) alerts and warnings used by health-conscious individuals, schools, and air quality regulators

### Archive
The shared AWS bucket holds an archive from 1 January 2020 onward, spanning AQMv5 (NAM-CMAQ era), AQMv6 (GFS-CMAQ offline-coupled era), and AQMv7 (UFS-CMAQ online-coupled era). Users conducting long-term studies should be aware that the model architectures have changed substantially across this archive — see the AQM version history for details.

---

## Operational history at a glance

- **2004:** Initial NAQFC operations, Northeast U.S. only
- **September 2007:** CONUS domain becomes operational
- **2009–2010:** Alaska and Hawaii domains added
- **June 2017:** AQMv5 with CMAQ v5.0.2 promoted to operations
- **20 July 2021:** AQMv6 transitions from NAM-CMAQ to GFS-CMAQ (offline-coupled)
- **28 June 2022:** Smoke forecasting transitions from HYSPLIT to RAP-Smoke
- **14 May 2024:** AQMv7 transitions from offline GFS-CMAQ to online UFS-CMAQ; unified North American domain
- **1 October 2024:** RAVE wildfire emissions updated to v2.0 with NOAA-21 data

---

## What this entry does not cover
- **Component-specific physics, chemistry, and assimilation details** — see the individual entries for [AQM](./aqm.md), [RAP](../../../nwp_models/regional/usa/rap.md), and [HYSPLIT-Dust](./hysplit-dust.md)
- **HYSPLIT in its non-NAQFC roles** — emergency response, volcanic ash, backward trajectories, and other applications are outside the operational-forecast scope of this repository

---

## Official documentation
- AWS Open Data Registry entry: https://registry.opendata.aws/noaa-nws-naqfc-pds/
- NCEP/EMC AQM change log: https://www.emc.ncep.noaa.gov/mmb/aq/AQChangelog.html
- NOAA OSTI Air Quality program page: https://vlab.noaa.gov/web/osti-modeling/air-quality
- NWS Air Quality Forecast Guidance viewer: https://airquality.weather.gov/
- NCEP AQM Products website: https://www.emc.ncep.noaa.gov/mmb/aq/
