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
  - Confirmed from live GRIB2 headers: `gridType = unstructured_grid`, `numberOfDataPoints = 2949120`, `numberOfGridUsed = 26`, `uuidOfHGrid = a27b8de618c411e4820ab5b098c6a5c0`. The matching grid definition file is `icon_grid_0026_R03B07_G.nc.bz2` under the CDO library path below.
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
  - 113 output steps for 00/12 UTC (79 hourly + 34 three-hourly); 93 steps for 06/18 UTC (79 + 14)

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
The Open Data tree carries **102 parameter directories** per cycle, identical across all four cycles. Verified live on 2026-08-05:

| Level type | Parameters | Levels |
|---|---|---|
| Single-level | 69 | surface / 2 m / 10 m / column-integrated |
| Model-level | 10 (`t`, `u`, `v`, `w`, `p`, `qv`, `qc`, `qi`, `clc`, `tke`) | see below |
| Pressure-level | 5 (`fi`, `t`, `u`, `v`, `relhum`) | 18 |
| Soil-level | 3 (`t_so`, `w_so`, `w_so_ice`) | 9 / 8 |
| Time-invariant | 18 (incl. `hhl`, `hsurf`, `clat`, `clon`, `elat`, `elon`, `fr_land`, `soiltyp`) | — |

(`t`, `u`, `v` appear under both model-level and pressure-level, hence 69 + 10 + 5 + 3 + 18 = 105 minus 3 duplicates = 102 directories.)

- **Pressure levels (18):** 30, 50, 70, 100, 150, 200, 250, 300, 400, 500, 600, 700, 800, 850, 900, 925, 950, 1000 hPa
- **Model levels — not uniform across parameters:**
  - `t`, `u`, `v`, `p`, `qv`, `qc`, `qi` — full levels 1–120
  - `w` — half-levels 1–121
  - `clc` — levels 39–120 only (82 levels; nothing above ~level 39)
  - `tke` — levels 61–121 only (61 levels)
- **Soil levels — `T_SO` (9):** 0, 0.005, 0.02, 0.06, 0.18, 0.54, 1.62, 4.86, 14.58 m (layer boundaries)
- **Soil levels — `W_SO` / `W_SO_ICE` (8):** 0, 0.01, 0.03, 0.09, 0.27, 0.81, 2.43, 7.29 m (layer mid-depths)

Fields include temperature, wind, humidity, pressure, precipitation (total, convective, grid-scale, rain/snow split), cloud cover and hydrometeors, radiation and surface flux components, lake (FLake) variables, sea-ice, snow, soil state, CAPE, weather interpretation (`ww`), and gust maxima.

