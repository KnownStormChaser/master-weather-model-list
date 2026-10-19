# ICON-D2-RUC (DWD Rapid Update Cycle convection-permitting model)

## What this model is
ICON-D2-RUC is DWD's **rapid-update-cycle, convection-permitting** regional NWP configuration of ICON, designed for very short-range nowcasting-to-forecasting of rapidly evolving severe weather such as thunderstorms, supercells, squall lines, hail events, and flash floods.

It runs the same ICON limited-area model core as the standard [ICON-D2](./icon-d2.md) over the same domain and at the same 2.2 km horizontal resolution, but with three key operational differences: **hourly initialisation** (24× per day), a **shorter +27 hour forecast range**, and **sub-hourly output** for the fields that matter most to nowcasting. DWD describes ICON-D2-RUC as a "fraternal twin" of ICON-D2 — same model, different operational use case. Each forecast is complete roughly 40 minutes after model start, allowing forecasters and aviation users to react to new observations almost in real time.

ICON-D2-RUC also uses the more advanced two-moment bulk microphysics scheme of Seifert and Beheng, which produces more realistic radar reflectivities than the single-moment scheme used in standard ICON-D2 — particularly at strong convective cores. The system grew out of DWD's SINFONY (Seamless INtegrated FOrecastiNg sYstem) project, which developed a seamless prediction framework integrating nowcasting techniques with NWP at the convective scale.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country:** Germany

---

## What area it covers
- **Coverage:** Germany, Switzerland, Austria, the Benelux countries, and parts of neighbouring countries (same domain as ICON-D2)
- **Domain extent:** 4.16°W – 20.54°E, 43.04°N – 58.17°N
- **Topography range:** −51.93 m to 4,080.44 m (`HHL` half-level 66, excluding masked cells)

---

## Basic details
- **Model type:** Regional convection-permitting deterministic NWP, rapid update cycle
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) limited-area configuration
- **Dynamical formulation:** Non-hydrostatic, triangular icosahedral horizontal grid
- **Convection-allowing:** Yes (2.2 km; deep convection resolved explicitly)
- **Horizontal resolution:** 2.2 km — R19B07 limited-area triangular grid, **542,040 cells**
  - Verified from live GRIB2 headers (re-checked on the 2026-10-18 21 UTC run): `gridType = unstructured_grid`, `numberOfDataPoints = 542040`, `numberOfGridUsed = 47`, `uuidOfHGrid = c6b12daa91ad64045b26c1b6452a2a20`. Identical grid identity to [ICON-D2](./icon-d2.md), [ICON-D2-EPS](../../../ensemble_models/regional/de/icon-d2-eps.md) and [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md). Matching grid file: `icon_grid_0047_R19B07_L.nc`.
- **Public output grid:** Native triangular grid only — no regular lat–lon variant. (Standard ICON-D2 also published a 0.02° lat–lon rendering, but that ends after 30 November 2026.)
- **Vertical levels:** **65 full levels** (66 `HHL` half-levels), all published
- **Model top:** **22,000 m** exactly — `HHL` half-level 1, spatially constant (`bitsPerValue = 0`)
- **Forecast length:** **+27 hours**
- **Update frequency / cycles:** Hourly (24× daily), all published
- **Temporal output resolution:** three cadences — see below
- **Availability latency:** run complete ~41–43 minutes after cycle time (measured 2026-10-18: e.g. 00 UTC 00:31:42 → 00:42:54 UTC; 21 UTC 21:30:20 → 21:41:12 UTC), matching DWD's "~40 minutes" figure
- **Cloud microphysics:** Two-moment bulk scheme (Seifert and Beheng), more advanced than the single-moment scheme used in standard ICON-D2. The published field set corroborates this: `QG` (graupel), `QH` (hail), `QR`, `QS`, `QI`, `QC` are all distributed as prognostic hydrometeor mixing ratios on all 65 model levels, and `TQH` (column-integrated hail) exists only in the two-moment configurations.
- **Operational since:** 12 July 2024. DWD's ICON-D2 change notice of 9 July 2024 refers to the "upcoming ICON-RUC" and tunes radar observation errors specifically because they "were found to be beneficial for the ICON-RUC system," which is consistent with a launch days later.

