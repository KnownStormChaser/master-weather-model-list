# MRMS (Multi-Radar/Multi-Sensor System)

## What this is
The Multi-Radar/Multi-Sensor System (MRMS) is NOAA's operational system for
fusing the US weather-radar network with other sensors into a seamless,
quality-controlled gridded mosaic. Fully automated algorithms integrate every
NEXRAD/WSR-88D radar (plus Canadian radars near the border) with rain gauges,
surface and upper-air observations, lightning detection, satellite, and NWP
model fields to produce 100+ decision-support products on a ~1 km grid, updated
every 2 minutes over the contiguous US and southern Canada. Products span
reflectivity mosaics (including true 3D mosaics), quantitative precipitation
estimation, rotation and hail diagnostics, lightning density, and derived
hydrologic and severe-weather guidance.

MRMS is an observational/analysis product, not a forecast model — it is a
present-state mosaic of what the sensors are seeing, quality-controlled and
merged. A small minority of the suite is derived short-range guidance (see
Scope note). It became operational at NCEP in 2014 and is the operational
successor to the WDSS-II and NMQ research systems. Its output is a foundational
input to US operational NWP and hydrology.

---

## Who operates it
- **Operator / coordinating programme:** NOAA / National Severe Storms Laboratory (NSSL); operational production at NOAA/NWS/NCEP. Co-developed with the Cooperative Institute for Severe and High-Impact Weather Research and Operations (CIWRO, formerly CIMMS) / University of Oklahoma.
- **Country / region:** United States. The primary CONUS domain's composite extends over southern Canada and northern Mexico; separate OCONUS domains cover Alaska, the Caribbean/Puerto Rico, Guam, and Hawaii (served as subtrees under `2D/`).
- **Data distributor:** NOAA Open Data Dissemination (NODD) via AWS; NCEP real-time HTTP server.

---

## Network composition
Built on the US **NEXRAD / WSR-88D** network (~160 S-band dual-polarization
radars) plus Canadian radars near the border. MRMS is genuinely *multi-sensor*:
beyond radar it ingests rain gauges, surface and upper-air observations, NLDN
lightning, satellite, and NWP (RAP) model fields — so the products are a blended
analysis rather than raw radar. The grid is a regular latitude/longitude
(Plate Carrée) mesh at 0.01° (~1 km); the 3D reflectivity and dual-pol mosaics
are provided on 33 CAPPI levels from 500 m to 19,000 m MSL.

---

## Products
Written against the **v12.2** product table (151 entries). Most 2D fields update
every 2 minutes; accumulations update at their stated windows.

- **Reflectivity mosaics:** 2D composite and base reflectivity (optimal-method and max-ref variants), composite-reflectivity height, and Level-III high-resolution echo top (HREET) and VIL. True **3D mosaics** — reflectivity (`MergedReflectivityQC`), RhoHV, and Zdr — each on 33 CAPPIs (500–19,000 m). The 3D gridded mosaics are the key discriminator from a 2D-only radar composite.
- **Precipitation (QPE):** surface precipitation type (`PrecipFlag`), radar precipitation rate, radar-only and multi-sensor (gauge-corrected) QPE, and 1/3/6/12/24/48/72-hour accumulations; plus Radar Quality Index and gauge-influence indices.
- **Rotation / severe:** azimuth shear at multiple AGL layers (`MergedAzShear0to2kmAGL`, `3to6kmAGL`) and rotation tracks over multiple time windows; hail (MESH) and related diagnostics.
- **Lightning:** NLDN cloud-to-ground density at 1/5/15/30-minute windows (CONUS only) and derived probabilities.
- **Environment / model:** surface temperature, wet-bulb temperature, warm-rain probability, and freezing/melting-level heights (from RAP), used in precipitation typing.
- **Hydro (FLASH):** flash-flood guidance — streamflow, unit streamflow, soil saturation — derived nowcast/forecast fields (out to ~6 h).
- **Nowcast (ANC):** 1-hour reflectivity forecast and next-hour convective likelihood.
- **ProbSevere:** object-based severe-storm probabilities, distributed as ASCII/JSON (2 products; **not** gridded GRIB2).

---

