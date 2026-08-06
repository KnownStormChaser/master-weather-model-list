# ICON-EPS (ICON Global Ensemble Prediction System)

## What this model is
ICON-EPS is DWD's operational global ensemble prediction system, designed to quantify forecast uncertainty in short- to medium-range weather prediction.

It is the ensemble counterpart of the deterministic [ICON](../../../nwp_models/global/germany/icon-global.md) global model, sharing the same dynamical core, the same triangular icosahedral grid family, and the same tightly-coupled European nest architecture. ICON-EPS produces 40 ensemble members on a global ~26 km grid with a higher-resolution (~13 km) European nest embedded within the same forecast integration. The European-nest portion is distributed separately as [ICON-EU-EPS](../../regional/de/icon-eu-eps.md).

ICON-EPS has been operational since January 2018, and serves a dual purpose: producing probabilistic forecasts directly, and providing initial conditions and boundary forcing to several downstream DWD systems including the [ICON-D2-EPS](../../regional/de/icon-d2-eps.md) convection-permitting ensemble. The same 40-member LETKF ensemble also supplies the flow-dependent background-error covariances for the deterministic ICON's hybrid EnVar analysis, so the two systems are coupled through the analysis as well as through the forecast.

The current configuration reflects DWD's **23 November 2022 resolution upgrade**, which increased horizontal resolution from 40 km to 26 km globally and from 20 km to 13 km over the European nest, while raising the vertical level count from 90 to 120.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany

---

## What area it covers
- **Coverage:** Global
- **Nested subdomain:** European nest at higher resolution, distributed separately as [ICON-EU-EPS](../../regional/de/icon-eu-eps.md) (domain 23.5°W – 62.5°E, 29.5°N – 70.5°N)

---

## Basic details
- **Model type:** Global ensemble NWP
- **Model system / core:** ICON (Icosahedral Nonhydrostatic), same core, physics, and vertical grid as deterministic ICON
- **Dynamical formulation:** Non-hydrostatic, triangular icosahedral horizontal grid
- **Convection-allowing:** No (deep convection parameterized at ~26 km)
- **Ensemble size:** **40 members, numbered 1–40. There is no separate unperturbed control run.**
- **Native horizontal grid:** R3B06 triangular grid, **737,280 cells**, ~26 km effective mesh — exactly one quarter of the deterministic ICON's R3B07 cell count
  - Verified from live GRIB2 headers: `gridType = unstructured_grid`, `numberOfDataPoints = 737280`, `numberOfGridUsed = 36`, `uuidOfHGrid = ae487d14fe2e11e4af85e50a2a56a360`. The matching grid definition file is `icon_grid_0036_R03B06_G.nc.bz2` under the CDO library path below.