> **Correction to earlier versions of this entry.** This entry previously gave the forecast length as **+14 hours**. Live enumeration shows **27 hours** — verified across ten consecutive cycles on 2026-08-05/06, each reaching `PT027H00M`.
>
> The 14-hour figure is not arbitrary: **+14 h is exactly where the high-cadence nowcasting window ends** for `T`, `U`, `V` at 500 and 700 hPa and for `QV` on model level 63, which switch from 15-minutely to hourly at that point (see below). The nowcasting horizon appears to have been recorded as the forecast length. The same error propagated to [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md) (also 27 h) and to the sibling reference in [ICON-D2](./icon-d2.md), which describes RUC as "+14 h range" in two places.

### Output cadence
Three schedules, reflecting the nowcasting orientation. Verified from the 2026-08-06 00 UTC run; step counts re-checked across all parameters of the 2026-10-18 18 UTC run (4 fields at 325 steps, 48 at 109, 24 at 28, `WW` at 27, 13 time-invariant).

| Cadence | Steps | Fields |
|---|---|---|
| **5-minutely**, 0–27 h | 325 | `TOT_PREC`, `TOT_PR`, `PREC_GSP`, `PR_GSP` |
| **15-minutely**, 0–27 h | 109 | Convective and severe-weather set: `DBZ_850`/`DBZ_CMAX`/`DBZ_CTMAX`, `DBZLMX_LOW`, `ECHOTOP`, `ECHOTOPinM`, `LPI`/`LPI_MAX`, `CAPE_ML`/`CIN_ML`, `UH_MAX`/`UH_MAX_LOW`/`UH_MAX_MED`, `SDI_2`, `TCOND_MAX`/`TCOND10_MX`, `W_CTMAX`, `VORW_CTMAX`, hail fields (`HAIL_GSP`, `KE_HAIL_S`, `KEF_HAIL_MAX_S`, `DEMAX_HAIL_S`, `DEMAX_HAIL_TMAX_S`), `GRAU_GSP`, `RAIN_GSP`, `SNOW_GSP`, the `PR*_GSP` rate family, column-integrated `TQ*`, `VIS` (withdrawn after 30 November 2026), `CEILING`, `HZEROCL`, `SNOWLMT`, `SYNMSG_BT_CL_IR10.8`, `SYNMSG_BT_CL_WV6.2`, `ASOB_S`/`ASWDIR_S`/`ASWDIFD_S` |
| **Hourly**, 0–27 h | 28 | Standard surface fields and the bulk of the model- and pressure-level output |

**Four level-specific exceptions.** `T`, `U`, `V` at **500 and 700 hPa** and `QV` on **model level 63** run 15-minutely from 0 to 14 h and hourly from 15 to 27 h (70 steps). `W` and `RELHUM` are published **only** at 500 and 700 hPa, and run 15-minutely across the full 27 h (109 steps). Every other level of those same parameters is hourly. This is a deliberate nowcasting subset for mid-level moisture and vertical motion, and it is the origin of the mistaken "+14 hours."

`WW` (weather interpretation) has 27 steps rather than 28 — no step 000. Time-invariant fields carry one file per run; `HHL` carries 66.

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** **hourly KENDA-LETKF** — substantially more frequent than the 3-hourly cycle used by standard ICON-D2. The hourly cycle is one of the system's defining operational features: it allows the model to absorb new radar volume scans, surface observations, and other near-real-time data each hour, forcing the forecast to "return to reality" much more often than coarser-cadence systems can.

The DA cycle restarts daily at 03 UTC from the standard ICON-D2 cycle rather than running continuous indefinite cycles. Assimilated observations include:
- 3D radar reflectivity volumes and radial wind from the German radar network, extended to French radars via EUMETNET OPERA — 9 sites from May 2024, all 14 in-domain sites from February 2025
- Conventional surface and upper-air observations
- Aircraft, radiosonde, and satellite observations inherited from the ICON-D2 DA stream
- Latent Heat Nudging (LHN) using radar-derived precipitation composites (German RY, DWD EUCOM, and OPERA European composites)

Radar reflectivity observation errors use a vertical profile — 7 dBZ near ground decreasing to 4 dBZ around 500 hPa and constant above — introduced in July 2024 specifically because it benefited the RUC system, replacing a constant 10 dBZ.

The combination of hourly DA, two-moment microphysics, and rapid-update cycling targets the forecast skill window between 0–6 hours, where pure nowcasting extrapolation begins to fail and traditional 3-hourly NWP cycles have not yet refreshed.

---

