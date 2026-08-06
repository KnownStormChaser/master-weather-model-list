# ICON-EU (ICON Europe)

## What this model is
ICON-EU is DWD's regional, convection-parameterized deterministic NWP configuration of ICON, covering Europe at higher resolution than the global run.

It is not a separately initialized limited-area model but the **two-way coupled European nest** embedded within the global [ICON](../../global/germany/icon-global.md) integration — boundary conditions and feedbacks are exchanged with the global domain every model time step. Its primary roles are higher-resolution deterministic guidance over Europe and supplying lateral boundary conditions to DWD's convection-permitting [ICON-D2](./icon-d2.md) family.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany / Europe

---

## What area it covers
- **Coverage:** Europe (regional nest of the global ICON integration)
- **Distributed grid extent:** **23.5°W – 62.5°E, 29.5°N – 70.5°N** — exact corner values decoded from GRIB2 headers (`longitudeOfFirstGridPointInDegrees = 336.5`, `latitudeOfFirstGridPointInDegrees = 29.5`, last point 62.5°E / 70.5°N). The `RLAT`/`RLON` time-invariant fields confirm the same span to three decimals.
- **Topography range:** −414.00 m to 3,793.38 m (`HHL` half-level 75, i.e. the surface, as interpolated onto the distribution grid)

---

## Basic details
- **Model type:** Regional deterministic NWP (two-way coupled nest)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic)
- **Dynamical formulation:** Non-hydrostatic, on a triangular (icosahedral) horizontal grid
- **Convection-allowing:** No (deep convection parameterized at ~6.5 km)
- **Native horizontal resolution:** ~6.5 km, R3B08 triangular nest grid (per DWD documentation — **not verifiable from Open Data**, see below)
- **Public output grid:** **regular latitude–longitude at 0.0625°**, 1377 × 657 = **904,689 points** — `gridType = regular_ll`, verified from live headers. This is the *only* grid distributed.
- **Vertical levels:** **74 full levels** (75 `HHL` half-levels), all published
- **Model top:** **22,770.33 m** — `HHL` half-level 1, spatially constant (`bitsPerValue = 0`). Half-level 2 is also flat, at 22,096.57 m. Identical to [ICON-EU-EPS](../../../ensemble_models/regional/de/icon-eu-eps.md), as expected for the same vertical grid.
- **Forecast length:**
  - **120 hours** for the main synoptic cycles (00, 06, 12, 18 UTC) — 93 steps
  - **48 hours** for the intermediate cycles (03, 09, 15, 21 UTC) — 34 steps (see correction below)
- **Update frequency / cycles:** 8× daily — 4 main synoptic cycles (00, 06, 12, 18 UTC) plus 4 intermediate off-synoptic cycles (03, 09, 15, 21 UTC)
- **Temporal output resolution:**
  - Main cycles: hourly to +78 h, then 3-hourly to +120 h (79 + 14 = 93 steps)
  - Intermediate cycles: hourly to +30 h, then 6-hourly to +48 h (31 + 3 = 34 steps)

> **Correction — the native grid is not distributed.** This entry previously listed the public output grids as "Native triangular grid (GRIB2); regular latitude–longitude grids interpolated from native for many element packages." That is backwards. A scan of all **496,788** manifest entries for `/weather/nwp/icon-eu/grib/` returned **zero** icosahedral filenames — every file is `icon-eu_europe_regular-lat-lon_*`. There are no `clat`/`clon`/`elat`/`elon` mesh-coordinate fields either, only the `RLAT`/`RLON` coordinate arrays of the regular grid.
>
> This makes ICON-EU the **inverse** of the rest of the DWD family: [ICON Global](../../global/germany/icon-global.md), [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md), [ICON-EU-EPS](../../../ensemble_models/regional/de/icon-eu-eps.md), [ICON-D2-EPS](../../../ensemble_models/regional/de/icon-d2-eps.md), [ICON-D2-RUC](./icon-d2-ruc.md), and [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md) are all native-grid only; [ICON-D2](./icon-d2.md) publishes both. ICON-EU alone publishes only the interpolated product.
>
> A consequence worth noting: **the R3B08 native grid identity cannot be confirmed from Open Data**, since no native-grid message is distributed and DWD's CDO library ships no `R03B08` grid file (it carries R2B06, R3B07 global, R2B07 nest, R3B06, R3B07 nest, and R19B07 limited-area). The ~6.5 km / R3B08 figures rest on DWD documentation, not on decoded headers. The ICON-ART-EU product under `/weather/nwp/v1/m/icon-art-eu/` *is* distributed on the native nest mesh and is the place to confirm the grid identity if needed.

