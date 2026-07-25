# NAVGEM (Navy Global Environmental Model)

## What this model is
The Navy Global Environmental Model (NAVGEM) is a global numerical weather prediction model developed by the Naval Research Laboratory (NRL) and run operationally by the U.S. Navy's Fleet Numerical Meteorology and Oceanography Center (FNMOC) for worldwide atmospheric forecasting.

NAVGEM is the U.S. Navy's primary medium-range global NWP model and is used to support naval and maritime forecasting requirements, including provision of initial and boundary conditions for the Navy's regional, ocean, wave, and aerosol prediction systems. It is the successor to the Navy Operational Global Atmospheric Prediction System (NOGAPS), which it replaced operationally on February 13, 2013. The current operational version is NAVGEM 2.0, which became operational on April 29, 2020.

---

## Who runs it
- **Organization:** Fleet Numerical Meteorology and Oceanography Center (FNMOC), with model development by the Naval Research Laboratory – Monterey
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Deterministic global NWP
- **Model system / core:**
  Semi-Lagrangian / semi-implicit (SL/SI) hydrostatic dynamical core
- **Dynamical formulation:** Hydrostatic, spectral, with semi-Lagrangian / semi-implicit time integration
- **Convection-allowing:** No (deep convection is parameterized at ~19 km resolution)
- **Spectral resolution:** Triangular truncation T681
- **Horizontal resolution:** ~19 km native; distributed on NOMADS at 0.5° (361 × 720 regular lat-lon grid)
- **Vertical levels:** 60 native (the NOMADS product is distributed on 21 pressure levels, 1000–70 hPa)
- **Model top:** ~0.04 hPa (~70 km)
- **Forecast length:**
  180 hours (7.5 days)
- **Update frequency / cycles:**
  4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (NOMADS GRIB2 distribution):**
  - 3-hourly through forecast hour 24
  - 6-hourly from forecast hour 30 through 180

---

## Data assimilation
NAVGEM uses **NAVDAS-AR** (NRL Atmospheric Variational Data Assimilation System – Accelerated Representer), a four-dimensional variational (4D-Var) data assimilation system. NAVDAS-AR was inherited from NOGAPS and has been operational since 2009. It assimilates conventional and satellite observations including radiances, GPS radio occultation, and atmospheric motion vectors, with variational bias correction for satellite radiances.

---

## What it provides
Deterministic global forecasts of temperature, wind, humidity, pressure, and precipitation, plus tropical-cyclone environment and track guidance. In full operational form NAVGEM also produces surface fluxes for ocean/wave forcing and fields extending into the mesosphere, but **the public NOMADS GRIB2 is a subset** — it tops out at 70 hPa and carries no flux, cloud, or radiation fields (see the NOMADS product contents under Data availability for the exact field set).

NAVGEM output is widely used as:
- Atmospheric forcing for ocean, sea-ice, and wave models
- Initial and boundary conditions for limited-area models
- The atmospheric component of the Navy Earth System Prediction Capability (ESPC)
- One of the three contributing single-center ensembles (via the related NAVGEM Ensemble) to the [557th WW GEPS](../../../ensemble_models/global/usa/557wg-geps.md) multi-model ensemble

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location (NOMADS):**
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/fnmoc/prod/
- **File path pattern:** `navgem.YYYYMMDD/navgem_YYYYMMDDHHfFFF.grib2`
  (where `YYYYMMDDHH` is the cycle date and time, and `FFF` is the forecast hour)
