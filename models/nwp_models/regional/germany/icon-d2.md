# ICON-D2 (DWD convection-permitting regional model)

## What this model is
ICON-D2 is DWD's convection-permitting (high-resolution) regional NWP configuration of ICON, run as a limited-area model over central Europe.

At 2.2 km horizontal resolution it resolves deep convection explicitly, making it well suited to short-range forecasting of small-scale, high-impact weather such as convective precipitation, thunderstorms, strong winds, and fog. ICON-D2 replaced COSMO-D2 on 10 February 2021 as part of DWD's migration from the COSMO model family to ICON.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany

---

## What area it covers
- **Coverage:** Central Europe — Germany, Switzerland, Austria, the Benelux countries, and parts of neighbouring countries
- **Native mesh extent:** 4.16°W – 20.54°E, 43.04°N – 58.17°N
- **Topography range:** −51.93 m to 4,080.44 m (`HHL` half-level 66, excluding masked cells)
- **Legacy lat–lon product extent (until 30 November 2026):** 3.94°W – 20.34°E, 43.18°N – 58.08°N

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) limited-area configuration
- **Dynamical formulation:** Non-hydrostatic, on a triangular (icosahedral) horizontal grid
- **Convection-allowing:** Yes (2.2 km; deep convection resolved explicitly)
- **Horizontal resolution:** 2.2 km — R19B07 limited-area triangular grid, **542,040 cells**
  - Verified from live GRIB2 headers (v1 feed, 2026-10-18 18 UTC run): `gridType = unstructured_grid`, `numberOfDataPoints = 542040`, `numberOfGridUsed = 47`, `uuidOfHGrid = c6b12daa91ad64045b26c1b6452a2a20`. Matching grid file: `icon_grid_0047_R19B07_L.nc`. Identical grid identity to [ICON-D2-EPS](../../../ensemble_models/regional/de/icon-d2-eps.md), [ICON-D2-RUC](./icon-d2-ruc.md), and [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md).
