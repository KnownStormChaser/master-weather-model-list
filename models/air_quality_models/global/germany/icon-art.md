# ICON-ART (Global – Mineral Dust)

## What this model is
ICON-ART is the operational global atmospheric composition extension of DWD's ICON global numerical weather prediction model, used specifically for forecasting **mineral dust** transport — including Saharan dust outbreaks affecting Europe.

The "ART" component (Aerosols and Reactive Trace gases) is a submodule developed by the Karlsruhe Institute of Technology (KIT) that extends ICON with online-coupled treatment of emissions, transport, gas-phase chemistry, aerosol dynamics, and removal processes. It was originally developed within the PerduS (Prediction of Saharan Dust) project to support photovoltaic power forecasting, where Saharan dust events significantly reduce incoming solar radiation across Europe.

ICON-ART runs operationally on the same global ICON grid as the standard ICON deterministic forecast, with the same horizontal and vertical resolution. The "online-coupled" design means meteorology and aerosol fields evolve together within a single model run — dust radiation feedbacks affect the meteorological forecast and vice versa, in contrast to offline-coupled chemical transport models that ingest meteorological fields from a separate NWP run.

Mineral dust forecasts using ICON-ART became technically operational at DWD in November 2023, after several years of quasi-operational use under the PerduS project.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service), with the ART module developed and maintained by the Karlsruhe Institute of Technology (KIT)
- **Country:** Germany
- **Programme:** PerduS (Prediction of Saharan Dust) lineage; now operational DWD product

---

## What area it covers
- **Coverage:** Global
- **Operational application focus:** Saharan dust transport into Europe, but the model itself is global
- **Native grid:** ICON icosahedral **R3B06** grid, ~26 km (DWD "Grid #36", 737,280 cells) — verified from the distributed GRIB2. This is **coarser than the operational ICON Global deterministic** (R3B07, ~13 km); the ART dust configuration runs at reduced horizontal resolution.

---

