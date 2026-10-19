# ICON (Icosahedral Nonhydrostatic) — Global

## What this model is
ICON is the operational global deterministic numerical weather prediction model run by the German Weather Service (Deutscher Wetterdienst, DWD).

It uses a non-hydrostatic dynamical core on a triangular icosahedral grid, with a tightly coupled two-way nest over Europe (ICON-EU) running at higher resolution within the same forecast integration. ICON predicts atmospheric conditions worldwide — temperature, wind, precipitation, pressure, humidity, clouds, and hydrometeor fields — out to 7.5 days for the main cycles.

ICON replaced DWD's previous global model (GME) on 20 January 2015. The model code is jointly developed by DWD and the Max Planck Institute for Meteorology (MPI-M), and has been open source under a permissive licence since January 2024.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD)
- **Country / region:** Germany
- **Code development:** Jointly by DWD, the Max Planck Institute for Meteorology (MPI-M), the German Climate Computing Centre (DKRZ), MeteoSwiss, and the Karlsruhe Institute of Technology (KIT)

---

## What area it covers
- **Coverage:** Global
- **Regional nest:** A two-way coupled nest covers Europe at ~6.5 km horizontal resolution; this is documented separately as [ICON-EU](../../regional/germany/icon-eu.md). Nest boundary conditions and feedbacks are exchanged every model time step. The nest is *not* present in the global Open Data tree — it is distributed under its own path.

---

## Basic details
- **Model type:** Global deterministic NWP
- **Model system / core:** ICON (Icosahedral Nonhydrostatic)
- **Model version:** `icon-2025.10-dwd-2.2` (since 30 June 2026)
- **Dynamical formulation:** Non-hydrostatic, on a triangular (icosahedral) horizontal grid
- **Convection-allowing:** No (deep convection is parameterized at ~13 km resolution)
- **Native horizontal grid:** R3B07 triangular grid, **2,949,120 cells**, mean cell area 173 km², ~13 km effective mesh size
  - Confirmed from live GRIB2 headers (v1 feed, 2026-10-18 12 UTC run): `gridType = unstructured_grid`, `numberOfDataPoints = 2949120`, `numberOfGridUsed = 26`, `uuidOfHGrid = a27b8de618c411e4820ab5b098c6a5c0`. The matching grid definition file is `icon_grid_0026_R03B07_G.nc` (see *Grid files and regridding*).
- **Public output grid:** Native triangular grid only (GRIB2). **No regular latitude–longitude output is distributed for global ICON** — see *Data availability*.
- **Vertical levels:** 120 (since November 2022; 90 levels prior), plus 121 half-level heights distributed as `HHL`
- **Vertical coordinate:** Smooth-Level Vertical (SLEVE) terrain-following hybrid coordinate. Levels are terrain-following near the surface, transitioning to flat (purely height-based) levels aloft.
  - Verified from the `HHL` time-invariant files: half-levels 1–59 are spatially constant (level 59 = 16,039.91 m everywhere; these messages carry `bitsPerValue = 0` and compress to ~210 bytes). Half-level 60 is the first with any terrain signal (15,636.71–15,669.48 m). The flat/terrain transition is therefore at **~16.0 km**, not an approximation.
- **Model top:** 75 km exactly (`HHL` half-level 1 = 75,000.0 m at every cell)
- **Forecast length:**
  - 180 hours (7.5 days) for 00 and 12 UTC cycles
  - 120 hours (5 days) for 06 and 18 UTC cycles
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC). Unlike [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md), the deterministic global run has no intermediate 3-hourly cycles on Open Data.
- **Temporal output resolution:** Hourly to +78 h, then 3-hourly to +180 h
  - 113 output steps for 00/12 UTC (79 hourly + 34 three-hourly); 93 steps for 06/18 UTC (79 + 14). Identical in the old and new download trees — verified 2026-10-18 for `U_10M`, `ASWDIR_S`, `T_2M` (v1) against `u_10m` (legacy tree).
  - **One exception in the v1 feed: `VMAX_10M` is hourly to +180 h** (180 files, steps 001–180). See *Time-integration conventions*. Every other forecast field — single-level, pressure-level, model-level and soil — follows the 113/93 pattern, except `TKE` (to +48 h only) and `SMI` (analysis only). Verified 2026-10-18 for all 69 single-level forecast fields and one level of each multi-level field.

---

## Data assimilation
ICON uses a **hybrid LETKF + EnVar** data assimilation system, distinctive among national centres in combining ensemble Kalman filtering with a variational analysis driven by ensemble-derived covariances:

