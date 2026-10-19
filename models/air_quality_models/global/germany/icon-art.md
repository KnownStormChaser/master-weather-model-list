# ICON-ART (Global – Mineral Dust)

## What this model is
ICON-ART is the operational global atmospheric-composition configuration of DWD's ICON model, used for forecasting **mineral dust** emission, transport and deposition — including Saharan dust outbreaks reaching Europe.

The "ART" component (Aerosols and Reactive Trace gases) is a submodule developed by the Karlsruhe Institute of Technology (KIT) that extends ICON with online-coupled treatment of emissions, transport, gas-phase chemistry, aerosol dynamics and removal processes. It was originally developed within the PerduS (Prediction of Saharan Dust) project to support photovoltaic power forecasting, where Saharan dust events significantly reduce incoming solar radiation across Europe.

Per DWD's change notice of 6 October 2026, the operational ICON-ART system consists of a **global domain plus a large nested domain over Europe, North Africa and the North Atlantic** (ICON-ART-EU-NA², distributed as [ICON-ART-EU](../../regional/germany/icon-art-eu.md)), with a deterministic forecast and **10 additional ensemble members at the same resolution** ([ICON-ART-EPS](./icon-art-eps.md)).

> **ICON-ART does not run on the deterministic ICON Global grid.** It shares the **ensemble** mesh: grid #39 (~20 km) since 6 October 2026, and grid #36 (~26 km) before that — the same grids as [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md). Operational ICON Global runs on grid #26 (R3B07, ~13 km, 2,949,120 cells). Earlier versions of this entry said ICON-ART used "the same grid and resolution as the standard ICON deterministic forecast"; that was never correct.

The "online-coupled" design means meteorology and aerosol fields evolve together within a single model run — dust–radiation feedbacks affect the meteorological forecast and vice versa, in contrast to offline chemical transport models that ingest meteorology from a separate NWP run.

Mineral dust forecasts using ICON-ART became technically operational at DWD in November 2023, after several years of quasi-operational use under the PerduS project.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service; GRIB centre `edzw`, Offenbach), with the ART module developed and maintained by the Karlsruhe Institute of Technology (KIT)
- **Country:** Germany
- **Programme:** PerduS (Prediction of Saharan Dust) lineage; now an operational DWD product

---

## What area it covers
- **Coverage:** Global
- **Operational application focus:** Saharan dust transport into Europe, but the model itself is global
- **Native grid (since 6 October 2026):** DWD grid **#39**, **1,310,720 cells**, ~20 km — the same mesh as global ICON-EPS
  - Verified from live GRIB2 headers (v1 feed, 2026-10-18 12 UTC run): `numberOfGridUsed = 39`, `numberOfDataPoints = 1310720`, `uuidOfHGrid = 49c340e2248d11e58105c12d5e3b00a4`, `gridType = unstructured_grid`. DWD's 28 July 2026 Open Data newsletter gives the same change: 26 km → 20 km, grid #39, 1.78 times the grid points.
  - **Until 5 October 2026:** R3B06 grid #36, 737,280 cells, ~26 km. Data on the two grids cannot be combined cell by cell.
- **Surface height on the new grid (`HSURF`):** −211.44 m to 6,032.56 m

---

