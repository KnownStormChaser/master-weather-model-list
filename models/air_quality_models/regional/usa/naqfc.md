# NAQFC (National Air Quality Forecast Capability)

## What this model is
The National Air Quality Forecast Capability (NAQFC) is NOAA/NWS's operational air quality forecast system for the United States. It has provided operational air quality forecast guidance since 2004 and supports daily air quality alerts issued by state and local air quality agencies.

NAQFC is not a single model but a **forecasting capability composed of three distinct prediction systems**, each handling a different set of pollutants:

1. **Air Quality Model (AQM)** — the primary system, producing ozone (O3) and fine particulate matter (PM2.5) forecasts
2. **Rapid Refresh (RAP)** with smoke component — producing smoke forecasts (subsumed HYSPLIT for this purpose on 28 June 2022)
3. **HYSPLIT** (Hybrid Single-Particle Lagrangian Integrated Trajectory model) — producing dust forecasts

The combined NAQFC products provide hourly forecast guidance for O3, PM2.5, smoke, and dust over the United States and its territories.

---

## Who runs it
- **Organization:** National Weather Service (NWS) and NOAA, with modeling centres at the National Centers for Environmental Prediction (NCEP) / Environmental Modeling Center (EMC) and the NOAA Air Resources Laboratory (ARL)
- **Country / region:** United States

---

## What area it covers
AQM generates guidance products for three separate domains:
- **CONUS:** ~5 km grid spacing
- **Alaska:** ~6 km grid spacing
- **Hawaii:** ~2.5 km grid spacing

Since AQMv7 (May 2024), products for all three domains are generated from forecasts run over a **single unified North American domain at 13 km**, and then regridded to the three user-facing product grids above.

Smoke (from RAP) and dust (from HYSPLIT) coverage:
- Smoke: available over all three domains (CONUS, Alaska, Hawaii)
- Dust: available over CONUS only

---

## Basic details
- **Model type:** Regional deterministic air quality forecasting capability (three component systems)
- **Horizontal resolution:** ~5 km (CONUS), ~6 km (Alaska), ~2.5 km (Hawaii) for user products; 13 km native unified domain for AQMv7
- **Forecast length:**
  - O3 and PM2.5 (from AQM): 72 hours
  - Smoke (from RAP): 51 hours
  - Dust (from HYSPLIT): 48 hours
- **Update frequency:**
  - O3, PM2.5, dust: 2× daily (06, 12 UTC)
  - Smoke: 1× daily (03 UTC)
- **Temporal output resolution:** Hourly
- **Output units:** O3 in parts per billion (ppb); PM2.5, smoke, and dust in µg/m³ at surface; column-integrated smoke in mg/m²

### O3 and PM2.5 output averaging
To align with U.S. EPA National Ambient Air Quality Standards, AQM provides:
- **Ozone:** 1-hour and 8-hour backward-calculated averages, plus preceding 1-hour and 8-hour maximum values
- **PM2.5:** 1-hour and 24-hour averages, plus 24-hour maximum values
- **Bias-corrected versions:** All O3 and PM2.5 fields are also available as bias-corrected outputs, using the Kalman Filter Analog (KFAN) technique trained against AirNow observational data

---

## Meteorological driver

### Since 14 May 2024 (AQMv7)
AQM is now a **UFS-based online-coupled atmosphere-chemistry system** — meteorology and chemistry are computed together within the same forecast. The core model components are derived from the community Unified Forecast System (UFS) and from the EPA's CMAQ (Community Multiscale Air Quality) model, version 5.2.1, embedded within the UFS framework.

### Prior to 14 May 2024
AQM used an offline-coupled system where a weather model generated meteorological fields that were then passed into CMAQ:
- **20 July 2021 – 14 May 2024 (AQMv6):** GFS v16 meteorology driving CMAQ v5.3.1
- **Before 20 July 2021 (AQMv5 and earlier):** NAM meteorology driving CMAQ

RAP-based smoke forecasts use meteorology from the RAP model itself (see the separate RAP entry in this repository for RAP meteorological details).

---

## Chemistry and aerosols (AQM)
- **Chemistry model:** CMAQ (Community Multiscale Air Quality) v5.2.1 embedded within UFS (AQMv7)
- **Gas-phase mechanism:** CB05 (Carbon Bond 05)
- **Aerosol scheme:** aero6 (sectional, 155 species including size distribution Aitken/accumulation/coarse modes)
- **Heterogeneous chemistry:** Including N2O5 hydrolysis with temperature and humidity dependence
- **Inorganic partitioning:** ISORROPIA

---

## Emissions

### AQMv7 (current)
- **Anthropogenic:** EPA National Emissions Inventory (NEI) data
- **Biogenic:** MEGAN (Model of Emissions of Gases and Aerosols from Nature), replacing the older BEIS system
- **Soil NOx:** Included during springtime (March, April, May)
- **Wildfire:** RAVE (Regional ABI and VIIRS Emissions) data at 0.03° resolution, hourly — replacing daily GBBEPx inputs used in AQMv6. Updated to RAVE v2.0 with NOAA-21 satellite data on 1 October 2024.
- **Fugitive dust:** ARL Fengsha wind-blown dust emission model
- **Point sources:** NEI point source data projected to the current year using DOE projection factors

---

## Data assimilation
AQM itself does not assimilate air quality observations directly. However, the **Kalman Filter Analog (KFAN) bias correction system** uses AirNow observations to correct raw model predictions:
- Applied post-forecast to O3 and PM2.5 outputs
- Trains on historical model forecasts matched to observations at AirNow surface monitoring sites
- Uses NOx, NOy, and O3 as parameters to identify analogs for O3
- Since AQMv7 (May 2024), KFAN is applied over the large unified North American domain with an expanded AirNow observation set
- Driving meteorological models (GFS or UFS) have their own independent data assimilation cycles

