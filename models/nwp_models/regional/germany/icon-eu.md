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
- **Native mesh extent (since 6 October 2026):** cell centres span **25.45°W – 63.98°E, 24.86°N – 71.64°N**, decoded from the v1 `CLAT`/`CLON` fields. The nest boundary is not a rectangle, so these are bounding values.
- **Topography range:** −414.00 m to 3,921.75 m (`HHL` half-level 75, i.e. the surface, on the native mesh). The legacy lat–lon rendering peaked at 3,793.38 m — interpolation flattened the highest Alpine cells.
- **Legacy lat–lon extent (until 30 November 2026):** 23.5°W – 62.5°E, 29.5°N – 70.5°N at 0.0625°

---

## Basic details
- **Model type:** Regional deterministic NWP (two-way coupled nest)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic)
- **Dynamical formulation:** Non-hydrostatic, on a triangular (icosahedral) horizontal grid
- **Convection-allowing:** No (deep convection parameterized at ~6.5 km)
- **Native horizontal grid:** R3B08 nest grid, **768,608 cells**, ~6.5 km
  - Verified from live GRIB2 headers (v1 feed, 2026-10-18 18 UTC run): `gridType = unstructured_grid`, `numberOfDataPoints = 768608`, `numberOfGridUsed = 62`, `uuidOfHGrid = 832c1507fbc73eb384139890094d2c60`. DWD's 2 September 2026 newsletter names the matching grid file `icon_grid_0062_R03B08_N02.nc`, which settles the R3B08 identity this entry previously could not confirm.
  - Grid #62 replaced grid #27 on **6 October 2026**, when the nest domain was enlarged. DWD's 28 July 2026 Open Data newsletter describes it as an extended EU-nest domain with 1.15 times the grid points; the resolution itself did not change. Data on the two grids cannot be concatenated cell-by-cell.
  - The same newsletter states that the legacy 0.0625° lat–lon product kept its grid through the change.
- **Public output grid:** **native triangular grid** in the v1 feed. The regular 0.0625° lat–lon product (1377 × 657 = 904,689 points) **ends after 30 November 2026** — see *Data availability*.
- **Vertical levels:** **74 full levels** (75 `HHL` half-levels), all published
- **Model top:** **22,770.33 m** — `HHL` half-level 1, spatially constant (`bitsPerValue = 0`). Unchanged by the grid switch. Identical to [ICON-EU-EPS](../../../ensemble_models/regional/de/icon-eu-eps.md), as expected for the same vertical grid.
- **Forecast length:**
  - **120 hours** for the main synoptic cycles (00, 06, 12, 18 UTC)
  - **51 hours** for the intermediate cycles (03, 09, 15, 21 UTC) in the v1 feed — see correction below
- **Update frequency / cycles:** 8× daily — 4 main synoptic cycles (00, 06, 12, 18 UTC) plus 4 intermediate off-synoptic cycles (03, 09, 15, 21 UTC)
- **Temporal output resolution (v1 feed):**
  - **Main cycles:** hourly to +78 h, then 3-hourly to +120 h (79 + 14 = 93 steps) — **except `U_10M`, `V_10M`, `VMAX_10M`, `ASWDIR_S` and `ASWDIFD_S`, which are hourly to +120 h (121 steps)**
  - **Intermediate cycles:** hourly to +51 h (52 steps) for every field except `MH`, which stops at +30 h (31 steps)

> **Undocumented step changes in the v1 feed.** DWD's 2 September 2026 newsletter states that parameter set, forecast range and time resolution are unchanged by the migration apart from `VIS`, `DEN` and `SMI`. A full step census on 2026-10-18 against the legacy tree shows otherwise for ICON-EU:
>
> | Field / cycle | Legacy lat–lon tree | v1 native tree |
> |---|---|---|
> | `U_10M`, `V_10M`, `VMAX_10M`, `ASWDIR_S`, `ASWDIFD_S` — main cycles | 93 (3-hourly after +78 h) | **121 (hourly to +120 h)** |
> | `LPI_CON_MAX` — main cycles | 65 (hourly to +48 h, then 3-hourly) | **93** (standard pattern) |
> | Most fields — intermediate cycles | 34 (hourly to +30 h, then 36, 42, 48) | **52 (hourly to +51 h)** |
> | The five wind/radiation fields — intermediate cycles | 52 | 52 |
> | `MH` — intermediate cycles | 31 | 31 |
>
> The five wind and radiation fields are the ones energy forecasters use, which may explain the extra output, but DWD has not said so. `U_10M` was checked across all eight cycles of 2026-10-18; the other four fields on the 18 UTC run.

