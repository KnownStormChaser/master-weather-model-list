# HAFS (Hurricane Analysis and Forecast System)

## What this model is
The Hurricane Analysis and Forecast System (HAFS) is a specialized, coupled tropical cyclone numerical weather prediction system developed within NOAA's **Unified Forecast System (UFS)** framework. It is NOAA's operational hurricane forecast model, replacing the legacy HWRF and HMON systems in 2023.

HAFS is designed specifically to forecast tropical cyclones and their associated hazards, using storm-centered, moving high-resolution nests to resolve the inner-core structure, intensity change, and air–sea interactions. The system includes specialized vortex initialization and inner-core data assimilation tailored to hurricane prediction, and is coupled to ocean and wave models to capture air-sea feedbacks that drive intensity change and rapid intensification.

HAFS runs in two parallel operational configurations — **HFSA** (primary) and **HFSB** (secondary) — both upgraded together at each version release. Each forecast cycle is tied to a specific active tropical cyclone rather than running on a fixed schedule.

The current version is **HAFSv2.1**, implemented operationally on July 29, 2025.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country:** United States
- **Developed by:** NOAA Environmental Modeling Center (EMC) and the UFS Hurricane Application Team
- **Operational implementation:** NCEP Central Operations (NCO)

---

## When this model runs
HAFS **runs only when an active tropical cyclone exists**. Each forecast is triggered by official storm messages (warnings or invest areas issued by the National Hurricane Center, Central Pacific Hurricane Center, or Joint Typhoon Warning Center) and is tied to a specific storm and forecast cycle rather than a fixed geographic domain.

Cycles are run at 00, 06, 12, and 18 UTC for each active storm.

---

## What area it covers
- **Coverage:** Storm-centered moving domains following tropical cyclones globally
- **Basins supported:**
  - North Atlantic (NATL)
  - Eastern Pacific (EPAC)
  - Central Pacific (CPAC)
  - Western Pacific (WPAC)
  - North Indian Ocean (NIO)
  - Southern Hemisphere (SH)

The moving nest follows the storm center, providing kilometer-scale resolution wherever tropical cyclones develop. The system supports multiple simultaneous storms across basins.

---

## Configurations: HFSA and HFSB

HAFS runs in two operational configurations:

- **HFSA (HAFS-A):** Primary operational configuration, the main forecast guidance distributed to operational forecasters.
- **HFSB (HAFS-B):** Secondary operational configuration, providing a parallel forecast with slightly different physics and initialization choices to give forecasters dual-system guidance.

Both configurations are upgraded together at each version release. Both are publicly available through the same data distribution channels under separate `hfsa.*` and `hfsb.*` directory and file naming patterns.

---

## Basic details
- **Model type:** Tropical cyclone–specific NWP system, coupled atmosphere–ocean–wave
- **Framework:** Unified Forecast System (UFS)
- **Atmospheric core:** FV3 (Finite-Volume Cubed-Sphere)
- **Coupling framework:** CMEPS (Community Mediator for Earth Prediction Systems)
- **Horizontal resolution:**
  - Parent domain: ~13 km
  - Moving nest(s): ~2 km
- **Forecast length:** 5.25 days (126 hours), output every 3 hours from f000 to f126
- **Update frequency:** Every 6 hours per active storm (00, 06, 12, 18 UTC cycles)
- **Output domains:** Both `parent` (full domain at ~13 km) and `storm` (moving nest at ~2 km) are distributed for each forecast hour
- **Output content:** Atmospheric (`atm`), satellite-simulated brightness temperature (`sat`), swath summary (`swath`), and ATCF track (`trak.atcfunix`) products

---

## Data assimilation and initialization

HAFS uses a sophisticated tropical cyclone–specific data assimilation system designed to improve intensity, rapid intensification, and storm structure forecasts.

### Vortex initialization
- Storm vortex relocation and initialization
- Improved VI for more accurate storm intensity representation (v2.1)
- Wavenumber filtering applied to DA increments (v2.1)
- Storm-following Three-Dimensional Incremental Analysis Update (3DIAU) in inner-core DA (v2.1)

### Assimilated observations
**Aircraft reconnaissance:**
- Tail Doppler Radar (TDR) data
- High-Density Observations (HDOB)
- Dropsondes

**Satellite observations:**
- Atmospheric Motion Vectors (AMVs)
- GPS Radio Occultation
- NOAA-21 Advanced Technology Microwave Sounder (ATMS) — added in v2.1
- NOAA-21 Cross-Track Infrared Sounder (CrIS) — added in v2.1