- **Ensemble component:** A 40-member Local Ensemble Transform Kalman Filter (LETKF) ensemble runs at 40 km horizontal resolution globally, with a 20 km nest over Europe. This is the same ensemble used to produce [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md).
- **Deterministic analysis:** An Ensemble Variational (EnVar) scheme in which the background error covariance is a weighted blend of a climatological estimate and a flow-dependent estimate derived from the 40-member ensemble — `B = (1−a)·B_clim + a·B_ens`, with `a` the mixing weight. DWD's 18 March 2026 change notice states this formulation explicitly. The EnVar produces the analysis that initializes the deterministic 13 km global / 6.5 km European nest forecasts.
- **Assimilation code:** DACE (Data Assimilation Coding Environment), version 2.28 since 22 July 2026
- **Cycle structure:** A 3-hour assimilation window centred on the analysis time is used, with the previous 3-hour forecast as the first guess.

The hybrid LETKF + EnVar system has been operational at DWD since January 2015 and provides initial conditions for both the deterministic ICON forecasts and ICON-EPS.

> **Documentation discrepancy.** DWD's public English model-description page still describes the global analysis as "a 3D variational assimilation method" valid for a three-hour window. The operational system has been hybrid EnVar since 2015, and the March 2026 change notice describes it as such. Prefer the change notices and the Database Reference over the model-description page on this point.

---

## What it provides
The v1 Open Data tree carries **104 parameter directories**, identical across all four cycles — the 102 of the legacy tree plus two new ones, `DEN` and `SMI`. Verified live 2026-10-18 by directory-listing diff against `/weather/nwp/icon/grib/00/`.

| Level type (v1 `lvt1`) | Parameters | Levels |
|---|---|---|
| Single-level (no `lvt1`) | 69 forecast fields + 17 time-invariant | surface / 2 m / 10 m / column-integrated |
| Model level (`150`) | 12: `T`, `U`, `V`, `W`, `P`, `QV`, `QC`, `QI`, `CLC`, `TKE`, `DEN`, `HHL` | see below |
| Pressure level (`100`) | 5: `FI`, `T`, `U`, `V`, `RELHUM` | 18 |
| Soil level (`106`) | 4: `T_SO`, `W_SO`, `W_SO_ICE`, `SMI` | 9 / 8 |

- **Pressure levels (18):** 30, 50, 70, 100, 150, 200, 250, 300, 400, 500, 600, 700, 800, 850, 900, 925, 950, 1000 hPa (v1 paths give these in **Pa**, e.g. `lv1/85000`)
- **Model levels — not uniform across parameters:**
  - `T`, `U`, `V`, `P`, `QV`, `QC`, `QI` — full levels 1–120
  - `W`, `HHL` — half-levels 1–121
  - `CLC`, `DEN` — levels 39–120 only (82 levels)
  - `TKE` — levels 61–121 only (61 levels), and **only to +48 h** (49 hourly steps). Same in the legacy tree.
- **Soil levels — `T_SO` (9):** 0, 0.005, 0.02, 0.06, 0.18, 0.54, 1.62, 4.86, 14.58 m (layer boundaries)
- **Soil levels — `W_SO` / `W_SO_ICE` / `SMI` (8):** 0, 0.01, 0.03, 0.09, 0.27, 0.81, 2.43, 7.29 m (layer mid-depths)

### New in the v1 feed (announced 2 September 2026)
- **`DEN` — density of moist air** (`paramId = 3089`, kg m⁻³), on model levels 39–120, full 113/93-step forecast range.
- **`SMI` — soil moisture index** (`paramId = 503079`, dimensionless) on the 8 soil layers. **Analysis only: one file per run, step `PT000H00M`.** This differs from [ICON-D2](../../regional/germany/icon-d2.md), where `SMI` runs the full 49-step forecast.

Fields include temperature, wind, humidity, pressure, precipitation (total, convective, grid-scale, rain/snow split), cloud cover and hydrometeors, radiation and surface flux components, lake (FLake) variables, sea-ice, snow, soil state, CAPE, weather interpretation (`WW`), and gust maxima.