> **Correction — intermediate-cycle range.** An earlier version of this entry corrected the intermediate-cycle range from 51 h to 48 h, based on the legacy lat–lon tree, which stopped at +48 h. The v1 feed publishes these cycles hourly to **+51 h**, matching the figure in DWD's operational-chain posters. The legacy tree was simply publishing a thinned subset.

---

## Data assimilation
ICON-EU does not run an independent analysis. As the two-way European nest of the global ICON integration, it inherits initial conditions from DWD's **hybrid LETKF + EnVar** data assimilation system, whose LETKF ensemble component runs a 20 km nest over Europe (see the [ICON Global entry](../../global/germany/icon-global.md#data-assimilation) for the full DA description).

---

## Initial and boundary conditions
- **Coupling:** Two-way nested within the global ICON integration, rather than one-way driven by an external global model. The global domain and the European nest exchange boundary information and feedbacks every model time step. The feedback relaxation time scale was reduced from 3 hours to 1 hour on 4 December 2024.
- **Downstream use:** ICON-EU in turn supplies the lateral boundary conditions for the one-way-driven [ICON-D2](./icon-d2.md) and [ICON-D2-RUC](./icon-d2-ruc.md) convection-permitting systems.

---

## What it provides

**97 parameter directories** in the v1 tree, identical across cycles. Against the legacy tree's 96: `VIS`, `RLAT` and `RLON` are gone; the native mesh coordinates `CLAT`, `CLON`, `ELAT` and `ELON` are added. Verified live 2026-10-18.

| Level type (v1 `lvt1`) | Levels | Parameters |
|---|---|---|
| Single-level (no `lvt1`) | — | 68 forecast fields + 12 time-invariant |
| Model level (`150`) | **1–74** (1–75 for half-level fields) | `T`, `U`, `V`, `W`, `P`, `QV`, `QC`, `QI`, `CLC`, `TKE`, `HHL` |
| Pressure level (`100`) | **20**: 50, 70, 100, 150, 200, 250, 300, 400, 500, 600, 700, 775, 800, 825, 850, 875, 900, 925, 950, 1000 hPa (in Pa in the path) | `FI`, `T`, `U`, `V`, `RELHUM`, `OMEGA`, `CLC` |
| Soil level (`106`) | `T_SO`: 0, 0.005, 0.02, 0.06, 0.18, 0.54, 1.62, 4.86, 14.58 m; `W_SO`/`W_SO_ICE`: 0, 0.01, 0.03, 0.09, 0.27, 0.81, 2.43, 7.29 m | `T_SO`, `W_SO`, `W_SO_ICE` |
| Time-invariant | — | `HSURF`, `CLAT`, `CLON`, `ELAT`, `ELON`, `FR_LAND`, `FR_LAKE`, `DEPTH_LK`, `SOILTYP`, `PLCOV`, `LAI`, `ROOTDP` (plus `HHL`) |

> **Correction.** Earlier versions omitted `CLC` from the pressure-level set and listed soil levels as a single mixed list of filename tokens. `CLC` is published on all 20 pressure levels in both trees; the soil layers are as given above, in metres.

Note the pressure-level set is **20 levels, not the 18 of ICON Global** — ICON-EU adds 775, 825, 875 hPa in the lower troposphere and drops 30 hPa at the top. Anyone building a combined global/regional pressure-level pipeline needs to handle both.

Content includes temperature, wind, precipitation by type, humidity, pressure, cloud and hydrometeor fields, radiation and surface flux components, lake (FLake) variables, soil state, `WW` (WMO weather interpretation), `CEILING`, `HZEROCL`, `SNOWLMT`, `CAPE_ML`/`CAPE_CON`/`CIN_ML`, **`LPI_CON_MAX`** (convective Lightning Potential Index maximum), and **synthetic Meteosat brightness temperatures** (`SYNMSG_BT_CL_IR10.8`, `SYNMSG_BT_CL_WV6.2`).

**`MH` — mixed layer depth (`mld`, metres)** — is now available on the native mesh for the first time (`/v1/m/icon-eu/p/MH/`). It was previously lat–lon only, and the lat–lon version ends with the legacy tree.

**`VIS` (near-surface visibility) is not carried into the v1 tree** and disappears entirely after 30 November 2026. DWD's aviation weather service judged the ICON visibility field unsuitable for forecasting visibility under visual flight rules, and does not use it in any of its own aviation products.

Time-integration conventions, verified by decoding `stepType`/`stepRange` (v1, 2026-10-18 18 UTC run):

