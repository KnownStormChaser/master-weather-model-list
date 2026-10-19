# ICON-EPS (ICON Global Ensemble Prediction System)

## What this model is
ICON-EPS is DWD's operational global ensemble prediction system, designed to quantify forecast uncertainty in short- to medium-range weather prediction.

It is the ensemble counterpart of the deterministic [ICON](../../../nwp_models/global/germany/icon-global.md) global model, sharing the same dynamical core, the same triangular icosahedral grid family, and the same tightly-coupled European nest architecture. ICON-EPS produces 40 ensemble members on a global ~20 km grid with a higher-resolution (~10 km) European nest embedded within the same forecast integration. The European-nest portion is distributed separately as [ICON-EU-EPS](../../regional/de/icon-eu-eps.md).

ICON-EPS has been operational since January 2018, and serves a dual purpose: producing probabilistic forecasts directly, and providing initial conditions and boundary forcing to several downstream DWD systems including the [ICON-D2-EPS](../../regional/de/icon-d2-eps.md) convection-permitting ensemble. The same 40-member LETKF ensemble also supplies the flow-dependent background-error covariances for the deterministic ICON's hybrid EnVar analysis, so the two systems are coupled through the analysis as well as through the forecast.

The current configuration dates from **6 October 2026**, when DWD increased the ensemble's horizontal resolution again — from ~26 km to ~20 km globally and from ~13 km to ~10 km over the European nest — and enlarged the nest domain. The previous step was the **23 November 2022 upgrade** (40 km → 26 km global, 20 km → 13 km nest, 90 → 120 levels).

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany

---

## What area it covers
- **Coverage:** Global
- **Nested subdomain:** European nest at higher resolution, distributed separately as [ICON-EU-EPS](../../regional/de/icon-eu-eps.md) (cell centres 25.53°W – 64.09°E, 24.80°N – 71.71°N since 6 October 2026)

---

## Basic details
- **Model type:** Global ensemble NWP
- **Model system / core:** ICON (Icosahedral Nonhydrostatic), same core, physics, and vertical grid as deterministic ICON
- **Dynamical formulation:** Non-hydrostatic, triangular icosahedral horizontal grid
- **Convection-allowing:** No (deep convection parameterized)
- **Ensemble size:** **40 members, numbered 1–40. There is no separate unperturbed control run.**
- **Native horizontal grid (since 6 October 2026):** **1,310,720 cells**, ~20 km — an R2B07 global grid (20 × 256² = 1,310,720 cells exactly)
  - Verified from live GRIB2 headers (v1 feed, 2026-10-18 12 UTC run, and the legacy tree): `gridType = unstructured_grid`, `numberOfDataPoints = 1310720`, `numberOfGridUsed = 39`, `uuidOfHGrid = 49c340e2248d11e58105c12d5e3b00a4`. DWD's 28 July 2026 Open Data newsletter gives the same figures: 26 km → 20 km, new grid #39 with 1.78 times the grid points. The matching grid definition file is grid number 39 on MPI-M's list (see *Grid files and regridding*). ICON-ART and ICON-ART-EPS moved to the same grid #39 that day.
  - Until 5 October 2026: R3B06 grid #36, 737,280 cells, ~26 km (`uuidOfHGrid = ae487d14fe2e11e4af85e50a2a56a360`). Data on the two grids cannot be combined cell-by-cell.
  - The deterministic ICON Global runs on R3B07 (2,949,120 cells) — about 2.25 times as many cells as the ensemble.
