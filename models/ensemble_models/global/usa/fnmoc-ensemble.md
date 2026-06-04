# FNMOC Ensemble (NAVGEM Ensemble)

## What this model is
The FNMOC Ensemble is the U.S. Navy's operational global ensemble forecast system, run by the Fleet Numerical Meteorology and Oceanography Center (FNMOC). It is the ensemble sibling of the deterministic [NAVGEM](../../../nwp_models/global/usa/navgem.md) global model, built around the same NAVGEM dynamical core and NAVDAS-AR analysis, and is run at coarser resolution to sample forecast uncertainty out to two weeks.

It is historically referred to as the FNMOC Ensemble Forecast System (EFS). The original EFS was built on the Navy Operational Global Atmospheric Prediction System (NOGAPS); since NOGAPS was retired in 2013 the system has been built on NAVGEM and is commonly called the **NAVGEM Ensemble** (or Navy-ESPCENS in the coupled Earth-system context).

The FNMOC Ensemble is one of the three single-center global ensembles — alongside NCEP's [GEFS](./gefs.md) and ECCC's [Canadian GEPS](../canada/geps.md) — that are combined into the multi-center [North American Ensemble Forecast System (NAEFS)](https://www.emc.ncep.noaa.gov/gmb/ens/NAEFS.html) and the related [557th WW GEPS](./557wg-geps.md) / NUOPC product. Its members are distributed publicly through the NAEFS feed on NOMADS, both as raw members and as statistically **bias-corrected** members.

---

## Who runs it
- **Organization:** Fleet Numerical Meteorology and Oceanography Center (FNMOC), with model development by the Naval Research Laboratory – Monterey
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Ensemble NWP (global)
- **Model system / core:** NAVGEM — semi-Lagrangian / semi-implicit (SL/SI) hydrostatic spectral dynamical core (same core as the deterministic [NAVGEM](../../../nwp_models/global/usa/navgem.md))
- **Dynamical formulation:** Hydrostatic, spectral, with semi-Lagrangian / semi-implicit time integration
- **Convection-allowing:** No (deep convection parameterized at ~33 km resolution)
- **Ensemble size:** 21 members (20 perturbed + 1 control)
- **Spectral resolution:** Triangular truncation T359
- **Horizontal resolution:** ~33 km (T359); some Navy Earth-System documentation cites ~37 km for the same T359L60 ensemble configuration (see verification note)
- **Vertical levels:** 60
- **Model top:** TBD (the deterministic NAVGEM L60 configuration extends into the upper atmosphere; confirm the ensemble's top)
- **Forecast length:** 16 days (384 hours)
- **Update frequency / cycles:** 2× daily (00, 12 UTC)
- **Temporal output resolution:** 6-hourly (raw member cadence on NOMADS to be confirmed; see verification note)

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** NAVDAS-AR (NRL Atmospheric Variational Data Assimilation System – Accelerated Representer), the same 4D-Var analysis used by the deterministic NAVGEM. The ensemble draws its analysis-error estimate from NAVDAS to initialize the ensemble-transform perturbations.

---

## Perturbations and design
- **Initial condition perturbations:** Ensemble Transform (ET) technique. A customized ET initialization rescales perturbations to match the analysis-error estimate from NAVDAS. The transform was historically solved over latitude bands (originally 5, later 9), tuning perturbation amplitude by latitude.
- **Model/physics perturbations:** TBD
- **Stochastic schemes:** TBD

---

## What it provides
Probabilistic global forecasts including:
- Individual ensemble member forecasts (20 perturbed + control)
- Ensemble mean
- Statistically **bias-corrected** member output (the "FNMOC Ensemble and Bias Corrected" product), produced as part of the NAEFS bias-correction step relative to a climatological reanalysis
- Tropical cyclone genesis and track guidance (the FNMOC ensemble contributes member tracks to the ATCF)
- Standard surface and upper-air fields (temperature, winds, geopotential height, humidity, pressure, etc.)

The FNMOC Ensemble also supplies atmospheric forcing for the Navy's 20-member global WAVEWATCH III wave ensemble.

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location (NOMADS, via the NAEFS feed):**
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/naefs/prod/
  - Daily/cycle directories: `fens.YYYYMMDD/CC/` (CC = 00 or 12)
  - Each cycle splits into two resolution subdirectories of the same primary field set:
    - `pgrb2ap5/` — 0.5° GRIB2 (recommended for most users)
    - `pgrb2a/` — 1.0° GRIB2 (coarser legacy NAEFS grid)
  - Only the primary ("a") field set is distributed (no secondary `pgrb2b`).
  - Also available as an FTP mirror: `ftp://ftp.ncep.noaa.gov/pub/data/nccf/com/naefs/prod`
- **Parameter subsetting:** NOMADS grib-filter dataset `ds=fens` (https://nomads.ncep.noaa.gov/gribfilter.php?ds=fens)
- **File identifiers (within `fens`):** perturbed members `fp##`, control `fc00`, ensemble mean `femn` (genesis/track files use `femn`/`ngx`)

---

## Notes
- **Relationship to its deterministic counterpart:** the FNMOC Ensemble is the ensemble version of [NAVGEM](../../../nwp_models/global/usa/navgem.md), sharing its dynamical core and NAVDAS-AR analysis but run at coarser resolution (T359L60 vs the deterministic T681L60 in NAVGEM 2.0).
- **Relationship to siblings / multi-center systems:** the FNMOC Ensemble is one of three contributing single-center ensembles (with [GEFS](./gefs.md) and [Canadian GEPS](../canada/geps.md)) that form [NAEFS](https://www.emc.ncep.noaa.gov/gmb/ens/NAEFS.html) and the [557th WW GEPS](./557wg-geps.md) / NUOPC multi-model product. Its public distribution rides on the NAEFS feed rather than a standalone FNMOC NOMADS path.
- **Public data is distributed by NCEP, not FNMOC directly:** the raw and bias-corrected `fens` members are published through NCEP's NAEFS production stream on NOMADS. (The deterministic NAVGEM, by contrast, is published under `…/com/fnmoc/prod/`.)
- **Legacy NOGAPS description:** older NOMADS/FNMOC product blurbs still describe the EFS in NOGAPS terms (T119 ~90 km, 30 levels, ensemble transform over 5 latitude bands). Those specs predate the 2013 NOGAPS→NAVGEM transition and do not describe the current system.

---

## Official documentation
- NAEFS overview (NCEP EMC): https://www.emc.ncep.noaa.gov/gmb/ens/NAEFS.html
- NAEFS products inventory (NCO): https://www.nco.ncep.noaa.gov/pmb/products/naefs/
- FNMOC: https://www.metoc.navy.mil/fnmoc/fnmoc.html
- NRL NAVGEM page: https://www.nrlmry.navy.mil/metoc/nogaps/navgem.html