| Convention | `stepType` | PDT | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | 8 | `TOT_PREC` (`0-12`), `RAIN_GSP`, `RAIN_CON`, `SNOW_GSP`, `RUNOFF_S` |
| Averaged from run start | `avg` | 8 | `ASOB_S`, `ATHB_S`, `ASWDIR_S` (`0-85`), `ALHFL_S` |
| Max over the preceding hour | `max` | 8 | `VMAX_10M` (`83-84`), `LPI_CON_MAX` (`11-12`) |
| Max/min since last 6 h boundary | `max` / `min` | 8 | `TMAX_2M` (`6-12`), `TMIN_2M` |
| Instantaneous | `instant` | 0 | `T_2M`, `PMSL`, `WW`, `CAPE_ML`, `MH`, and all multi-level fields |

Unlike [ICON Global](../../global/germany/icon-global.md) and [ICON-D2](./icon-d2.md), **every field including `WW` and `VMAX_10M` carries a step-000 file**.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, one message per file, **plain `.grib2`** (no bzip2 wrapper)
- **GRIB2 packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library built without libaec/CCSDS support cannot decode these files.
- **GRIB2 tables:** `tablesVersion = 19`, `localTablesVersion = 1`, `centre = edzw`, `typeOfProcessedData = fc`, `generatingProcessIdentifier = 2`
- **No bitmap.** `bitmapPresent = 0`; all 768,608 cells carry values. (ICON-D2 and ICON-D2-RUC, by contrast, mask their lateral boundary zone.)
- **Official download location:** https://opendata.dwd.de/weather/nwp/v1/m/icon-eu/

### Path structure (v1)
- **Single-level and time-invariant:**
  `/p/{PARAM}/r/{YYYY-MM-DD}T{HH}%3A00/s/PT{hhh}H{mm}M.grib2`
- **Multi-level:**
  `/p/{PARAM}/lvt1/{100|106|150}/lv1/{level}/r/{run}/s/PT{hhh}H{mm}M.grib2`

Pressure levels are given in Pa (`lv1/85000`), soil depths in metres (`lv1/0.005`). The run token's colon is percent-encoded as `%3A`. Example:

`https://opendata.dwd.de/weather/nwp/v1/m/icon-eu/p/U_10M/r/2026-10-18T18%3A00/s/PT000H00M.grib2`

- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 (covers the v1 tree)
- **Retention:** each run has its own directory; ~24 h rolling window (8–9 runs visible, the oldest removed lazily).
- **Volume (v1):** **~64.8 GB and 90,902 files per main run**; **~35.7 GB and 50,766 files per intermediate run**. Daily total roughly **402 GB across ~567,000 files**. Intermediate runs are larger than in the legacy tree (24.9 GB) because they now run hourly to +51 h.
- **Publication latency (v1):** main cycles start ~2 h 37–42 min after cycle time and complete ~3 h 31–37 min after (00 UTC run 2026-10-18: 02:37:23 → 03:31:23 UTC). Intermediate cycles start ~2 h 38 min after and complete ~2 h 55–58 min after (03 UTC: 05:38:23 → 05:55:23). Essentially in lockstep with [ICON Global](../../global/germany/icon-global.md), as expected for two products of the same integration.

### Legacy lat–lon tree — discontinued after 30 November 2026
The old layout at https://opendata.dwd.de/weather/nwp/icon-eu/grib/ stops after **30 November 2026** (DWD Open Data newsletter, 2 September 2026). It is the last place ICON-EU exists on a regular grid; DWD does not plan a replacement lat–lon product. Until then it runs in parallel:

- Layout: `/<cycle>/<parameter>/icon-eu_europe_regular-lat-lon_<leveltype>_<YYYYMMDDHH>_<step>[_<level>]_<PARAM>.grib2.bz2`
- 0.0625° regular grid, 1377 × 657 points, `gridType = regular_ll`, CCSDS-packed, no bitmap
- `VIS`, `RLAT`, `RLON` present; no native mesh coordinates
- ~68.7 GB / 90,825 files per main run; ~24.9 GB / 33,372 files per intermediate run
- The bzip2 wrapper adds nothing since the June 2026 CCSDS switch (ratio ~1.002)
- Thinner time steps than v1 for several fields (see the table under *Basic details*)

> **Correction — "the native grid is not distributed" no longer holds.** Until August 2026 ICON-EU was published only on the interpolated lat–lon grid, unlike every other member of DWD's ICON family. The v1 directory `/v1/m/icon-eu/` appeared in late August 2026 (directory created 21 August per dynamical.org) and was announced on 2 September. After 30 November ICON-EU, like the rest of the family, will be native-grid only.