ICON output also serves as boundary forcing for downstream regional configurations including several non-DWD ICON-LAM applications.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Data formats:** GRIB2, one message per file, **plain `.grib2`** (no bzip2 wrapper)
- **GRIB2 packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`, `missingValue = 9999`, no bitmap
  - Since **16 June 2026** all ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS forecast data are CCSDS-compressed. ecCodes, CDO, and wgrib2 handle this transparently, but a GRIB library built **without libaec CCSDS support will fail to decode these files**.
- **GRIB2 tables:** `tablesVersion = 19`, `localTablesVersion = 1`, `centre = edzw` (Offenbach), `typeOfProcessedData = fc`, `generatingProcessIdentifier = 1`. DWD local table definitions are required to resolve local shortNames such as `T_SO`, `W_SO`, `HHL`, `ASWDIR_S`, `SMI`.
- **Official download location:** https://opendata.dwd.de/weather/nwp/v1/m/icon/

### Path structure (v1)
- **Single-level and time-invariant:**
  `/p/{PARAM}/r/{YYYY-MM-DD}T{HH}%3A00/s/PT{hhh}H{mm}M.grib2`
- **Multi-level:**
  `/p/{PARAM}/lvt1/{100|106|150}/lv1/{level}/r/{run}/s/PT{hhh}H{mm}M.grib2`

`lvt1` is the GRIB2 `typeOfFirstFixedSurface` (150 = model level, 100 = isobaric in Pa, 106 = depth below land surface in m). The run token is ISO 8601 with the colon percent-encoded as `%3A`; the step token is an ISO 8601 duration. Example:

`https://opendata.dwd.de/weather/nwp/v1/m/icon/p/U_10M/r/2026-10-18T12%3A00/s/PT000H00M.grib2`

- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 — a pipe-delimited `path|bytes|mtime` listing of the entire `/weather/nwp/` tree, v1 included (~78 MB compressed on 2026-10-18). Far cheaper than recursive directory crawling for enumeration.
- **Retention:** each run has its own directory; ~24 h rolling window (four runs). Longer archives must be maintained by the user.
- **Volume (v1):** **~385 GB and 151,119 files per 00/12 UTC run**; **~317 GB and 124,899 files per 06/18 UTC run** — about 1.4 TB/day (measured from `content.log`, 2026-10-18). Roughly 7% more files than the legacy tree, almost all from the new `DEN` field. Full-tree mirroring is a serious bandwidth commitment; most users will want a parameter/level subset.
- **Publication latency (v1):** first files ~2 h 37–42 min after cycle time; run complete ~3 h 10–21 min after. Measured 2026-10-18: 00 UTC 02:37:23 → 03:19:12; 06 UTC 08:42:24 → 09:14:43; 12 UTC 14:40:23 → 15:21:33; 18 UTC 20:39:23 → 21:10:22 UTC. This is 15–25 minutes earlier than the legacy tree finished the same runs.

### Legacy tree — discontinued after 30 November 2026
DWD's 2 September 2026 Open Data newsletter announces that the old layout at https://opendata.dwd.de/weather/nwp/icon/grib/ (and the matching `icon-eps/` tree) stops after **30 November 2026**. Until then both trees carry the same runs. The legacy layout was:

`/<cycle>/<parameter>/icon_global_icosahedral_<leveltype>_<YYYYMMDDHH>_<step>[_<level>]_<PARAM>.grib2.bz2`

Differences that matter when migrating:
- **bzip2 wrapper dropped.** Since the June CCSDS switch the wrapper added almost nothing.
- **No `DEN`/`SMI`** in the legacy tree.
- **`VMAX_10M` is 3-hourly beyond +78 h** in the legacy tree (112 files), hourly in v1 (180 files).
- Legacy volume was ~348 GB / 141,777 files (00/12) and ~287 GB / 117,237 files (06/18) per run.

### Grid files and regridding
Global ICON is distributed on the **native triangular grid only**. DWD does not publish a regular lat–lon rendering; interpolation is user-side.

- **Grid definition files:** https://icon-downloads.mpimet.mpg.de/dwd_grids.xml (MPI-M). Global ICON uses `icon_grid_0026_R03B07_G.nc`.
- **Regridding guide:** https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid — DWD's cdo-based instructions, which generate the weights file as a by-product.
- **`/weather/lib/cdo/` is deleted after 30 November 2026.** It currently holds a few grid files (including `icon_grid_0026_R03B07_G.nc.bz2`) and prebuilt CDO weights (`ICON_GLOBAL2WORLD_025_EASY.tar.bz2`, `ICON_GLOBAL2WORLD_0125_EASY.tar.bz2`, `ICON_GLOBAL2EUAU_025_EASY.tar.bz2`, `ICON_GLOBAL2EUAU_0125_EASY.tar.bz2`). DWD has stopped maintaining the weights and asks users to take grid files from MPI-M only.