ICON output also serves as boundary forcing for downstream regional configurations including several non-DWD ICON-LAM applications.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Data formats:** GRIB2, one message per file, **bzip2-compressed** (`.grib2.bz2`)
- **GRIB2 packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`, `missingValue = 9999`
  - Since **16 June 2026** all ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS forecast data are CCSDS-compressed (ICON-D2-RUC and ICON-D05 already were). DWD reports the compressed GRIB is 40–50% smaller than uncompressed. ecCodes, CDO, and wgrib2 handle this transparently, but a GRIB library built **without libaec/libeccodes CCSDS support will fail to decode these files**. This is the single most likely breakage point for existing ICON pipelines built before mid-2026.
- **GRIB2 tables:** `tablesVersion = 19`, `localTablesVersion = 1`, `centre = edzw` (Offenbach). DWD local table definitions are required to resolve local shortNames such as `T_SO`, `W_SO`, `HHL`, `ASWDIR_S`.
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/icon/grib/
  - Layout: `/<cycle>/<parameter>/icon_global_icosahedral_<leveltype>_<YYYYMMDDHH>_<step>[_<level>]_<PARAM>.grib2.bz2`
- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 — a pipe-delimited `path|bytes|mtime` listing of the entire `/weather/nwp/` tree (~9.1 M lines, ~55 MB compressed). Far cheaper than recursive directory crawling for enumeration.
- **Retention:** Each cycle directory holds exactly one run — the current run at that hour. Verified 2026-08-05: `00/`, `06/`, and `12/` each contained only `2026080500`, `2026080506`, `2026080512` respectively. Effective retention is therefore ~24 h per cycle. Longer archives must be maintained by the user.
- **Volume:** ~341 GB per 00/12 UTC run and ~281 GB per 06/18 UTC run, bzip2-compressed, across ~141,800 and ~117,200 files respectively (measured from `content.log`, 2026-08-05). Full-tree mirroring is a serious bandwidth commitment; most users will want a parameter/level subset.
- **Publication latency:** First files appear ~2 h 40 min after cycle time; the run completes ~3 h 40–45 min after cycle time. Measured 2026-08-05: 00 UTC run 02:39:35 → 03:44:56 UTC; 06 UTC run 08:42:17 → 09:37:50 UTC; 12 UTC run 14:39:20 → 15:46:22 UTC.

### Regular lat–lon grids: interpolation is user-side
Global ICON is distributed on the **native triangular grid only**. DWD does not publish a regular lat–lon rendering of global ICON on the Open Data server (this differs from ICON-EU and ICON-D2, which do carry `regular-lat-lon` trees).

What DWD provides instead is a CDO toolkit for users to do the interpolation themselves:
- https://opendata.dwd.de/weather/lib/cdo/
  - `ICON_GLOBAL2WORLD_025_EASY.tar.bz2` — global target grid at 0.25°
  - `ICON_GLOBAL2WORLD_0125_EASY.tar.bz2` — global target grid at 0.125°
  - `ICON_GLOBAL2EUAU_025_EASY.tar.bz2`, `ICON_GLOBAL2EUAU_0125_EASY.tar.bz2` — Europe/Australia subdomain equivalents
  - `icon_grid_0026_R03B07_G.nc.bz2` — the R3B07 grid description matching `numberOfGridUsed = 26`

> **Correction to earlier versions of this entry.** This entry previously listed "regular latitude–longitude at 0.25° and 0.125°" among the grids *available on Open Data*. That is not the case: 0.25° and 0.125° are the target grids of DWD's downloadable CDO weight files, not distributed products. Every file in `/weather/nwp/icon/grib/` matches `icon_global_icosahedral_*` — verified by full manifest scan (519,386 entries, zero `regular-lat-lon` matches).

---

## Time-integration conventions
Four different conventions coexist in the single-level set. All verified by decoding `stepType`/`stepRange` from the 2026-08-05 00 UTC run.

| Convention | `stepType` | Window | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | `0-<step>` | `TOT_PREC`, `RAIN_GSP`, `RAIN_CON`, `SNOW_GSP`, `RUNOFF_S` |
| Averaged from run start | `avg` | `0-<step>` | `ASOB_S`, `ASWDIR_S`, `ATHB_S`, `ALHFL_S`, `ASHFL_S` (the `A*` flux family) |
| Max/min since last 6 h boundary | `max` / `min` | `0-3`, `6-7`, `12-18`, `78-81` … | `TMAX_2M`, `TMIN_2M` |
| Max over the preceding hour only | `max` | `11-12`, `83-84` | `VMAX_10M` |
| Instantaneous | `instant` | `<step>` | `T_2M`, `PMSL`, `CAPE_ML`, model-level and pressure-level fields |

Two traps here:

- **`VMAX_10M` beyond +78 h covers only one hour in three.** In the 3-hourly output range the gust maximum is still a 1-hour window (step 084 → `83-84`), so gusts in the two skipped hours of each 3-hour interval are not represented in any file. Peak-gust products built by taking the running maximum of `VMAX_10M` will systematically under-report beyond +78 h.
- **`VMAX_10M` has no step 000** (112 files per 00/12 run rather than 113). `TMAX_2M`/`TMIN_2M` *do* have a step-000 file, but with a degenerate `stepRange` of `0m` — an empty interval, not a valid instantaneous value.

### Unit-string discrepancy in `RAIN_GSP`
ecCodes resolves `RAIN_GSP` to `paramId = 228219` (`lsrr`, large-scale rain rate) with declared units `kg m**-2 s**-1`, while the message itself is encoded as `stepType = accum` over `0-12`. The values confirm the accumulation reading: at +12 h from the 2026-08-05 00 UTC run, `RAIN_GSP` ranges 0–271.3 against `TOT_PREC` 0–289.0 in the same run — comparable magnitudes, and absurd as a rate (271 mm s⁻¹).

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
- DWD's machine-learning global model **[AICON-Global](./aicon-global.md)** is trained on the ICON reanalysis (ICON-DREAM), runs on this same R3B07 grid, and is initialized from ICON analyses. It is distributed on Open Data under `/weather/nwp/v1/m/aicon/` and is *not* part of the operational ICON suite.
- **`ICON-D05`** — a ~0.5 km deterministic configuration over Germany, introduced 27 February 2025 per the ICON Database Reference. Initialized from an interpolated ICON-D2 analysis rather than its own DA; 3-hourly cycling, 48 h range; no ensemble. It is referenced in DWD's 16 June 2026 CCSDS notice as already compressed, implying an internal or restricted distribution — **it does not appear anywhere under `/weather/nwp/`** as of 2026-08-05. Not catalogued here; worth re-checking for an Open Data release.

### File-listing hazards
- **Two runs coexist during upload.** The cycle directories are *not* swapped atomically. Files from the outgoing run are removed lazily while the incoming run uploads, so for roughly an hour the directory contains both. Observed live at 2026-08-05 20:47 UTC: `18/t_2m/` held steps 000–019 of `2026080518` alongside steps 013–120 of `2026080418`, with steps 013–019 present **from both runs simultaneously**.

  Because the run datetime is embedded in the filename, this is disambiguable — but a naive glob such as `*_013_T_2M.grib2.bz2` returns two files from different initializations. **Always match on the full `<YYYYMMDDHH>` token, never on step alone.** This is the same overwrite-window hazard documented for CWA's GRIB2 products, in a milder form: DWD's filenames make the collision detectable, whereas fixed-key archives make it silent.

- **Soil-level filename tokens mix units and do not sort by depth.** The numeric token in a soil-level filename is `scaledValueOfFirstFixedSurface` with the scale factor omitted. For `T_SO`, the 0.005 m level carries `scaledValue = 5, scaleFactor = 3` while every other level uses `scaleFactor = 2`. So the token `5` means 5 mm, but `2` means 2 cm. A numeric sort of the filename tokens gives `0, 2, 5, 6, 18, 54, 162, 486, 1458`, whereas true depth order is `0, 5, 2, 6, 18, 54, 162, 486, 1458`. Sorting soil layers by filename silently swaps the top two.

- **HHL levels 1–59 are constant fields** encoded with `bitsPerValue = 0` (~210 bytes compressed). Some readers and range-request-based tooling handle zero-bit-width GRIB2 messages poorly. These files are valid and decode correctly under ecCodes 2.48.

---

## Recent version history

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
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD Open Data help portal: https://www.dwd.de/opendatahelp
- DWD Geoportal: https://dwd-geoportal.de/
- DWD Open Data root: https://opendata.dwd.de/weather/nwp/
- DWD CDO grid description and weight files: https://opendata.dwd.de/weather/lib/cdo/
- DWD ICON open source repository: https://gitlab.dkrz.de/icon/icon-model
- ICON model documentation: https://docs.icon-model.org/

### Key references
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378
- Reinert, D., Prill, F., Frank, H., Denhard, M., Baldauf, M., Schraff, C., Gebhardt, C., Marsigli, C., and Zängl, G. (2024). *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.* Version 2.x.
- Espenak, F. and Meeus, J. (2009). *Five Millennium Catalog of Solar Eclipses: −1999 to +3000.* NASA Technical Report NASA/TP–2009–214174. https://eclipse.gsfc.nasa.gov/5MCSE/TP2009-214174.pdf

---

*Live verification performed 2026-08-05 against `https://opendata.dwd.de/weather/nwp/icon/grib/` (00, 06, 12, 18 UTC cycles of 2026-08-05, plus the residual 2026-08-04 18 UTC run) and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.48.0.*