## Data availability
- **Is the data free?** Yes — no account, no registration.
- **Is the data downloadable?** Yes.
- **Access tier:** Open (no account, no registration).
- **Data formats:** GRIB2, gzip-compressed (`.grib2.gz`). ProbSevere as ASCII/JSON (non-gridded).
- **Update cadence:** Real-time; 2-minute update cycle for most 2D products.
- **Primary access:**
  - **NCEP real-time HTTP:** https://mrms.ncep.noaa.gov/ — top-level trees `2D/`, `3DRefl/`, `3DRhoHV/`, `3DZdr/`, `ProbSevere/`. The ~150 CONUS products sit directly under `2D/` as per-product subfolders; the OCONUS domains are nested as `2D/ALASKA/`, `2D/CARIB/`, `2D/GUAM/`, `2D/HAWAII/`. (`RIDGEII/` is rendered imagery — see Notes.)
  - **Filename convention:** `MRMS_<Product>_<level>_<YYYYMMDD>-<HHMMSS>.grib2.gz`, timestamped in UTC at the 2-minute cycle. `<level>` is `00.00` for 2D surface fields; the `3D*/` trees carry per-CAPPI-level files. A rolling `MRMS_<Product>.latest.grib2.gz` in each folder always points to the most recent file.
    - Example (timestamped): `https://mrms.ncep.noaa.gov/2D/PrecipRate/MRMS_PrecipRate_00.00_20260706-020000.grib2.gz` (~627 KB)
    - Example (rolling latest): `https://mrms.ncep.noaa.gov/2D/PrecipRate/MRMS_PrecipRate.latest.grib2.gz`
  - **AWS Open Data:** `s3://noaa-mrms-pds` (region `us-east-1`, no account: `aws s3 ls --no-sign-request s3://noaa-mrms-pds/`); browse at https://noaa-mrms-pds.s3.amazonaws.com/index.html. Mirrors the same product/domain layout and filename convention.
- **New-data notifications:** SNS `arn:aws:sns:us-east-1:123901341784:NewMRMSObject` (Lambda and SQS protocols only).
- **Archive depth:** The NCEP HTTP server is a short rolling real-time window; the AWS bucket holds the operational archive. Note the v11→v12 break on 14 October 2020 (below) — treat pre-2020 v11 holdings with caution.
- **Licence:** NOAA Open Data Dissemination (NODD) — open to the public and usable as desired; as a U.S. Government work, effectively public domain (CC0-equivalent). Attribution requested; no stating or implying NOAA endorsement; modified data must not be presented as original, unaltered NOAA data.

---

## Scope note
- **Observation vs forecast.** MRMS is predominantly an observational/analysis mosaic (reflectivity, QPE, rotation, lightning) and belongs in the observations section on that basis. The suite bundles a small set of *derived* short-range guidance — FLASH flash-flood forecasts (to ~6 h) and the ANC 1-hour reflectivity nowcast. These are noted rather than excluded; the core product identity is observational.
- **Legacy NMQ sharing policy is superseded.** An older "MRMS/NMQ Dataset Sharing Policy" (Gourley & Howard, NSSL) carries restrictive language — requests not to redistribute without NSSL permission and not to duplicate methods to mimic the dataset. That document governs the *experimental NMQ research data streams* and does **not** apply to the operational product, which is distributed under NODD's open terms. The License field above reflects the operational feed; the NMQ policy is legacy and non-applicable here.
- **Software vs data.** The University of Oklahoma retains commercial-licensing rights to the MRMS *software*. This concerns the processing code, not the output data — the operational data itself carries no such restriction.

---

## Notes
- **Multi-sensor, not raw radar.** MRMS blends radar with gauges, lightning, satellite, and RAP model fields — it is a merged analysis, and QPE in particular is a multi-sensor product.
- **Relationship to NWP and hydrology.** MRMS reflectivity and QPE feed operational US systems — radar reflectivity assimilation in HRRR/RAP, and rainfall input to hydrologic and flash-flood modeling — closing the loop with those model entries.
- **Data feed vs viewer.** `RIDGEII/` on the NCEP server is the RIDGE II imagery/tile service (rendered raster), not gridded data — viewer-only and out of scope. The gridded feed is `2D/` and the `3D*/` trees.
- **Version caution.** The v11→v12 upgrade (14 Oct 2020) added and discontinued products and changed the underlying science; this entry is written against the v12.2 table. Cross-version time series should account for the break.
- **Two non-GRIB2 products.** The ProbSevere ASCII/JSON products are object-based, not gridded — flag if the catalog prefers to scope the entry to the GRIB2 gridded suite only.

---

## Recent version history
- **14 October 2020 — v11 → v12:** new products introduced, others discontinued, science and QC upgraded across the suite. Subsequent v12.x minor updates followed (current product table: v12.2).
- **2014:** MRMS became operational at NCEP, superseding the WDSS-II / NMQ research systems.

---

## Official documentation
- MRMS project page: https://www.nssl.noaa.gov/projects/mrms/
- Operational product tables: https://www.nssl.noaa.gov/projects/mrms/operational/tables.php
- NCEP real-time HTTP server: https://mrms.ncep.noaa.gov/
- AWS Open Data registry: https://registry.opendata.aws/noaa-mrms-pds/
- Zhang et al. (2016), *MRMS QPE: Initial Operating Capabilities*, BAMS: https://doi.org/10.1175/BAMS-D-14-00174.1
- Smith et al. (2016), *MRMS Severe Weather and Aviation Products: Initial Operating Capabilities*, BAMS: https://doi.org/10.1175/BAMS-D-14-00173.1