> **Correction to earlier versions of this entry.** This entry once listed "regular latitude–longitude at 0.25° and 0.125°" among the grids *available on Open Data*. Those are only the target grids of DWD's CDO weight files, not distributed products — and the weight files themselves are being withdrawn.

---

## Time-integration conventions
Verified by decoding `stepType`/`stepRange` from the 2026-10-18 12 UTC run (v1).

| Convention | `stepType` | Window | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | `0-<step>` | `TOT_PREC`, `RAIN_GSP`, `RAIN_CON`, `SNOW_GSP`, `RUNOFF_S` |
| Averaged from run start | `avg` | `0-<step>` | `ASOB_S`, `ASWDIR_S`, `ATHB_S`, `ALHFL_S`, `ASHFL_S` (the `A*` flux family) |
| Max/min since last 6 h boundary | `max` / `min` | `0-3`, `6-12`, `78-81` … | `TMAX_2M`, `TMIN_2M` |
| Max over the preceding hour | `max` | `11-12`, `83-84`, `84-85` | `VMAX_10M` |
| Instantaneous | `instant` | `<step>` | `T_2M`, `PMSL`, `CAPE_ML`, `WW`, model-level and pressure-level fields |

Step units are hours (`stepUnits = h`) for forecast fields; time-invariant fields decode as `stepRange = 0m` (minutes).

- **`VMAX_10M` in v1 is hourly all the way to +180 h** — files 001 through 180, each a 1-hour maximum (`83-84`, `84-85`, …). The legacy tree published it only at the 3-hourly steps beyond +78 h, so two hours in every three had no gust maximum at all. Peak-gust products built on the legacy tree under-reported beyond +78 h; the v1 feed closes that gap. DWD's 2 September memo does not mention this change.
- **`VMAX_10M` still has no step 000** (180 files, not 181). `TMAX_2M`/`TMIN_2M` *do* have a step-000 file, but with a degenerate zero-length window — not a valid value.

### Unit-string discrepancy in `RAIN_GSP`
ecCodes resolves `RAIN_GSP` to `paramId = 228219` (`lsrr`, large-scale rain rate) with declared units `kg m**-2 s**-1`, while the message itself is encoded as `stepType = accum` over `0-12`. Still present in the v1 feed (2026-10-18 12 UTC, +12 h: values 0–320.5). As a rate that would be 320 mm s⁻¹, so the accumulation reading is the right one.

**Treat the unit string as unreliable; `RAIN_GSP` values are accumulated kg m⁻² (mm) since run start.** The same caution applies to the other `*_GSP` / `*_CON` precipitation components.

---

## Open source
The ICON modelling system became open source in **January 2024** under a permissive licence (BSD-3-Clause-style), with the source code, documentation, and runtime tooling available via the joint DWD/MPI-M GitLab. This makes ICON one of the few major operational global NWP systems with publicly available source code, alongside the open-sourcing of components by ECMWF (Anemoi/AIFS, ecCodes) and others.

---

## Notes
- ICON Global and [ICON-EU](../../regional/germany/icon-eu.md) are not separate models — they are the global parent and European nest of a single tightly-coupled two-way nested integration. This is structurally different from regional models elsewhere (e.g., ARPEGE/AROME at Météo-France) where the limited-area model is run as a separate process driven by the global model's lateral boundary conditions.
- The November 2022 vertical resolution upgrade from 90 to 120 levels (with the model top remaining at 75 km) targeted improvements in the stratosphere; the upgrade also brought ICON-EPS from 40 km to 26 km horizontal resolution and increased ICON-EU vertical levels from 60 to 74.
- The ensemble counterpart is [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md). The European nest portion of ICON-EPS is distributed separately as [ICON-EU-EPS](../../../ensemble_models/regional/de/icon-eu-eps.md).
- DWD's regional convection-permitting model [ICON-D2](../../regional/germany/icon-d2.md) and its rapid-update variant [ICON-D2-RUC](../../regional/germany/icon-d2-ruc.md) are separate limited-area systems driven by ICON-EU boundaries.
- DWD's machine-learning global model **[AICON-Global](./aicon-global.md)** is trained on the ICON reanalysis (ICON-DREAM), runs on this same R3B07 grid, and is initialized from ICON analyses. It is distributed under `/weather/nwp/v1/m/aicon/` and is *not* part of the operational ICON suite.
- **With the 2 September 2026 migration, every DWD NWP system is distributed in the same v1 scheme** — ICON, ICON-EU, ICON-D2 and their ensembles join ICON-D2-RUC, AICON and the ICON-ART family under `/weather/nwp/v1/m/`.
- **`ICON-D05`** — a ~0.5 km deterministic configuration over Germany, introduced 27 February 2025 per the ICON Database Reference. It does not appear anywhere under `/weather/nwp/` (re-checked 2026-10-18, including `v1/m/`). Not catalogued here; worth re-checking for an Open Data release.