**Removed in v2.1:**
- P-3 aircraft Stepped Frequency Microwave Radiometer (SFMR) surface wind speed
- C-130 aircraft SFMR surface wind speed

The SFMR removal is operationally significant — earlier HAFS versions assimilated these aircraft-borne surface wind retrievals, but they have been turned off in v2.1.

---

## Earth-system components
HAFS is a coupled multi-component Earth system prediction model. Components include:

- **Atmosphere:** FV3
- **Ocean:** Initialized from RTOFS v2.5 (deployed simultaneously with HAFSv2.1); HYCOM core; v2.1 includes upgraded ocean coupling and improved ocean mixed layer scheme
- **Waves:** WAVEWATCH III
- **Sea ice:** CICE (when relevant for higher-latitude storms)
- **Land:** Noah-MP

The atmosphere–ocean–wave coupling is online via CMEPS, allowing real-time exchange of fluxes and surface fields during the forecast.

---

## Atmospheric physics

The HAFSv2.1 upgrade brought several physics scheme improvements based on the July 2024 UFS revision:

- **Convection:** Improved Scale-Aware Simplified Arakawa-Schubert (sa-SAS) scheme with scale-adaptive convective cloud water calculations and prognostic sigma closure for all TC basins
- **Boundary layer:** Improved Turbulent Kinetic Energy (TKE)-based Eddy-Diffusivity Mass-Flux (EDMF) PBL scheme
- **Radiation:** Exponential-random cloud overlap method enabled in the Rapid Radiative Transfer Model for GCMs (RRTM-G)

---

## What it predicts
- Tropical cyclone track
- Maximum sustained winds and minimum central pressure
- Storm structure and size
- Rainfall and precipitation totals
- Surface winds and hazards
- Storm-generated waves and air-sea interaction effects
- Storm surge precursor fields (winds and pressure for input to surge models)

Primary outputs include **ATCF track and intensity guidance** (the standard format used operationally by NHC and JTWC forecasters), along with gridded forecast fields on both the parent domain and the storm-following moving nest.

---

## Data availability

### Distribution channels

HAFS data is publicly available through three channels:

**1. NOMADS (NCEP operational distribution):**
- Production: https://nomads.ncep.noaa.gov/pub/data/nccf/com/hafs/prod/
- Parallel feed: https://nomads.ncep.noaa.gov/pub/data/nccf/com/hafs/para/
- FTP equivalent: ftp://ftp.ncep.noaa.gov/data/nccf/com/hafs/prod/

**2. AWS Open Data (NOAA Open Data Dissemination):**
- S3 bucket: `s3://noaa-nws-hafs-pds/`
- Browser access: https://noaa-nws-hafs-pds.s3.amazonaws.com/index.html
- AWS CLI (no account required): `aws s3 ls --no-sign-request s3://noaa-nws-hafs-pds/`
- AWS region: `us-east-1`
- SNS notifications for new data: `arn:aws:sns:us-east-1:709902155096:NewHAFSObject` (Lambda and SQS protocols)

**3. NCEP parallel/sample feeds (for development testing):**
- HFSA samples: https://www.emc.ncep.noaa.gov/gc_wmb/vxt/zack/HAFS_Sample_Files/HFSA_sample/hafs/v2.1/
- HFSB samples: https://www.emc.ncep.noaa.gov/gc_wmb/vxt/zack/HAFS_Sample_Files/HFSB_sample/hafs/v2.1/

### Data details
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (gridded), ATCF (track files)
- **Licence:** NOAA public domain data — open use, attribution requested
- **File structure:** Files are organized by configuration (`hfsa` or `hfsb`), date, cycle, then storm. Each storm's directory contains parent atmospheric files, storm-centered moving nest files, satellite-simulated brightness temperature, swath summaries, and ATCF track files at 3-hour intervals from f000 to f126.

---

## Version history

### July 29, 2025 — HAFSv2.1 (current)
- Model code updated to UFS revision of July 3, 2024
- New atmospheric physics: improved sa-SAS convection, TKE-EDMF PBL, exponential-random cloud overlap in RRTM-G
- Ocean model now initialized from RTOFS v2.5 (deployed simultaneously); upgraded ocean coupling; improved mixed layer scheme
- Improved vortex initialization for storm intensity representation
- Wavenumber filtering on DA increments
- Storm-following 3DIAU in inner-core DA
- NOAA-21 ATMS and CrIS observations added to assimilation
- P-3 and C-130 SFMR surface wind observations removed from assimilation