> **Correction — intermediate-cycle forecast length is 48 h, not 51 h.** Live enumeration of all four intermediate cycles on 2026-08-05 shows a maximum step of **048**, with 34 steps: hourly 000–030, then 036, 042, 048. The 51-hour figure taken from DWD's EWGLAM/ICCARUS operational-chain posters does not match the distributed data. Whether the model integrates to 51 h and only 48 h is published, or the range itself changed, is not resolvable from the feed — but 48 h is what reaches Open Data.

---

## Data assimilation
ICON-EU does not run an independent analysis. As the two-way European nest of the global ICON integration, it inherits initial conditions from DWD's **hybrid LETKF + EnVar** data assimilation system, whose LETKF ensemble component runs a 20 km nest over Europe (see the [ICON Global entry](../../global/germany/icon-global.md#data-assimilation) for the full DA description).

---

## Initial and boundary conditions
- **Coupling:** Two-way nested within the global ICON integration, rather than one-way driven by an external global model. The global domain and the European nest exchange boundary information and feedbacks every model time step. The feedback relaxation time scale was reduced from 3 hours to 1 hour on 4 December 2024.
- **Downstream use:** ICON-EU in turn supplies the lateral boundary conditions for the one-way-driven [ICON-D2](./icon-d2.md) and [ICON-D2-RUC](./icon-d2-ruc.md) convection-permitting systems.

---

## What it provides

**96 parameter directories** per cycle, identical across cycles. Level structure verified live 2026-08-06:

| Level type | Levels | Notes |
|---|---|---|
| Single-level | — | the bulk of the set |
| Model-level | **1–74** (1–75 for half-level fields) | `t`, `u`, `v`, `w`, `p`, `qv`, `qc`, `qi`, `clc`, `tke` |
| Pressure-level | **20**: 50, 70, 100, 150, 200, 250, 300, 400, 500, 600, 700, 775, 800, 825, 850, 875, 900, 925, 950, 1000 hPa | `fi`, `t`, `u`, `v`, `relhum`, `omega` |
| Soil-level | 0, 1, 2, 3, 5, 6, 9, 18, 27, 54, 81, 162, 243, 486, 729, 1458 | `t_so`, `w_so`, `w_so_ice` |
| Time-invariant | — | `hhl`, `hsurf`, `rlat`, `rlon`, `fr_land`, `fr_lake`, `depth_lk`, `soiltyp`, `plcov`, `lai`, `rootdp` |

Note the pressure-level set is **20 levels, not the 18 of ICON Global** — ICON-EU adds 775, 800, 825, 875, and 925 hPa in the lower troposphere while dropping 30, 850… (the sets overlap but are not nested). Anyone building a combined global/regional pressure-level pipeline needs to handle both.

Content includes temperature, wind, precipitation by type, humidity, pressure, cloud and hydrometeor fields, radiation and surface flux components, lake (FLake) variables, soil state, `ww` (WMO weather interpretation), `vis`, `ceiling`, `hzerocl`, `snowlmt`, `cape_ml`/`cape_con`/`cin_ml`, **`lpi_con_max`** (convective Lightning Potential Index maximum), and **synthetic Meteosat brightness temperatures** (`synmsg_bt_cl_ir10.8`, `synmsg_bt_cl_wv6.2`).

**`mh` — mixed layer depth (`mld`, metres)** — is present here, as it is on the ICON-D2 lat–lon tree. Since ICON-EU publishes only a lat–lon grid, there is no native-grid asymmetry to note; but the field's association with the interpolated product across both models is consistent.

**`lpi_con_max` runs on a reduced step set** — 65 steps rather than 93: hourly 000–048, then 3-hourly to +120. Every other field in the main cycles carries the full 93.

Time-integration conventions, verified by decoding `stepType`/`stepRange` at +12 h:

| Convention | `stepType` | PDT | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | 8 | `tot_prec`, `rain_gsp`, `rain_con`, `snow_gsp`, `runoff_s` |
| Averaged from run start | `avg` | 8 | `asob_s`, `athb_s`, `aswdir_s`, `alhfl_s` |
| Max over the preceding hour | `max` | 8 | `vmax_10m` (`11-12`), `lpi_con_max` (`11-12`) |
| Max/min since last 6 h boundary | `max` / `min` | 8 | `tmax_2m` (`6-12`), `tmin_2m` |
| Instantaneous | `instant` | 0 | `t_2m`, `pmsl`, `ww`, `cape_ml`, and all multi-level fields |

