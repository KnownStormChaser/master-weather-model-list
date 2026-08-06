# ICON-EU-EPS (ICON European Regional Ensemble Prediction System)

## What this model is
ICON-EU-EPS is DWD's operational regional ensemble prediction system for Europe. It is the European-nest portion of [ICON-EPS](../../global/de/icon-eps.md) (DWD's global ensemble) — the 40 ensemble members are produced by the two-way-nested European subdomain embedded in the global ICON ensemble, and then distributed separately as a regional ensemble product.

ICON-EU-EPS is the probabilistic counterpart of DWD's deterministic [ICON-EU](../../../nwp_models/regional/germany/icon-eu.md) model. It provides 5-day probabilistic forecasts over the European domain at approximately 13 km horizontal resolution.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany / Europe

---

## What area it covers
- **Coverage:** European nest of the global ICON-EPS
- **Measured domain extent:** **24.86°W – 63.73°E, 28.86°N – 71.15°N** — decoded from the `clat`/`clon` time-invariant cell-centre fields of the 2026-08-05 00 UTC run. These are the actual mesh bounds of the distributed data.
- **Topography range:** −346.54 m to 3,401.59 m (`HHL` half-level 75, i.e. the surface)

> **Correction to earlier versions of this entry.** This entry previously gave the domain as 23.5°W – 62.5°E, 29.5°N – 70.5°N and stated that the Open Data distribution was "restricted to 23.5°W to 45.0°E". Neither holds: the published mesh extends to 63.73°E, and there is no eastern truncation on the Open Data server. The measured bounds above are used instead.

---

## Basic details
- **Model type:** Regional ensemble prediction system (regional nest of a global ensemble)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) — same core as the deterministic ICON-EU, and the same integration as the global ICON-EPS
- **Dynamical formulation:** Non-hydrostatic, triangular icosahedral horizontal grid
- **Convection-allowing:** No (deep convection parameterized at ~13 km)
- **Ensemble size:** **40 members, numbered 1–40, inherited from ICON-EPS. There is no separate unperturbed control run.**
- **Native horizontal grid:** R3B07 nest grid, **164,984 cells**, ~13 km effective mesh
  - Verified from live GRIB2 headers: `gridType = unstructured_grid`, `numberOfDataPoints = 164984`, `numberOfGridUsed = 37`, `uuidOfHGrid = ae487d28fe2e11e4af85e50a2a56a360`. The matching grid definition file is `icon_grid_0037_R03B07_N02.nc.bz2` under the CDO library path below (`N02` = nest 2).
- **Public output grid:** Native triangular grid only. **No regular latitude–longitude output is distributed** — see *Data availability*.
- **Vertical levels:** **74 full levels** (75 `HHL` half-levels)
- **Model top:** **22,770.33 m** — `HHL` half-level 1, spatially constant. Half-level 2 is also flat at 22,096.57 m; both carry `bitsPerValue = 0`.
- **Forecast length:** 120 hours (5 days) for **all four published cycles**
- **Update frequency / cycles:** 4× daily on Open Data (00, 06, 12, 18 UTC). The parent ICON-EPS cycles 8× daily; the intermediate cycles are not published — see Notes.
- **Temporal output resolution:** two schedules within the same run — see below

> **Correction to earlier versions of this entry.** This entry previously stated ~20 km (R2B07) and 60 vertical levels. Both figures predate DWD's 23 November 2022 upgrade, which took the European nest from R2B07/20 km to R3B07/13 km and from 60 to 74 levels. The live headers above confirm the current configuration; the [ICON-EPS entry](../../global/de/icon-eps.md#recent-version-history) already recorded the upgrade, so the two entries disagreed. This entry is now the one that matches the data.

### Output step structure
Two distinct step schedules coexist in every run. Verified from the 2026-08-05 00 UTC manifest slice:

| Field group | Steps | Cadence |
|---|---|---|
| Single-level and pressure-level | 65 | hourly 0–48 h; 3-hourly 48–72 h; 6-hourly 72–120 h |
| Model-level (`t`, `u`, `v`, `qv` on levels 72–74) | 68 | hourly 0–51 h; 3-hourly 51–78 h; 6-hourly 78–120 h |