## Basic details
- **Model type:** Global atmospheric composition / mineral dust forecast
- **Underlying core:** ICON (Icosahedral Nonhydrostatic) — same model core as the deterministic ICON Global
- **ART module developer:** Karlsruhe Institute of Technology (KIT)
- **Coupling:** Online — ART is integrated within ICON; aerosol and meteorological fields share the same grid, advection scheme, and time integration
- **Horizontal resolution:** ~26 km (ICON R3B06, DWD Grid #36; verified from the GRIB2 — coarser than the ~13 km operational ICON Global)
- **Vertical levels:** ICON model levels (3D dust fields such as `DUST_TOTAL_MC` are on model levels). The exact count on the ART grid was not verifiable from the open feed at check time (3D fields are pruned quickly); the operational ICON Global uses 120 levels, and the ART configuration may match or differ given its coarser horizontal grid.
- **Forecast length:** +180 h from the 00 and 12 UTC runs; +120 h from the 06 and 18 UTC runs (verified from the feed). Output is hourly to +72 h, then 3-hourly.
- **Update frequency:** 4× daily at the main synoptic hours (00/06/12/18 UTC). Unlike the full ICON Global, the ART dust feed does **not** publish the off-synoptic 03/09/15/21 UTC short runs.
- **Operational since:** November 2023 (mineral dust forecast)

---

## What it provides
ICON-ART produces 3D fields of mineral dust mass concentrations across the global atmosphere, including:
- Mineral dust mass concentrations across multiple aerosol size bins
- Total dust column / dust optical depth
- Dust-radiation interaction effects on the meteorological forecast
- Dispersion and deposition fields supporting downstream applications

The primary operational user community includes:
- **Photovoltaic power producers** — Saharan dust events significantly reduce incoming solar radiation, with measurable impact on PV power generation across Europe. The PerduS project specifically targeted PV power prediction improvements through prognostic dust forecasting.
- **Aviation forecasters** — dust visibility and engine ingestion concerns
- **Health forecasters** — dust as a respiratory health hazard
- **Forecasters tracking dust-related extreme events** including air quality episodes during major Saharan outbreaks

---

## Online coupling
The online-coupling architecture is a defining feature of ICON-ART:

- **Shared transport:** Grid-scale advection and subgrid-scale (convection, turbulent diffusion) transport of dust tracers use the same algorithms as ICON's specific moisture quantities
- **High update frequency:** Aerosol fields are updated synchronously with the meteorological forecast at every model time step
- **Aerosol-radiation feedback:** Mineral dust modulates incoming shortwave radiation, with the modified radiation field feeding back into ICON's forecast (rather than being a post-processing diagnostic)
- **Aerosol-cloud feedback:** Dust particles can act as ice nucleation nuclei, with effects on cloud microphysics

ART-specific processes — dust emission, sedimentation, wet deposition, dry deposition, and chemical transformations — are computed by the ART modules at each time step.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data format:** GRIB2 (native ICON R3B06 unstructured grid; GRIB messages carry no lat/lon, so georeferencing requires the matching ICON grid/coordinate description file)
- **Primary access:** DWD Open Data Server
  - Root: https://opendata.dwd.de/weather/nwp/v1/m/icon-art/
  - **Path structure:** `.../icon-art/p/{VARIABLE}/r/{YYYY-MM-DDTHH:MM}/s/PT{hhh}H{mm}M.grib2` — `{VARIABLE}` = parameter folder (e.g. `TAOD_DUST`, `DUST_TOTAL_MC_VI`), `r/{run}` = init time (00/06/12/18 UTC), `s/PT…` = lead time
- **On open data since:** April 2025 (ICON-ART and ICON-ART-EPS added to the `/weather/nwp/v1/m/` tier)
- **Ensemble companion:** `icon-art-eps` (same tier)
- **Parameter set:** standard ICON Global meteorological fields plus 28 ART mineral-dust parameters — `TAOD_DUST`, `DUST_TOTAL_MC`/`_VI`, `DUST_MAX_TOTAL_MC_LAYER`, per-mode `DUST{A,B,C}` (+ `{A,B,C}0`), per-mode accumulated emission / dry-deposition / wet-deposition (convective + grid-scale) / sedimentation, plus `AER_DUST`, `CEIL_BSC_DUST`, `SAT_BSC_DUST`
- **Distribution path note:** ICON-ART sits under `/weather/nwp/v1/m/` rather than alongside ICON/ICON-EU/ICON-D2 at `/weather/nwp/`. The `v1/m/` tier also hosts `aicon`, `icon-art-eps`, the regional `icon-art-eu`/`icon-art-eu-eps`, and `icon-d2-ruc`/`icon-d2-ruc-eps`.
- **Licence:** DWD Open Data licence (GeoNutzV; CC BY 4.0-compatible, attribution required)
- **Retention note:** Short rolling retention; elapsed lead-times are pruned as a run ages (3D model-level fields in particular are removed quickly), so at any moment only the still-forecast steps of the most recent runs are present.

---

## Relationship to other models

### Companion DWD systems
- **[ICON Global](../../../nwp_models/global/germany/icon-global.md):** The base meteorological model that ICON-ART extends. ICON-ART runs the same dynamical core, same grid, same vertical structure, and same physics — with ART aerosol modules added.
- **[ICON-ART-EU](../../regional/germany/icon-art-eu.md):** The regional companion — DWD's ICON-EU-nested **mineral-dust** forecast (~6.5 km) on the same open-data tier.
- **[ICON-ART Pollen](../../regional/germany/icon-art-pollen.md):** DWD's limited-area ICON-ART **pollen** forecast (five allergenic taxa), distributed as daily-mean NetCDF for Germany under `/climate_environment/health/forecasts/pollen/` — a separate configuration and distribution channel from both dust products.
- **COSMO-ART:** Predecessor regional system based on the now-retired COSMO model. COSMO-ART supported the same operational use cases (dust, volcanic ash, radionuclide dispersion) before DWD's transition to ICON.

### Peer global atmospheric composition systems
ICON-ART is structurally distinct from broader global air quality systems:

- **[CAMS Global](../eu/cams-global.md):** ECMWF's IFS-based global composition forecast covers a much wider species set (56 reactive trace gases, 7 aerosol types including dust, sulfate, nitrate, ammonium, organic carbon, black carbon, sea salt) at ~40 km resolution. CAMS Global is a comprehensive air quality system; ICON-ART is currently a focused mineral-dust system on the operational side, though the ART module supports many more capabilities in research and quasi-operational use.
- **[CAMS Global GHG Forecasts](../eu/cams-ghg-global.md):** Focused on long-lived greenhouse gases (CO₂, CH₄). Different scope from ICON-ART.

The narrower operational scope of ICON-ART reflects its specific purpose: producing high-quality mineral dust forecasts to support European aerosol-radiation applications, leveraging ICON's high-resolution global grid and online-coupled aerosol-meteorology dynamics.

### Architectural lineage
The ART module is jointly developed by KIT (primary developer), with contributions from DWD, MeteoSwiss, Empa, and the broader ICON community. It supports significantly more capability than DWD currently runs operationally — full gas-phase chemistry (MECCA, MOZART), aerosol microphysics (AERODYN), and multiple tracer types — but the operational DWD configuration is currently focused on mineral dust.

---

## Notes
- ICON-ART is a focused atmospheric composition system, not a full air quality model. Users looking for comprehensive air quality forecasting (O₃, PM, NO₂, etc.) over Europe should look to [CAMS Global](../eu/cams-global.md), [CAMS Regional](../../regional/eu/cams-regional.md), or national air quality systems.
- The PerduS project lineage matters operationally: ICON-ART's forecast quality has been validated specifically for mineral dust applications (notably PV power prediction), with extensive comparison to PV system data via the meteocontrol GmbH operational system.
- The ART module is also used operationally by MeteoSwiss for pollen forecasts and supports DWD's emergency response capabilities (volcanic ash dispersion, radionuclide release modelling).
- The wider ART ecosystem includes research configurations supporting full tropospheric and stratospheric chemistry, but these are not currently part of DWD's operational ICON-ART distribution.
- The ICON-ART codebase, including dust and pollen configurations, is documented at the [KIT ICON-ART website](https://www.icon-art.kit.edu/) and within the broader [ICON model documentation](https://docs.icon-model.org/atmosphere/art/art.html). The most recent comprehensive description is Hoshyaripour et al. (2026), *Geosci. Model Dev.*, 19, 1645–1681.

---

## Official documentation
- DWD ICON-ART overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/03_environmental_forecasts/icon_art_cosmo_art_en.html
- KIT ICON-ART website: https://www.icon-art.kit.edu/
- ICON-ART module documentation: https://docs.icon-model.org/atmosphere/art/art.html
- DWD Open Data Server v1/m subtree: https://opendata.dwd.de/weather/nwp/v1/m/

### Key references
- Rieger, D., et al. (2015). ICON-ART 1.0 — a new online-coupled model system from the global to regional scale. *Geosci. Model Dev. Discuss.*, 8, 567–614.
- Schröter, J., et al. (2018). ICON-ART 2.1: a flexible tracer framework and its application for composition studies in numerical weather forecasting and climate simulations. *Geosci. Model Dev.*, 11, 4043–4068.
- Hoshyaripour, G. A., et al. (2026). The atmospheric composition component of the ICON modeling framework: ICON-ART version 2025.10. *Geosci. Model Dev.*, 19, 1645–1681.
