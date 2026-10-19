# ICON-EU-EPS (ICON European Regional Ensemble Prediction System)

## What this model is
ICON-EU-EPS is DWD's operational regional ensemble prediction system for Europe. It is the European-nest portion of [ICON-EPS](../../global/de/icon-eps.md) (DWD's global ensemble) — the 40 ensemble members are produced by the two-way-nested European subdomain embedded in the global ICON ensemble, and then distributed separately as a regional ensemble product.

ICON-EU-EPS is the probabilistic counterpart of DWD's deterministic [ICON-EU](../../../nwp_models/regional/germany/icon-eu.md) model. It provides 5-day probabilistic forecasts over the European domain at approximately 10 km horizontal resolution (13 km until 6 October 2026).

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany / Europe

---

## What area it covers
- **Coverage:** European nest of the global ICON-EPS
- **Measured domain extent (since 6 October 2026):** **25.53°W – 64.09°E, 24.80°N – 71.71°N** — decoded from the `CLAT`/`CLON` cell-centre fields of the 2026-10-18 12 UTC run. The nest was enlarged on 6 October, mainly to the south (previously 24.86°W – 63.73°E, 28.86°N – 71.15°N).
- **Topography range:** −410.23 m to 3,629.46 m (`HHL` half-level 75, i.e. the surface), up from −346.54 to 3,401.59 m on the old 13 km grid

---

## Basic details
- **Model type:** Regional ensemble prediction system (regional nest of a global ensemble)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) — same core as the deterministic ICON-EU, and the same integration as the global ICON-EPS
- **Dynamical formulation:** Non-hydrostatic, triangular icosahedral horizontal grid
- **Convection-allowing:** No (deep convection parameterized)
- **Ensemble size:** **40 members, numbered 1–40, inherited from ICON-EPS. There is no separate unperturbed control run.**
- **Native horizontal grid (since 6 October 2026):** nest grid #63, **342,428 cells**, ~10 km
  - Verified from live GRIB2 headers (v1 feed and legacy tree): `gridType = unstructured_grid`, `numberOfDataPoints = 342428`, `numberOfGridUsed = 63`, `uuidOfHGrid = f0e39c8238525790474a63af800a2dc0`. DWD's 28 July 2026 Open Data newsletter confirms the change: 13 km → 10 km, new grid #63 with 2.08 times the grid points (164,984 × 2.08 ≈ 343,000). The newsletter does not name the grid type; the cell density matches an R2B08 nest of the parent R2B07 global grid.
  - Not the same grid as ICON-ART-EU-EPS, which moved to a separate 10 km grid #61 on the same date.
  - Until 5 October 2026: R3B07 nest grid #37, 164,984 cells, ~13 km (`uuidOfHGrid = ae487d28fe2e11e4af85e50a2a56a360`). Data on the two grids cannot be combined cell-by-cell.
- **Public output grid:** Native triangular grid only. **No regular latitude–longitude output is distributed.**
- **Vertical levels:** **74 full levels** (75 `HHL` half-levels)
- **Model top:** **22,770.33 m** — `HHL` half-level 1, spatially constant; unchanged on the new grid (confirmed from the legacy-tree `HHL` file).
- **Forecast length:** 120 hours (5 days) for **all four published cycles**
- **Update frequency / cycles:** 4× daily on Open Data (00, 06, 12, 18 UTC). The parent ICON-EPS cycles 8× daily; the intermediate cycles are not published — see Notes.
- **Temporal output resolution:** several schedules within each run — see below

> **Earlier correction, still valid.** Versions of this entry before August 2026 gave ~20 km (R2B07) and 60 levels — the pre-November-2022 configuration. The 6 October 2026 change has since moved the nest again, to ~10 km.

### Output step structure
Verified from the 2026-10-18 runs (member 01). The v1 feed is denser than the legacy tree for most surface and pressure-level fields.

| Field group | v1 feed | Legacy tree |
|---|---|---|
| Single-level and pressure-level — all cycles (most fields) | **91 steps:** hourly 0–75 h, then 3-hourly to 120 h | 65 steps: hourly 0–48 h; 3-hourly 48–72 h; 6-hourly 72–120 h |
| `VMAX_10M` — all cycles | **120 steps:** hourly from +1 h | 64 |
| Model level (`T`, `U`, `V`, `QV` on levels 72–74) — all cycles | 68 steps: hourly 0–51 h; 3-hourly 54–78 h; 6-hourly 84–120 h | 68 (same) |
| `CAPE_ML` — 06/18 UTC | 20 steps: 6-hourly from +6 h | 20 (same) |
| `SNOW_CON`, `SNOW_GSP` — 06/18 UTC | 21 steps: 6-hourly from 0 h | 21 (same) |
| `T`, `U`, `V` on pressure levels — 06/18 UTC | **500 and 850 hPa only**, 20 steps, 6-hourly from +6 h | not published |

