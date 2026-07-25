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
- **Horizontal resolution:** ~33 km (T359) native; some Navy Earth-System documentation cites ~37 km for the same T359L60 ensemble configuration (see verification note). Distributed on NOMADS at two grids: 0.5° (720 × 361, `pgrb2ap5`) and 1.0° (360 × 181, `pgrb2a`).
- **Vertical levels:** 60 native (the NOMADS product is distributed on ~10 pressure levels, 1000–10 hPa)
- **Model top:** TBD (the deterministic NAVGEM L60 configuration extends into the upper atmosphere; confirm the ensemble's top)
- **Forecast length:** 16 days (384 hours)
- **Update frequency / cycles:** 2× daily (00, 12 UTC)
- **Temporal output resolution (live-verified 2026-07-24 12Z):** differs by grid — 0.5° (`pgrb2ap5`) is **3-hourly to f240, then 6-hourly to f384**; 1.0° (`pgrb2a`) is **6-hourly throughout (f000–f384)**.

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
The `fens` feed distributes **raw individual members only** — the control plus 20 perturbed members. Neither an ensemble mean nor the statistically **bias-corrected / downscaled** products appear under `fens/`: the mean is not published here (`femn` → HTTP 404), and the bias-corrected products are the separate combined [NAEFS](https://www.emc.ncep.noaa.gov/gmb/ens/NAEFS.html) product under `naefs.YYYYMMDD/` (a distinct feed, not verified here).

Each member GRIB2 file carries (live-verified 2026-07-24 12Z, 0.5°):
- Isobaric geopotential height, temperature, relative humidity, and U/V wind (surface plus ~10 levels, 1000–10 hPa), and vertical velocity at 850 hPa
- Surface up/down short- and longwave radiation and sensible/latent heat fluxes; precipitable water; surface pressure; MSLP
- 2 m temperature and relative humidity; 10 m winds
- Total precipitation (`0/1/8`) plus FNMOC-local precipitation parameters, as 6-hour accumulations at forecast steps (absent at f000)

Separately (not in these gridded files), the FNMOC ensemble contributes member tropical-cyclone genesis and track guidance to the ATCF.

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
  - Each cycle splits into two resolution subdirectories carrying the same primary ("a") field set at different grids and cadences:
    - `pgrb2ap5/` — 0.5° GRIB2 (720 × 361), 3-hourly to f240 then 6-hourly to f384 (recommended for most users)
    - `pgrb2a/` — 1.0° GRIB2 (360 × 181), 6-hourly f000–f384 (coarser legacy NAEFS grid)
  - Only the primary ("a") field set is distributed (no secondary `pgrb2b`).
  - Also available as an FTP mirror: `ftp://ftp.ncep.noaa.gov/pub/data/nccf/com/naefs/prod`
- **Parameter subsetting:** NOMADS grib-filter dataset `ds=fens` (https://nomads.ncep.noaa.gov/gribfilter.php?ds=fens)
- **File naming / member packaging (live-verified 2026-07-24 12Z):** one file per member per forecast hour, with the member token in the filename:
  - 0.5°: `ENSEMBLE.halfDegree.MET.fcst_etMMM.FFF.YYYYMMDDHH`
  - 1.0°: `ENSEMBLE.MET.fcst_etMMM.FFF.YYYYMMDDHH`
  - `MMM` = member (`000`–`020`): **`et000` is the control (`perturbationNumber` 0); `et001`–`et020` are the 20 perturbed members (`perturbationNumber` 1–20)**. `FFF` = forecast hour. (The earlier `fp##` / `fc00` / `femn` identifiers were incorrect — those paths return HTTP 404.)
  - GRIB2 encoding: Product Definition Template 4.1, `numberOfForecastsInEnsemble = 20`, `centre = fnmo`. FNMOC stamps a **local** `typeOfEnsembleForecast = 192` on every member (control and perturbed alike), so members must be distinguished by `perturbationNumber`, not by `typeOfEnsembleForecast`.

---

## Notes
- **Relationship to its deterministic counterpart:** the FNMOC Ensemble is the ensemble version of [NAVGEM](../../../nwp_models/global/usa/navgem.md), sharing its dynamical core and NAVDAS-AR analysis but run at coarser resolution (T359L60 vs the deterministic T681L60 in NAVGEM 2.0).
- **Relationship to siblings / multi-center systems:** the FNMOC Ensemble is one of three contributing single-center ensembles (with [GEFS](./gefs.md) and [Canadian GEPS](../canada/geps.md)) that form [NAEFS](https://www.emc.ncep.noaa.gov/gmb/ens/NAEFS.html) and the [557th WW GEPS](./557wg-geps.md) / NUOPC multi-model product. Its public distribution rides on the NAEFS feed rather than a standalone FNMOC NOMADS path.
- **Public data is distributed by NCEP, not FNMOC directly:** the raw and bias-corrected `fens` members are published through NCEP's NAEFS production stream on NOMADS. (The deterministic NAVGEM, by contrast, is published under `…/com/fnmoc/prod/`.)
- **Legacy NOGAPS description:** older NOMADS/FNMOC product blurbs still describe the EFS in NOGAPS terms (T119 ~90 km, 30 levels, ensemble transform over 5 latitude bands). Those specs predate the 2013 NOGAPS→NAVGEM transition and do not describe the current system.
- **Live verification (2026-07-24 12Z cycle):** member packaging, `perturbationNumber` range (0–20), grids, cadences, and the f384 horizon were confirmed by pulling raw `et000`/`et001`/`et020` files from both `pgrb2a` and `pgrb2ap5`. The prior `fp##`/`fc00`/`femn` file identifiers were found incorrect (404) and corrected to the `etMMM` scheme. The `~37 km` figure remains a documentation-only discrepancy — it can't be confirmed from the regridded 0.5°/1.0° public products.

---

## Official documentation
- NAEFS overview (NCEP EMC): https://www.emc.ncep.noaa.gov/gmb/ens/NAEFS.html
- NAEFS products inventory (NCO): https://www.nco.ncep.noaa.gov/pmb/products/naefs/
- FNMOC: https://www.metoc.navy.mil/fnmoc/fnmoc.html
- NRL NAVGEM page: https://www.nrlmry.navy.mil/metoc/nogaps/navgem.html