## Initial and boundary conditions
- **Initial conditions:** ICON-D2-RUC's own hourly KENDA-LETKF analysis, restarted daily at 03 UTC from the standard ICON-D2 cycle.
- **Boundary conditions:** lateral boundaries from [ICON-EU](./icon-eu.md), as for standard ICON-D2. One-way driven limited-area configuration.

---

## What it provides

**114 parameter directories** as of 2026-10-18, dropping to **113 after 30 November 2026** when `VIS` is removed. Substantially richer than its own ensemble counterpart (99). (The 2026-08-06 check recorded 115; which directory went missing in between has not been identified.) Level structure verified live 2026-08-06, `lvt1` types re-checked 2026-10-18:

| Level type (`lvt1`) | Meaning | Levels | Parameters |
|---|---|---|---|
| `150` | General vertical (model) layer | **1–65** (1–66 for half-level fields) | `T`, `U`, `V`, `P`, `QV`, `QC`, `QC_DIA`, `QI`, `QI_DIA`, `QR`, `QS`, `QG`, `QH`, `CLC`, `TKE`, `W`, `HHL` |
| `100` | Isobaric surface (Pa) | **23 levels**: 150, 200, 250, 300, 350, 400, 450, 500, 550, 600, 650, 700, 750, 775, 800, 825, 850, 875, 900, 925, 950, 975, 1000 hPa | `FI`, `T`, `U`, `V`, `QV` (all 23); `W`, `RELHUM` (500 and 700 hPa only) |
| `103` | Height above ground (m) | **1000, 3000, 6000** | `SRH`, `WSHEAR_U`, `WSHEAR_V` |
| `25` | Radar reflectivity threshold (dBZ) | **18, 30** | `ECHOTOP`, `ECHOTOPinM` |

The `lvt1/25` level type is unusual: `ECHOTOP` and `ECHOTOPinM` are indexed by **reflectivity threshold** (18 and 30 dBZ) rather than by height or pressure — the level value is the dBZ contour whose top is being reported.

**The full 65-level, 23-pressure-level output is the main practical difference from the ensemble**, which publishes only model levels 56–65 and six pressure levels. If you need a complete vertical profile from a rapid-update system, the deterministic RUC is the one that has it.

Distinctive content: the complete two-moment hydrometeor set (`QG`, `QH`, `QR`, `QS` alongside `QC`/`QI`), hail diagnostics (`HAIL_GSP`, `KE_HAIL_S` kinetic energy, `KEF_HAIL_MAX_S`, `DEMAX_HAIL_S`, `DEMAX_HAIL_TMAX_S`), **synthetic Meteosat brightness temperatures** (`SYNMSG_BT_CL_IR10.8`, `SYNMSG_BT_CL_WV6.2`, GRIB2 PDT 32) for direct satellite-image comparison, storm-relative helicity and shear on three layers, `WW` (WMO weather interpretation), `LPI`/`LPI_MAX`, `SDI_2`, `SNOWLMT`, `HZEROCL`, `CEILING`, and `TKE` on all 66 half-levels. Also present but not otherwise described here: `CAPE_MU`/`CIN_MU`, `LAPSE_RATE`, `TOT_PREC_D`, `QV_2M`.

**`VIS` (near-surface visibility) is removed from the RUC delivery after 30 November 2026.** DWD's 2 September 2026 newsletter withdraws `VIS` from all ICON Open Data products, because its aviation weather service judged the field unsuitable for forecasting visibility under visual flight rules. `CEILING` stays.

Time-integration conventions, verified by decoding at +12 h:

| Convention | `stepType` | PDT | Notes |
|---|---|---|---|
| Accumulated from run start | `accum` | 8 | `TOT_PREC` at `PT012H05M` decodes as `stepRange = 0m-725m`, `stepUnits = 0` |
| Averaged from run start | `avg` | 8 | `ASOB_S`, `ASWDIR_S`, `ASWDIFD_S` |
| Max over the preceding interval | `max` | 8 | `VMAX_10M`, `LPI_MAX`, `UH_MAX`, `TOT_PR_MAX` |
| Instantaneous | `instant` | 0 | `T_2M`, `PMSL`, `WW`, model- and pressure-level fields |
| Synthetic satellite | `instant` | **32** | `SYNMSG_BT_CL_*` |

`typeOfProcessedData = fc` (deterministic forecast), against `cp` in the ensembles.

