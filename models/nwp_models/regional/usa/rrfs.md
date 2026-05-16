# RRFS (Rapid Refresh Forecast System)

## What this model is
The Rapid Refresh Forecast System (RRFS) is NOAA's next-generation convection-allowing, hourly-updating regional numerical weather prediction system for North America.

RRFS is built on the Unified Forecast System (UFS) framework and is designed to consolidate and replace several legacy NCEP regional modeling systems, including the NAM, HiresW (except the Guam domain), HREF, SREF, and NARRE. It provides both deterministic and ensemble guidance, with the ensemble component distributed as REFS (RRFS Ensemble Forecast System).

RRFS and REFS are scheduled to become operational on **August 31, 2026 at 12 UTC** under NWS Service Change Notice 26-48 (May 12, 2026), subject to the standard CWD/ECE postponement contingency. A pre-implementation real-time parallel feed is expected on NOMADS on or about June 9, 2026.

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
- **Forecast length:**
  - **84 hours** at the 00, 06, 12, and 18 UTC synoptic cycles
  - **18 hours** at the other 20 hourly cycles
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

## Ensemble member output (used by REFS)
In addition to the deterministic forecast, the RRFS run produces **five ensemble forecast members** at the 00, 06, 12, and 18 UTC cycles, running to 60 hours over the same North America domain. These members use different initial conditions, lateral boundary conditions, and model physics relative to the deterministic forecast. The members are consumed by [REFS](../../../ensemble_models/regional/usa/refs.md) (along with 6 h time-lagged copies of both the deterministic and ensemble RRFS, and HRRR members for CONUS/AK) to generate combined ensemble products.

---

## Output organization

Under `rrfs.YYYYMMDD/CC/` on NOMADS, the operational output naming convention is:

- `rrfs.tCCz.prslev.3km.fFFF.{conus|ak}.grib2` — pressure-level output, 3 km CONUS/AK
- `rrfs.tCCz.prslev.2p5km.fFFF.{hi|pr}.grib2` — pressure-level output, 2.5 km HI/PR
- `rrfs.tCCz.2dfld.3km.fFFF.{conus|ak}.grib2` — 2D fields (single-level), 3 km CONUS/AK
- `rrfs.tCCz.2dfld.2p5km.fFFF.{hi|pr}.grib2` — 2D fields, 2.5 km HI/PR
- `rrfs.tCCz.2dfld.3km.subh.fFFF.{conus|ak}.grib2` — 15-minute subhourly 2D fields, 3 km CONUS/AK
- `rrfs.tCCz.2dfld.2p5km.subh.fFFF.{hi|pr}.grib2` — 15-minute subhourly 2D fields, 2.5 km HI/PR

Fire-weather output is provided under a separate `firewx.YYYYMMDD/CC/` directory:

- `rrfs.tCCz.prslev.1p5km.fFFF.firewx_lcc.grib2`
- `rrfs.tCCz.2dfld.1p5km.fFFF.firewx_lcc.grib2`

A full description of RRFS products including variables and encoding is available at https://www.nco.ncep.noaa.gov/pmb/products/rrfs.

---

## Relationship to other models
RRFS is intended to replace the following legacy NCEP regional systems on August 31, 2026:
- **NAM** (12 km parent domain and 3 km nests – CONUS, AK, HI, PR, fire weather)
- **NAM Nest**
- **HiresW** (all domains except Guam)
- **HREF** (replaced by REFS)
- **SREF** (replaced by REFS)
- **NARRE** (replaced by REFS)
- **NAM MOS** (retired alongside NAM)

HRRR, RAP, and the NAM 12 km parent domain are not retired with RRFSv1. These systems are expected to be retired later in conjunction with RRFSv2, which is planned to transition to the MPAS dynamical core. HRRR additionally contributes two members (current and 6 h old cycles) to the CONUS and Alaska REFS domains, making it an explicit operational input to REFS during the RRFSv1 era.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**
  - **NOMADS (pre-implementation parallel feed, on or about June 9, 2026):**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/rrfs/para/
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/para/noaaport/rrfs
  - **NOMADS (post-implementation, August 31, 2026):**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/rrfs/prod/
  - **AWS Open Data:** https://registry.opendata.aws/noaa-rrfs/ (prototype and operational data)

---

## Status
- Proposal for legacy model retirement published in NWS Public Information Statement 25-41 (June 26, 2025), with a public comment period through July 26, 2025.
- Originally targeted for operational implementation in early 2026; implementation slipped through pre-operational evaluation.
- **NWS Service Change Notice 26-48 (May 12, 2026)** scheduled RRFS and REFS operational implementation for August 31, 2026 at 12 UTC, with retirement of NAM, HREF, SREF, and HiresW (except Guam) on the same day. Per SCN 26-48, if the implementation date is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, implementation moves to 12 UTC on the next eligible weekday.
- Pre-implementation parallel data feed expected on NOMADS on or about June 9, 2026.
- RRFSv2 (based on the MPAS dynamical core) is under development and will drive the next phase of legacy model retirements (HRRR, RAP, NAM 12 km parent).

---

## Notes
- RRFS is a convection-allowing model and does not use a cumulus parameterization.
- Not all legacy NAM and HiresW products are reproduced in RRFS; some products are generated via the Smartinit post-processing system applied to RRFS output.
- A new RRFS verification website will replace the legacy regional verification graphics at EMC once RRFS is officially implemented.
- The cycle structure (84 h at 00/06/12/18 UTC, 18 h at all other hourly cycles) means RRFS is materially different from both NAM (which produced 84-hour forecasts only 4× daily) and HRRR (which produces 18 h hourly with 48 h extended runs at 00/06/12/18 UTC). For downstream applications that depended on the NAM/HiresW 84-hour synoptic schedule, RRFS preserves that cadence at the same cycles. For applications that depended on hourly short-range cycling, RRFS provides equivalent coverage at 18 h.

---

## Official documentation
- NWS Service Change Notice 26-48 (RRFS and REFS implementation, May 12, 2026):  
  https://www.weather.gov/media/notification/pdf_2026/scn26-48_RRFS_and_REFS_Implementation.pdf
- NWS Public Information Statement 25-41 (legacy model retirement proposal, June 26, 2025):  
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- RRFS product description at NCO:  
  https://www.nco.ncep.noaa.gov/pmb/products/rrfs
- RRFS output grids:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/rrfs_grids.txt
- NOAA RRFS prototype on AWS:  
  https://registry.opendata.aws/noaa-rrfs/
- RRFS/REFS evaluation page (EMC):  
  https://www.emc.ncep.noaa.gov/users/meg/rrfsv1/index.html
- RRFS feedback contact: rrfs.feedback@noaa.gov