The model-level fields still carry steps the single-level fields do not (in v1: 54, 57, 60, 63, 66, 69, 84, 90, …), so a single step list per run will not fit every field. DWD's 2 September 2026 newsletter describes the v1 migration as leaving time resolution unchanged; the table above shows it does not.

---

## Data assimilation
- **Data assimilation:** Yes — but not its own.
- **Method / cadence:** ICON-EU-EPS inherits initial conditions from the global 40-member **LETKF** ensemble data assimilation that initializes [ICON-EPS](../../global/de/icon-eps.md#data-assimilation), cycled every 3 hours.

There is no separate regional analysis. This is the structural distinction from regional ensembles such as MOGREPS-UK, AROME-EPS, or [ICON-D2-EPS](./icon-d2-eps.md), all of which run their own limited-area data assimilation.

---

## Initial and boundary conditions
- **Initial conditions:** the European-nest portion of the global LETKF analysis ensemble (see above).
- **Boundary conditions:** **not applicable in the usual sense.** ICON-EU-EPS is not driven by lateral boundary files from a parent model — the European nest is two-way coupled inside the same forecast integration as the global ICON-EPS, exchanging information with the parent domain every model time step. Nest and parent are one model run, split into two distribution products.

---

## Perturbations and design
All perturbation machinery is inherited from ICON-EPS; nothing is added at the regional level.

- **Initial condition perturbations:** each member is the nest portion of a different global LETKF analysis member.
- **Model/physics perturbations:** stochastic physics parameter perturbations assigned randomly across members before each run and held fixed for the duration of the integration. Same parameter set and same assignment as the global members.
- **SST perturbations:** since 30 June 2026, a 0.5 K uniform stochastic background plus an IFS-derived spatial amplitude pattern (2017–2025), replacing the previous globally uniform ~1.05 K spread.
- **Stochastic schemes:** none. No SPPT, SPP, or SKEB — model uncertainty is represented purely through static parameter perturbations plus SST perturbations.

**Member correspondence is exact.** ICON-EU-EPS member *n* is the European-nest portion of ICON-EPS member *n*, because they are the same integration. Members 1–20 are also the members whose forecasts historically supplied lateral boundary conditions to [ICON-D2-EPS](./icon-d2-eps.md).

---

## What it provides

**28 parameter directories**, the same in both download trees (verified 2026-10-18). Richer than the global ICON-EPS feed (22).

| Level type | Parameters |
|---|---|
| Single-level (16) | `ASWDIFD_S`, `ASWDIR_S`, `ATHB_S`, `CAPE_ML`, `CLCT`, `PS`, `SNOW_CON`, `SNOW_GSP`, `SOBS_RAD`, `T_2M`, `THBS_RAD`, `TOT_PREC`, `TQV`†, `U_10M`, `V_10M`, `VMAX_10M` |
| Pressure level (`lvt1/100`) | `FI`†, `T`, `U`, `V` — at **300, 500, 850 hPa** in the 00/12 UTC cycles; `T`, `U`, `V` at **500 and 850 hPa only** in the 06/18 UTC cycles |
| Model level (`lvt1/150`) | `T`, `U`, `V`, `QV` — at **levels 72, 73, 74** only (the lowest three of 74) |
| Time-invariant | `CLAT`, `CLON`, `ELAT`, `ELON`, `FR_LAND`, `HSURF`, `HHL` |

† **`FI` and `TQV` are published only in the 00 and 12 UTC cycles.** In the v1 tree their directories exist but contain no 06 or 18 UTC runs — easy to miss when building a cycle-agnostic ingest.

**`HHL` is reduced in the v1 feed:** only half-levels **72–75** are published, per member. The legacy tree carried all 75 half-levels in a single file; that full-column file ends with the legacy tree after 30 November 2026.

Compared with the global [ICON-EPS](../../global/de/icon-eps.md), this feed adds genuine upper-air content — geopotential, temperature, and wind on pressure levels — plus CAPE, convective and grid-scale snow, total column water vapour, and diffuse shortwave radiation. It drops `TD_2M`, `RELHUM_2M`, and `TKE`. The upper-air coverage is still thin: three pressure levels at most, and three near-surface model levels.

Time-integration conventions, verified by decoding `stepType`/`stepRange`:

| Convention | `stepType` | PDT | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | 11 | `TOT_PREC`, `SNOW_CON`, `SNOW_GSP` |
| Averaged from run start | `avg` | 11 | `ASWDIR_S`, `ASWDIFD_S`, `ATHB_S` |
| Max over the preceding hour | `max` | 11 | `VMAX_10M` (`99-100` at +100 h in v1) |
| Instantaneous | `instant` | 1 | `T_2M`, `PS`, `CLCT`, `CAPE_ML`, `TQV`, `SOBS_RAD`, `THBS_RAD`, and all pressure- and model-level fields |

As in the global product, `SOBS_RAD` and `THBS_RAD` decode as **instantaneous** net radiation fluxes while the `A*` family are run-start averages of the same quantities. Both are published; they are not duplicates.

Probability, percentile, mean, and spread products are **not** distributed — the public feed carries raw member fields only. All ensemble statistics must be computed by the user from the 40 members.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, plain `.grib2` (no bzip2 wrapper), **one member per file**
- **Official download location:** https://opendata.dwd.de/weather/nwp/v1/m/icon-eu-eps/

### Path structure (v1)
- **Single-level and time-invariant:**
  `/p/{PARAM}/r/{YYYY-MM-DD}T{HH}%3A00/e/{01..40}/s/PT{hhh}H{mm}M.grib2`
- **Multi-level:**
  `/p/{PARAM}/lvt1/{100|150}/lv1/{level}/r/{run}/e/{member}/s/PT{hhh}H{mm}M.grib2`

Pressure levels are given in Pa (`lv1/85000`). Member directories are zero-padded (`01`–`40`) and match `perturbationNumber`. The run token's colon is percent-encoded as `%3A`. Example:

`https://opendata.dwd.de/weather/nwp/v1/m/icon-eu-eps/p/T_2M/r/2026-10-18T12%3A00/e/01/s/PT012H00M.grib2`

- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 — covers the v1 tree.
- **Retention:** each run has its own directory; ~24 h rolling window (four runs).
- **Volume (v1):** **~55.7 GB and 135,160 files per 00/12 UTC run**; **~36.8 GB and 84,200 files per 06/18 UTC run** — about 185 GB/day (2026-10-18).
- **Publication latency (v1):** first files ~2 h 38–43 min after cycle time; run completes ~3 h 09–17 min after. Measured 2026-10-18: 00 UTC 02:38:23 → 03:09:14; 06 UTC 08:43:25 → 09:17:29; 12 UTC 14:40:56 → 15:12:02; 18 UTC 20:40:29 → 21:14:25 UTC. Starts at the same moment as the global ICON-EPS.

### Member packaging and encoding (v1)
- **Packaging:** **one GRIB message per file, one member per file**, under `e/{member}/`. The legacy tree concatenated all 40 members into one file per parameter, level, and step.
- **Member indexing:** `perturbationNumber` runs **1 to 40**. There is no member 0 and no separate control forecast.
- **GRIB2 encoding:** `productDefinitionTemplateNumber = 1` for instantaneous fields and `11` for time-processed fields; `typeOfEnsembleForecast = 192`; `numberOfForecastsInEnsemble = 40`; `typeOfProcessedData = cp`; `generatingProcessIdentifier = 2` (the same as deterministic ICON-EU); `centre = edzw`; `tablesVersion = 19`, `localTablesVersion = 1`; `missingValue = 9999`, no bitmap.
- **Packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library built without libaec/CCSDS support cannot decode these files.

### Legacy tree — discontinued after 30 November 2026
The old layout at https://opendata.dwd.de/weather/nwp/icon-eu-eps/grib/ stops after **30 November 2026** (DWD Open Data newsletter, 2 September 2026). It moved to grid #63 on 6 October along with the v1 feed. Differences until the shutdown:

- Layout: `/<cycle>/<parameter>/icon-eu-eps_europe_icosahedral_<leveltype>_<YYYYMMDDHH>_<step>[_<level>]_<param>.grib2.bz2`
- All 40 members in one bzip2-wrapped file (the wrapper adds almost nothing since the June 2026 CCSDS switch)
- Thinner step schedule (65 steps) and no pressure-level output at all in the 06/18 UTC cycles
- Full 75-level `HHL` in one file
- ~43 GB / 2,618 files per 00/12 run; ~28 GB / 1,640 files per 06/18 run

### Grid files and regridding
- **Grid definition files:** https://icon-downloads.mpimet.mpg.de/dwd_grids.xml (MPI-M). Look up grid number **63** for the current nest (37 for data before 6 October 2026).
- **Regridding guide:** https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid (cdo-based; generates the weights file as a by-product).
- **`/weather/lib/cdo/` is deleted after 30 November 2026.** It never held a grid #63 file; its `icon_grid_0037_R03B07_N02` file only fits pre-October data.

---

## Relationship to other models
- **[ICON-EPS](../../global/de/icon-eps.md):** the parent global ensemble. ICON-EU-EPS is not an independent system — it is the nest portion of the same 40-member integration, sharing initial conditions, physics perturbations, member numbering, and cycle schedule. Since 6 October 2026 the global product runs on grid #39 (1,310,720 cells, ~20 km) and this nest on grid #63 (342,428 cells, ~10 km).
- **[ICON-EU](../../../nwp_models/regional/germany/icon-eu.md):** deterministic counterpart, sharing the model core, physics, and nest architecture. The deterministic nest runs at ~6.5 km (grid #62) against this ensemble's ~10 km — the ensemble is still *coarser* than its deterministic sibling.
- **[ICON Global](../../../nwp_models/global/germany/icon-global.md):** the deterministic global model whose European nest is ICON-EU.
- **[ICON-D2-EPS](./icon-d2-eps.md):** DWD's convection-permitting regional ensemble at 2.2 km, historically driven by ICON-EPS members 1–20. For convection-allowing probabilistic guidance over central Europe, that is the relevant product.
- **[ICON-D2-RUC-EPS](./icon-d2-ruc-eps.md):** hourly-updating convection-permitting ensemble, typically taking boundaries from ICON-EU-EPS members.

---

## Notes
- **Public data is a subset of the cycles.** The parent ICON-EPS cycles 8× daily, but only 00, 06, 12, and 18 UTC are published. The 03/09/15/21 UTC cycles are limited to +30 h and exist mainly to refresh boundaries for the convection-permitting ensembles.
- **Not an independent regional ensemble.** ICON-EU-EPS shares initial conditions, physics perturbations, and member identity with the global ICON-EPS. Combining the two products into a "multi-model" or "multi-resolution" ensemble is not statistically meaningful — they are the same 40 trajectories at two grid spacings.
- **No control member.** Products expecting a control/perturbed split (common when ingesting ECMWF ENS or GEFS) need adapting: `perturbationNumber` starts at 1 and every member is perturbed.
- **The ensemble nest is coarser than the deterministic nest** — ~10 km against ~6.5 km.
- **Cycle-dependent content.** `FI` and `TQV` appear only at 00/12 UTC; the 06/18 UTC cycles carry pressure-level `T`/`U`/`V` at 500 and 850 hPa only, and `CAPE_ML` and the snow fields at 6-hourly steps.
- **The 6 October 2026 grid change splits any archive.** Grid #37 (13 km) before, grid #63 (10 km, larger domain) after. Check `numberOfGridUsed` before stacking data from either side. Open-Meteo's regular-grid ICON-EU-EPS product stopped updating at that date while it moved to the native grid.
- **DWD's English ensemble-prediction page is stale.** It still describes the ICON ensemble as "approximately 40 km ... with a grid refinement to 20 km over Europe" and references COSMO-D2-EPS, retired in February 2021. Its resolution figures should not be trusted.

---

## Recent version history

ICON-EU-EPS has no independent change-notice series. It is upgraded as part of the global ICON/ICON-EPS system, and DWD's notices state that changes apply to all global ICON configurations including the EU nests and ensemble systems. The nest-relevant items are listed here; see the [ICON-EPS](../../global/de/icon-eps.md#recent-version-history) and [ICON Global](../../../nwp_models/global/germany/icon-global.md#recent-version-history) entries for the full sequence.

> Model-change notices are covered through 30 June 2026. Later entries are taken from Open Data newsletters and live data; the change notice for the 6 October 2026 upgrade has not been reviewed for this entry.

### 30 November 2026 (scheduled) — legacy tree and `lib/cdo` withdrawn
Per DWD's 2 September 2026 newsletter: delivery under `/weather/nwp/icon-eu-eps/` stops after 30 November; `/weather/lib/cdo/` is deleted on the same date.

### 6 October 2026 — resolution increase and domain enlargement
Nest mesh changed from R3B07 grid #37 (164,984 cells, ~13 km) to grid #63 (342,428 cells, ~10 km), with the domain extended — chiefly southward, to about 24.8°N. The global ensemble moved to ~20 km at the same time. Announced in DWD's 28 July 2026 Open Data newsletter (13 → 10 km, grid #63, 2.08× the grid points, enlarged EU-nest domain), which also noted that the `CLAT`/`CLON` and `ELAT`/`ELON` coordinate fields change with the grid and offered test data for the 2026-07-15 00 UTC run under `/test/weather/nwp/icon-eu-eps/`. Applies to both download trees. Vertical structure unchanged.

### 2 September 2026 — v1 URL scheme
Published additionally under `/weather/nwp/v1/m/icon-eu-eps/` (directory created 21 August 2026): one member per file, plain CCSDS GRIB2, ISO 8601 run/step tokens. Undocumented differences from the legacy tree: 91-step surface and pressure-level output, hourly `VMAX_10M`, pressure-level `T`/`U`/`V` in the 06/18 UTC cycles, and `HHL` reduced to half-levels 72–75.

### 30 June 2026 — model version `icon-2025.10-dwd-2.2` (effective 12 UTC run)
Revised SST ensemble perturbations (0.5 K uniform background plus IFS-derived spatial pattern, replacing globally uniform ~1.05 K); retuned ensemble physics perturbations for the convection scheme and SSO low-level blocking; reduced humidity relaxation in the lower stratosphere; assimilation of MWR radiances from the AWS satellite. Applies to all global ICON configurations including the EU nests.

### 16 June 2026 — CCSDS compression (effective 09 UTC run)
All ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS GRIB2 output switched to lossless CCSDS packing, 40–50% smaller than the previous encoding. **Most likely change to break older downstream tooling.**

### 18 March 2026 — DACE 2.26, EnVar weighting bugfix (effective 00 UTC assimilation / 06 UTC forecast)
Correction of a coding error in the weighting between climatological and ensemble-derived background error covariances in the global hybrid EnVar. **Reduced ensemble spread as a side effect** — relevant when comparing spread statistics across this date.

### 24 January 2024 — sea-ice bottom heat flux, model version `icon-2.6.6-nwp2` (effective 12 UTC run)
Sea-ice scheme revised to account for ocean-to-ice heat flux; external parameter bugfix for false glacier points; adaptive time-step reduction extended to horizontal CFL exceedances.

### 23 November 2022 — resolution upgrade
- **European nest horizontal resolution increased from 20 km to 13 km** (R2B07 → R3B07)
- **Nest vertical levels increased from 60 to 74**
- Global ensemble simultaneously went from 40 km to 26 km (R2B06 → R3B06) and 90 to 120 levels
- New high-resolution global orography (MERIT, REMA, GLOBE); adaptive surface friction adjustment
- The upgrade applied to the ensemble only — deterministic ICON-EU remained at 6.5 km

### January 2018 — operational launch
ICON-EU-EPS became operational with ICON-EPS, at 20 km and 60 nest levels, with the same 40-member design retained today.

---

## Official documentation
- DWD ICON model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
- DWD ensemble prediction overview (⚠️ resolution figures stale — see Notes): https://www.dwd.de/EN/research/weatherforecasting/num_modelling/04_ensemble_methods/ensemble_prediction/ensemble_prediction_node.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD ICON-EPS change notices: https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_eps_gesamt.html
- DWD ICON change notices (carry most post-2024 ensemble changes): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_gesamt.html
- DWD ICON-EPS resolution upgrade notice (November 2022): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/icon_eps/pdf_2022/pdf_icon_eps_23_11_2022.pdf
- DWD Open Data newsletter, 2 September 2026 (v1 URL scheme, legacy shutdown, lib/cdo): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD Open Data newsletter, 28 July 2026 (6 October resolution and domain change): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_juli2026_2.html
- DWD-Geoportal ICON entry: https://dwd-geoportal.de/products/G_EJM/
- DWD Open Data v1 tree: https://opendata.dwd.de/weather/nwp/v1/m/icon-eu-eps/
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- ICON grid definition files (MPI-M): https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- DWD regridding guide for native ICON grids: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid

### Key references
- Reinert, D., Prill, F., Frank, H., Denhard, M., Baldauf, M., Schraff, C., Gebhardt, C., Marsigli, C., and Zängl, G. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378

---

*Live verification performed 2026-10-18 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-eu-eps/` (parameter and step census of the 12 and 18 UTC runs, member 01; 06 UTC spot checks), the legacy `https://opendata.dwd.de/weather/nwp/icon-eu-eps/grib/` tree, and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.50.0.*