The model-level fields carry three steps the rest of the run does not: **049, 050, and 075**. Code that assumes a single step list across an ICON-EU-EPS run will either miss those files or fail to find matching single-level fields at those times.

`vmax_10m` has 64 steps rather than 65 — no step 000, as expected for an interval maximum.

This is also unlike the parent [ICON-EPS](../../global/de/icon-eps.md), whose 06 and 18 UTC cycles are 6-hourly throughout. ICON-EU-EPS runs the same dense schedule in all four cycles.

---

## Data assimilation
- **Data assimilation:** Yes — but not its own.
- **Method / cadence:** ICON-EU-EPS inherits initial conditions from the global 40-member **LETKF** ensemble data assimilation that initializes [ICON-EPS](../../global/de/icon-eps.md#data-assimilation), cycled every 3 hours at 40 km globally with a 20 km European nest.

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

The Open Data parameter set is **richer than the global ICON-EPS feed** — 28 parameter directories at 00/12 UTC and 26 at 06/18 UTC, against 22 for the global product. Verified live 2026-08-05:

| Level type | Parameters |
|---|---|
| Single-level (17) | `aswdifd_s`, `aswdir_s`, `athb_s`, `cape_ml`, `clct`, `ps`, `snow_con`, `snow_gsp`, `sobs_rad`, `t_2m`, `thbs_rad`, `tot_prec`, `tqv`†, `u_10m`, `v_10m`, `vmax_10m` |
| Pressure-level (4) | `fi`†, `t`, `u`, `v` — at **300, 500, 850 hPa** only |
| Model-level (4) | `t`, `u`, `v`, `qv` — at **levels 72, 73, 74** only (the lowest three of 74) |
| Time-invariant (7) | `clat`, `clon`, `elat`, `elon`, `fr_land`, `hhl`, `hsurf` |

† **`fi` and `tqv` are published only in the 00 and 12 UTC cycles.** They are absent from 06 and 18 UTC. This is the reason for the 28-vs-26 directory count and is easy to miss when building a cycle-agnostic ingest.

(`t`, `u`, `v` appear under both pressure-level and model-level, so 17 + 4 + 4 + 7 = 32 minus 3 duplicates + the two cycle-dependent fields resolves to 28 directories at 00/12.)

Compared with the global [ICON-EPS](../../global/de/icon-eps.md), this feed adds genuine upper-air content — geopotential, temperature, and wind on three pressure levels — plus CAPE, convective and grid-scale snow, total column water vapour, and diffuse shortwave radiation. It drops `td_2m`, `relhum_2m`, and `tke`. The upper-air coverage is still thin: three pressure levels and three near-surface model levels.

Time-integration conventions, verified by decoding `stepType`/`stepRange`:

| Convention | `stepType` | PDT | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | 11 | `tot_prec`, `snow_con`, `snow_gsp` |
| Averaged from run start | `avg` | 11 | `aswdir_s`, `aswdifd_s`, `athb_s` |
| Max over the preceding output interval | `max` | 11 | `vmax_10m` (`11-12` at +12 h) |
| Instantaneous | `instant` | 1 | `t_2m`, `ps`, `clct`, `cape_ml`, `tqv`, `sobs_rad`, `thbs_rad`, and all pressure- and model-level fields |

As in the global product, `sobs_rad` and `thbs_rad` decode as **instantaneous** net radiation fluxes (`snswrf`, W m⁻²) while the `a*` family are run-start averages of the same quantities. Both are published; they are not duplicates.

Probability, percentile, mean, and spread products are **not** distributed — the public feed carries raw member fields only. All ensemble statistics must be computed by the user from the 40 members.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, bzip2-wrapped (`.grib2.bz2`)
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/icon-eu-eps/grib/
  - Layout: `/<cycle>/<parameter>/icon-eu-eps_europe_icosahedral_<leveltype>_<YYYYMMDDHH>_<step>[_<level>]_<param>.grib2.bz2`
- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 — pipe-delimited `path|bytes|mtime` for the whole `/weather/nwp/` tree; far cheaper than recursive crawling.
- **Retention:** each cycle directory holds exactly one run — the current run at that hour. Effective retention ~24 h per cycle.
- **Volume:** ~22.1 GB per 00/12 UTC run (2,618 files) and ~14.6–15.1 GB per 06/18 UTC run (1,640–1,700 files).
- **Publication latency:** first files ~2 h 41 min after cycle time; run completes ~2 h 69–3 h 13 min after cycle time. Measured 2026-08-05: 00 UTC run 02:41:16 → 03:09:45 UTC; 06 UTC 08:41:17 → 09:13:17; 12 UTC 14:41:39 → 15:09:27. ICON-EU-EPS starts uploading at the same moment as the global ICON-EPS and finishes a few minutes earlier.

### Member packaging (verified)
- **Packaging:** all 40 members for a given parameter, level, and step are concatenated into a **single GRIB2 file**, one message per member. No per-member file split, no member token in the filename.
- **Member indexing:** `perturbationNumber` runs **1 to 40**. There is no member 0 and no separate control forecast.
- **GRIB2 encoding:** `productDefinitionTemplateNumber = 1` for instantaneous fields and `11` for time-processed fields; `numberOfForecastsInEnsemble = 40`; `centre = edzw`; `tablesVersion = 19`, `localTablesVersion = 1`; `missingValue = 9999`.
- **Packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library built without libaec/CCSDS support cannot decode these files.

### The `.bz2` wrapper no longer compresses
Since DWD switched all ICON-family GRIB2 output to CCSDS packing on **16 June 2026**, the bzip2 wrapper adds essentially nothing. Measured on the 2026-08-05 00 UTC run:

| File | `.bz2` bytes | decompressed bytes | ratio |
|---|---|---|---|
| `… 012_t_2m` | 9,260,768 | 9,222,518 | **1.004** |
| `… 012_aswdir_s` | 9,360,220 | 9,320,497 | **1.004** |

Both wrapped files are marginally **larger** than their own contents. Budget transfer volume on the compressed size and do not assume decompression will shrink anything. The same holds across the ICON family — see [ICON-EPS](../../global/de/icon-eps.md#the-bz2-wrapper-no-longer-compresses) for a wider sample.

### No regular lat–lon output
Every file under `/weather/nwp/icon-eu-eps/grib/` matches `icon-eu-eps_europe_icosahedral_*` — a scan of all 8,576 manifest entries for this tree returned **zero** non-icosahedral filenames.

> **Correction to earlier versions of this entry.** This entry previously stated that "the DWD Open Data Server also provides pre-interpolated regular lat-lon grids for many element packages". It does not, for this product. Users needing lat–lon must interpolate themselves; DWD ships CDO grid description and weight files for that purpose:
> - https://opendata.dwd.de/weather/lib/cdo/
>   - `icon_grid_0037_R03B07_N02.nc.bz2` — the nest grid description matching `numberOfGridUsed = 37`
>   - `ICON_GLOBAL2EUAU_025_EASY.tar.bz2` / `ICON_GLOBAL2EUAU_0125_EASY.tar.bz2` — Europe-domain target grids at 0.25° and 0.125° (built against the global R3B07 mesh; verify applicability before reuse on the nest grid)

---

## Relationship to other models
- **[ICON-EPS](../../global/de/icon-eps.md):** the parent global ensemble. ICON-EU-EPS is not an independent system — it is the nest portion of the same 40-member integration, sharing initial conditions, physics perturbations, member numbering, and cycle schedule. The global product runs on R3B06 (737,280 cells, grid number 36); this nest runs on R3B07 (164,984 cells, grid number 37).
- **[ICON-EU](../../../nwp_models/regional/germany/icon-eu.md):** deterministic counterpart, sharing the model core, physics, and nest architecture. The deterministic nest runs at 6.5 km against this ensemble's 13 km — the ensemble is *coarser* than its deterministic sibling, the opposite of the global pairing where ICON-EPS at 26 km is coarser than ICON Global at 13 km but both nests differ.
- **[ICON Global](../../../nwp_models/global/germany/icon-global.md):** the deterministic global model whose European nest is ICON-EU.
- **[ICON-D2-EPS](./icon-d2-eps.md):** DWD's convection-permitting regional ensemble at 2.2 km, historically driven by ICON-EPS members 1–20. For convection-allowing probabilistic guidance over central Europe, that is the relevant product.
- **[ICON-D2-RUC-EPS](./icon-d2-ruc-eps.md):** hourly-updating convection-permitting ensemble, typically taking boundaries from ICON-EU-EPS members.

---

## Notes
- **Public data is a subset of the cycles.** The parent ICON-EPS cycles 8× daily, but only 00, 06, 12, and 18 UTC are published. The 03/09/15/21 UTC cycles are limited to +30 h and exist mainly to refresh boundaries for the convection-permitting ensembles.
- **Not an independent regional ensemble.** ICON-EU-EPS shares initial conditions, physics perturbations, and member identity with the global ICON-EPS. Combining the two products into a "multi-model" or "multi-resolution" ensemble is not statistically meaningful — they are the same 40 trajectories at two grid spacings.
- **No control member.** Products expecting a control/perturbed split (common when ingesting ECMWF ENS or GEFS) need adapting: `perturbationNumber` starts at 1 and every member is perturbed.
- **The ensemble nest is coarser than the deterministic nest.** ICON-EU-EPS runs at 13 km with 74 levels; deterministic ICON-EU runs at 6.5 km. Users pairing deterministic and probabilistic ICON-EU guidance are working at two different resolutions with different vertical structures.
- **Upper-air coverage is thin but real.** Three pressure levels (300/500/850 hPa) and the lowest three model levels. Enough for synoptic-scale probabilistic diagnostics; not enough for profile-based applications.
- **`fi` and `tqv` are cycle-dependent** — present at 00/12 UTC, absent at 06/18 UTC.
- **Two step schedules per run.** Model-level fields carry steps 049, 050, and 075 that no other field group has.
- **DWD's English ensemble-prediction page is stale.** It still describes the ICON ensemble as "approximately 40 km ... with a grid refinement to 20 km over Europe" — the pre-November-2022 configuration — and repeatedly references COSMO-D2-EPS, retired in February 2021. Its resolution figures should not be trusted; prefer the ICON Database Reference and the change notices.

---

## Recent version history

ICON-EU-EPS has no independent change-notice series. It is upgraded as part of the global ICON/ICON-EPS system, and DWD's notices state that changes apply to all global ICON configurations including the EU nests and ensemble systems. The nest-relevant items are listed here; see the [ICON-EPS](../../global/de/icon-eps.md#recent-version-history) and [ICON Global](../../../nwp_models/global/germany/icon-global.md#recent-version-history) entries for the full sequence.

### 30 June 2026 — model version `icon-2025.10-dwd-2.2` (effective 12 UTC run)
Revised SST ensemble perturbations (0.5 K uniform background plus IFS-derived spatial pattern, replacing globally uniform ~1.05 K); retuned ensemble physics perturbations for the convection scheme and SSO low-level blocking; reduced humidity relaxation in the lower stratosphere; assimilation of MWR radiances from the AWS satellite. Applies to all global ICON configurations including the EU nests.

### 16 June 2026 — CCSDS compression (effective 09 UTC run)
All ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS GRIB2 output switched to lossless CCSDS packing, 40–50% smaller than the previous encoding. **Most likely change to break older downstream tooling**, and the point at which the `.bz2` wrapper stopped providing meaningful compression.

### 18 March 2026 — DACE 2.26, EnVar weighting bugfix (effective 00 UTC assimilation / 06 UTC forecast)
Correction of a coding error in the weighting between climatological and ensemble-derived background error covariances in the global hybrid EnVar. **Reduced ensemble spread as a side effect** — relevant when comparing spread statistics across this date.

### 24 January 2024 — sea-ice bottom heat flux, model version `icon-2.6.6-nwp2` (effective 12 UTC run)
Sea-ice scheme revised to account for ocean-to-ice heat flux; external parameter bugfix for false glacier points; adaptive time-step reduction extended to horizontal CFL exceedances.

### 23 November 2022 — resolution upgrade (current configuration)
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
- DWD-Geoportal ICON entry: https://dwd-geoportal.de/products/G_EJM/
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD CDO grid description and weight files: https://opendata.dwd.de/weather/lib/cdo/

### Key references
- Reinert, D., Prill, F., Frank, H., Denhard, M., Baldauf, M., Schraff, C., Gebhardt, C., Marsigli, C., and Zängl, G. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378

---

*Live verification performed 2026-08-05 against `https://opendata.dwd.de/weather/nwp/icon-eu-eps/grib/` (00, 06, 12, 18 UTC cycles of 2026-08-05) and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.48.0.*
