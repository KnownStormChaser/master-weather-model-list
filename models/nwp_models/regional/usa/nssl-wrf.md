# NSSL-WRF (Experimental)

## What this model is
The NSSL-WRF is an experimental, real-time, convection-allowing WRF-ARW forecast run by NOAA's National Severe Storms Laboratory over a CONUS domain at 4 km grid spacing. It is one of the longest-running real-time convection-allowing models in the United States, dating to the mid-2000s, and was a foundational member of the Storm Scale Ensemble of Opportunity (SSEO) that became the operational [HREF](../../../ensemble_models/regional/usa/href.md). It is used as guidance at the NOAA Storm Prediction Center (SPC) but is an experimental research run, not part of the NCEP operational production suite.

This is the model behind the `hiresw_conusnssl` files in NSSL's THREDDS repository. Note that despite the `hiresw_` filename prefix, it is **not** part of NCEP's operational [HiresW](./hiresw.md) system — NSSL packages its own run alongside the operational HiresW members it collects for HREF input.

---

## Who runs it
- **Organization:** NOAA / National Severe Storms Laboratory (NSSL), Forecast Research and Development Division (FRDD)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Contiguous United States (CONUS)
- **Domain details:** Full-CONUS 4 km grid (the domain was expanded from a partial-CONUS coverage to the full CONUS in June 2009).

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing), experimental
- **Model system / core:** WRF-ARW (Advanced Research WRF; developed and maintained by NCAR)
- **Dynamical formulation:** Non-hydrostatic, Arakawa C-grid
- **Convection-allowing:** Yes — 4 km grid spacing; no cumulus parameterization
- **Horizontal resolution:** 4 km
- **Vertical levels:** TBD (not confirmed in current materials)
- **Forecast length:** Up to 48 hours (hourly output)
- **Update frequency / cycles:** 2× daily (00 and 12 UTC)
- **Temporal output resolution:** Hourly

---

## Data assimilation
- **Data assimilation:** No own analysis — initialized from a parent model (cold start).

---

## Initial and boundary conditions
- **Initial conditions:** Historically NAM-based (the corresponding 00/12 UTC [NAM](./nam.md) analysis); current source **not confirmed** in the materials reviewed (TBD). Note that NAM is itself scheduled to retire on August 31, 2026, so any NAM dependency here is worth re-checking.
- **Boundary conditions:** TBD (historically NAM forecasts; not confirmed)

---

## What it provides
Deterministic convection-allowing forecasts of standard atmospheric fields — temperature, wind, precipitation, pressure, humidity, simulated/composite radar reflectivity, and severe-weather diagnostics such as updraft helicity (the model carries NSSL-specific UH and maximum-vertical-velocity diagnostics) — distributed as GRIB2.

### Documented physics (historical)
Per Kain et al. and subsequent NSSL-WRF studies, the configuration has used WSM-6 microphysics, the MYJ boundary-layer scheme, the Noah land-surface model, and Dudhia (shortwave) / RRTM (longwave) radiation. This reflects the long-documented configuration and may have been updated; see the NSSL CAMs site for the current physics suite.

---

## Data availability
- **Is the data free?** Yes
- **License:** "Freely available" — the rights statement carried in the dataset's own THREDDS/ISO 19115 metadata (publisher: DOC/NOAA/OAR/NSSL). As a U.S. Government work it is effectively public domain (CC0-equivalent). Note: the CC BY 4.0 banner in the THREDDS server's page header is generic boilerplate describing the NSSL ATD radar archive and does **not** apply at this dataset level.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  - Browse (THREDDS catalog): `https://data.nssl.noaa.gov/thredds/catalog/FRDD/HREF/<YYYY>/<YYYYMMDD>/catalog.html`
    - e.g., https://data.nssl.noaa.gov/thredds/catalog/FRDD/HREF/2026/20260608/catalog.html
  - Direct download (HTTPServer): `https://data.nssl.noaa.gov/thredds/fileServer/FRDD/HREF/<YYYY>/<YYYYMMDD>/hiresw_conusnssl_<YYYYMMDDHH>f<FFF>.grib2`
    - e.g., `.../FRDD/HREF/2026/20260608/hiresw_conusnssl_2026060800f024.grib2`
  - Also exposed per file via **OpenDAP** (`/thredds/dodsC/...`) and the **NetCDF Subset Service** (`/thredds/ncss/grid/...`)
- **File naming:** `hiresw_conusnssl_<YYYYMMDDHH>f<FFF>.grib2` (HH = `00` or `12`; FFF = zero-padded forecast hour, `000`–`048`)
- **Metadata:** ISO 19115 record available per dataset via the THREDDS `iso/` service

---

## Relationship to other models
- **[HiresW](./hiresw.md):** Separate system. NSSL's `FRDD/HREF/` repository co-locates this run with re-hosted copies of NCEP operational members (`hiresw_conusarw`, `hiresw_conusfv3`, `hrrr_ncep`, `nam_conusnest`). Only the `hiresw_conusnssl` files are NSSL's own model; the rest are mirrors of NOMADS data and should be obtained from their operational sources.
- **[HREF](../../../ensemble_models/regional/usa/href.md):** The NSSL-WRF was a founding member of the SSEO, HREF's experimental predecessor at the SPC/NSSL Hazardous Weather Testbed.
- **[NSSL MPAS](./nssl-mpas.md):** The development line NSSL is shifting toward. NSSL's announced wind-down of the 4 km WRF-NSSL is tied to redirecting resources to MPAS (the RRFSv2 development track).

---

## Notes
- **Display/viewer:** Rendered imagery is viewable on the NSSL CAMs site (https://cams.nssl.noaa.gov/, model `wrf_nssl`); that is a visualization front-end, not the data feed.
- **Distinct from the NSSL-WRF 1 km run:** NSSL also runs a separate 1 km WRF (initialized at 06 UTC from the 00 UTC HRRR 6 h forecast, over a 2/3-CONUS domain). That is a different system from this 4 km run and is not covered by this entry.

---

## Status
- Experimental / research run — not an operational NCEP product, and supporting infrastructure is not maintained 24/7.
- **Cessation announced vs. data still flowing — verify before relying on this:** NSSL's CAMs page announced (17 March 2026) that the 4 km WRF-NSSL run would cease on or near 31 March 2026 due to the decommissioning of the NOAA Jet HPC, with resources shifting to MPAS development. However, fresh `hiresw_conusnssl` files are present in the THREDDS repository well after that date (e.g., 8 June 2026), so the run appears to have continued or migrated (NSSL has been moving its runs to the NOAA Ursa HPC). Current status is unconfirmed — check the repository and CAMs page before depending on continued availability.

---

## Official documentation
- PARR (NSSL Data Repository) THREDDS root: https://data.nssl.noaa.gov/thredds/catalog/catalog.html
- NSSL CAMs (viewer + run descriptions): https://cams.nssl.noaa.gov/
- NSSL Forecast webpage: https://www.nssl.noaa.gov/tools/forecast/
- Data contact / publisher: DOC/NOAA/OAR/NSSL — nssl.support@noaa.gov