Unlike [ICON Global](../../global/germany/icon-global.md) and [ICON-D2](./icon-d2.md), **every field including `ww` and `vmax_10m` carries a step-000 file** — all 93 steps in the main cycles.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, bzip2-wrapped (`.grib2.bz2`)
- **GRIB2 packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library built without libaec/CCSDS support cannot decode these files. **This differs from [ICON-D2](./icon-d2.md), whose lat–lon tree is still `grid_simple`** — ICON-EU's interpolated product did get the CCSDS treatment.
- **GRIB2 tables:** `tablesVersion = 19`, `localTablesVersion = 1`, `centre = edzw`, `typeOfProcessedData = fc`
- **No bitmap.** `bitmapPresent = 0` and there are no missing values — the 0.0625° rectangle is fully covered by the nest domain, unlike ICON-D2's lat–lon rendering where 16.7% of points fall outside the triangular domain and are masked.
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/icon-eu/grib/
  - Layout: `/<cycle>/<parameter>/icon-eu_europe_regular-lat-lon_<leveltype>_<YYYYMMDDHH>_<step>[_<level>]_<PARAM>.grib2.bz2`
- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2
- **Retention:** each cycle directory holds exactly one run — a ~24 h rolling window across the eight cycles.
- **Volume:** **~68.3 GB and 90,825 files per main run**; **~24.9 GB and 33,372 files per intermediate run**. Daily total roughly **373 GB across ~497,000 files**.
- **Publication latency:** main cycles start ~2 h 39 min after cycle time and complete ~3 h 40 min after (00 UTC run 2026-08-05: 02:39:35 → 03:40:03 UTC). Intermediate cycles start ~2 h 36 min after and complete ~3 h 10 min after (03 UTC: 05:36:16 → 06:10:26). Essentially in lockstep with [ICON Global](../../global/germany/icon-global.md), as expected for two products of the same integration.

### The `.bz2` wrapper no longer compresses
Since the 16 June 2026 CCSDS switch the bzip2 wrapper adds nothing. Measured on the 2026-08-06 12 UTC run: `..._012_T_2M.grib2.bz2` is 1,075,225 bytes against 1,072,719 decompressed — a ratio of **1.002**, i.e. the wrapped file is marginally *larger* than its own contents. Budget transfer volume on the compressed size.

### Interpolating further
DWD provides CDO grid description and weight files at https://opendata.dwd.de/weather/lib/cdo/. Note that the library carries no `R03B08` nest grid file, consistent with ICON-EU's native mesh not being distributed. The `ICON_GLOBAL2EUAU_025_EASY.tar.bz2` / `ICON_GLOBAL2EUAU_0125_EASY.tar.bz2` bundles target the Europe domain but are built against the global R3B07 mesh, not this nest.

---