### File-listing hazards
- **Incomplete runs during upload.** A run directory appears as soon as its first files are written and fills over ~30–40 minutes. A listing taken mid-upload shows a partial run. Use the manifest's mtimes or wait for the last expected step before treating a run as complete.
  - The legacy tree had a worse version of this: run directories were not separate, so files from two initializations coexisted under one cycle folder for about an hour, distinguishable only by the `YYYYMMDDHH` token in the filename. The v1 layout puts the run in the path, which removes that ambiguity.
- **Soil depths are now plain metres in the path.** In the legacy tree, the soil-level filename token was `scaledValueOfFirstFixedSurface` with the scale factor dropped, so the 5 mm `T_SO` level (`5`, scale 3) sorted after the 2 cm level (`2`, scale 2). v1 paths use decimal metres (`lv1/0.005`, `lv1/0.02`), which sort correctly. Inside the GRIB header the scale factors still differ (3 for 0.005 m, 2 elsewhere), so decode depth as `scaledValue × 10^−scaleFactor`.
- **HHL levels 1–59 are constant fields** encoded with `bitsPerValue = 0` (~210 bytes compressed). Some readers and range-request-based tooling handle zero-bit-width GRIB2 messages poorly. These files are valid and decode correctly under ecCodes.

---

## Recent version history

> Model-change notices are covered through 5 August 2026. Entries dated after that are Open Data distribution changes only; DWD's ICON change notices since August have not yet been reviewed for this entry.

### 30 November 2026 (scheduled) — legacy Open Data tree and `lib/cdo` withdrawn
Per DWD's 2 September 2026 newsletter: delivery under `/weather/nwp/icon/` and `/weather/nwp/icon-eps/` stops after 30 November; `/weather/lib/cdo/` (grid files and CDO weights) is deleted on the same date. Grid files move to MPI-M exclusively.

### 2 September 2026 — v1 URL scheme for ICON, ICON-EU, ICON-D2 and their ensembles
All six systems published additionally under `/weather/nwp/v1/m/`: native grid only, plain CCSDS-packed GRIB2 without bzip2, one file per ensemble member and per sub-hourly step, ISO 8601 run and step tokens. Two new global ICON parameters, `DEN` (density of moist air) and `SMI` (soil moisture index). DWD states that parameter set, range and time resolution are otherwise unchanged; live checks on 2026-10-18 found one undocumented difference for global ICON — `VMAX_10M` is hourly to +180 h in v1.

### 5 August 2026 — solar eclipse parametrization (effective 12 UTC run, global ICON)
New parametrization reduces incoming solar radiation during solar eclipses, allowing feedback onto 2 m temperature, boundary-layer height, cloud cover, and 10 m wind. Based on Besselian elements (data from Espenak & Meeus, NASA); limb-darkening is not considered. Motivated primarily by photovoltaic production forecasting.

### 22 July 2026 — MTG/FCI radiances, DACE 2.28 (effective 09 UTC assimilation / 12 UTC forecast)
- **FCI on METEOSAT-12 (MTG-I1)** enters the global assimilation system, replacing SEVIRI on METEOSAT-10; SEVIRI is retained as automatic fallback. All-sky radiances, thinned to one observation per 120 km, emissivity from CAMEL-CLIM, skin temperature from FCI channel 6.
- **DACE updated to version 2.28** — includes a fix for radiosonde descent profiles being erroneously merged into ascent profiles when the balloon-burst location was within 0.1° of the station.
- Minor MWR quality-control tightening in satellite preprocessing.

### 30 June 2026 — model version `icon-2025.10-dwd-2.2` (effective 12 UTC run, all global configurations incl. EU nests and ensembles)
- **Revised SST ensemble perturbations:** replaces a globally uniform ~1.05 K stochastic spread with a 0.5 K uniform background plus a spatial amplitude pattern derived from IFS SST analyses 2017–2025. Net effect: substantially reduced tropical ensemble spread, comparable spread in marginal seas, larger spread along the Gulf Stream edge.
- **Retuned ensemble physics perturbations** (convection scheme, SSO low-level blocking) to partly compensate.
- **Reduced humidity relaxation in the lower stratosphere.**
- **Assimilation of MWR radiances from the AWS satellite.**