### 2024 — HAFSv2.0
- Operational upgrade to v2.0 with HFSA and HFSB configurations
- Continued physics, DA, and coupling refinements

### June 2023 — HAFSv1.0 (initial operational implementation)
- HAFS replaced HWRF and HMON as NOAA's operational hurricane forecast system
- First operational deployment of moving-nest hurricane forecasting in the UFS framework
- Initial atmosphere–ocean–wave coupling via CMEPS

### Pre-2023 — Development and pre-operational testing
- Multi-year development under the UFS Hurricane Application Team
- Built on prior FV3-based hurricane modeling experiments

---

## Relationship to other models

### Predecessors
HAFS replaced **HWRF (Hurricane Weather Research and Forecasting)** and **HMON (Hurricanes in a Multi-scale Ocean-coupled Non-hydrostatic)** as NOAA's operational hurricane forecast system in 2023. Both legacy systems have been retired.

### Companion NOAA operational models
- **GFS (Global Forecast System):** Provides large-scale environmental conditions and boundary information
- **GEFS (Global Ensemble Forecast System):** Provides ensemble track guidance complementing HAFS deterministic intensity guidance
- **RTOFS (Real-Time Ocean Forecast System):** RTOFS v2.5 provides ocean initial conditions for HAFSv2.1's coupled ocean component

### International peers
HAFS is one of several operational tropical cyclone-focused NWP systems globally. Peer systems include:
- **HMW (Met Office hurricane configuration of MetUM)** — UK Met Office
- **CMA-TYM (China Meteorological Administration tropical cyclone model)**
- **JMA Typhoon Model** — Japan Meteorological Agency

These systems differ in nest design, coupling architecture, and DA configurations, and are typically used as part of multi-model consensus guidance by operational forecasters.

---

## Notes
- HAFS output availability depends on tropical cyclone activity and operational scheduling. The system is **not intended for general weather forecasting outside of active storms**. During quiet periods in all basins, no HAFS data is produced.
- The two configurations (HFSA and HFSB) are designed to provide forecasters with parallel guidance from slightly different model setups, similar in spirit to the dual-deterministic approach used historically with GFS and HWRF, or with HWRF and HMON.
- The forecast length of 5.25 days (126 hours) is shorter than typical global model forecasts because tropical cyclone forecast skill — particularly for intensity — degrades rapidly beyond this range, and the computational cost of the high-resolution moving nest is significant. The AWS Open Data registry refers to the system's design goal of "out to seven days," but this has not yet been operationally implemented as of HAFSv2.1.
- The removal of SFMR observations in v2.1 is a notable methodological change. SFMR provides aircraft-measured surface wind speeds in tropical cyclones and was previously a key data source for inner-core wind structure DA. The reasons for its removal are discussed in NOAA's HAFSv2.1 implementation documentation.
- HAFSv2.1's parallel deployment with RTOFS v2.5 is operationally significant — HAFS now uses ocean conditions from a more recent and improved version of NOAA's operational global ocean forecast, addressing a known weakness in earlier HAFS versions where ocean initialization quality affected hurricane intensity forecasts.

---

## Official documentation
- WPO HAFS overview: https://wpo.noaa.gov/the-hurricane-analysis-and-forecast-system-hafs/
- HAFS GitHub: https://github.com/hafs-community/HAFS
- HAFSv2.1 implementation notice (July 2025): https://www.weather.gov/media/notification/pdf_2025/scn25-48_updated_HAFS_v2.1_aaa.pdf
- AWS Open Data registry: https://registry.opendata.aws/noaa-nws-hafs/
- NOAA Open Data Dissemination (NODD) program: https://www.noaa.gov/information-technology/open-data-dissemination
- UFS hurricane application: https://ufscommunity.org/

### Operational contacts
- Hurricane Modeling Project Lead: Dr. Zhan Zhang, NOAA/NCEP/EMC (zhan.zhang@noaa.gov)
- HAFS development team: NOAA EMC and AOML

### Key references
- WPO HAFS program overview (NOAA Weather Program Office)
- AOML "Storm-following hurricane model improves intensity forecasts": https://www.aoml.noaa.gov/hurricane-model-that-follows-multiple-storms-improves-intensity-forecasts/
- NCEP Service Change Notice 25-48 (HAFSv2.1 implementation, July 2025)