---

## What it provides
- **Surface-level concentrations:**
  - O3 (hourly, 1h and 8h averages, 1h and 8h maxima — raw and bias-corrected)
  - PM2.5 (hourly, 1h and 24h averages, 24h maximum — raw and bias-corrected)
  - Smoke (hourly, from RAP)
  - Dust (hourly, from HYSPLIT — CONUS only)
- **Column-integrated values:** Smoke and dust vertically integrated mass
- **Supplemental meteorology:** Surface fields consistent with the driving NWP model

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data format:** GRIB2
- **Primary access:** NOAA Open Data Dissemination (NODD) via AWS S3 bucket
  - Bucket: `noaa-nws-naqfc-pds`
  - Region: `us-east-1`
  - Browse: https://noaa-nws-naqfc-pds.s3.amazonaws.com/index.html
  - CLI: `aws s3 ls --no-sign-request s3://noaa-nws-naqfc-pds/`
- **Historical archive:** Available from 1 January 2020 onward (spans AQMv5, AQMv6, and AQMv7 — users should be aware that model physics and resolution differ across this time span; see version history)
- **New-data notifications:** AWS SNS topic `arn:aws:sns:us-east-1:709902155096:NewNWSAirQualityObject` (Lambda and SQS protocols)
- **Operational forecast viewer:** https://airquality.weather.gov/

---

## Version history

### AQMv7.0.8 — operational 1 October 2024
- RAVE (wildfire emissions) input data updated from v1.3 to v2.0 (incorporating NOAA-21 satellite observations)
- Updated AirNow observational site list for O3 and PM2.5 bias correction
- Mitigation of fugitive dust overprediction
- Improvements to predicted dry deposition velocities

### AQMv7.0.7 — operational 14 May 2024
Major architectural shift:
- Transition from **offline-coupled GFS-CMAQ** to **online-coupled UFS-based** atmosphere-chemistry system embedding CMAQ v5.2.1
- Three separate domains (CONUS, Alaska, Hawaii at ~12 km) replaced by a **single unified North American domain at 13 km** (with product output regridded to domain-specific grids)
- Vertical levels increased from 35 to **65 levels**; model top raised from 60 hPa to **0.2 hPa** (into the mesosphere)
- Fire emissions migrated from daily GBBEPx to real-time hourly **RAVE** data at 0.03° resolution
- Biogenic emissions migrated from BEIS to **MEGAN**
- Soil NOx emissions added for springtime
- KFAN bias correction applied over the unified domain with expanded AirNow data

### AQMv6 — operational 20 July 2021 – 14 May 2024
- CMAQ updated to **EPA v5.3.1** with 2016 NEI emissions
- Meteorological driver changed from **NAM to GFS v16**
- Forecast length extended from **48 to 72 hours** (06 and 12 UTC cycles)
- ARL Fengsha dust model updated with improved threshold velocities by soil type
- BELD v5 for biogenic emissions processing
- Greenness vegetation fraction (GVF) updated daily with 7-day average from NESDIS
- Leaf Area Index (LAI) upgraded from a constant to a climatological field
- GBBEPx wildfire emissions introduced, with plume rise driven by Fire Radiative Power

### Smoke guidance transition — 28 June 2022
- **RAP with smoke component** replaced **HYSPLIT** for operational smoke forecast guidance
- HYSPLIT retained for dust forecasts only

### AQMv5.1 — operational 18 December 2018
- CMAQ v5.0.2 with 155 chemical species
- NEI 2014v2 emissions
- Unified KFAN bias correction for both O3 and PM2.5
- Alaska and Hawaii domains upgraded to CMAQ v5.0.2

### AQMv5 (initial) — operational June 2017
- CMAQ v5.0.2 CONUS system promoted to operations, replacing earlier CMAQ v4.7.2
- CB05 gas-phase, aero6 aerosol chemistry
- Improved heterogeneous, aqueous, and winter-time reactions
- Dynamic dust boundary conditions from NEMS Global Aerosol Capability (NGAC V2)
- KFAN bias correction introduced

---

## Notes
- NAQFC's operational history dates to 2004, initially with a Northeast U.S. forecast domain. The CONUS domain became operational in September 2007, and Alaska and Hawaii domains were added later.
- The AWS archive spans multiple AQM versions (v5, v6, v7) with different physics, driving models, and domains. Users conducting long-term studies should be aware of the transitions, particularly the major AQMv6 → AQMv7 shift in May 2024.
- NAQFC outputs directly feed into the **AirNow** air quality index system and the EPA-NOAA joint air quality forecasting program. AQHI-style alerts in the U.S. are based in part on NAQFC guidance.
- Because smoke forecasts come from the RAP model, the RAP entry in this repository may also be relevant to users looking for NAQFC smoke details.
- For smoke from wildfires specifically, users should be aware that RAVE wildfire emissions rely on satellite fire radiative power detection, which can be limited by cloud cover, thick smoke, or small/low-intensity fires.

---

## Official documentation
- AWS Open Data Registry entry: https://registry.opendata.aws/noaa-nws-naqfc-pds/
- NCEP/EMC AQM change log: https://www.emc.ncep.noaa.gov/mmb/aq/AQChangelog.html
- NOAA OSTI Air Quality program page: https://vlab.noaa.gov/web/osti-modeling/air-quality
- NWS Air Quality Forecast Guidance viewer: https://airquality.weather.gov/
- NCEP AQM Products website: https://www.emc.ncep.noaa.gov/mmb/aq/
- Campbell et al. (2022), Geosci. Model Dev.: https://doi.org/10.5194/gmd-15-3281-2022 — development and evaluation of NAQFC using GFS v16
