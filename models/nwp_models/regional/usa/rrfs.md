# RRFS (Rapid Refresh Forecast System)

## What this model is
The Rapid Refresh Forecast System (RRFS) is NOAA's next-generation convection-allowing, hourly-updating regional numerical weather prediction system for North America.

RRFS is built on the Unified Forecast System (UFS) framework and is designed to consolidate and replace several legacy NCEP regional modeling systems, including the NAM, HiresW (except the Guam domain), HREF, and NARRE. It provides both deterministic and ensemble guidance, with the ensemble component distributed as REFS (RRFS Ensemble Forecast System).

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** North America
- **Domain details:**  
  Full North America parent domain at 3 km grid spacing, with subset output grids for:
  - CONUS (3 km)
  - Alaska (3 km)
  - Hawaii (2.5 km)
  - Puerto Rico (2.5 km)
  - A separate 1.5 km fire-weather RRFS run over a 5° × 5° rotated latitude–longitude domain

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing)
- **Framework:** Unified Forecast System (UFS)
- **Dynamical core (v1):** FV3 (Finite-Volume Cubed-Sphere)
- **Horizontal resolution:**  
  - 3 km (North America, CONUS, Alaska)  
  - 2.5 km (Hawaii, Puerto Rico)  
  - 1.5 km (fire-weather domain)
- **Forecast length:** Up to 84 hours
- **Update frequency / cycles:** Hourly (24× daily)
- **Temporal output resolution:**  
  - 15-minute output from +15 min to +18 h (subhourly "SUBH" files)  
  - Hourly output thereafter

---

## Data assimilation
- **Data assimilation:** Yes
- **Method:** Hourly cycling convective-scale data assimilation built on the UFS framework

---

## What it provides
Deterministic short-range forecasts of:
- Near-surface temperature, humidity, wind, and pressure
- Convective and severe storm evolution
- Precipitation amount and type
- Low clouds, ceilings, and visibility
- Aviation-relevant fields
- Fire-weather–relevant fields (dedicated 1.5 km domain)
- Subhourly (15-minute) output in early forecast hours

RRFS is designed to provide a single, unified convection-allowing guidance source across North America, replacing the role previously filled by multiple separate systems.

---

## Relationship to other models
RRFS is intended to replace the following legacy NCEP regional systems:
- **NAM** (12 km parent domain and 3 km nests – CONUS, AK, HI, PR, fire weather)
- **HiresW** (all domains except Guam)
- **HREF** (replaced by REFS)
- **NARRE** (replaced by REFS)

HRRR, RAP, NAM 12 km, and SREF are not retired with RRFSv1. These systems are expected to be retired later in conjunction with RRFSv2, which is planned to transition to the MPAS dynamical core.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**  
  - https://nomads.ncep.noaa.gov/ (once fully operational)  
  - https://registry.opendata.aws/noaa-rrfs/ (prototype and operational data)

---

## Status
- Proposal for legacy model retirement published in NWS Public Information Statement 25-41 (June 26, 2025), with a public comment period through July 26, 2025.
- Originally targeted for operational implementation in early 2026; implementation was delayed due to operational and scheduling factors.
- As of April 2026, RRFSv1 is in late-stage pre-operational status, with prototype data available via the NOAA Open Data Dissemination (NODD) program on AWS.
- RRFSv2 (based on the MPAS dynamical core) is under development and will drive the next phase of legacy model retirements.

---

## Notes
- RRFS is a convection-allowing model and does not use a cumulus parameterization.
- Not all legacy NAM and HiresW products are reproduced in RRFS; some products are generated via the Smartinit post-processing system applied to RRFS output.
- A new RRFS verification website will replace the legacy regional verification graphics at EMC once RRFS is officially implemented.

---

## Official documentation
- NWS Public Information Statement 25-41 (PDF):  
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- RRFS output grids:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/rrfs_grids.txt
- NOAA RRFS prototype on AWS:  
  https://registry.opendata.aws/noaa-rrfs/
