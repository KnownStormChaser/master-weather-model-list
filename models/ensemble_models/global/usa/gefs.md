# GEFS (Global Ensemble Forecast System)

## What this model is
The Global Ensemble Forecast System (GEFS) is the United States' primary global ensemble numerical weather prediction system, operated by NOAA's National Centers for Environmental Prediction (NCEP).

GEFS is the ensemble counterpart to the deterministic [GFS](../../../nwp_models/global/usa/gfs.md), built on the same FV3-based atmospheric model and the same Earth system framework, with 31 ensemble members (1 control + 30 perturbed) running 4× daily. It produces probabilistic medium-range forecasts for most cycles and extends to **35 days at the 00 UTC cycle**, providing the United States' operational sub-seasonal forecast guidance.

GEFS is also a contributing member to the [North American Ensemble Forecast System (NAEFS)](./naefs.md), the [557th WW GEPS](./557wg-geps.md) multi-model ensemble, and the [HGEFS](./hgefs.md) hybrid physics-AI grand ensemble — making it one of the most heavily-used ensemble forecast products in operational meteorology.

The current operational version is **GEFSv12** (operational since 23 September 2020), with **GEFSv13** under development for joint deployment alongside [GFSv17](../../../nwp_models/global/usa/gfs.md#upcoming-changes) (proposed for October 2026).

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global ensemble NWP
- **Model system / core:** Unified Forecast System (UFS) atmospheric model with **FV3** (Finite-Volume Cubed-Sphere) dynamical core
- **Dynamical formulation:** Non-hydrostatic, on a cubed-sphere finite-volume grid; semi-Lagrangian vertical coordinate
- **Convection-allowing:** No (deep convection is parameterized at ~25 km resolution)
- **Ensemble size:** 31 (1 control + 30 perturbed). A **32nd unperturbed member (GEFS-Aerosols)** runs separately to 5 days with the GOCART aerosol chemistry component (one-way coupled).
- **Native horizontal resolution:** ~25 km (C384 cubed-sphere grid)
- **Public output grids:**
  - 0.25° (~28 km) — primary output
  - 0.5° (~55 km) — alternative resolution
- **Vertical levels:** 64 (hybrid sigma-pressure)
- **Forecast length:**
  - **35 days (840 h)** for the 00 UTC cycle (sub-seasonal range)
  - 16 days (384 h) for 06, 12, and 18 UTC cycles
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Coupled wave model:** WAVEWATCH III ensemble, replacing the previous standalone Global Wave Ensemble

---

## Initial conditions
GEFS is initialized from **6-hour EnKF forecasts** drawn from the **same 80-member EnKF ensemble** that informs the GFS hybrid 4DEnVar analyses (see [GDAS in the GFS entry](../../../nwp_models/global/usa/gfs.md#data-assimilation)).

This shared infrastructure means GEFS benefits from the full GDAS observation suite — AMSU-A, ATMS, IASI, AIRS, CrIS, GPS-RO, scatterometer winds, atmospheric motion vectors, conventional observations — without running its own separate analysis cycle. The EnKF perturbations are recentered around the deterministic GFS analysis before being used as GEFS initial conditions.

The use of EnKF forecast perturbations replaced the older breeding-based scheme (with ensemble transformation and rescaling, ETR) in GEFSv11 (December 2015).

---

## Perturbations and design
- **Initial condition perturbations:** 6-hour forecasts from the GDAS EnKF ensemble, providing flow-dependent perturbations sampled from the full operational data assimilation system
- **Model uncertainty representation:** Two complementary stochastic schemes:
  - **5-scale Stochastically Perturbed Physics Tendencies (SPPT)** — perturbs the total physical tendencies at five spatial scales (Zhu et al. 2019; Zhou et al. 2019)
  - **Stochastic Kinetic Energy Backscatter (SKEB)** — adds small-scale kinetic energy perturbations to represent unresolved processes (Berner et al. 2009)
- **Stochastic schemes — historical note:** SPPT and SKEB replaced the long-standing **STTP** (Stochastic Total Tendency Perturbation) scheme in GEFSv12 (September 2020), specifically motivated by the need for better ensemble spread at sub-seasonal lead times.
- **Lower boundary conditions:** A **2-tiered SST approach** combining NSST analyses with bias-corrected CFS forecasts handles the evolving SST field across the 35-day forecast range. Climatology-relaxation (the v11 approach) was inadequate for sub-seasonal forecasts.
- **No tropical cyclone relocation:** Unlike older GEFS versions which relocated TCs in all members to match observed positions, GEFSv12 does not perform TC relocation, allowing the ensemble to sample uncertainty in storm position naturally.

---

## What it provides
Probabilistic global forecasts including:
- Individual ensemble member forecasts (30 perturbed + 1 control)
- Ensemble mean and ensemble spread
- Surface and upper-air fields: temperature, wind components, geopotential height, specific humidity, mean sea level pressure
- Total precipitation, convective precipitation, snowfall, and precipitation type
- Cloud cover, hydrometeor fields, and convective indices
- Surface fluxes, radiation components, and boundary-layer diagnostics
- Land and soil variables: soil moisture, soil temperature, snow depth and water equivalent
- Wave forecasts (from coupled WAVEWATCH III ensemble)
- Aerosol forecasts to 5 days (from the GEFS-Aerosols 32nd member)
- Sub-seasonal probabilistic guidance (00 UTC cycle only)

---

## Reanalysis and reforecast
GEFSv12 includes a **30-year (1989–2019) reanalysis and reforecast** dataset, supporting downstream calibration, anomaly forecasting, and bias correction applications. The reforecast is one of the most widely-used such datasets in operational meteorology and underpins many calibrated ensemble products including those in the [NAEFS](./naefs.md) bias-correction system.

---

## Relationship to other models
- **[GFS](../../../nwp_models/global/usa/gfs.md):** Deterministic counterpart, sharing the FV3 dynamical core and GDAS analysis infrastructure
- **[NAEFS](./naefs.md):** Multi-center ensemble combining GEFS with Canadian GEPS and (for the 557th WW GEPS) FNMOC NAVGEM ensemble; provides bias-corrected probabilistic guidance across North America
- **[557th WW GEPS](./557wg-geps.md):** U.S. Air Force statistical multi-model ensemble that uses GEFS as one of its three contributing single-center ensembles
- **[HGEFS](./hgefs.md):** Hybrid grand ensemble combining the 31 GEFS members with 31 [AIGEFS](./aigefs.md) AI-based members, providing a 62-member physics-plus-AI ensemble
- **[AIGEFS](./aigefs.md):** AI-based ensemble counterpart trained to emulate GEFS behavior
- **[GFSv17](../../../nwp_models/global/usa/gfs.md#upcoming-changes) and GEFSv13:** Co-developed; GEFSv13 will be the ensemble counterpart to the proposed coupled Earth-system GFSv17

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data formats:** GRIB2
- **Official download locations:**
  - **NOMADS:** https://nomads.ncep.noaa.gov/ (real-time, 10-day rolling)
  - **NCEI archive:** https://www.ncei.noaa.gov/products/weather-climate-models/global-ensemble-forecast (long-term archive; pre-September 2020 at older resolutions)
  - **AWS Open Data:** https://registry.opendata.aws/noaa-gefs/ (real-time, with archive from 2017 onward)
  - **AWS reforecast archive:** https://registry.opendata.aws/noaa-gefs-reforecast/ (30-year reforecast dataset)
  - **Microsoft Planetary Computer** and **Google Cloud Storage** mirrors are also available via the NOAA Big Data Program

The NOAA Big Data Program provides parallel access via AWS, Azure, and GCP — typically with no rate limits and at lower latency than NOMADS for users co-located with one of those clouds.

---

## Notes
- GEFS uses the same FV3 dynamical core and physics suite as GFSv15, but at coarser resolution (C384 vs C768) and with stochastic physics added. The 64-level vertical structure is also coarser than the deterministic GFS's 127 levels.
- The 35-day forecast range at 00 UTC was a major v12 enhancement, motivated by NOAA's growing emphasis on sub-seasonal-to-seasonal (S2S) prediction. The other three cycles (06, 12, 18 UTC) remain at 16 days because the additional sub-seasonal members are computationally expensive.
- GEFSv12 was the **first global-scale coupled forecast system at NCEP** under the Unified Forecast System (UFS) framework, deployed in September 2020 — predating the FV3 transition for the deterministic GFS by about nine months (GFSv16 transitioned in March 2021).
- GEFS analyses are widely used as initial and boundary conditions for downstream regional ensemble systems including some configurations of the upcoming [REFS](../regional/usa/refs.md).

---

## Upcoming changes

### GEFSv13 — co-developed with GFSv17
GEFSv13 is in development as the ensemble counterpart to the proposed [GFSv17](../../../nwp_models/global/usa/gfs.md#upcoming-changes), with implementation tied to the GFSv17 timeline (proposed October 2026 per PNS 26-29). Final implementation timing depends on the GFSv17 service change notice and may shift.

**Planned major changes:**
- **Full Earth-system coupling:** Two-way coupling of atmosphere, ocean (MOM6), sea ice (CICE6), waves (WAVEWATCH III), land (Noah-MP), and aerosols (GOCART) — the same UFS coupled framework as GFSv17
- **All 31 members coupled with aerosols** — replacing the v12 architecture where only the unperturbed 32nd "GEFS-Aerosols" member ran with GOCART chemistry
- **Forecast extension to 48 days** at 00 UTC (from 35 days in v12); 16 days retained at 06, 12, 18 UTC
- **Updated initial perturbations** from a coupled EnKF analysis (atmosphere, land, ocean) — the same JEDI-based DA architecture being developed for [GDASv17](../../../nwp_models/global/usa/gfs.md#upcoming-changes)
- **30-year reforecast** (1994–2023) generated using the GEFSv13 "replay" methodology (replaying the coupled UFS to ERA5 atmosphere and ORAS5 ocean reanalyses)
- **Updated physics suite** matching GFSv17, including new convection schemes with stochastic cellular-automata triggering, Thompson-Eidhammer two-moment microphysics, and unified gravity wave drag

The 30-year GEFSv13 replay reforecast (1994–2023) was completed by the NOAA Physical Sciences Laboratory in 2024 and is now being released as a training dataset for AI weather models, in addition to its primary purpose of supporting GEFSv13 calibration.

---

## Recent version history

### GEFSv12 — operational 23 September 2020 (current)
First operational deployment of the UFS framework at NCEP and the first global-scale coupled forecast system at NCEP. Headline changes from v11:
- **Replaced legacy Global Spectral Model (GSM) with FV3 dynamical core**
- **Horizontal resolution increased** from TL574 (~34 km) at days 0-8 / TL382 (~55 km) at days 8-16 to uniform C384 (~25 km)
- **Ensemble size increased** from 21 to 31 members (1 control + 30 perturbed)
- **Forecast length extended** from 16 days to 35 days at 00 UTC, supporting sub-seasonal forecasts
- **STTP replaced by 5-scale SPPT + SKEB** stochastic physics
- **NSST and 2-tiered SST approach** replacing climatology-relaxation for lower boundary conditions
- **Coupled WAVEWATCH III** wave ensemble (replacing the standalone Global Wave Ensemble)
- **GEFS-Aerosols** 32nd member added with GOCART chemistry, replacing the legacy NEMS GFS Aerosol Component (NGAC)
- **30-year reanalysis and reforecast** dataset (1989-2019)
- Output resolution upgraded from 0.5°/1.0° to 0.25°/0.5°

### GEFSv11 — operational December 2015
Last spectral-model version. Used 21 members (1 control + 20 perturbed) and STTP for stochastic physics. Initial perturbations transitioned from breeding (ETR) to EnKF in this version.

### Earlier history
GEFS has a continuous operational lineage at NCEP since the early 1990s, with major upgrades roughly every 3-5 years. The GEFSv11→v12 transition (2020) was the largest architectural change in the system's history.

---

## Official documentation
- GEFS documentation (EMC): https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/gefs.php
- GEFS implementation notice (SCN 20-75): https://www.weather.gov/media/notification/pdf2/scn20-75gefs_v12_changes.pdf
- GEFS NCEI archive: https://www.ncei.noaa.gov/products/weather-climate-models/global-ensemble-forecast
- GEFS-Aerosols documentation: https://vlab.noaa.gov/web/emc/gefs-aero
- AWS Open Data: https://registry.opendata.aws/noaa-gefs/
- GEFS reforecast: https://registry.opendata.aws/noaa-gefs-reforecast/
- NOAA upcoming model changes: https://www.nco.ncep.noaa.gov/pmb/changes/
- Global Workflow source repository: https://github.com/NOAA-EMC/global-workflow
- GEFSv13 replay dataset: https://epic.noaa.gov/introducing-gefsv13-replay-dataset-designed-for-training-of-ai-models-for-coupled-prediction/

### Key references
- Zhou, X., Y. Zhu, D. Hou, B. Fu, W. Li, H. Guan, E. Sinsky, et al. (2022). *The Development of the NCEP Global Ensemble Forecast System Version 12.* Wea. Forecasting, 37(6), 1069-1084. https://doi.org/10.1175/WAF-D-21-0112.1
- Zhu, Y., et al. (2019). *Toward the Improvement of Subseasonal Prediction in the National Centers for Environmental Prediction Global Ensemble Forecast System.* J. Geophys. Res. Atmos.
- Berner et al. (2009). *A spectral stochastic kinetic energy backscatter scheme and its impact on flow-dependent predictability in the ECMWF ensemble prediction system.* J. Atmos. Sci., 66, 603-626.