- **Public output grid:** Native triangular grid only. **No regular latitude–longitude output is distributed** — see *Data availability*.
- **Vertical levels:** 120 full levels (121 `HHL` half-levels), confirming the 2022 upgrade from 90
- **Vertical coordinate:** SLEVE terrain-following hybrid, identical in structure to deterministic ICON. Verified from the `HHL` file: half-level 1 = 75,000.0 m everywhere, half-level 60 = 15,636.87–15,668.14 m (first level with terrain signal), half-level 121 (surface) = −117.28 to 5,858.09 m. The coarser mesh smooths orography relative to ICON Global, whose surface field spans −346.5 to 6,220.2 m.
- **Model top:** 75 km exactly
- **Forecast length:**
  - 180 hours for the 00 and 12 UTC cycles
  - 120 hours for the 06 and 18 UTC cycles
  - 30 hours for the intermediate 03, 09, 15, 21 UTC cycles (per DWD's ensemble page; not published on Open Data — see Notes)
- **Update frequency / cycles:** 8× daily internally, every 3 hours. **Only the four main cycles (00, 06, 12, 18 UTC) reach Open Data** — verified by tree enumeration.
- **Temporal output resolution:** varies by cycle and by parameter — see below

### Output step structure
Not the "hourly to +78, 3-hourly thereafter" pattern used by deterministic ICON. Verified from live listings of the 2026-08-05 runs:

| Cycle | Steps | Cadence |
|---|---|---|
| 00, 12 UTC | 70 | hourly 0–48 h; 3-hourly 48–72 h; 6-hourly 72–120 h; 12-hourly 120–180 h |
| 06, 18 UTC | 21 | 6-hourly throughout, 0–120 h |

The 06 and 18 UTC cycles carry no sub-6-hourly output at all. Anyone building a uniform ensemble time series across cycles needs to handle four distinct cadences within a single 00 UTC run and a completely different resolution in the off-cycles.

Two parameters deviate further within the 00/12 runs:
- **`ps`** — 26 steps only: 6-hourly to +120, then 12-hourly to +180. It does not follow the hourly early range.
- **`tke`** — 37 steps, hourly 0–36 h only, and only on model levels 99–104 (6 of 120).
- **`vmax_10m`** — 69 steps; no step 000, as expected for an interval maximum.

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** 40-member **Local Ensemble Transform Kalman Filter (LETKF)** ensemble data assimilation at 40 km globally with a 20 km European nest, cycled every 3 hours. Each cycle begins from 3-hour forecasts of the previous analysis ensemble; observation increments produce 40 slightly different analysis states, which become the initial conditions for the next ensemble forecast.

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

**The Open Data parameter set is a small subset of the model's output** — 22 parameter directories, against 102 for deterministic [ICON Global](../../../nwp_models/global/germany/icon-global.md). Verified live 2026-08-05, identical across all four published cycles:

| Level type | Parameters |
|---|---|
| Single-level (19) | `asob_s`, `aswdir_s`, `athb_s`, `clct`, `ps`, `relhum_2m`, `sobs_rad`, `t_2m`, `td_2m`, `thbs_rad`, `tot_prec`, `u_10m`, `v_10m`, `vmax_10m` |
| Model-level (1) | `tke` — levels 99–104 only |
| Time-invariant (7) | `clat`, `clon`, `elat`, `elon`, `fr_land`, `hhl`, `hsurf` |

**There are no pressure-level fields, no 3D temperature, wind, or humidity, and no `pmsl`.** Users needing upper-air ensemble fields from ICON-EPS will not find them here; the public feed is oriented toward surface and near-surface probabilistic products. This is a materially narrower parameter set than the entry's earlier description implied.

Time-integration conventions, verified by decoding `stepType`/`stepRange`:

| Convention | `stepType` | Examples |
|---|---|---|
| Accumulated from run start | `accum` | `tot_prec` (`0-12` at +12 h) |
| Averaged from run start | `avg` | `asob_s`, `aswdir_s`, `athb_s` |
| Max over the preceding output interval | `max` | `vmax_10m` (`11-12` at +12 h) |
| Instantaneous | `instant` | `t_2m`, `td_2m`, `relhum_2m`, `clct`, `ps`, `sobs_rad`, `thbs_rad`, `tke` |

Note that `sobs_rad` and `thbs_rad` decode as **instantaneous** net radiation fluxes (`snswrf`, W m⁻²), while `asob_s` and `athb_s` are the run-start averages of the same quantities. Both families are published; they are not duplicates.

Probability and percentile products are **not** distributed — the public feed carries raw member fields only. Ensemble mean, spread, and exceedance probabilities must be computed by the user from the 40 members.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, bzip2-wrapped (`.grib2.bz2`)
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/icon-eps/grib/
  - Layout: `/<cycle>/<parameter>/icon-eps_global_icosahedral_<leveltype>_<YYYYMMDDHH>_<step>[_<level>]_<param>.grib2.bz2`
- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 — pipe-delimited `path|bytes|mtime` for the whole `/weather/nwp/` tree; far cheaper than recursive crawling.
- **Retention:** each cycle directory holds exactly one run. Verified 2026-08-05: `00/`, `06/`, `12/` each contained only `2026080500`, `2026080506`, `2026080512`. Effective retention ~24 h per cycle.
- **Volume:** ~37.2 GB per 00/12 UTC run (1,087 files) and ~16–18 GB per 06/18 UTC run (521–595 files). Roughly a ninth of the deterministic ICON Global feed by volume, and two orders of magnitude fewer files, because all 40 members ride in a single file per parameter-step.
- **Publication latency:** first files ~2 h 41 min after cycle time; run completes ~3 h 16–19 min after cycle time. Measured 2026-08-05: 00 UTC run 02:41:16 → 03:16:21 UTC; 06 UTC 08:41:17 → 09:13:12; 12 UTC 14:41:39 → 15:19:15. ICON-EPS therefore lands about half an hour ahead of deterministic ICON Global, which finishes near T+3h45.

### Member packaging (verified)
- **Packaging:** all 40 members for a given parameter and step are concatenated into a **single GRIB2 file**, one message per member. There is no per-member file split and no member token in the filename.
- **Member indexing:** `perturbationNumber` runs **1 to 40**. There is no member 0 and no separate control forecast.
- **GRIB2 encoding:** `productDefinitionTemplateNumber = 1` for instantaneous fields and `11` for time-processed fields (accumulations, averages, maxima); `typeOfEnsembleForecast = 192` (DWD local code); `numberOfForecastsInEnsemble = 40`; `typeOfProcessedData = cp`; `centre = edzw`; `tablesVersion = 19`, `localTablesVersion = 1`; `missingValue = 9999`.
- **Packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`.

### The `.bz2` wrapper no longer compresses
Since DWD switched all ICON-family GRIB2 output to CCSDS packing on **16 June 2026**, the bzip2 wrapper adds almost nothing and is sometimes counterproductive. Measured on the 2026-08-05 00 UTC run:

| File | `.bz2` bytes | decompressed bytes | ratio |
|---|---|---|---|
| `icon-eps … 012_t_2m` | 35,878,416 | 35,714,504 | **1.005** |
| `icon-eps … 012_tot_prec` | 22,243,458 | 22,754,805 | 0.978 |
| `icon-eps … 012_clct` | 42,107,200 | 47,873,172 | 0.880 |
| `icon … 012_T_2M` (deterministic) | 3,356,687 | 3,341,444 | **1.005** |

For smooth fields such as `clct` bzip2 still recovers ~12%; for others the wrapped file is marginally **larger** than its own contents. Budget for transfer volume on the compressed size, and don't assume decompression will shrink anything.

### No regular lat–lon output
Every file under `/weather/nwp/icon-eps/grib/` matches `icon-eps_global_icosahedral_*` — a scan of all 3,290 manifest entries for this tree returned **zero** non-icosahedral filenames.

> **Correction to earlier versions of this entry.** This entry previously stated that "regular lat-lon grids [are] also distributed for many element packages". That is not the case for ICON-EPS on Open Data. Users needing lat–lon must interpolate themselves; DWD ships CDO grid description and weight files for that purpose:
> - https://opendata.dwd.de/weather/lib/cdo/
>   - `icon_grid_0036_R03B06_G.nc.bz2` — the R3B06 grid description matching `numberOfGridUsed = 36`
>   - `ICON_GLOBAL2WORLD_025_EASY.tar.bz2` / `ICON_GLOBAL2WORLD_0125_EASY.tar.bz2` — global 0.25° and 0.125° target grids (built for R3B07; check applicability before reuse on the coarser EPS mesh)

---

## Relationship to other models
- **[ICON Global](../../../nwp_models/global/germany/icon-global.md):** deterministic counterpart, sharing the dynamical core, vertical structure, and coupled European nest architecture. ICON-EPS runs on R3B06 (737,280 cells) against ICON Global's R3B07 (2,949,120 cells) — a factor of four. The two systems share the LETKF ensemble at the heart of DWD's data assimilation.
- **[ICON-EU-EPS](../../regional/de/icon-eu-eps.md):** the European-nest portion of ICON-EPS, distributed as a separate regional product. Not an independent regional ensemble — it is the regional subset of this same forecast integration.
- **[ICON-D2-EPS](../../regional/de/icon-d2-eps.md):** convection-permitting regional ensemble at 2.2 km, driven at the boundaries by ICON-EPS members 1–20.
- **[ICON-D2-RUC-EPS](../../regional/de/icon-d2-ruc-eps.md):** hourly-updating convection-permitting ensemble; like ICON-D2-EPS, it depends on ICON-EPS for boundary forcing.
- **[AICON-Global](../../../nwp_models/global/germany/aicon-global.md):** DWD's deterministic ML global model, trained on the ICON-DREAM reanalysis. No AI ensemble counterpart exists at DWD as of 2026-08-05.

ICON-EPS is part of the ICON framework, jointly developed with the Max Planck Institute for Meteorology, and has been open source under a permissive licence since January 2024 (see the [ICON entry](../../../nwp_models/global/germany/icon-global.md#open-source)).

---

## Notes
- **Public data is a subset in two dimensions.** Only four of the eight daily cycles are published, and only 22 of the model's parameters. The 03/09/15/21 UTC cycles exist to refresh regional-ensemble boundaries and are limited to +30 h; they do not appear on Open Data.
- **40 members is smaller than ECMWF's 51-member ENS or NCEP's 31-member GEFS**, but ICON-EPS partly compensates through 8× daily cycling, giving a fresh ensemble every 3 hours — more frequent than most operational global ensembles. Only the four main cycles produce extended-range forecasts.
- **No control member.** Products expecting a control/perturbed split (common when ingesting ECMWF ENS or GEFS) will need adapting: `perturbationNumber` starts at 1 and every member is perturbed.
- **`tke` is the only 3D field, and barely.** Six model levels (99–104, near-surface) for the first 36 hours. It is not a substitute for upper-air ensemble output.
- Output should be interpreted probabilistically rather than as a deterministic forecast.
- **DWD's English ensemble-prediction page is stale.** It still describes ICON-EPS as "approximately 40 km with a grid refinement to 20 km over Europe" — the pre-November-2022 configuration — and repeatedly references COSMO-D2-EPS, retired in February 2021. Its cycle structure (8× daily, +30 h at the intermediate cycles, 120 h otherwise, 180 h at 00/12) is consistent with observed behaviour and is used above, but the resolution figures on that page should not be trusted. Prefer the ICON Database Reference and the change notices.
- **Cross-entry inconsistency to resolve.** This entry's version history records the November 2022 nest upgrade to 13 km (R3B07) with 74 levels, while [`icon-eu-eps.md`](../../regional/de/icon-eu-eps.md) still states ~20 km (R2B07) and 60 levels. Live decoding settles it in favour of this entry: an ICON-EU-EPS `t_2m` message from the 2026-08-05 00 UTC run reports `numberOfDataPoints = 164984` and `numberOfGridUsed = 37`, matching `icon_grid_0037_R03B07_N02.nc.bz2` — R3B07, i.e. 13 km. The ICON-EU-EPS entry needs updating.

---

## Recent version history

Since January 2024, ICON-EPS changes have generally been announced through the main ICON change notices rather than a separate ICON-EPS series — the notices state that changes apply to all global ICON configurations including the EU nests and the ensemble systems. The ensemble-relevant items are listed here; see the [ICON Global entry](../../../nwp_models/global/germany/icon-global.md#recent-version-history) for the full sequence.

### 30 June 2026 — model version `icon-2025.10-dwd-2.2` (effective 12 UTC run)
The most ensemble-specific upgrade in the recent series:
- **Revised SST ensemble perturbations** — replaces globally uniform ~1.05 K stochastic spread with a 0.5 K uniform background plus an IFS-derived spatial amplitude pattern (2017–2025). Substantially reduced tropical spread; larger spread along the Gulf Stream edge.
- **Retuned ensemble physics perturbations** (convection scheme, SSO low-level blocking) to partly compensate for the reduced spread.
- Reduced humidity relaxation in the lower stratosphere; assimilation of MWR radiances from the AWS satellite.

### 16 June 2026 — CCSDS compression (effective 09 UTC run)
All ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS GRIB2 output switched to lossless CCSDS packing, 40–50% smaller than the previous encoding. **Most likely change to break older downstream tooling** — a GRIB library without libaec/CCSDS support cannot decode these files. Also the point at which the `.bz2` wrapper stopped providing meaningful compression.

### 18 March 2026 — DACE 2.26, EnVar weighting bugfix (effective 00 UTC assimilation / 06 UTC forecast)
Correction of a coding error in the weighting between climatological and ensemble-derived background error covariances in the global hybrid EnVar. **Reduced ICON-EPS ensemble spread as a side effect** — relevant when comparing spread statistics across this date.

### 24 January 2024 — sea-ice bottom heat flux, model version `icon-2.6.6-nwp2` (effective 12 UTC run)
Joint ICON / ICON-EPS notice:
- Sea-ice scheme (Mironov et al. 2012) revised to account for heat flux from the underlying ocean toward the ice, previously neglected. Significantly reduces ICON's Arctic cold bias in winter and, to a lesser extent, the Antarctic coastal cold bias.
- External parameter data bugfix for false glacier points (soiltype vs. land-cover consistency check now defers to land cover; MODIS albedo adjustment corrected).
- Adaptive time-step reduction extended to horizontal CFL exceedances.

### 23 November 2022 — major resolution upgrade (current configuration)
The most significant ICON-EPS upgrade since operational launch:
- **Global horizontal resolution increased** from 40 km to 26 km (R2B06 → R3B06)
- **European nest horizontal resolution increased** from 20 km to 13 km (R2B07 → R3B07; the ICON-EU-EPS portion)
- **Vertical levels increased** from 90 to 120 globally; from 60 to 74 in the European nest
- New high-resolution global orography (MERIT, REMA, GLOBE); adaptive surface friction adjustment
- The horizontal upgrade applied to the EPS only — deterministic ICON remained at 13 km / 6.5 km — bringing ICON-EPS into a resolution range where its skill characteristics align more closely with the deterministic system

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
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD CDO grid description and weight files: https://opendata.dwd.de/weather/lib/cdo/

### Key references
- Reinert, D., Prill, F., Frank, H., Denhard, M., Baldauf, M., Schraff, C., Gebhardt, C., Marsigli, C., and Zängl, G. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378
- Mironov, D., et al. (2012). *Parameterisation of sea and lake ice in numerical weather prediction models of the German Weather Service.* Tellus A, 64, 17330.

---

*Live verification performed 2026-08-05 against `https://opendata.dwd.de/weather/nwp/icon-eps/grib/` (00, 06, 12, 18 UTC cycles of 2026-08-05) and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.48.0.*