### 16 June 2026 — CCSDS compression of all forecast data (effective 09 UTC run)
All ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS GRIB2 output switched to lossless CCSDS packing; 40–50% size reduction, no change to meteorological content. ICON-D2-RUC and ICON-D05 were already CCSDS-compressed. **This is the change most likely to break older downstream tooling.**

### 28 April 2026 — MODE-S aircraft observations (effective 09 UTC assimilation)
High-density Mode-S EHS aircraft data over Europe, plus globally available MODE-S, added to the global assimilation system.

### 18 March 2026 — DACE 2.26, EnVar bugfix (effective 00 UTC assimilation / 06 UTC forecast)
Correction of a coding error in the weighting between climatological and ensemble-derived background error covariances in the global hybrid EnVar. Reduced ICON-EPS ensemble spread as a side effect.

### 18 February 2026 — model version `icon-2025.04-dwd-4.0` (effective 09 UTC run)
Revised visibility and ceiling diagnostics (ceiling now uses a cloud-overlap assumption); ICON-D2-specific changes to SSO-corrected 10 m winds and adaptive surface friction.

### 2 December 2025 — model version `icon-2025.04-dwd-3.0` (effective 06 UTC run)
More consistent bare-soil evaporation (all configurations); retuned convective entrainment/detrainment profile; revised filtering time scales for adaptive parameter tuning; changes to GNSS radio-occultation assimilation (global configurations).

### 24 September 2025 — model version `icon-2025.04-dwd-2.0`, DACE 2.25 (effective 06 UTC run)
Revised and extended inversion cloud parameterization; extended adaptive parameter tuning; improved radiosonde humidity processing; corrected station heights for several Antarctic stations.

### 23 November 2022 — vertical resolution upgrade
Global vertical levels increased from 90 to 120 (model top unchanged at 75 km); ICON-EU nest from 60 to 74 levels; vertical nest interface stays near 22.5 km. The simultaneous horizontal-resolution increase (40 km → 26 km global, 20 km → 13 km EU nest) applied to the ensemble systems only — deterministic ICON remained at 13 km / 6.5 km.

### 20 January 2015 — operational launch
ICON replaced the legacy GME global model. The European nest ICON-EU followed in June 2015, and ICON-EPS in January 2018.

---

## Official documentation
- DWD ICON model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_description.html
- DWD NWP forecast data overview: https://www.dwd.de/EN/ourservices/nwp_forecast_data/nwp_forecast_data.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD ICON Tutorial 2025: https://www.dwd.de/EN/ourservices/nwp_icon_tutorial/pdf_volume/icon_tutorial2025_en.pdf
- DWD ICON change notices (Änderungsmitteilungen), all years: https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_gesamt.html
- DWD Open Data newsletter, 2 September 2026 (v1 URL scheme, legacy tree shutdown, DEN/SMI, VIS, lib/cdo): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD Open Data news RSS feed: https://www.dwd.de/SiteGlobals/Functions/RSSFeed/DE/opendata/opendata_lsb.xml
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD Open Data help portal: https://www.dwd.de/opendatahelp
- DWD Geoportal: https://dwd-geoportal.de/
- DWD Open Data v1 tree: https://opendata.dwd.de/weather/nwp/v1/m/icon/
- ICON grid definition files (MPI-M): https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- DWD regridding guide for native ICON grids: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid
- DWD ICON open source repository: https://gitlab.dkrz.de/icon/icon-model
- ICON model documentation: https://docs.icon-model.org/

### Key references
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378
- Reinert, D., Prill, F., Frank, H., Denhard, M., Baldauf, M., Schraff, C., Gebhardt, C., Marsigli, C., and Zängl, G. (2024). *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.* Version 2.x.
- Espenak, F. and Meeus, J. (2009). *Five Millennium Catalog of Solar Eclipses: −1999 to +3000.* NASA Technical Report NASA/TP–2009–214174. https://eclipse.gsfc.nasa.gov/5MCSE/TP2009-214174.pdf

---

*Live verification performed 2026-10-18 against `https://opendata.dwd.de/weather/nwp/v1/m/icon/` (all four cycles of 2026-10-18; full parameter and step census of the 12 UTC run), the legacy `https://opendata.dwd.de/weather/nwp/icon/grib/` tree, and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.50.0. Model-level, `HHL` and data-assimilation details carried over from the 2026-08-05 verification.*
