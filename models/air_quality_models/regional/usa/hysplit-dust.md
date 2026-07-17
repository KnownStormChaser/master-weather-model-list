# HYSPLIT-Dust (NAQFC Dust Forecast)

## What this model is
HYSPLIT-Dust is the operational atmospheric dust forecast component of NOAA/NWS's National Air Quality Forecast Capability (NAQFC). It uses the Hybrid Single-Particle Lagrangian Integrated Trajectory model (HYSPLIT) — a widely used Lagrangian transport and dispersion model developed by NOAA's Air Resources Laboratory (ARL) — configured specifically for windblown dust transport and deposition over the contiguous United States.

This entry documents HYSPLIT in its specific role as the operational NAQFC dust component. HYSPLIT itself is a far broader system used internationally for emergency response (volcanic ash, nuclear and hazmat releases), backward trajectory analysis for source attribution, and many other dispersion applications. Those uses are outside the operational-forecast scope of this repository.

HYSPLIT-Dust is one of three component prediction systems making up NAQFC — alongside [AQM](./aqm.md) (operational ozone and PM2.5 forecasts) and [RAP](../../../nwp_models/regional/usa/rap.md) (operational smoke forecasts).

Prior to 28 June 2022, HYSPLIT also produced the operational smoke forecast guidance for NAQFC. On that date, RAP-Smoke replaced HYSPLIT for operational smoke prediction; HYSPLIT is now used solely for the dust forecast.

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP), with model development at NOAA's Air Resources Laboratory (ARL)
- **Country / region:** United States
- **Programme:** Component of the National Air Quality Forecast Capability (NAQFC), a joint NOAA-EPA programme

---

## What area it covers
- **Coverage:** Contiguous United States (CONUS) only
- **Note:** Dust forecasts are not produced for Alaska or Hawaii. Both AK and HI receive smoke (from RAP) and ozone/PM2.5 (from AQM), but dust forecasting is CONUS-only under the current NAQFC configuration.

---

## Basic details
- **Model type:** Lagrangian atmospheric transport and dispersion (windblown dust)
- **Core model:** HYSPLIT (Hybrid Single-Particle Lagrangian Integrated Trajectory model)
- **Forecast length:** 48 hours
- **Update frequency:** 2× daily (06, 12 UTC)
- **Temporal output resolution:** Hourly
- **Output units:** µg/m³ (surface concentration); mg/m² (vertically integrated column dust)
- **Output products:**
  - Surface dust concentration (hourly)
  - Vertically integrated dust column

---

## Meteorological driver
HYSPLIT is an offline-coupled dispersion model — it ingests pre-computed meteorological fields from a separate NWP run rather than computing meteorology itself. The specific NWP source used for the operational NAQFC dust forecast follows whichever NCEP NWP system is currently configured to drive it.

For the most accurate current driving model, see the AWS Open Data registry description and the NCEP AQM change log linked below.

---

## Dust emissions
Windblown dust emissions for HYSPLIT-Dust are computed using a wind-driven erosion scheme that depends on:
- Surface wind speed
- Soil characteristics (texture, moisture)
- Vegetation cover (greenness vegetation fraction)
- Threshold friction velocities by soil type

The dust scheme is most active over arid and semi-arid regions of the western and southwestern United States, where dust events from playas, deserts, and disturbed land surfaces contribute meaningfully to PM10 and PM2.5 air quality during wind events.

---

## What it provides
- Hourly surface dust concentration over the CONUS
- Vertically integrated column dust mass
- Forecast horizon out to 48 hours from each cycle

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data format:** GRIB2
- **Primary access (live) — NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/hysplit/prod/
  - Dust is under the per-cycle `dustcs.YYYYMMDD/` directories: `dustcs.tCCz.sfc.1hr.grib2` (surface) and `dustcs.tCCz.pbl.1hr.grib2` (boundary-layer), each also on NCEP Grid 227 (~5 km CONUS) as the `_227.grib2` variant. Cycles 06 and 12 UTC.
- **Historical archive — AWS Open Data (NODD):** `s3://noaa-nws-naqfc-pds/HYSPLIT_Dust/` (browse: https://noaa-nws-naqfc-pds.s3.amazonaws.com/index.html). This mirror spans 2020-01-01 to 2026-04-20 and has not updated since — use NOMADS for current data.
- **Operational forecast viewer:** https://airquality.weather.gov/

---

## Notes
- Until 28 June 2022, HYSPLIT also produced the operational smoke forecast for NAQFC — that role has since transitioned to [RAP-Smoke](../../../nwp_models/regional/usa/rap.md). HYSPLIT remains in operational use within NAQFC purely as the dust component.
- This entry intentionally focuses on the operational NAQFC dust application. HYSPLIT in its broader form (run interactively via the READY system, used for emergency response, backward trajectory analysis, and a wide range of non-NAQFC applications) is outside this repository's operational-forecast scope. For those uses, see the ARL HYSPLIT page in the official documentation below.
- HYSPLIT-Dust is the only one of the three NAQFC component models that does **not** cover Alaska or Hawaii. Users in those domains need not look for dust forecasts in NAQFC output.

---

## Official documentation
- AWS Open Data Registry entry: https://registry.opendata.aws/noaa-nws-naqfc-pds/
- NCEP/EMC AQM change log (covers HYSPLIT-Dust as a NAQFC component): https://www.emc.ncep.noaa.gov/mmb/aq/AQChangelog.html
- NOAA Air Resources Laboratory HYSPLIT page: https://www.ready.noaa.gov/HYSPLIT.php
- NOAA OSTI Air Quality program page: https://vlab.noaa.gov/web/osti-modeling/air-quality
- NWS Air Quality Forecast Guidance viewer: https://airquality.weather.gov/
- NCEP AQM Products website: https://www.emc.ncep.noaa.gov/mmb/aq/