**The minute-resolution step encoding is the key parsing hazard.** Because output is sub-hourly, `stepUnits = 0` (minutes) on time-processed fields and `stepRange` is expressed in minutes — `0m-725m` is 0 to 12 h 05 min. Tooling that assumes `stepUnits = 1` (hours) will misread accumulation windows by a factor of 60. Note this also applies to the *time-invariant* fields, whose `stepRange` reads `0m`.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, plain `.grib2` with no bzip2 wrapper — the same as every other `/weather/nwp/v1/m/` product
- **GRIB2 packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library without libaec/CCSDS support cannot decode these files.
- **GRIB2 tables:** `tablesVersion = 19`, `localTablesVersion = 1`, `centre = edzw`, `missingValue = 9999`, `generatingProcessIdentifier = 16` (standard ICON-D2 uses 11 — a quick way to tell the two apart, since they share a grid and parameter names)
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-ruc/

### Path structure
- **Single-level and time-invariant:**
  `/p/{PARAM}/r/{YYYY-MM-DD}T{HH}%3A00/s/PT{hhh}H{mm}M.grib2`
- **Multi-level:**
  `/p/{PARAM}/lvt1/{25|100|103|150}/lv1/{level}/r/{run}/s/PT{hhh}H{mm}M.grib2`

The run token is an ISO-8601 timestamp with the colon percent-encoded as `%3A` and must be sent encoded. There is **no `e/` member segment** — that exists only in the ensemble counterpart.

Example: `https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-ruc/p/T/lvt1/150/lv1/65/r/2026-08-06T00%3A00/s/PT012H00M.grib2`

One GRIB message per file throughout.

### Every physical field carries a bitmap
Messages are bitmapped: `bitmapPresent = 1` with **16,968 of 542,040 cells masked (3.13%)** — the lateral boundary relaxation zone. Verified on `T_2M`, `TOT_PREC`, `WW`, `SYNMSG_BT_CL_IR10.8`, and `HHL` at levels 1 and 66. `CLAT`/`CLON` carry no bitmap and cover the full mesh, so zipping coordinate arrays against value arrays without applying the bitmap silently misaligns 3% of the domain. Readers that ignore `bitmapPresent` will see 9999 as a physical value around the domain rim.

### Retention, volume, and timing
- **Retention:** **24 runs**, a rolling ~24 h window. Re-verified 2026-10-18 ~21:45 UTC: 24 run directories from `2026-10-17T22:00` through `2026-10-18T21:00`.
- **Volume:** **~17.8 GB and 41,124 files per run** (2026-10-18; up from ~16.6 GB in August with an unchanged file count). At 24 runs per day, roughly **427 GB and 987,000 files per day** — about a fifth the daily volume of its own ensemble, which carries 20 members but far fewer levels.
- **Publication latency:** first files ~30 minutes after cycle time, complete ~41–43 minutes after.

### No regular lat–lon output
The `/weather/nwp/v1/m/icon-d2-ruc/` tree contains a single `p/` branch, and every file decodes as `unstructured_grid` on the R19B07 limited-area mesh. After 30 November 2026 this is true of the whole D2 family: standard [ICON-D2](./icon-d2.md) loses its 0.02° regular lat–lon rendering when its legacy tree shuts down.

Users needing lat–lon must interpolate:
- **Grid definition file:** `icon_grid_0047_R19B07_L.nc`, from https://icon-downloads.mpimet.mpg.de/dwd_grids.xml (MPI-M)
- **Regridding guide:** https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid — DWD's cdo instructions, which produce the weights file as a by-product
- **`/weather/lib/cdo/` is deleted after 30 November 2026.** It currently still holds `icon_grid_0047_R19B07_L.nc.bz2` and the prebuilt `ICON_D2_002_EASY.tar.bz2` 0.02° weights; DWD has stopped maintaining both.

---