- **Public output grid:** **native triangular grid.** The additional regular 0.02° lat–lon product **ends after 30 November 2026** — see *Data availability*.
- **Vertical levels:** **65 full levels** (66 `HHL` half-levels), all published
- **Model top:** **22,000 m** exactly — `HHL` half-level 1, spatially constant (`bitsPerValue = 0`)
- **Forecast length:** **48 hours, all eight cycles**
- **Update frequency / cycles:** 8× daily, every 3 hours (00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Temporal output resolution:** two cadences
  - **15-minutely, 0–48 h (193 steps)** for 29 precipitation, radiation, convective and column-integrated fields — see *What it provides*
  - **Hourly, 0–48 h (49 steps)** for everything else
- **Cloud microphysics:** Single-moment bulk scheme. The rapid-update sibling [ICON-D2-RUC](./icon-d2-ruc.md) uses the two-moment Seifert–Beheng scheme. The published field set shows the difference directly: ICON-D2 distributes `QC`, `QI`, `QR`, `QS`, `QG` and a `Q_SEDIM` diagnostic, but **no `QH` (hail)** and no `TQH` — those exist only in the two-moment configurations.

> **Correction — temporal resolution.** Earlier versions gave ICON-D2's output as hourly only. According to DWD's 2 September 2026 newsletter, the legacy tree packed quarter-hour steps into hourly files (DWD's "1h windows", with `TOT_PREC` as the named example); legacy `tot_prec` file sizes are consistent with four steps per file. The v1 feed publishes each quarter-hour step as its own file, which is what makes the 15-minute cadence visible in a directory listing.

> **Resolution wording.** 2.2 km is DWD's official figure for grid 47 (R19B07, 542,040 cells). A ~2.1 km average-spacing figure appears in development-era COSMO General Meeting material; it describes the same grid, not a different configuration.

---

## Data assimilation
ICON-D2 runs a **3-hourly KENDA-LETKF** data assimilation cycle. The DA stream ingests conventional surface and upper-air observations, aircraft and radiosonde data, satellite observations, and radar information, including Latent Heat Nudging (LHN) from radar-derived precipitation.

Radar assimilation extends beyond the German C-band network: volumetric reflectivity and radial wind from 9 French radar sites were added in May 2024 via EUMETNET OPERA, extended to all 14 in-domain French sites in February 2025. LHN draws on three composites — the German RY, DWD's European EUCOM, and the OPERA European composite. Reflectivity observation errors use a vertical profile (7 dBZ near ground decreasing to 4 dBZ around 500 hPa, constant above) since July 2024, replacing a constant 10 dBZ.

The rapid-update [ICON-D2-RUC](./icon-d2-ruc.md) tightens this to an hourly cycle.

---

## Initial and boundary conditions
- **Initial conditions:** ICON-D2's own 3-hourly KENDA-LETKF analysis.
- **Boundary conditions:** lateral boundaries supplied by [ICON-EU](./icon-eu.md). Unlike the global↔ICON-EU two-way nest, ICON-D2 is a one-way-driven limited-area model.

---

## What it provides

**131 parameter directories** in the v1 tree. Against the legacy tree: `VIS` is gone and `FRZ_RAIN` is new; the count happens to be unchanged. Verified live 2026-10-18.

| Level type (v1 `lvt1`) | Levels | Parameters |
|---|---|---|
| Single-level (no `lvt1`) | — | 95 forecast fields + 13 time-invariant |
| Model level (`150`) | **1–65** (1–66 for half-level fields) | `T`, `U`, `V`, `W`, `P`, `QV`, `QC`, `QI`, `QR`, `QS`, `QG`, `Q_SEDIM`, `CLC`, `TKE`, `HHL` |
| Pressure level (`100`) | **11**: 200, 250, 300, 400, 500, 600, 700, 850, 950, 975, 1000 hPa (in Pa in the path) | `FI`, `T`, `U`, `V`, `RELHUM`, `OMEGA` |
| Soil level (`106`) | `T_SO`: 0, 0.005, 0.02, 0.06, 0.18, 0.54, 1.62, 4.86, 14.58 m; `W_SO`/`W_SO_ICE`/`SMI`: 0, 0.01, 0.03, 0.09, 0.27, 0.81, 2.43, 7.29 m | `T_SO`, `W_SO`, `W_SO_ICE`, `SMI` |
| Radar reflectivity threshold (`25`) | 18 dBZ | `ECHOTOP` |
| Time-invariant | — | `HSURF`, `CLAT`, `CLON`, `ELAT`, `ELON`, `FR_LAND`, `FR_LAKE`, `FR_ICE`, `DEPTH_LK`, `SOILTYP`, `PLCOV`, `LAI`, `ROOTDP` (plus `HHL`) |

**15-minute fields (193 steps):** `TOT_PREC`, `RAIN_GSP`, `RAIN_CON`, `SNOW_GSP`, `SNOW_CON`, `GRAU_GSP`, `PRR_GSP`, `PRS_GSP`, `PRG_GSP`, `ASWDIR_S`, `ASWDIFD_S`, `CAPE_ML`, `CIN_ML`, `LPI`, `SDI_2`, `DBZ_850`, `DBZ_CMAX`, `HZEROCL`, `SNOWLMT`, `HBAS_SC`, `HTOP_SC`, `TQC`, `TQI`, `TQR`, `TQS`, `TQG`, `TQV`, `SYNMSG_BT_CL_IR10.8`, `SYNMSG_BT_CL_WV6.2`. Every other single-level forecast field is hourly (49 steps); `WW` has 48 (no step 000).

Content includes temperature, wind, precipitation by type, humidity, pressure, cloud and hydrometeor fields; convection-allowing severe-weather diagnostics (`LPI`/`LPI_MAX`, `UH_MAX`/`UH_MAX_LOW`/`UH_MAX_MED`, `SDI_2`, `DBZ_850`/`DBZ_CMAX`/`DBZ_CTMAX`, `ECHOTOP`, `TCOND_MAX`/`TCOND10_MX`, `W_CTMAX`, `VORW_CTMAX`, `CAPE_ML`/`CIN_ML`); aviation-relevant fields (`CEILING`, `HZEROCL`, `SNOWLMT`); lake (FLake) variables; and **synthetic Meteosat brightness temperatures** (`SYNMSG_BT_CL_IR10.8`, `SYNMSG_BT_CL_WV6.2`).

### Field changes in 2026
- **`MH` — mixed layer depth (`mld`, metres)** — is now published on the native mesh (`/v1/m/icon-d2/p/MH/`, 49 steps). Until the v1 delivery it existed only in the lat–lon rendering; that version ends with the legacy tree.
- **`VIS` (near-surface visibility)** is not carried into the v1 tree and disappears entirely after 30 November 2026. DWD's aviation weather service judged the ICON visibility field unsuitable for forecasting visibility under visual flight rules, and does not use it in any of its own aviation products. `CEILING` remains.
- **`FRZ_RAIN` — accumulated freezing rain water equivalent** (`fzrawe`, `paramId = 231001`, kg m⁻², accumulated from run start, 49 steps). New in the v1 tree; its directory was created on 9 September 2026. It is not mentioned in DWD's 2 September newsletter and has no counterpart in the legacy tree.

Time-integration conventions, verified by decoding `stepType`/`stepRange` (v1, 2026-10-18 18 UTC run):

| Convention | `stepType` | PDT | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | 8 | `TOT_PREC`, `RAIN_GSP`, `RAIN_CON`, `SNOW_GSP`, `GRAU_GSP`, `RUNOFF_S`, `FRZ_RAIN` |
| Averaged from run start | `avg` | 8 | `ASOB_S`, `ATHB_S`, `ASWDIR_S`, `ALHFL_S` |
| Max over the preceding hour | `max` | 8 | `VMAX_10M` (`11-12`), `LPI_MAX`, `UH_MAX`, `W_CTMAX` |
| Max/min since last 6 h boundary | `max` / `min` | 8 | `TMAX_2M` (`6-12`), `TMIN_2M` |
| Instantaneous | `instant` | 0 | `T_2M`, `PMSL`, `WW`, `CAPE_ML`, `MH`, and all multi-level fields |

**Quarter-hour steps are encoded in minutes.** `TOT_PREC` at `PT012H15M` decodes as `stepUnits = m`, `stepRange = 0m-735m` (0 to 12 h 15 min). Hourly steps decode in hours (`0-12`). Tooling that assumes hours throughout will misread the 15-minute accumulation windows by a factor of 60 — the same hazard as [ICON-D2-RUC](./icon-d2-ruc.md).

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, one message per file, **plain `.grib2`** (no bzip2 wrapper)
- **GRIB2 packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library without libaec/CCSDS support cannot decode these files.
- **GRIB2 tables:** `tablesVersion = 19`, `localTablesVersion = 1`, `centre = edzw`, `typeOfProcessedData = fc`, `generatingProcessIdentifier = 11`, `missingValue = 9999`
- **Bitmap:** every physical field carries one — `bitmapPresent = 1`, **16,968 of 542,040 cells masked (3.13%)**, the lateral boundary relaxation zone. `CLAT`/`CLON` cover the full mesh without a bitmap, so apply the bitmap before pairing coordinates with values.
- **Official download location:** https://opendata.dwd.de/weather/nwp/v1/m/icon-d2/

### Path structure (v1)
- **Single-level and time-invariant:**
  `/p/{PARAM}/r/{YYYY-MM-DD}T{HH}%3A00/s/PT{hhh}H{mm}M.grib2`
- **Multi-level:**
  `/p/{PARAM}/lvt1/{25|100|106|150}/lv1/{level}/r/{run}/s/PT{hhh}H{mm}M.grib2`

Pressure levels are given in Pa, soil depths in metres, `ECHOTOP` by dBZ threshold. The run token's colon is percent-encoded as `%3A`. Example:

`https://opendata.dwd.de/weather/nwp/v1/m/icon-d2/p/U_10M/r/2026-10-18T18%3A00/s/PT000H00M.grib2`

- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 (covers the v1 tree)
- **Retention:** each run has its own directory; ~24 h rolling window across the eight cycles.
- **Volume (v1):** **~27.3 GB and 58,641 files per run**, near-constant across cycles — roughly **218 GB/day**. More files than the legacy icosahedral set (54,272) because the 15-minute steps are no longer bundled; about half the legacy tree's combined daily volume, since the lat–lon copy is gone.
- **Publication latency (v1):** first files ~43 min 50 s after cycle time; run completes ~1 h 15–17 min after. Measured 2026-10-18 00 UTC: 00:43:51 → 01:16:05 UTC.

### Legacy tree — discontinued after 30 November 2026
The old layout at https://opendata.dwd.de/weather/nwp/icon-d2/grib/ (and `icon-d2-eps/`) stops after **30 November 2026** (DWD Open Data newsletter, 2 September 2026). With it goes the only regular lat–lon rendering in the D2 family.

- Layout: `/<cycle>/<parameter>/icon-d2_germany_<grid>_<leveltype>_<YYYYMMDDHH>_<step>_<level|2d>_<param>.grib2.bz2`, with `<grid>` either `icosahedral` or `regular-lat-lon`
- **Lat–lon grid:** plain regular 0.02° (`gridType = regular_ll`, 1215 × 746 = 906,390 points, not rotated), still `grid_simple`-packed, 16.7% of points masked
- 15-minute fields bundled four steps per hourly file
- `VIS` present; no `FRZ_RAIN`; `MH` on the lat–lon grid only
- ~22.9 GB / 54,272 files icosahedral plus ~31.7 GB / 54,317 files lat–lon per run; the lat–lon half finished ~2 h 00 min after cycle time

### Grid files and regridding
- **Grid definition file:** `icon_grid_0047_R19B07_L.nc`, from https://icon-downloads.mpimet.mpg.de/dwd_grids.xml (MPI-M).
- **Regridding guide:** https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid (cdo-based; produces the weights file as a by-product).
- **`/weather/lib/cdo/` is deleted after 30 November 2026**, including `icon_grid_0047_R19B07_L.nc.bz2` and the prebuilt `ICON_D2_002_EASY.tar.bz2` weights for a 0.02° target grid. Users of the lat–lon product who want to keep that grid will need to generate their own weights.

---

## Notes
- ICON-D2 is the convection-permitting limited-area member of DWD's ICON suite, driven by [ICON-EU](./icon-eu.md) boundaries. Its rapid-update sibling is [ICON-D2-RUC](./icon-d2-ruc.md) — same core, same domain, same grid, same 65 levels and 22 km model top, but **hourly cycling, a +27 h range, 5-minute precipitation output, and two-moment microphysics**.
- Ensemble counterparts: [ICON-D2-EPS](../../../ensemble_models/regional/de/icon-d2-eps.md) (3-hourly, +48 h, 20 members) and [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md) (hourly, +27 h, 20 members).
- **After 30 November 2026 the whole D2 family is native-grid only**, and all four systems share the same v1 path scheme. Workflows built on the ICON-D2 lat–lon rendering need regridding from that date.
- **The 48-hour range applies to all eight cycles.** The predecessor COSMO-D2 ran 27 h with 45 h at 03 UTC only; that asymmetry no longer exists.
- The ICON model code has been open source under a permissive licence since January 2024 (repository: https://gitlab.dkrz.de/icon/icon-model).
- **ICON-D05** — a ~500 m configuration operational since 27 February 2025 per DWD's ICON Database Reference, initialized from an interpolated ICON-D2 analysis rather than its own data assimilation, with 3-hourly cycling and a 48 h range. It does not appear anywhere under `/weather/nwp/` (re-checked 2026-10-18, including `v1/m/`). Not catalogued; worth re-checking for an Open Data release.

---

## Recent version history

> Model-change notices are covered through 18 February 2026. Entries dated after that are Open Data distribution changes; DWD's ICON-D2 change notices since then have not yet been reviewed for this entry.

### 30 November 2026 (scheduled) — lat–lon product, legacy tree, `VIS` and `lib/cdo` withdrawn
Per DWD's 2 September 2026 newsletter: delivery under `/weather/nwp/icon-d2/` and `/weather/nwp/icon-d2-eps/` stops after 30 November, ending the regular lat–lon product. `VIS` is withdrawn from all ICON Open Data deliveries. `/weather/lib/cdo/` is deleted.

### 9 September 2026 — `FRZ_RAIN` appears in the v1 tree
Accumulated freezing rain water equivalent added as a new ICON-D2 parameter (directory date). Not covered by the 2 September newsletter.

### 2 September 2026 — v1 URL scheme
ICON-D2 and ICON-D2-EPS published under `/weather/nwp/v1/m/`: native grid only, plain CCSDS-packed GRIB2, quarter-hour steps and ensemble members split into separate files, ISO 8601 run/step tokens. `MH` available on the native grid for the first time.

### 16 June 2026 — CCSDS compression (effective 09 UTC run)
All ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS GRIB2 output switched to lossless CCSDS packing. In the legacy tree this applied to the icosahedral files only; the lat–lon rendering stayed `grid_simple`.

### 18 February 2026 — model version `icon-2025.04-dwd-4.0` (effective 09 UTC run)
- **Revised ceiling diagnostic:** now uses a cloud-overlap assumption consistent with the layer-wise cloud cover fractions (`CLCL`, `CLCM`, …), diagnosing ceiling as the lowest layer where upward-integrated cloud fraction exceeds 50%. Fill value for grid points with too little cloud changed to **16 km above ground**, matching observation reports, rather than the height above sea level of the uppermost model level.
- **Revised visibility diagnostic:** the humidity contribution (relevant absent fog and precipitation) was reworked.
- **ICON-D2-specific:** additional output field for SSO-corrected 10 m winds; adaptive surface friction restricted to grid points with small SSO standard deviation.

### 23 July 2025 — model version `icon-2025.04-dwd-1.0` (effective 06 UTC, all ICON configurations)
Dissipative heating parameterization based on grid-scale kinetic energy loss (reduces the boundary-layer winter cold bias); ocean warm-layer parameterization introducing a diurnal SST cycle; bug fix for rime deposition on snow-free ground; retuning of interception storage and ozone–tropopause coupling.

### 4 June 2025 — DACE 2.24 (effective 09 UTC assimilation / 12 UTC forecast)
Bugfix for station identifiers in the German supplemental SYNOP network (WIGOS identifiers had displaced the older "C" identifiers needed for blacklists and redundancy checks); preparations for NOAA-21 satellite data; quality-control fixes for radiosonde and aircraft humidity.

### 7 May 2025 — saturation vapour pressure coefficients, `icon-2024.10-dwd-4.0` (effective 09 UTC ICON-D2 run)
Magnus-formula Tetens coefficients replaced with the more accurate ECMWF set, reducing errors by at least a factor of four at temperatures far below freezing. Applied consistently across model, data assimilation, and verification. DWD flagged this as preparation for an upcoming cloud microphysics upgrade.

### 26 February 2025 — KENDA: full French radar network (effective 06 UTC assimilation / 09 UTC forecast)
DACE 2.23 removed the computational cap on French radar assimilation; **all 14 French radar sites within the ICON-D2/RUC domain** are now assimilated, up from 9. Measurable Fraction Skill Score improvement for precipitation in the south-western quarter of the domain.

### 5 February 2025 — `uuidOfVGrid` change (all ICON configurations)
The GRIB2 `uuidOfVGrid` parameter changed due to new compiler options. Present only for model full- and half-level variables; pressure-level and single-level fields unaffected. Actual model level heights did **not** change for ICON-D2. Consumers keying on `uuidOfVGrid` for vertical-grid identity needed to update at this date.

### 4 December 2024 — model version `icon-2024.10-dwd-2.0` (effective 09 UTC run)
Extended adaptive parameter tuning (soil hydraulic diffusivity, land albedo, snow cover fraction diagnosis); revised treatment of snow cover in surface transfer calculation, reducing surface fluxes over snow beneath high vegetation.

### 9 July 2024 — gust parameterization and radar DA, `icon-2024.01-dwd-3.1` (effective 09 UTC run)
- Gust parameterization revised to use 10-minute averages of 10 m wind speed instead of instantaneous values, with excess gust speed limited relative to the resolved maximum wind in the lowest 1500 m (tuning factor 1.75 for ICON-D2). RMS error reduced ~5%, with significant improvement in the 20 m s⁻¹ and 25 m s⁻¹ gust categories.
- Latent Heat Nudging input migrated to HDF5 OPERA and EUCOM composites.
- Radar reflectivity observation errors changed from a constant 10 dBZ to a vertical profile — a change made for the then-upcoming ICON-RUC, with near-neutral impact on ICON-D2 itself.

### 22 May 2024 — KENDA: French radar volumes introduced (effective 06 UTC assimilation / 09 UTC forecast)
First assimilation of volumetric reflectivity and radial wind from **9 French radar stations** via EUMETNET OPERA. Also: removal of a rudimentary bias correction for near-saturated humidity observations, and correction of the vertical influence of SYNOP observations in the LETKF.

### 23 April 2024 — near-surface visibility output (effective 09 UTC run)
`VIS` (near-surface visibility) added as an output variable for ICON-D2 and ICON-EU, requested by DWD's aviation forecasting offices for use alongside ceiling in VFR forecasting. (Withdrawn from Open Data after 30 November 2026.)

### 24 January 2024 — sea-ice bottom heat flux, `icon-2.6.6-nwp2` (effective 12 UTC run)
Sea-ice scheme revised to account for ocean-to-ice heat flux; external parameter bugfix for false glacier points; adaptive time-step reduction extended to horizontal CFL exceedances.

### 29 November 2023 — all-sky satellite assimilation (effective 06 UTC assimilation / 09 UTC forecast)
Change to the ICON-D2 KENDA system introducing all-sky satellite radiance handling.

### 20 June 2023 — SST bugfix (effective 09 UTC run)
End of unintentional use of the ICON-EU sea-surface-temperature field in ICON-D2. Database requests had been supplying ICON-EU SST valid at 03 UTC, which then persisted until the ICON-D2 SST analysis at 00 UTC the following day. No forecast-quality impact expected, but SST time series spanning this date will show a discontinuity.

### 10 February 2021 — COSMO-D2 → ICON-D2
ICON-D2 replaced COSMO-D2 as part of DWD's migration from the COSMO model family to ICON. All aviation weather products migrated at the same time.

---

## Official documentation
- DWD ICON-D2 model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_d2/icon_d2_node.html
- DWD NWP forecast data overview: https://www.dwd.de/EN/ourservices/nwp_forecast_data/nwp_forecast_data.html
- DWD ICON-D2 change notices: https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_d2_gesamt.html
- DWD ICON change notices (all-configuration changes): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_gesamt.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD Open Data newsletter, 2 September 2026 (v1 URL scheme, lat–lon shutdown, MH, VIS, lib/cdo): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD Open Data v1 tree: https://opendata.dwd.de/weather/nwp/v1/m/icon-d2/
- ICON grid definition files (MPI-M): https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- DWD regridding guide for native ICON grids: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid
- ICON open source repository: https://gitlab.dkrz.de/icon/icon-model

### Key references
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378
- Schraff, C., Reich, H., Rhodin, A., Schomburg, A., Stephan, K., Periáñez, A., and Potthast, R. (2016). *Kilometre-scale ensemble data assimilation for the COSMO model (KENDA).* Quarterly Journal of the Royal Meteorological Society, 142(696), 1453–1472. https://doi.org/10.1002/qj.2748
- Reinert, D., et al. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*

---

*Live verification performed 2026-10-18 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-d2/` (full parameter and step census of the 18 UTC run; all eight cycles in the manifest), the legacy `https://opendata.dwd.de/weather/nwp/icon-d2/grib/` tree, and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.50.0.*