## Basic details
- **Model type:** Global atmospheric composition / mineral-dust forecast
- **Underlying core:** ICON (Icosahedral Nonhydrostatic) — same dynamical core and physics as ICON Global and ICON-EPS
- **ART module developer:** Karlsruhe Institute of Technology (KIT)
- **Coupling:** Online — ART is integrated within ICON; aerosol and meteorological fields share the same grid, advection scheme and time integration
- **Horizontal resolution:** ~20 km (grid #39) since 6 October 2026; ~26 km (grid #36) before
- **Vertical levels:** **120 full levels** (121 `HHL` half levels) — verified from the published level indices and the `HHL` header (`nlev = 121`). Unchanged by the October grid switch. Only small subsets are distributed (see *What it provides*).
- **Forecast length (verified):** +180 h from the 00 and 12 UTC runs; +120 h from the 06 and 18 UTC runs
- **Update frequency:** 4× daily (00/06/12/18 UTC), with dust fields in **all four** cycles. The off-synoptic 03/09/15/21 UTC short runs of ICON Global are not produced for ICON-ART.
- **Temporal output resolution:** parameter-dependent — the main meteorological group is hourly to +78 h then 3-hourly; `TAOD_DUST` is hourly to +72 h then 3-hourly; 3D dust mass and the budget terms are 3-hourly throughout. See *Step schedules*.
- **Publication latency (v1, measured 2026-10-18/19):** first files ~2 h 37–40 min after cycle time; run complete ~3 h 03 min (06/18 UTC) to ~3 h 13 min (00/12 UTC). Example: 2026-10-19 00 UTC run 02:36:40 → 03:13:05 UTC; 2026-10-18 18 UTC run 20:37:47 → 21:03:47 UTC.
- **Operational since:** November 2023 (mineral dust forecast)

---

## What it provides
**139 parameter folders:** a standard ICON meteorological set plus **28 ART mineral-dust parameters**.

### ART dust parameters

| Group | Parameters | Notes |
|---|---|---|
| Optical depth | `TAOD_DUST` | Column-integrated, dimensionless; GRIB `productDefinitionTemplateNumber = 48` (optical properties of aerosol) |
| Optical depth (layered) | `AER_DUST` | GRIB `shortName` decodes as `AOD_DUST` — folder name and shortName differ |
| Mass concentration | `DUST_TOTAL_MC`, `DUST_TOTAL_MC_VI` | 3D (kg m⁻³) and column (kg m⁻²) |
| Layer maximum | `DUST_MAX_TOTAL_MC_LAYER` | Deterministic feeds only; published under `lvt1/100/` in 7 layer directories with non-standard level values (14748 … 84307) |
| Prognostic modes | `DUSTA`, `DUSTB`, `DUSTC` (+ `DUSTA0`, `DUSTB0`, `DUSTC0`) | Fine / medium / coarse modes |
| Accumulated budget terms | `ACCEMISS_DUST{A,B,C}`, `ACCDRYDEPO_DUST{A,B,C}`, `ACCWETDEPO_CON_DUST{A,B,C}`, `ACCWETDEPO_GSP_DUST{A,B,C}`, `ACCSEDIM_DUST{A,B,C}` | 15 fields, per mode |
| Lidar backscatter | `CEIL_BSC_DUST`, `SAT_BSC_DUST` | 532 nm and 1064 nm (`wvl1/532/`, `wvl1/1064/`) |

`USTAR` and `USTAR_THRES` (friction velocity and its dust-emission threshold) are also published, which makes the emission trigger inspectable from the public data.

The meteorological set covers screen-level and 10 m fields, MSLP, cloud cover by layer, precipitation split by phase and convective/grid-scale, radiation terms, CAPE, column water species, lake, snow, sea-ice and soil fields, and `FI`/`RELHUM` on **18 pressure levels** (30, 50, 70, 100, 150, 200, 250, 300, 400, 500, 600, 700, 800, 850, 900, 925, 950, 1000 hPa).

### Vertical coverage is heavily subset

| Field group | Levels published |
|---|---|
| `DUST_TOTAL_MC`, `CEIL_BSC_DUST`, `SAT_BSC_DUST` | 44 model levels, **77–120** |
| `T`, `P`, `QV`, `QC`, `QI`, `QR`, `QS`, `U`, `V`, `AER_DUST`, `DUSTA/B/C`, `DUSTA0/B0/C0` | 3 model levels, **118–120** (near-surface) |
| `HHL`, `W` | 4 half levels, 118–121 |
| `FI`, `RELHUM` | 18 pressure levels (above) |
| `T_SO` | 9 soil depths, 0–14.58 m |
| `W_SO`, `W_SO_ICE`, `SMI` | 8 soil layers, 0–7.29 m |

### Step schedules (verified on the 2026-10-18 06 and 12 UTC runs)

| 00/12 UTC | 06/18 UTC | Schedule | Parameters |
|---|---|---|---|
| 180 | 120 | Hourly from +1 h | `VMAX_10M` |
| 113 | 93 | Hourly to +78 h, then 3-hourly | 85 meteorological fields, incl. `T_2M`, `TOT_PREC`, `PMSL`, `CLCT`, `ASWDIR_S`, model-level `T/U/V/P/Q*`, `FI`, `RELHUM`, soil fields |
| 109 | 89 | Hourly to +72 h, then 3-hourly | `TAOD_DUST` |
| 61 | 41 | 3-hourly from 0 | All 15 accumulated budget terms, `DUST_TOTAL_MC`, `DUST_TOTAL_MC_VI`, `DUST_MAX_TOTAL_MC_LAYER`, `USTAR`, `USTAR_THRES` |
| 49 | 49 | Hourly to +48 h only | `LPI_CON_MAX` |
| 20 | 15 | 6-hourly to +48 h, then 12-hourly | `AER_DUST`, `DUSTA/B/C`, `DUSTA0/B0/C0`, `CEIL_BSC_DUST`, `SAT_BSC_DUST` |
| 1 | 1 | Step 0 only | `CLAT`, `CLON`, `ELAT`, `ELON`, `HHL`, `HSURF`, `FR_LAND`, `FR_LAKE`, `DEPTH_LK`, `SOILTYP`, `SSO_STDH`, `LAI`, `PLCOV`, `ROOTDP`, `NDVIRATIO`, `EVAP_PL`, `ALB_SEAICE`, `HSNOW_MAX`, `SNOAG`, `SMI` |

Earlier versions of this entry gave one schedule for everything ("hourly to +72 h, then 3-hourly"). Only `TAOD_DUST` follows that pattern.

### User communities
- **Photovoltaic power producers** — Saharan dust events measurably reduce PV output across Europe; the PerduS project targeted exactly this
- **Aviation forecasters** — dust visibility and engine-ingestion concerns
- **Health forecasters** — dust as a respiratory hazard
- **Forecasters tracking dust-related extreme events**, including air-quality episodes during major Saharan outbreaks

---

## Online coupling
- **Shared transport:** Grid-scale advection and subgrid-scale (convection, turbulent diffusion) transport of dust tracers use the same algorithms as ICON's moisture quantities
- **High update frequency:** Aerosol fields are updated synchronously with the meteorology at every model time step
- **Aerosol–radiation feedback:** Mineral dust modulates incoming shortwave radiation, and the modified radiation field feeds back into the forecast rather than being a post-processing diagnostic
- **Aerosol–cloud feedback:** Dust particles can act as ice-nucleating particles, affecting cloud microphysics

ART-specific processes — dust emission, sedimentation, wet and dry deposition — are computed by the ART modules at each time step.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes — plain HTTPS, no registration
- **Licence:** **CC BY 4.0**, attribution required. DWD's legal notice places all its open spatial data and its EU High Value Datasets under CC BY 4.0 with source acknowledgement.
- **Data format:** GRIB2, one message per file, **no `.bz2` wrapper**; GRIB-internal CCSDS packing (`grid_ccsds`). Native triangular grid only — GRIB messages carry no lat/lon.
- **Primary access:** https://opendata.dwd.de/weather/nwp/v1/m/icon-art/
- **Path structure:**
  - `…/icon-art/p/{PARAM}/r/{YYYY-MM-DDTHH%3A00}/s/PT{hhh}H{mm}M.grib2` — single-level fields
  - `…/icon-art/p/{PARAM}/lvt1/{levelType}/lv1/{level}/r/{run}/s/PT{hhh}H{mm}M.grib2` — multi-level fields (`150` model level, `100` isobaric in Pa, `106` soil depth in m)
  - `…/icon-art/p/{PARAM}/wvl1/{nm}/lvt1/150/lv1/{level}/r/{run}/s/PT{hhh}H{mm}M.grib2` — `CEIL_BSC_DUST`, `SAT_BSC_DUST`
  - Example: `https://opendata.dwd.de/weather/nwp/v1/m/icon-art/p/TAOD_DUST/r/2026-10-18T12%3A00/s/PT024H00M.grib2`
- **Run volume (measured from the server manifest):** ~29.4 GB / ~26,800 files per 00/12 UTC run; ~23.3 GB / ~21,100 files per 06/18 UTC run
- **Retention (observed 2026-10-19):** about 24 hours — the four most recent runs. Each run keeps its **complete** step list until it is removed; the 06 UTC run still had all 21,138 files about 19 hours after its last file arrived. Earlier versions of this entry described elapsed lead times being pruned as a run ages; that does not happen.
- **On open data since:** April 2025 (DWD Open Data newsletter of 8 April 2025, which introduced the v1 URL scheme with ICON-ART)
- **Ensemble companion:** [`icon-art-eps`](./icon-art-eps.md); regional nest: [`icon-art-eu`](../../regional/germany/icon-art-eu.md)

### Grid files and regridding
- **Lightest route:** the `CLAT`/`CLON` (cell centres) and `ELAT`/`ELON` (cell vertices) folders on this feed give coordinates as GRIB fields at step 0. They changed with the grid on 6 October 2026, so re-download them for grid #39.
- **Grid definition files:** https://icon-downloads.mpimet.mpg.de/dwd_grids.xml (MPI-M) — grid number **39** for current data, **36** for data before 6 October 2026.
- **Regridding guide:** https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid (cdo-based).
- **`/weather/lib/cdo/` is deleted after 30 November 2026** (DWD Open Data newsletter, 2 September 2026). It never held a grid #39 file.

---

## Relationship to other models

### Companion DWD systems
- **[ICON Global](../../../nwp_models/global/germany/icon-global.md):** the deterministic NWP model. ICON-ART uses the same core and physics but **not** the same grid — ICON Global is ~13 km (grid #26), ICON-ART ~20 km (grid #39).
- **[ICON-EPS](../../../ensemble_models/global/de/icon-eps.md):** the 40-member global meteorological ensemble, on the same grid #39 since 6 October 2026 (and the same grid #36 before).
- **[ICON-ART-EPS](./icon-art-eps.md):** the 10-member ensemble companion, same grid and resolution as this deterministic run.
- **[ICON-ART-EU](../../regional/germany/icon-art-eu.md):** the regional nest of this system — DWD's ICON-ART-EU-NA² domain over Europe, North Africa and the North Atlantic, ~10 km (grid #61) since 6 October 2026. It is **not** the ~6.5 km ICON-EU nest.
- **[ICON-ART Pollen](../../regional/germany/icon-art-pollen.md):** DWD's limited-area ICON-ART **pollen** forecast, distributed as daily-mean NetCDF for Germany under `/climate_environment/health/forecasts/pollen/` — a separate configuration and distribution channel.
- **COSMO-ART:** predecessor regional system based on the retired COSMO model.

### Peer global atmospheric composition systems
- **[CAMS Global](../eu/cams-global.md):** ECMWF's IFS-based global composition forecast covers a much wider species set (reactive gases and several aerosol types including dust, sulfate, nitrate, ammonium, organic and black carbon, sea salt) at ~40 km. CAMS Global is a comprehensive air-quality system; ICON-ART is operationally a focused mineral-dust system.
- **[CAMS Global GHG Forecasts](../eu/cams-ghg-global.md):** long-lived greenhouse gases (CO₂, CH₄). Different scope.

### Architectural lineage
The ART module is developed primarily by KIT, with contributions from DWD, MeteoSwiss, Empa and the wider ICON community. It supports far more than DWD runs operationally — full gas-phase chemistry (MECCA, MOZART), aerosol microphysics (AERODYN) and many tracer types — but the operational DWD configuration is focused on mineral dust.

---

## Notes
- ICON-ART is a focused composition system, not a full air-quality model. For O₃, PM, NO₂ etc. over Europe see [CAMS Global](../eu/cams-global.md), [CAMS Regional](../../regional/eu/cams-regional.md) or national air-quality systems.
- **The 6 October 2026 grid change splits any archive.** Grid #36 (26 km) before, grid #39 (20 km) after. Check `numberOfGridUsed` before stacking data across that date.
- **No pressure-level wind or temperature.** `FI` and `RELHUM` are the only isobaric fields; `T`, `U`, `V` are published on the three lowest model levels only.
- **Folder name ≠ GRIB shortName for `AER_DUST`** (decodes as `AOD_DUST`).
- `generatingProcessIdentifier = 1`, the same as ICON Global and ICON-EPS; `typeOfProcessedData = fc`.
- The PerduS lineage matters operationally: forecast quality has been validated specifically for mineral-dust applications (notably PV power prediction), including comparison with PV system data via meteocontrol GmbH.
- The ART module is also used operationally by MeteoSwiss for pollen forecasts and supports DWD's emergency-response capabilities (volcanic ash, radionuclide dispersion).
- The most recent comprehensive description of ICON-ART is Hoshyaripour et al. (2026), *Geosci. Model Dev.*, 19, 1645–1681.

---

## Recent version history

### After 30 November 2026 — `/weather/lib/cdo/` removed
Announced in DWD's Open Data newsletter of 2 September 2026. The ICON-ART feed itself is not otherwise affected: it has been on the v1 scheme since launch, and the shutdown of the legacy `/weather/nwp/icon*/grib/` trees on the same date does not apply to it. Visibility (`VIS`), which is withdrawn from all ICON open data at that date, was never published in this global feed.

### 6 October 2026 — resolution increase (effective 06 UTC run)
Per DWD's change notice of 6 October 2026 and the 28 July 2026 Open Data newsletter: the ICON-ART global domain refined from 26 km to 20 km (grid #36 → grid #39, 1.78× the grid points), and the ICON-ART-EU-NA² nest from 13 km to 10 km (grid #51 → grid #61). Vertical resolution unchanged. The same change was applied to ICON-EPS; the deterministic ICON and ICON-EU resolutions were not changed. `CLAT`/`CLON`/`ELAT`/`ELON` changed with the grid. Test data for the 2026-07-15 00 UTC run were offered beforehand under `https://opendata.dwd.de/test/weather/nwp/v1/m/icon-art/`. DWD's verification figures in the notice are for ICON-EPS (CRPS reductions of typically 1–3% at the surface and in the lower troposphere); no separate dust-forecast scores were given.

### 16 June 2026 — CCSDS packing
DWD switched ICON-family GRIB2 output to CCSDS packing. On this tier files are distributed uncompressed, so the change shows directly as the `grid_ccsds` packing type.

### April 2025 — publication on DWD Open Data
ICON-ART, ICON-ART-EPS, ICON-ART-EU and ICON-ART-EU-EPS were added under `/weather/nwp/v1/m/`, introducing the ART mineral-dust parameter set and DWD's revised URL scheme.

### November 2023 — operational at DWD
Mineral dust forecasting with ICON-ART became technically operational, after several years of quasi-operational use under PerduS.

---

## Official documentation
- DWD ICON-ART overview: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/03_environmental_forecasts/icon_art_cosmo_art_en.html
- KIT ICON-ART website: https://www.icon-art.kit.edu/
- ICON-ART module documentation: https://docs.icon-model.org/atmosphere/art/art.html
- DWD Open Data Server, ICON-ART: https://opendata.dwd.de/weather/nwp/v1/m/icon-art/
- DWD Open Data newsletter, ICON-ART launch (8 April 2025): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_april2025_1.html
- DWD Open Data newsletter, October resolution change (28 July 2026): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_juli2026_2.html
- DWD Open Data newsletter, new URL scheme / VIS / lib/cdo (2 September 2026): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD ICON-EPS / ICON-ART resolution upgrade notice (6 October 2026, `pdf_icon_eps_06_10_2026.pdf`): on DWD's ICON-EPS model-change notices page
- DWD ICON grids at MPI-M: https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- Regridding guide: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html

### Key references
- Rieger, D., et al. (2015). ICON-ART 1.0 — a new online-coupled model system from the global to regional scale. *Geosci. Model Dev.*, 8, 1659–1676.
- Schröter, J., et al. (2018). ICON-ART 2.1: a flexible tracer framework and its application for composition studies in numerical weather forecasting and climate simulations. *Geosci. Model Dev.*, 11, 4043–4068.
- Hoshyaripour, G. A., et al. (2026). The atmospheric composition component of the ICON modeling framework: ICON-ART version 2025.10. *Geosci. Model Dev.*, 19, 1645–1681.

---

*Live verification performed 2026-10-18/19 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-art/` (2026-10-18 06/12/18 UTC and 2026-10-19 00 UTC runs): all 139 parameter folders enumerated, step schedules and level sets taken from the server manifest (`content.log.bz2`), GRIB2 headers decoded with ecCodes.*