## Notes
- ICON-D2-RUC is the rapid-update sibling of the standard [ICON-D2](./icon-d2.md) — same model core, same domain, same 2.2 km grid, same 65 levels and 22 km model top, but hourly cycling, a 27 h range, sub-hourly output, and two-moment microphysics. The two systems run in parallel, complementing rather than replacing each other.
- **Distribution tier.** ICON-D2-RUC was one of the first systems distributed under `/weather/nwp/v1/m/`, alongside [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md), [AICON-Global](../../global/germany/aicon-global.md), and the ICON-ART family. Since 2 September 2026 ICON, ICON-EU, ICON-D2 and their ensembles use the same scheme, and their older flat trees close after 30 November 2026. ICON-D2-RUC itself is unaffected by that shutdown except for the loss of `VIS`.
- The hourly update cadence is operationally rare. Internationally, only a small number of weather services run convection-permitting models in true rapid-update mode — peers include NOAA's HRRR (also hourly, 18–48 h depending on cycle) and experimental MeteoSwiss rapid-update configurations. DWD's combination of hourly DA, two-moment microphysics, 5-minute precipitation output, and ~40-minute availability sits in a thin field globally.
- The two-moment microphysics scheme is a meaningful difference from standard ICON-D2 for radar-related applications. Users comparing simulated reflectivity between the two systems should expect ICON-D2-RUC to show more realistic values at strong convective cores. The distributed field set makes this checkable: `QG`, `QH`, and `TQH` exist only here and in the RUC ensemble.
- **The 27-hour range is longer than the SINFONY framing suggests.** SINFONY targeted the 0–12 h nowcasting-to-NWP transition, and the sub-hourly output concentrates there, but the model itself runs to 27 h — the same range as the RUC ensemble, and long enough to cover the following day from any cycle.
- **DWD publishes no dedicated ICON-D2-RUC change-notice series.** Changes arrive through the ICON-D2 notices (which title themselves "ICON-D2/ICON-RUC (-EPS)") and the all-configuration ICON notices, which typically give the RUC its own effective time — e.g. "06 UTC forecast run for the global system, 09 UTC for ICON-D2 and 08 UTC for ICON-D2-RUC."
- The ensemble counterpart is [ICON-D2-RUC-EPS](../../../ensemble_models/regional/de/icon-d2-ruc-eps.md) — 20 members, same hourly cadence and 27 h range, but only model levels 56–65 and six pressure levels.

---

## Recent version history

> **Note on sourcing.** Earlier versions of this entry carried a "5 February 2025 — model version icon-2024.10-dwd-2.1" block listing TERRA_URB urban canopy activation, assimilation of ICOS tower data, and a retuned visibility diagnostic. The DWD ICON-D2 notice actually dated 5 February 2025 covers **only** a change of the GRIB2 `uuidOfVGrid` parameter caused by new compiler options, and explicitly states that model level heights do not change for ICON-D2 or ICON-RUC. TERRA_URB and ICOS do not appear in any ICON or ICON-D2 change notice from 2024–2026 checked here. The block below is rebuilt from primary notices; the TERRA_URB/ICOS items are **TBD** pending a locatable source.

> Model-change notices are covered through 18 February 2026. The 30 November 2026 entry is an Open Data distribution change; DWD's ICON-D2/ICON-RUC change notices since February have not yet been reviewed for this entry.

### 30 November 2026 (scheduled) — `VIS` withdrawn
Per DWD's 2 September 2026 Open Data newsletter, `VIS` is removed from the existing v1 delivery of ICON-D2-RUC and ICON-D2-RUC-EPS (and ICON-ART-EU) after 30 November, at the same time as it disappears from ICON-EU and ICON-D2. The same date also deletes `/weather/lib/cdo/`.

### 18 February 2026 — model version `icon-2025.04-dwd-4.0` (effective 09 UTC ICON-D2 run)
Revised ceiling diagnostic (cloud-overlap assumption consistent with the layer-wise cloud cover fractions; fill value now 16 km above ground, matching observation reports) and revised visibility diagnostic (reworked humidity contribution). Applies to all configurations producing these fields. ICON-D2-specific: additional SSO-corrected 10 m wind output; adaptive surface friction restricted to grid points with small SSO standard deviation.

### 23 July 2025 — model version `icon-2025.04-dwd-1.0` (effective 06 UTC, all ICON configurations)
Dissipative heating parameterization based on grid-scale kinetic energy loss; ocean warm-layer parameterization introducing a diurnal SST cycle; bug fix for rime deposition on snow-free ground; retuning of interception storage and ozone–tropopause coupling. The notice explicitly covers ICON-D2-RUC.

### 4 June 2025 — DACE 2.24 (effective 09 UTC assimilation / 12 UTC forecast)
Bugfix for station identifiers in the German supplemental SYNOP network (WIGOS identifiers had displaced the older "C" identifiers needed for blacklists and redundancy checks); preparations for NOAA-21 satellite data; quality-control fixes for radiosonde and aircraft humidity.