### Grid files and regridding
- **Grid definition file:** `icon_grid_0062_R03B08_N02.nc`, from https://icon-downloads.mpimet.mpg.de/dwd_grids.xml (MPI-M). DWD's `/weather/lib/cdo/` folder never held an ICON-EU nest grid file and is deleted after 30 November 2026.
- **Regridding guide:** https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid. DWD's 2 September newsletter gives a worked cdo example for exactly this grid: `cdo selname,cell_area icon_grid_0062_R03B08_N02.nc cell-areas.nc`, then `cdo gennn,<target_grid> -setgrid,cell-areas.nc PT000H00M.grib2 weights.nc`.
- Anyone who relied on the lat–lon product will need to regrid themselves after 30 November. Some downstream services are already doing so — Open-Meteo, for example, is moving its `dwd_icon_eu` API model onto the native grid.

---

## Notes
- ICON-EU and the global [ICON](../../global/germany/icon-global.md) are not separate models — ICON-EU is the higher-resolution European nest of a single tightly coupled two-way integration. This is structurally different from regional models elsewhere (e.g., ARPEGE/AROME at Météo-France) where the limited-area model runs as a separate process driven by one-way lateral boundary conditions.
- The EU nest caps at **120 h even for the 00 and 12 cycles**, whereas the global ICON integration runs those to 180 h. Guidance beyond +120 h over Europe has to come from the coarser global product.
- The ensemble counterpart is [ICON-EU-EPS](../../../ensemble_models/regional/de/icon-eu-eps.md), distributed as the European-nest portion of [ICON-EPS](../../../ensemble_models/global/de/icon-eps.md). It also changed resolution and domain on 6 October 2026; see its entry. Both carry 74 levels and the same 22,770.33 m model top.
- **ICON-ART-EU** (`/weather/nwp/v1/m/icon-art-eu/`) is **not** ICON-EU with ART added. It is the ICON-ART system's own, much larger Europe / North Africa / North Atlantic nest (DWD's "ICON-ART-EU-NA²", grid #61, ~10 km since 6 October 2026), and the ICON-EU domain enlargement did not affect it. It loses its `VIS` field after 30 November 2026 as well. See [ICON-ART-EU](../../../air_quality_models/regional/germany/icon-art-eu.md).
- The ICON model code has been open source under a permissive licence since January 2024 (repository: https://gitlab.dkrz.de/icon/icon-model).
- **DWD's English model-description page** describes the global analysis as "a 3D variational assimilation method"; the operational system has been hybrid LETKF + EnVar since 2015. See the [ICON Global entry](../../global/germany/icon-global.md#data-assimilation) for the discrepancy note.

### File-listing hazards
- **Incomplete runs during upload.** Each run gets its own directory, which fills over ~20–55 minutes. A listing taken mid-upload shows a partial run.
- **Soil depths in the path are plain metres** (`lv1/0.005`), so they sort correctly. Inside the GRIB header the 0.005 m `T_SO` level still uses `scaleFactor = 3` while the others use 2; decode depth as `scaledValue × 10^−scaleFactor`.
- **Grid #27 vs grid #62.** Archives that span 6 October 2026 contain two incompatible native meshes. Check `numberOfGridUsed` before stacking data from either side of that date.

---

## Recent version history

ICON-EU has no independent change-notice series — it is upgraded as part of the global ICON system, and DWD's notices state that changes apply to all global ICON configurations including the EU nests. The nest-relevant items are listed here; see the [ICON Global entry](../../global/germany/icon-global.md#recent-version-history) for the full sequence.

> Model-change notices are covered through 5 August 2026, plus the 6 October 2026 domain change. Other ICON change notices since August have not yet been reviewed for this entry.

### 30 November 2026 (scheduled) — lat–lon product, legacy tree, `VIS` and `lib/cdo` withdrawn
Per DWD's 2 September 2026 newsletter: delivery under `/weather/nwp/icon-eu/` and `/weather/nwp/icon-eu-eps/` stops after 30 November. ICON-EU is no longer available on a regular lat–lon grid from DWD. `VIS` ends with it. `/weather/lib/cdo/` is deleted.

### 6 October 2026 — enlarged nest domain, native grid #27 → #62 (effective 06 UTC run)
Per DWD's change notice of 6 October 2026, the ICON-EU domain was "slightly expanded" at the request of customers and consortium partners: mostly to the south, with just a few grid rows added in the west and north so that Iceland and Norway are fully included. Resolution (6.5 km) and vertical levels unchanged. DWD found no measurable impact on forecast quality.

The European nest domain was expanded, moving the native mesh from grid #27 to grid #62 (`icon_grid_0062_R03B08_N02`, 768,608 cells, 1.15 times the previous count). Announced in DWD's 28 July 2026 Open Data newsletter, which also noted that the regular lat–lon product would keep its grid. Applies to the v1 delivery. ICON-EU-EPS (→ 10 km, grid #63) and ICON-ART-EU (→ 10 km, grid #61) changed on the same date.

### 2 September 2026 — native-grid v1 delivery
ICON-EU published on its native triangular grid for the first time, under `/weather/nwp/v1/m/icon-eu/`: plain CCSDS-packed GRIB2, ISO 8601 run/step tokens, `MH` on the native grid. DWD describes the parameter set and time resolution as otherwise unchanged; live checks found several step-pattern differences (see *Basic details*).

### 5 August 2026 — solar eclipse parametrization (effective 12 UTC run)
New parametrization reduces incoming solar radiation during solar eclipses, feeding through to 2 m temperature, boundary-layer height, cloud cover, and 10 m wind. Based on Besselian elements; limb-darkening not considered. Motivated by photovoltaic production forecasting.

### 30 June 2026 — model version `icon-2025.10-dwd-2.2` (effective 12 UTC run)
Applies to all global ICON configurations including the EU nests: revised SST ensemble perturbations, retuned ensemble physics perturbations, reduced humidity relaxation in the lower stratosphere, assimilation of MWR radiances from the AWS satellite.

### 16 June 2026 — CCSDS compression (effective 09 UTC run)
All ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS GRIB2 output switched to lossless CCSDS packing, 40–50% smaller than the previous encoding. **Most likely change to break older downstream tooling** — and the point at which the `.bz2` wrapper stopped providing meaningful compression.

### 18 February 2026 — model version `icon-2025.04-dwd-4.0` (effective 09 UTC run)
Revised ceiling diagnostic (cloud-overlap assumption; fill value now 16 km above ground) and revised visibility diagnostic (reworked humidity contribution).

### 2 December 2025 — model version `icon-2025.04-dwd-3.0` (effective 06 UTC run)
More consistent bare-soil evaporation across all configurations; retuned convective entrainment/detrainment profile; revised filtering time scales for adaptive parameter tuning.

### 23 July 2025 — model version `icon-2025.04-dwd-1.0` (effective 06 UTC run, all ICON configurations)
Dissipative heating parameterization; ocean warm-layer parameterization introducing a diurnal SST cycle; bug fix for rime deposition on snow-free ground; retuning of interception storage and ozone–tropopause coupling.

### 5 February 2025 — `uuidOfVGrid` change (all ICON configurations)
The GRIB2 `uuidOfVGrid` parameter changed due to new compiler options. Present only for model full- and half-level variables. Unlike ICON-D2, **ICON-EU orography heights at a few grid points in the boundary interpolation zone changed by a few centimetres** as a side effect.

### 4 December 2024 — model version `icon-2024.10-dwd-2.0` (effective 06 UTC global run)
Extended adaptive parameter tuning (soil hydraulic diffusivity, land albedo, snow cover fraction diagnosis); revised treatment of snow cover in surface transfer calculation; and **reduction of the nest feedback relaxation time scale from 3 hours to 1 hour**, which slightly improved near-surface variables and the pressure/geopotential field over the first 2–3 forecast days.

### 23 April 2024 — near-surface visibility output (effective 09 UTC run)
`VIS` (near-surface visibility) added as an output variable for ICON-EU and ICON-D2, requested by DWD's aviation forecasting offices for use alongside ceiling in VFR forecasting. (Withdrawn from Open Data after 30 November 2026.)

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
- DWD Open Data newsletter, 2 September 2026 (v1 URL scheme, lat–lon shutdown, MH, VIS, lib/cdo): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD Open Data newsletter, 28 July 2026 (6 October grid change): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_juli2026_2.html
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD Open Data v1 tree: https://opendata.dwd.de/weather/nwp/v1/m/icon-eu/
- ICON grid definition files (MPI-M): https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- DWD regridding guide for native ICON grids: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid
- DWD ICON open source repository: https://gitlab.dkrz.de/icon/icon-model

### Key references
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378
- Reinert, D., et al. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*

---

*Live verification performed 2026-10-18 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-eu/` (full parameter and step census of the 18 UTC main run and 15 UTC intermediate run; `U_10M` across all eight cycles), the legacy `https://opendata.dwd.de/weather/nwp/icon-eu/grib/` tree, and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.50.0.*