## Notes
- ICON-EU and the global [ICON](../../global/germany/icon-global.md) are not separate models — ICON-EU is the higher-resolution European nest of a single tightly coupled two-way integration. This is structurally different from regional models elsewhere (e.g., ARPEGE/AROME at Météo-France) where the limited-area model runs as a separate process driven by one-way lateral boundary conditions.
- **ICON-EU is the only DWD ICON product distributed exclusively on an interpolated grid.** Everything else in the family is native-grid-only, except ICON-D2 which publishes both. Workflows built on ICON-EU's lat–lon output will not transfer directly to ICON-EU-EPS, which is native-mesh only at 13 km.
- The EU nest caps at **120 h even for the 00 and 12 cycles**, whereas the global ICON integration runs those to 180 h. Guidance beyond +120 h over Europe has to come from the coarser global product.
- The ensemble counterpart is [ICON-EU-EPS](../../../ensemble_models/regional/de/icon-eu-eps.md), distributed as the European-nest portion of [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md). **The ensemble nest is coarser than the deterministic nest** — 13 km against 6.5 km — inverting the usual expectation. Both carry 74 levels and the same 22,770.33 m model top.
- **ICON-ART-EU** (`/weather/nwp/v1/m/icon-art-eu/`) is ICON-EU plus the ART mineral-dust module, distributed on the **native nest mesh**. It is the only public route to ICON-EU-family output on the triangular grid. See [ICON-ART-EU](../../../air_quality_models/regional/germany/icon-art-eu.md).
- The ICON model code has been open source under a permissive licence since January 2024 (repository: https://gitlab.dkrz.de/icon/icon-model).
- **DWD's English model-description page** describes the global analysis as "a 3D variational assimilation method"; the operational system has been hybrid LETKF + EnVar since 2015. See the [ICON Global entry](../../global/germany/icon-global.md#data-assimilation) for the discrepancy note.

---

## Recent version history

ICON-EU has no independent change-notice series — it is upgraded as part of the global ICON system, and DWD's notices state that changes apply to all global ICON configurations including the EU nests. The nest-relevant items are listed here; see the [ICON Global entry](../../global/germany/icon-global.md#recent-version-history) for the full sequence.

### 5 August 2026 — solar eclipse parametrization (effective 12 UTC run)
New parametrization reduces incoming solar radiation during solar eclipses, feeding through to 2 m temperature, boundary-layer height, cloud cover, and 10 m wind. Based on Besselian elements; limb-darkening not considered. Motivated by photovoltaic production forecasting.

### 30 June 2026 — model version `icon-2025.10-dwd-2.2` (effective 12 UTC run)
Applies to all global ICON configurations including the EU nests: revised SST ensemble perturbations, retuned ensemble physics perturbations, reduced humidity relaxation in the lower stratosphere, assimilation of MWR radiances from the AWS satellite.

### 16 June 2026 — CCSDS compression (effective 09 UTC run)
All ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS GRIB2 output switched to lossless CCSDS packing, 40–50% smaller than the previous encoding. **Most likely change to break older downstream tooling** — and the point at which the `.bz2` wrapper stopped providing meaningful compression.

### 18 February 2026 — model version `icon-2025.04-dwd-4.0` (effective 09 UTC run)
Revised ceiling diagnostic (cloud-overlap assumption; fill value now 16 km above ground) and revised visibility diagnostic (reworked humidity contribution) — both fields are published for ICON-EU.

### 2 December 2025 — model version `icon-2025.04-dwd-3.0` (effective 06 UTC run)
More consistent bare-soil evaporation across all configurations; retuned convective entrainment/detrainment profile; revised filtering time scales for adaptive parameter tuning.

### 23 July 2025 — model version `icon-2025.04-dwd-1.0` (effective 06 UTC run, all ICON configurations)
Dissipative heating parameterization; ocean warm-layer parameterization introducing a diurnal SST cycle; bug fix for rime deposition on snow-free ground; retuning of interception storage and ozone–tropopause coupling.

### 4 December 2024 — model version `icon-2024.10-dwd-2.0` (effective 06 UTC global run)
Extended adaptive parameter tuning (soil hydraulic diffusivity, land albedo, snow cover fraction diagnosis); revised treatment of snow cover in surface transfer calculation; and **reduction of the nest feedback relaxation time scale from 3 hours to 1 hour**, which slightly improved near-surface variables and the pressure/geopotential field over the first 2–3 forecast days.

### 5 February 2025 — `uuidOfVGrid` change (all ICON configurations)
The GRIB2 `uuidOfVGrid` parameter changed due to new compiler options. Present only for model full- and half-level variables. Unlike ICON-D2, **ICON-EU orography heights at a few grid points in the boundary interpolation zone changed by a few centimetres** as a side effect.

### 23 April 2024 — near-surface visibility output (effective 09 UTC run)
`VIS` (near-surface visibility) added as an output variable for ICON-EU and ICON-D2, requested by DWD's aviation forecasting offices for use alongside ceiling in VFR forecasting.

### 24 January 2024 — sea-ice bottom heat flux, `icon-2.6.6-nwp2` (effective 12 UTC run)
Sea-ice scheme revised to account for ocean-to-ice heat flux; external parameter bugfix for false glacier points; adaptive time-step reduction extended to horizontal CFL exceedances.

### 23 November 2022 — vertical resolution upgrade
European nest vertical levels increased from 60 to 74 as part of the broader ICON November 2022 upgrade; the vertical nest interface stays near 22.5 km. The deterministic ICON-EU horizontal resolution remained at ~6.5 km — the simultaneous horizontal-resolution increase (20 km → 13 km nest) applied to ICON-EPS, not the deterministic nest.

### June 2015 — operational launch
ICON-EU introduced as the European nest of the ICON system, replacing the legacy COSMO-EU regional model. (Global ICON had replaced GME five months earlier, in January 2015.)

---

## Official documentation
- DWD ICON model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
- DWD NWP forecast data overview: https://www.dwd.de/EN/ourservices/nwp_forecast_data/nwp_forecast_data.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD ICON change notices (carry all ICON-EU changes): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_gesamt.html
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD CDO grid description and weight files: https://opendata.dwd.de/weather/lib/cdo/
- DWD ICON open source repository: https://gitlab.dkrz.de/icon/icon-model

### Key references
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378
- Reinert, D., et al. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*

---

*Live verification performed 2026-08-06 against `https://opendata.dwd.de/weather/nwp/icon-eu/grib/` (all eight cycles) and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.48.0.*