- **NOMADS product contents (public GRIB2 subset — live-verified against the 2026-07-24 00Z cycle):**
  The public NOMADS feed is a regridded 0.5° subset of the full operational output (~89 fields per file), not the complete model state.
  - **Pressure levels:** 21 isobaric levels, 1000 → 70 hPa. The product does **not** reach the model's mesospheric top — 70 hPa (~18 km) is the ceiling. Per-variable coverage varies: geopotential height, temperature, and winds on 12 levels; relative humidity on 15 (300–1000 hPa); vertical velocity on 11; absolute vorticity at 500 hPa only.
  - **Near-surface:** 2 m temperature; 10 m and 20 m winds; and a 20 m **wind-gust** field (FNMOC-local parameter `0/2/243`). The 20 m level and gust are Navy marine-forecasting features.
  - **Other single-level:** MSLP; tropopause temperature and pressure; max-wind-level temperature, height, pressure, and wind.
  - **Precipitation:** total and convective precipitation as **6-hour accumulations, present only on 6-hourly-aligned steps** (f006, f012, f018, f024, …); the intermediate 3-hourly steps carry none. Total precip encodes as WMO `0/1/8` (APCP) but reports as `unknown` under eccodes 2.48 — resolve via GRIB2 Code Table 4.2.
  - No surface-flux, cloud, or radiation fields are distributed in the public GRIB2 (they exist in full ops but not here).

---

## Version history

### NAVGEM 2.0 — operational April 29, 2020 (current)
- Spectral resolution increased from T425 to T681 (~31 km → ~19 km)
- Distributed on a 2048 × 1024 Gaussian grid (effective ~0.176° native), regridded to 0.5° for NOMADS distribution
- Continued use of T681L60 vertical structure with model top at ~0.04 hPa

### NAVGEM 1.4 — operational October 2016
- Spectral resolution at T425L60 (~31 km, 60 vertical levels)
- Used as atmospheric forcing for GOFS 3.1 and several downstream Navy ocean/wave systems

### NAVGEM 1.3 — operational June 2015
- Earlier T425L50 configuration; replaced by 1.4 in October 2016

### NAVGEM 1.2 — operational November 6, 2013
- Added a mass flux scheme (in addition to eddy diffusion vertical mixing) to reduce the cold temperature bias of the lower troposphere over ocean

### NAVGEM 1.1 — operational February 13, 2013
- Initial NAVGEM transition, replacing NOGAPS
- T359L50 spectral resolution
- Introduced semi-Lagrangian/semi-implicit dynamical core
- Cloud liquid water, cloud ice water, and ozone added as fully predicted constituents
- Rapid Radiative Transfer Model for GCMs (RRTMG) introduced for shortwave and longwave radiation

---

## Notes
- NAVGEM uses a semi-Lagrangian / semi-implicit formulation to allow higher global resolution without prohibitively small time steps, a key limitation of its predecessor NOGAPS.
- The model is developed primarily at the Naval Research Laboratory and transitioned operationally at FNMOC.
- A USGODAE archive path (`usgodae.org/ftp/outgoing/fnmoc/models/navgem_0.5/`) was previously listed as a "higher-resolution archive." It was removed: the path returns HTTP 404 (verified 2026-07-25), and the 0.5° USGODAE feed was a resolution upgrade over the retired 1° NOGAPS — the same 0.5° as NOMADS, not finer. NOMADS is the confirmed public source.
- NAVGEM serves as the atmospheric core of the Navy Earth System Prediction Capability (ESPC), where it is coupled with HYCOM (ocean), CICE (sea ice), and WAVEWATCH III (waves).
- A separate **NAVGEM Ensemble** (NAVGEM-EPS) is also operated by FNMOC at coarser resolution (historically T359L60) with 21 members and a 16-day forecast length. The NAVGEM Ensemble is one of the three contributing systems to the [557th WW GEPS](../../../ensemble_models/global/usa/557wg-geps.md) multi-model ensemble. The NAVGEM Ensemble is a separate system from the deterministic NAVGEM described here.
- While NAVGEM data are publicly available, the model is optimized for naval and maritime applications rather than civilian public forecasting.

---

## Official documentation
- https://www.metoc.navy.mil/fnmoc/fnmoc.html
- NRL NAVGEM page: https://www.nrlmry.navy.mil/metoc/nogaps/navgem.html
- Hogan et al. (2014), *The Navy Global Environmental Model*, Oceanography 27(3):116–125, https://doi.org/10.5670/oceanog.2014.73
