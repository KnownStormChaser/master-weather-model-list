# GFS (Global Forecast System)

## What this model is
The Global Forecast System (GFS) is a **global deterministic numerical weather prediction (NWP) model** operated by the U.S. National Centers for Environmental Prediction (NCEP).

It produces medium- to extended-range forecasts of atmospheric, land, and near-surface conditions worldwide.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global deterministic NWP
- **Dynamical core:** FV3 (Finite-Volume Cubed-Sphere)
- **Data assimilation:** GSI (Grid-Point Statistical Interpolation)
- **Native horizontal resolution:** ~13 km (C768 cubed-sphere grid)
- **Public output grids:**  
  - ~0.25° (~28 km) for most standard products  
  - ~0.5° (~50–70 km) for extended-range products
- **Vertical levels:** 127 (surface to mesopause)
- **Forecast length:** Up to 16 days
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)

---

## What it provides
Deterministic forecasts of:
- temperature, wind, pressure, humidity
- precipitation and cloud fields
- land and soil variables (e.g. soil moisture, surface fluxes)
- additional atmospheric tracers (e.g. ozone, by product)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://www.ncei.noaa.gov/products/weather-climate-models/global-forecast  
  https://nomads.ncep.noaa.gov/

---

## Notes
The operational GFS uses **warm-start initial conditions** derived from the Global Data Assimilation System (GDAS).

GFS serves as the deterministic counterpart to the **Global Ensemble Forecast System (GEFS)**.

---

## Upcoming changes

### GFSv17 and GDASv17 — proposed for October 2026
NOAA/NWS published **PNS 26-29** on April 15, 2026, proposing a major upgrade of GFS and GDAS from v16 to v17, targeted for October 2026. A companion notice, **PNS 26-30** (April 16, 2026), covers associated product removals.

This is a proposal open for public comment — not a scheduled implementation. The comment periods run through May 15, 2026 (science changes) and May 16, 2026 (product removals). A formal Service Change Notice would be issued 30 days before implementation if NOAA proceeds.

**Structural changes:**
- Transition from atmosphere-centric model to a **fully coupled Earth-system model** with atmosphere, land, ocean, sea ice, and waves
- Deterministic atmospheric resolution increase from **C768 (~13 km) to C1152 (~9 km)** on the FV3 dynamical core
- Fractional grids introduced along oceanic coastlines for improved atmosphere-land-ocean coupling

**New Earth-system components:**
- **MOM6** ocean model on a 0.25° tripolar grid
- **CICE6** sea-ice model on a 0.25° tripolar grid
- **WAVEWATCH III** wave model with a new unstructured grid, receiving ice and current as inputs and feeding back to the atmosphere and ocean
- **CMEPS** mediator for coupling between Earth-system components, continuing use of the NUOPC/ESMF framework
- All components run within the **Unified Forecast System (UFS)** framework

**Atmospheric and land surface science changes:**
- Convection scheme revisions (triggering, rain evaporation, downdraft detrainment, shallow/deep separation, cellular-automata stochastic convective initiation, positive-definite tracer transport)
- New environmental wind shear parameterization and revised PBL-convection interaction targeting improved hurricane intensity forecasts
- New sea spray parameterization and updated surface roughness/stability treatment
- **GFDL one-moment cloud microphysics replaced by Thompson-Eidhammer two-moment scheme** with semi-Lagrangian hydrometeor sedimentation
- Radiation updates including MERRA-2 aerosol climatology and fixes for excessive shortwave radiation into the ocean at low sun angles
- **Noah LSM replaced by Noah-MP** (Noah with Multi-Parameterization)
- Unified orographic and non-orographic gravity wave drag, plus turbulent orographic form drag

**GDASv17 changes:**
- GSI Hybrid 4DEnVar upgraded to a multi-scale algorithm with scale-dependent localization
- **GLDAS replaced** by soil temperature and moisture analysis embedded in the existing atmospheric LETKF
- New **JEDI-based snow data assimilation** (2DVar) using station snow depth and IMS snow cover
- New **JEDI-based ocean and sea-ice data assimilation** (3DVar-FGAT) using altimetry (Jason-3, Sentinel-6 MF, SARAL/AltiKa, CryoSat-2), SST (VIIRS, AVHRR), sea-ice concentration (AMSR2), and in-situ Argo/drifter temperature and salinity observations

**Products slated for removal (per PNS 26-30):**
- Synthetic nadir ABI GOES-R GRIB2 files (`gfs.t{CC}z.special.grib2f{FFF}`, `gfs.t{CC}z.goessimpgrb2.0p25.f{FFF}`, `gfs.t{CC}z.goessimpgrb2f{FFF}.grd221`)
- Time-averaged categorical precipitation-type flags (`CRAIN`, `CSNOW`, `CFRZR`, `CICEP` in `pgrb2.LpLL` files) — removed because not physically meaningful
- All post-processed **GRIB1** products interpolated to CONUS Grid 211 — v17 will be GRIB2-only
- Wave buoy point outputs for buoys 44040, 44043, 44061, 51207, and 51210 (outside computational domain)
- NetCDF model history and analysis files (`atmf{FFF}.nc`, `sfcf{FFF}.nc`, `atmanl.nc`, `sfcanl.nc` for both GFS and GDAS)
- Lower-resolution **0.5° and 1.0° GRIB2** products including `pgrb2`, `pgrb2b`, and `pgrb2full` families
- Associated NOMADS GRIB filters for `gfs_0p50` and `gfs_1p00` will also stop serving

**Not yet published (as of April 2026):**
- Exact implementation day within October 2026
- Final Service Change Notice
- Full folder directory structure and file naming changes

**Comment paths:**
- Science proposal: `gfs.feedback@noaa.gov`
- Product removals: `emc.products.feedback@noaa.gov`

**Interpretation note:** A coupled 9 km GFS with a materially different GDAS initialization represents a large enough structural change that pre- and post-v17 forecast comparisons should be treated as separate model eras for validation, backtesting, and climatological analysis.

---

## Official documentation
- https://www.weather.gov/media/notification/pdfs/scn20-99gfs_v16_implementation.pdf
- https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/gfs.php