- **Public output grid:** Native triangular grid only. **No regular latitude–longitude output is distributed.**
- **Vertical levels:** 120 full levels (121 `HHL` half-levels) — unchanged by the October grid switch, confirmed from the legacy-tree `HHL` file on grid #39
- **Vertical coordinate:** SLEVE terrain-following hybrid, identical in structure to deterministic ICON. Half-level 1 = 75,000.0 m everywhere. Surface (`HHL` half-level 121) on the new grid spans −211.44 m to 6,032.56 m, up from −117.28 to 5,858.09 m on the old 26 km grid — the finer mesh resolves more of the high terrain.
- **Model top:** 75 km exactly
- **Forecast length:**
  - 180 hours for the 00 and 12 UTC cycles
  - 120 hours for the 06 and 18 UTC cycles
  - 30 hours for the intermediate 03, 09, 15, 21 UTC cycles (per DWD's ensemble page; not published on Open Data — see Notes)
- **Update frequency / cycles:** 8× daily internally, every 3 hours. **Only the four main cycles (00, 06, 12, 18 UTC) reach Open Data** — re-verified 2026-10-18 in the v1 tree.
- **Temporal output resolution:** varies by cycle, parameter and download tree — see below

### Output step structure
The v1 feed is considerably denser than the legacy tree for the 00/12 UTC cycles. Verified from listings of the 2026-10-18 runs (member 01):

| Cycle / field | v1 feed | Legacy tree |
|---|---|---|
| 00, 12 UTC — most fields | **111 steps:** hourly 0–75 h, then 3-hourly to 180 h | 70 steps: hourly 0–48 h; 3-hourly 48–72 h; 6-hourly 72–120 h; 12-hourly 120–180 h |
| 06, 18 UTC — most fields | 21 steps: 6-hourly 0–120 h | 21 steps (same) |
| `VMAX_10M` | **hourly** from +1 h: 180 steps (00/12), 120 steps (06/18) | 69 (00/12), 20 (06/18) |
| `PS`, `RELHUM_2M` — 00/12 | 31 steps: 6-hourly 0–180 h | `PS` 26 (6-hourly to 120, then 12-hourly) |
| `ASWDIR_S` | 3-hourly from +3 h (60 steps, 00/12); 6-hourly from +6 h (20, 06/18) | 37 (00/12), 20 (06/18) |
| `TKE` (model levels 99–104) | 37 steps: hourly 0–36 h | same |

DWD's 2 September 2026 newsletter states that forecast range and time resolution are unchanged by the v1 migration. The table above shows they are not, for the main cycles. The 06/18 UTC cycles still carry no sub-6-hourly output except `VMAX_10M`.

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** 40-member **Local Ensemble Transform Kalman Filter (LETKF)** ensemble data assimilation, cycled every 3 hours. Each cycle begins from 3-hour forecasts of the previous analysis ensemble; observation increments produce 40 slightly different analysis states, which become the initial conditions for the next ensemble forecast. The LETKF resolution figures previously given here (40 km global / 20 km nest) have not been re-checked since the 6 October 2026 resolution change.

The same LETKF ensemble supplies the flow-dependent component of the background-error covariance in the deterministic ICON's hybrid EnVar analysis (`B = (1−a)·B_clim + a·B_ens`). ICON-EPS and deterministic ICON are therefore not merely siblings — the ensemble is a required input to the deterministic analysis.

---

## Initial and boundary conditions

> *The template's "Initial and boundary conditions" section is scoped to limited-area ensembles. ICON-EPS is global and has no lateral boundaries; the initial-condition half is recorded here.*

- **Initial conditions:** the 40-member LETKF analysis ensemble described above.
- **Boundary conditions:** not applicable (global domain). The European nest is two-way coupled within the same integration rather than driven by external boundaries.

---

## Perturbations and design
- **Initial condition perturbations:** each member is initialized from a different LETKF analysis member.
- **Model/physics perturbations:** stochastic **physics parameter perturbations**. Before each run, a set of defined tuning-parameter perturbations is randomly assigned across members. A constraint ensures each forecast run perturbs the same total number of parameters, while the distribution across members varies run-to-run. Parameters are held fixed for the duration of each member's integration — they are not varied spatially or temporally during the forecast.
- **SST perturbations:** since 30 June 2026, a 0.5 K uniform stochastic background combined with a spatial amplitude pattern derived from IFS SST analyses 2017–2025, replacing the previous globally uniform ~1.05 K spread. Net effect is substantially reduced tropical spread, comparable spread in marginal seas, and larger spread along the Gulf Stream edge.
- **Stochastic schemes:** none. Unlike ECMWF (SPP) or NCEP (SPPT + SKEB), ICON-EPS does not use stochastic perturbation of tendencies or stochastic kinetic energy backscatter. Model uncertainty is represented purely through static parameter perturbations applied at the start of each run, plus the SST perturbations above.

### Use as boundary conditions for regional ensembles
- The European-nest portion is the [ICON-EU-EPS](../../regional/de/icon-eu-eps.md) regional ensemble.
- Forecasts from **members 1–20** provide lateral boundary conditions for [ICON-D2-EPS](../../regional/de/icon-d2-eps.md), DWD's 2.2 km convection-permitting ensemble. The 3-hourly internal cycling exists largely to keep these boundaries fresh.

---

## What it provides

**The Open Data parameter set is a small subset of the model's output** — 22 parameter directories, against 104 for deterministic [ICON Global](../../../nwp_models/global/germany/icon-global.md). Identical in the v1 and legacy trees and across all four published cycles (verified 2026-10-18):

| Level type | Parameters |
|---|---|
| Single-level (14) | `ASOB_S`, `ASWDIR_S`, `ATHB_S`, `CLCT`, `PS`, `RELHUM_2M`, `SOBS_RAD`, `T_2M`, `TD_2M`, `THBS_RAD`, `TOT_PREC`, `U_10M`, `V_10M`, `VMAX_10M` |
| Model level (1) | `TKE` — levels 99–104 only |
| Time-invariant (7) | `CLAT`, `CLON`, `ELAT`, `ELON`, `FR_LAND`, `HSURF`, `HHL` |

**There are no pressure-level fields, no 3D temperature, wind, or humidity, and no `PMSL`.** The public feed is oriented toward surface and near-surface probabilistic products.

**`HHL` is reduced in the v1 feed:** only half-levels **99–121** (the lowest 23) are published, per member. The legacy tree carried all 121 half-levels in a single file, which is how the 75 km model top and the October grid change were confirmed; that full-column file ends with the legacy tree after 30 November 2026.

Time-integration conventions, verified by decoding `stepType`/`stepRange`:

| Convention | `stepType` | Examples |
|---|---|---|
| Accumulated from run start | `accum` | `TOT_PREC` |
| Averaged from run start | `avg` | `ASOB_S`, `ASWDIR_S`, `ATHB_S` |
| Max over the preceding hour | `max` | `VMAX_10M` (`99-100` at +100 h in v1) |
| Instantaneous | `instant` | `T_2M`, `TD_2M`, `RELHUM_2M`, `CLCT`, `PS`, `SOBS_RAD`, `THBS_RAD`, `TKE` |

Note that `SOBS_RAD` and `THBS_RAD` decode as **instantaneous** net radiation fluxes, while `ASOB_S` and `ATHB_S` are the run-start averages of the same quantities. Both families are published; they are not duplicates.

Probability and percentile products are **not** distributed — the public feed carries raw member fields only. Ensemble mean, spread, and exceedance probabilities must be computed by the user from the 40 members.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, plain `.grib2` (no bzip2 wrapper), **one member per file**
- **Official download location:** https://opendata.dwd.de/weather/nwp/v1/m/icon-eps/

### Path structure (v1)
- **Single-level and time-invariant:**
  `/p/{PARAM}/r/{YYYY-MM-DD}T{HH}%3A00/e/{01..40}/s/PT{hhh}H{mm}M.grib2`
- **Multi-level (`TKE`, `HHL`):**
  `/p/{PARAM}/lvt1/150/lv1/{level}/r/{run}/e/{member}/s/PT{hhh}H{mm}M.grib2`

Member directories are zero-padded (`01`–`40`) and match `perturbationNumber`. The run token's colon is percent-encoded as `%3A`. Example:

`https://opendata.dwd.de/weather/nwp/v1/m/icon-eps/p/T_2M/r/2026-10-18T12%3A00/e/01/s/PT012H00M.grib2`

- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 — pipe-delimited `path|bytes|mtime` for the whole `/weather/nwp/` tree, v1 included.
- **Retention:** each run has its own directory; ~24 h rolling window (four runs).
- **Volume (v1):** **~103 GB and 66,520 files per 00/12 UTC run**; **~36 GB and 25,720 files per 06/18 UTC run** — about 280 GB/day (2026-10-18). The jump from the legacy tree's ~1,000 files per run is the per-member split; the jump in bytes is the denser 00/12 step schedule plus the finer grid.
- **Publication latency (v1):** first files ~2 h 38–43 min after cycle time; run completes ~3 h 15–21 min after. Measured 2026-10-18: 06 UTC 08:43:25 → 09:17:33; 12 UTC 14:40:59 → 15:21:34; 18 UTC 20:40:29 → 21:14:28 UTC.

### Member packaging and encoding (v1)
- **Packaging:** **one GRIB message per file, one member per file.** The legacy tree concatenated all 40 members into one file per parameter-step; the v1 feed splits them under `e/{member}/`.
- **Member indexing:** `perturbationNumber` runs **1 to 40**. There is no member 0 and no separate control forecast.
- **GRIB2 encoding:** `productDefinitionTemplateNumber = 1` for instantaneous fields and `11` for time-processed fields; `typeOfEnsembleForecast = 192` (DWD local code); `numberOfForecastsInEnsemble = 40`; `typeOfProcessedData = cp`; `generatingProcessIdentifier = 1` (the same as deterministic ICON — use `typeOfProcessedData` to tell them apart); `centre = edzw`; `tablesVersion = 19`, `localTablesVersion = 1`; `missingValue = 9999`, no bitmap.
- **Packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library without libaec/CCSDS support cannot decode these files.

### Legacy tree — discontinued after 30 November 2026
The old layout at https://opendata.dwd.de/weather/nwp/icon-eps/grib/ stops after **30 November 2026** (DWD Open Data newsletter, 2 September 2026). It moved to the new grid #39 on 6 October along with the v1 feed, so until the shutdown the two trees carry the same data on the same grid, with these differences:

- Layout: `/<cycle>/<parameter>/icon-eps_global_icosahedral_<leveltype>_<YYYYMMDDHH>_<step>[_<level>]_<param>.grib2.bz2`
- All 40 members in one bzip2-wrapped file per parameter-step (the wrapper compresses little since the June 2026 CCSDS switch)
- Thinner 00/12 UTC step schedule (70 steps) and thinner `VMAX_10M`
- Full 121-level `HHL` in one file
- ~65 GB / 1,087 files per 00/12 run and ~28 GB / 521 files per 06/18 run on the new grid (up from ~37 GB and ~16–18 GB on the old 26 km grid)

### Grid files and regridding
- **Grid definition files:** https://icon-downloads.mpimet.mpg.de/dwd_grids.xml (MPI-M). Look up grid number **39** for the current mesh (36 for data before 6 October 2026).
- **Regridding guide:** https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid (cdo-based; generates the weights file as a by-product).
- **`/weather/lib/cdo/` is deleted after 30 November 2026.** It never held a grid #39 file, and its prebuilt global weights (`ICON_GLOBAL2WORLD_*_EASY`) were built for the deterministic R3B07 mesh, not the ensemble.

---

## Relationship to other models
- **[ICON Global](../../../nwp_models/global/germany/icon-global.md):** deterministic counterpart, sharing the dynamical core, vertical structure, and coupled European nest architecture. ICON-EPS now runs on an R2B07 mesh (1,310,720 cells) against ICON Global's R3B07 (2,949,120 cells). The two systems share the LETKF ensemble at the heart of DWD's data assimilation.
- **[ICON-EU-EPS](../../regional/de/icon-eu-eps.md):** the European-nest portion of ICON-EPS, distributed as a separate regional product. Not an independent regional ensemble — it is the regional subset of this same forecast integration.
- **[ICON-D2-EPS](../../regional/de/icon-d2-eps.md):** convection-permitting regional ensemble at 2.2 km, driven at the boundaries by ICON-EPS members 1–20.
- **[ICON-D2-RUC-EPS](../../regional/de/icon-d2-ruc-eps.md):** hourly-updating convection-permitting ensemble; like ICON-D2-EPS, it depends on ICON-EPS for boundary forcing.
- **[AICON-Global](../../../nwp_models/global/germany/aicon-global.md):** DWD's deterministic ML global model, trained on the ICON-DREAM reanalysis. No AI ensemble counterpart exists at DWD as of 2026-10-18.

ICON-EPS is part of the ICON framework, jointly developed with the Max Planck Institute for Meteorology, and has been open source under a permissive licence since January 2024 (see the [ICON entry](../../../nwp_models/global/germany/icon-global.md#open-source)).

---

## Notes
- **Public data is a subset in two dimensions.** Only four of the eight daily cycles are published, and only 22 of the model's parameters. The 03/09/15/21 UTC cycles exist to refresh regional-ensemble boundaries and are limited to +30 h; they do not appear on Open Data in either tree.
- **40 members is smaller than ECMWF's 51-member ENS or NCEP's 31-member GEFS**, but ICON-EPS partly compensates through 8× daily cycling, giving a fresh ensemble every 3 hours — more frequent than most operational global ensembles. Only the four main cycles produce extended-range forecasts.
- **No control member.** Products expecting a control/perturbed split (common when ingesting ECMWF ENS or GEFS) will need adapting: `perturbationNumber` starts at 1 and every member is perturbed.
- **`TKE` is the only 3D field, and barely.** Six model levels (99–104, near-surface) for the first 36 hours. It is not a substitute for upper-air ensemble output.
- **The 6 October 2026 grid change splits any archive.** Grid #36 (26 km) before, grid #39 (20 km) after. Check `numberOfGridUsed` before stacking data from either side.
- Output should be interpreted probabilistically rather than as a deterministic forecast.
- **DWD's English ensemble-prediction page is stale.** It still describes ICON-EPS as "approximately 40 km with a grid refinement to 20 km over Europe" — two upgrades out of date — and repeatedly references COSMO-D2-EPS, retired in February 2021. Its cycle structure (8× daily, +30 h at the intermediate cycles, 120 h otherwise, 180 h at 00/12) is consistent with observed behaviour, but the resolution figures should not be trusted.
- **Cross-entry consistency.** The earlier mismatch with the ICON-EU-EPS entry (20 km vs 13 km nest) was resolved in favour of 13 km; since 6 October 2026 both entries give the new ~10 km nest grid #63.

---

## Recent version history

Since January 2024, ICON-EPS changes have generally been announced through the main ICON change notices rather than a separate ICON-EPS series — the notices state that changes apply to all global ICON configurations including the EU nests and the ensemble systems. The ensemble-relevant items are listed here; see the [ICON Global entry](../../../nwp_models/global/germany/icon-global.md#recent-version-history) for the full sequence.

> Model-change notices are covered through 30 June 2026. Later entries are taken from DWD's Open Data newsletters (28 July and 2 September 2026) and live data. The ICON/ICON-EPS model change notice for the 6 October upgrade has not been reviewed, so any physics or data-assimilation changes made alongside the grid change are not recorded here.

### 30 November 2026 (scheduled) — legacy tree and `lib/cdo` withdrawn
Per DWD's 2 September 2026 newsletter: delivery under `/weather/nwp/icon-eps/` stops after 30 November; `/weather/lib/cdo/` is deleted on the same date.

### 6 October 2026 — resolution increase and nest enlargement
Global ensemble mesh changed from R3B06 (grid #36, 737,280 cells, ~26 km) to an R2B07 mesh (grid #39, 1,310,720 cells, ~20 km); European nest from grid #37 (164,984 cells, ~13 km) to grid #63 (342,428 cells, ~10 km) with a larger domain. Announced in DWD's 28 July 2026 Open Data newsletter, which gave 26 → 20 km and 1.78× the grid points for the global ensemble and 13 → 10 km and 2.08× for the nest, warned that `CLAT`/`CLON`/`ELAT`/`ELON` change with the grid, and offered test data for the 2026-07-15 00 UTC run under `/test/weather/nwp/icon-eps/`. Applies to both download trees. Vertical structure unchanged (120 levels, 75 km top). The deterministic ICON Global grid (#26) was not changed.

### 2 September 2026 — v1 URL scheme
ICON-EPS published additionally under `/weather/nwp/v1/m/icon-eps/`: one member per file, plain CCSDS GRIB2, ISO 8601 run/step tokens. Undocumented differences from the legacy tree: hourly-to-75 h / 3-hourly-to-180 h output in the 00/12 UTC runs, hourly `VMAX_10M` in all cycles, and `HHL` reduced to half-levels 99–121.

### 30 June 2026 — model version `icon-2025.10-dwd-2.2` (effective 12 UTC run)
The most ensemble-specific upgrade in the recent series:
- **Revised SST ensemble perturbations** — replaces globally uniform ~1.05 K stochastic spread with a 0.5 K uniform background plus an IFS-derived spatial amplitude pattern (2017–2025). Substantially reduced tropical spread; larger spread along the Gulf Stream edge.
- **Retuned ensemble physics perturbations** (convection scheme, SSO low-level blocking) to partly compensate for the reduced spread.
- Reduced humidity relaxation in the lower stratosphere; assimilation of MWR radiances from the AWS satellite.

### 16 June 2026 — CCSDS compression (effective 09 UTC run)
All ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS GRIB2 output switched to lossless CCSDS packing, 40–50% smaller than the previous encoding. **Most likely change to break older downstream tooling** — a GRIB library without libaec/CCSDS support cannot decode these files.

### 18 March 2026 — DACE 2.26, EnVar weighting bugfix (effective 00 UTC assimilation / 06 UTC forecast)
Correction of a coding error in the weighting between climatological and ensemble-derived background error covariances in the global hybrid EnVar. **Reduced ICON-EPS ensemble spread as a side effect** — relevant when comparing spread statistics across this date.

### 24 January 2024 — sea-ice bottom heat flux, model version `icon-2.6.6-nwp2` (effective 12 UTC run)
Joint ICON / ICON-EPS notice:
- Sea-ice scheme (Mironov et al. 2012) revised to account for heat flux from the underlying ocean toward the ice, previously neglected. Significantly reduces ICON's Arctic cold bias in winter and, to a lesser extent, the Antarctic coastal cold bias.
- External parameter data bugfix for false glacier points (soiltype vs. land-cover consistency check now defers to land cover; MODIS albedo adjustment corrected).
- Adaptive time-step reduction extended to horizontal CFL exceedances.

### 23 November 2022 — major resolution upgrade
- **Global horizontal resolution increased** from 40 km to 26 km (R2B06 → R3B06)
- **European nest horizontal resolution increased** from 20 km to 13 km (R2B07 → R3B07; the ICON-EU-EPS portion)
- **Vertical levels increased** from 90 to 120 globally; from 60 to 74 in the European nest
- New high-resolution global orography (MERIT, REMA, GLOBE); adaptive surface friction adjustment
- The horizontal upgrade applied to the EPS only — deterministic ICON remained at 13 km / 6.5 km

### January 2018 — operational launch
ICON-EPS became operational alongside DWD's broader transition to the ICON framework. The original configuration used 40 km global / 20 km nest resolution with 90 vertical levels and the same 40-member design retained today.

### Earlier history
Developed as part of DWD's transition from the legacy GME global model and COSMO-EU regional model (replaced by ICON in January 2015 and ICON-EU in June 2015). The ensemble system followed three years later.

---

## Official documentation
- DWD ICON model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
- DWD ensemble prediction overview (⚠️ resolution figures stale — see Notes): https://www.dwd.de/EN/research/weatherforecasting/num_modelling/04_ensemble_methods/ensemble_prediction/ensemble_prediction_node.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD ICON-EPS change notices: https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_eps_gesamt.html
- DWD ICON change notices (carry most post-2024 ICON-EPS changes): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_gesamt.html
- DWD ICON-EPS resolution upgrade notice (November 2022): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/icon_eps/pdf_2022/pdf_icon_eps_23_11_2022.pdf
- DWD Open Data newsletter, 2 September 2026 (v1 URL scheme, legacy shutdown, lib/cdo): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD Open Data newsletter, 28 July 2026 (6 October resolution and domain change): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_juli2026_2.html
- DWD Open Data v1 tree: https://opendata.dwd.de/weather/nwp/v1/m/icon-eps/
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- ICON grid definition files (MPI-M): https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- DWD regridding guide for native ICON grids: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid

### Key references
- Reinert, D., Prill, F., Frank, H., Denhard, M., Baldauf, M., Schraff, C., Gebhardt, C., Marsigli, C., and Zängl, G. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378
- Mironov, D., et al. (2012). *Parameterisation of sea and lake ice in numerical weather prediction models of the German Weather Service.* Tellus A, 64, 17330.

---

*Live verification performed 2026-10-18 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-eps/` (parameter and step census of the 12 and 18 UTC runs, member 01; 06 UTC spot checks), the legacy `https://opendata.dwd.de/weather/nwp/icon-eps/grib/` tree, and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.50.0.*