### 7 May 2025 — saturation vapour pressure coefficients, `icon-2024.10-dwd-4.0` (effective **08 UTC ICON-D2-RUC run**)
Magnus-formula Tetens coefficients replaced with the more accurate ECMWF set, reducing errors by at least a factor of four at temperatures far below freezing (the old coefficients approached 10% relative error near 200 K). Applied consistently across model, data assimilation, and verification. DWD flagged this as preparation for an upcoming cloud microphysics upgrade.

### 26 February 2025 — KENDA: full French radar network (effective 06 UTC assimilation / 09 UTC ICON-D2 forecast)
DACE 2.23 removed the computational cap on French radar assimilation; **all 14 French radar sites within the ICON-D2/RUC domain** are now assimilated, up from 9. Notice title explicitly covers ICON-RUC.

### 5 February 2025 — `uuidOfVGrid` change (all ICON configurations incl. ICON-RUC)
The GRIB2 `uuidOfVGrid` parameter changed due to new compiler options. Present only for model full- and half-level variables; pressure-level and single-level fields unaffected. Actual model level heights did **not** change for ICON-D2 or ICON-RUC. Consumers keying on `uuidOfVGrid` for vertical-grid identity needed to update at this date.

### 4 December 2024 — model version `icon-2024.10-dwd-2.0` (effective 09 UTC ICON-D2 run)
Extended adaptive parameter tuning (soil hydraulic diffusivity, land albedo, snow cover fraction diagnosis); revised treatment of snow cover in surface transfer calculation, reducing surface fluxes over snow beneath high vegetation.

### 12 July 2024 — operational launch
ICON-D2-RUC entered operations as the deterministic component of DWD's rapid-update chain, developed within the SINFONY project.

### 9 July 2024 — gust parameterization and radar DA, `icon-2024.01-dwd-3.1` (effective 09 UTC ICON-D2 run)
Immediately preceding the RUC launch and explicitly preparing for it:
- Gust parameterization revised to use 10-minute averages of 10 m wind speed instead of instantaneous values, with excess gust speed limited relative to the resolved maximum wind in the lowest 1500 m. RMS error reduced ~5%, with significant improvement in the 20 m s⁻¹ and 25 m s⁻¹ categories.
- Latent Heat Nudging input migrated to HDF5 OPERA and EUCOM composites.
- **Radar reflectivity observation errors changed from a constant 10 dBZ to a vertical profile** (7 dBZ near ground → 4 dBZ around 500 hPa, constant above) — a change made because it "was found to be beneficial for the ICON-RUC system," with near-neutral impact on ICON-D2 itself.

---

## Official documentation
- DWD ICON-D2 model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_d2/icon_d2_node.html
- DWD NWP forecast data overview: https://www.dwd.de/EN/ourservices/nwp_forecast_data/nwp_forecast_data.html
- DWD ICON-D2 change notices (cover ICON-D2/ICON-RUC (-EPS)): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_d2_gesamt.html
- DWD ICON change notices (all-configuration changes): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_gesamt.html
- DWD ICON Database Reference: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD SINFONY project page: https://www.dwd.de/EN/research/researchprogramme/sinfony/sinfony_node.html
- DWD Open Data newsletter, 2 September 2026 (v1 migration, VIS withdrawal, lib/cdo deletion): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD Open Data Server v1/m subtree: https://opendata.dwd.de/weather/nwp/v1/m/
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- ICON grid definition files (MPI-M): https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- DWD regridding guide for native ICON grids: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid

### Key references
- Seifert, A., and Beheng, K. D. (2006). *A two-moment cloud microphysics parameterization for mixed-phase clouds. Part 1: Model description.* Meteorology and Atmospheric Physics, 92, 45–66. https://doi.org/10.1007/s00703-005-0112-4
- Schraff, C., Reich, H., Rhodin, A., Schomburg, A., Stephan, K., Periáñez, A., and Potthast, R. (2016). *Kilometre-scale ensemble data assimilation for the COSMO model (KENDA).* Quarterly Journal of the Royal Meteorological Society, 142(696), 1453–1472. https://doi.org/10.1002/qj.2748
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378

---

*Live verification performed 2026-08-06 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-ruc/` (24 hourly cycles, 2026-08-05 03 UTC through 2026-08-06 02 UTC) and the `/weather/nwp/content.log.bz2` manifest, GRIB2 headers decoded with ecCodes 2.48.0. Re-checked 2026-10-18: parameter list, full step census of the 18 UTC run, grid header of the 21 UTC run (ecCodes 2.50.0), and retention/volume/latency for all 24 runs from the manifest.*
