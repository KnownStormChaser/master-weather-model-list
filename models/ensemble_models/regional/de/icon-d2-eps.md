# ICON-D2-EPS (ICON-D2 Regional Convection-Allowing Ensemble)

## What this model is
ICON-D2-EPS is DWD's operational convection-allowing regional ensemble prediction system for Germany and surrounding central European countries. It is the ensemble counterpart of DWD's deterministic [ICON-D2](../../../nwp_models/regional/germany/icon-d2.md) model, with the same 2.2 km horizontal resolution — fine enough to resolve deep convective processes explicitly without parameterization.

ICON-D2-EPS replaced the earlier COSMO-D2-EPS on 10 February 2021, when DWD migrated all aviation weather products from the COSMO model to ICON-D2. The system is specifically designed to capture probabilistic forecasts of hazardous weather events with strong fine-scale structure: thunderstorms, squall lines, heavy convective precipitation, flash-flood-inducing storms, and fine-scale topographic effects (Föhn winds, downslope winds, ground fog).

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country:** Germany

---

## What area it covers
- **Coverage:** Germany, Benelux (Belgium, the Netherlands, Luxembourg), Switzerland, Austria, and parts of neighbouring countries
- **Measured domain extent:** **4.16°W – 20.54°E, 43.04°N – 58.17°N** — decoded from the `CLAT`/`CLON` time-invariant cell-centre fields
- **Topography range:** −51.93 m to 4,080.44 m (`HHL` half-level 66, i.e. the surface, excluding masked cells)

---

## Basic details
- **Model type:** Regional convection-allowing ensemble prediction system
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) limited-area configuration
- **Dynamical formulation:** Non-hydrostatic, triangular icosahedral horizontal grid
- **Convection-allowing:** Yes — deep convection is explicitly resolved, not parameterized
- **Ensemble size:** **20 members, numbered 1–20. There is no separate unperturbed control run.**
- **Native horizontal grid:** R19B07 limited-area triangular grid, **542,040 cells**, 2.2 km
  - Verified from live GRIB2 headers (v1 feed, 2026-10-18 18 UTC run): `gridType = unstructured_grid`, `numberOfDataPoints = 542040`, `numberOfGridUsed = 47`, `uuidOfHGrid = c6b12daa91ad64045b26c1b6452a2a20`. The matching grid definition file is `icon_grid_0047_R19B07_L.nc` (`L` = limited area). Unlike the global and European ensembles, ICON-D2-EPS did not change grid in October 2026.
- **Public output grid:** Native triangular grid only. **No regular latitude–longitude output is distributed.**
- **Vertical levels:** **65 full levels** (66 `HHL` half-levels)
- **Model top:** **22,000 m** exactly — `HHL` half-level 1, spatially constant (`bitsPerValue = 0`). Half-level 2 is also flat, at 19,401.85 m.
- **Forecast length:** **48 hours**, all eight cycles
- **Update frequency / cycles:** 8× daily — 00, 03, 06, 09, 12, 15, 18, 21 UTC. All eight are published on Open Data, unlike the global and European ensembles where only four cycles reach the public server.
- **Temporal output resolution:** two cadences, identical across all eight cycles
  - **15-minutely, 0–48 h (193 steps):** `TOT_PREC`, `VMAX_10M`, `LPI`, `SDI_2`, `DBZ_850`, `DBZ_CMAX`
  - **Hourly, 0–48 h (49 steps):** everything else

> **Correction — temporal resolution.** Earlier versions gave the output as uniformly hourly. The six fields above have always had 15-minute output, but the legacy tree packed four quarter-hour steps into each hourly file — verified on a legacy `tot_prec` file, which holds 80 messages: 20 members × steps 12:00, 12:15, 12:30 and 12:45. The v1 feed publishes each quarter-hour step as its own file.

Apart from those six fields, the structure is still unusually regular: every cycle has the same parameter set and the same steps, with no reduced off-cycles.

---

## Data assimilation
- **Data assimilation:** Yes — its own, unlike [ICON-EU-EPS](./icon-eu-eps.md)
- **Method / cadence:** **KENDA** (Kilometre-scale ENsemble Data Assimilation), a Local Ensemble Transform Kalman Filter run at the same 2.2 km resolution, cycled hourly. KENDA assimilates a wide observation set including **volumetric radar reflectivity and radial wind** from the German C-band network and, since 2024–2025, from French radars via EUMETNET OPERA.

The first 20 members of the KENDA analysis ensemble supply the initial conditions, including stochastically perturbed soil moisture fields.

This is the structural distinction from [ICON-EU-EPS](./icon-eu-eps.md), which has no regional analysis of its own and is simply the nest portion of the global ensemble integration.

---

## Initial and boundary conditions
- **Initial conditions:** KENDA / LETKF analysis ensemble at 2.2 km (members 1–20), with perturbed soil moisture.
- **Boundary conditions:** ICON-EPS / ICON-EU-EPS forecasts, initialized 3 hours before the ICON-D2-EPS initial time. The driving ensembles moved to finer grids (~20 km global, ~10 km nest) on 6 October 2026.

> **Sourcing note on "various global models".** DWD's NWP forecast data page states that forecasts from various global models are used for varying the lateral boundary conditions and the initial state. Earlier versions of this entry read that as evidence that DWD had diversified boundary sources beyond ICON-EPS. The technical literature does not support that reading: COSMO General Meeting material, EMS 2022 conference abstracts, and published ICON-D2-EPS studies all describe boundaries as coming from ICON-EPS or ICON-EU-EPS alone. The multi-model phrasing appears to be inherited from **COSMO-DE-EPS**, which genuinely did draw boundaries from four different global models before 2017, when it switched to ICON-EPS. Treat "various global models" as legacy website wording rather than a current configuration statement. **TBD** — worth a direct question to DWD if it matters for a given application.

---

## Perturbations and design
Four perturbation channels, per DWD's own description:

- **Lateral boundary conditions:** varied across members (see above).
- **Initial state:** KENDA/LETKF analysis ensemble members 1–20.
- **Soil moisture:** stochastically perturbed at initialization.
- **Model physics:** randomized parameter perturbations. A set of physics tuning parameters is drawn per member from defined ranges before each run and held fixed for the integration — the same static-parameter approach used throughout the ICON ensemble family. No SPPT, SPP, or SKEB.

Because ICON-D2 is convection-allowing, it resolves deep convective features that are parameterized at coarser scales. Error growth at small spatial and temporal scales is correspondingly faster, which is what makes probabilistic guidance especially valuable for short-range severe-weather forecasting.

---

## What it provides

**92 parameter directories** in the v1 tree — the legacy tree's 93 minus `VIS`, which DWD did not carry over (see below). By far the richest of DWD's 3-hourly ensembles (against 22 for [ICON-EPS](../../global/de/icon-eps.md) and 28 for [ICON-EU-EPS](./icon-eu-eps.md)). Verified live 2026-10-18:

| Level type (v1 `lvt1`) | Levels | Parameters |
|---|---|---|
| Single-level (no `lvt1`) | — | 66 forecast fields + 13 time-invariant |
| Pressure level (`100`) | **500, 700, 850, 950, 975, 1000 hPa** (in Pa in the path) | `FI`, `T`, `U`, `V`, `RELHUM`, `OMEGA` |
| Model level (`150`) | **56–65** (lowest 10 of 65) | `T`, `U`, `V`, `QV` |
| Model half-level (`150`) | **56–66** | `HHL` |
| Soil level (`106`) | `T_SO`: 0, 0.005, 0.02, 0.06, 0.18, 0.54, 1.62, 4.86, 14.58 m; `W_SO`/`W_SO_ICE`/`SMI`: 0, 0.01, 0.03, 0.09, 0.27, 0.81, 2.43, 7.29 m | `T_SO`, `W_SO`, `W_SO_ICE`, `SMI` |
| Radar reflectivity threshold (`25`) | 18 dBZ | `ECHOTOP` |
| Time-invariant | — | `CLAT`, `CLON`, `ELAT`, `ELON`, `HSURF`, `FR_LAND`, `FR_LAKE`, `FR_ICE`, `DEPTH_LK`, `SOILTYP`, `PLCOV`, `LAI`, `ROOTDP` |

> **Correction.** Earlier versions listed the model-level set as "`t`, `u`, `v`, `w`, `qv`, `relhum`, `tke`-family". There is no `W`, `RELHUM` or `TKE` on model levels in either download tree; `RELHUM` exists only on pressure levels.

**`HHL` is reduced in the v1 feed:** only half-levels **56–66** are published, per member. The legacy tree carried all 66 half-levels, which is where the 22 km model top above comes from; those files end with the legacy tree after 30 November 2026.

Convection-allowing and severe-weather diagnostics are the distinguishing content: **`LPI` / `LPI_MAX`** (Lightning Potential Index), **`UH_MAX`** (updraught helicity), **`VORW_CTMAX`**, **`W_CTMAX`**, **`DBZ_850` / `DBZ_CMAX` / `DBZ_CTMAX`** (simulated radar reflectivity), **`ECHOTOP`**, **`CEILING`**, **`SDI_2`** (supercell detection index), **`TCOND_MAX` / `TCOND10_MX`**, **`CAPE_ML` / `CIN_ML`**, **`HBAS_SC` / `HTOP_SC`**, and **`GRAU_GSP`** (graupel).

**`VIS` (near-surface visibility) is not in the v1 tree** and disappears entirely after 30 November 2026. DWD's aviation weather service judged the ICON visibility field unsuitable for forecasting visibility under visual flight rules, and does not use it in its own aviation products. `CEILING` remains.

Time-integration conventions, verified by decoding `stepType`/`stepRange`:

| Convention | `stepType` | PDT | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | 11 | `TOT_PREC` (`0m-735m` at `PT012H15M`), `RAIN_GSP`, `RAIN_CON`, `SNOW_GSP`, `GRAU_GSP`, `RUNOFF_S` |
| Averaged from run start | `avg` | 11 | `ASOB_S`, `ATHB_S`, `ASWDIR_S`, `ALHFL_S` (the `A*` flux family) |
| Max over the preceding 15 minutes | `max` | 11 | `VMAX_10M` (`720m-735m` at `PT012H15M`) |
| Max over the preceding hour | `max` | 11 | `LPI_MAX`, `UH_MAX`, `W_CTMAX` |
| Max/min since last 6 h boundary | `max` / `min` | 11 | `TMAX_2M` (`6-12`), `TMIN_2M` |
| Instantaneous | `instant` | 1 | `T_2M`, `PMSL`, `CAPE_ML`, `DBZ_CMAX`, and all pressure-, model-, and soil-level fields |

**Quarter-hour steps are encoded in minutes** (`stepUnits = m`, `stepRange = 0m-735m`), hourly steps in hours. Tooling that assumes hours throughout will misread the 15-minute accumulation and maximum windows by a factor of 60 — the same hazard as [ICON-D2-RUC-EPS](./icon-d2-ruc-eps.md).

Note the different max conventions: `VMAX_10M` covers 15 minutes, convective maxima cover one hour, and `TMAX_2M`/`TMIN_2M` reset every six hours.

Probability, percentile, mean, and spread products are **not** distributed — the public feed carries raw member fields only. All ensemble statistics must be computed by the user from the 20 members.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, plain `.grib2` (no bzip2 wrapper), **one member per file**
- **Official download location:** https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-eps/

### Path structure (v1)
- **Single-level and time-invariant:**
  `/p/{PARAM}/r/{YYYY-MM-DD}T{HH}%3A00/e/{01..20}/s/PT{hhh}H{mm}M.grib2`
- **Multi-level:**
  `/p/{PARAM}/lvt1/{25|100|106|150}/lv1/{level}/r/{run}/e/{member}/s/PT{hhh}H{mm}M.grib2`

Member directories are zero-padded (`01`–`20`) and match `perturbationNumber`. The run token's colon is percent-encoded as `%3A`. Example:

`https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-eps/p/T_2M/r/2026-10-18T18%3A00/e/01/s/PT012H00M.grib2`

- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 — covers the v1 tree.
- **Retention:** each run has its own directory; ~24 h rolling window across the eight cycles.
- **Volume (v1):** **~118 GB and 193,120 files per run**, essentially constant across all eight cycles — about **945 GB/day**. The legacy tree carried the same content in ~100 GB and 8,752 files per run; the per-member and per-quarter-hour split multiplies the file count by 22.
- **Publication latency (v1):** first files ~44 min after cycle time; run completes ~1 h 40–41 min after. Measured 2026-10-18, e.g. 00 UTC 00:43:56 → 01:40:53, 12 UTC 12:43:55 → 13:40:57 UTC. That is **about 28 minutes earlier** than the legacy tree, which still starts at ~1 h 31 min and finishes at ~2 h 09 min.

### Member packaging and encoding (v1)
- **Packaging:** **one GRIB message per file, one member per file**, under `e/{member}/`. The legacy tree concatenated all 20 members (and, for the 15-minute fields, four quarter-hour steps) into one file.
- **Member indexing:** `perturbationNumber` runs **1 to 20**. There is no member 0 and no separate control forecast.
- **GRIB2 encoding:** `productDefinitionTemplateNumber = 1` for instantaneous fields and `11` for time-processed fields; `typeOfEnsembleForecast = 192` (DWD local code); `numberOfForecastsInEnsemble = 20`; `typeOfProcessedData = cp`; `generatingProcessIdentifier = 11` (the same as deterministic ICON-D2); `centre = edzw`; `tablesVersion = 19`, `localTablesVersion = 1`; `missingValue = 9999`.
- **Packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library built without libaec/CCSDS support cannot decode these files.

### Every physical field carries a bitmap
ICON-D2-EPS messages are **bitmapped**: `bitmapPresent = 1`, **16,968 of 542,040 cells masked (3.13%)** — the lateral boundary relaxation zone, where the limited-area solution is nudged toward the driving model. `CLAT`/`CLON` carry no bitmap and cover the full mesh. Two consequences:

- **Readers that ignore `bitmapPresent` will read 9999 as a physical value.** A 9999 K 2 m temperature or 9999 mm of precipitation around the domain rim is the signature.
- **Coordinate arrays and value arrays are different lengths in effect.** Zipping `CLAT`/`CLON` against values without applying the bitmap silently misaligns 3% of the domain.

### Legacy tree — discontinued after 30 November 2026
The old layout at https://opendata.dwd.de/weather/nwp/icon-d2-eps/grib/ stops after **30 November 2026** (DWD Open Data newsletter, 2 September 2026). Until then:

- Layout: `/<cycle>/<parameter>/icon-d2-eps_germany_icosahedral_<leveltype>_<YYYYMMDDHH>_<step>_<level|2d>_<param>.grib2.bz2` (single-level files carry the literal token `2d` in the level position)
- All 20 members in one bzip2-wrapped file per parameter, level and step; 15-minute fields bundled in hourly files
- `VIS` present; full 66-level `HHL`
- ~100 GB / 8,752 files per run

### No regular lat–lon output
Neither download tree has ever carried a lat–lon rendering of ICON-D2-EPS; DWD's product description says it is provided on the native triangular grid. After 30 November 2026 the deterministic [ICON-D2](../../../nwp_models/regional/germany/icon-d2.md) loses its lat–lon product too, so the whole D2 family will be native-grid only.

- **Grid definition file:** `icon_grid_0047_R19B07_L.nc`, from https://icon-downloads.mpimet.mpg.de/dwd_grids.xml (MPI-M)
- **Regridding guide:** https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid (cdo-based; generates the weights file as a by-product)
- **`/weather/lib/cdo/` is deleted after 30 November 2026**, including `icon_grid_0047_R19B07_L.nc.bz2` and the prebuilt `ICON_D2_002_EASY.tar.bz2` 0.02° weights.

---

## Relationship to other models
- **[ICON-D2](../../../nwp_models/regional/germany/icon-d2.md):** deterministic counterpart, sharing model core, domain, resolution, vertical structure, and cycle schedule. Its 0.02° lat–lon product ends after 30 November 2026.
- **[ICON-EPS](../../global/de/icon-eps.md) / [ICON-EU-EPS](./icon-eu-eps.md):** the global and European ensembles supplying lateral boundary conditions. ICON-D2-EPS has 20 members against their 40 — member *n* of ICON-D2-EPS is not the downscaled member *n* of the global ensemble, because KENDA supplies independent initial conditions.
- **[ICON-D2-RUC-EPS](./icon-d2-ruc-eps.md):** the hourly-updating sibling, running to +27 h with 5-minute precipitation output and two-moment microphysics. The two run in parallel and serve complementary forecast windows.
- **[ICON-CH1-EPS / ICON-CH2-EPS](../../../nwp_models/regional/switzerland/icon-ch-eps.md)** (MeteoSwiss): the closest international peers, also ICON-based limited-area ensembles with KENDA-family assimilation, at 1 km / 11 members and 2.1 km / 21 members respectively. MeteoSwiss distributes via a STAC API rather than a directory tree and does publish a control member.

---

## Notes
- ICON-D2-EPS is the ensemble counterpart of the deterministic ICON-D2 — see that entry for the shared model core and operational details.
- **All eight cycles are published.** This is the only 3-hourly DWD ICON ensemble whose full cycle schedule reaches Open Data; the global and European ensembles publish four of eight.
- **No control member.** `perturbationNumber` starts at 1 and every member is perturbed. Products expecting a control/perturbed split (common when ingesting ECMWF ENS or GEFS) need adapting.
- **File count is now the binding constraint.** ~193,000 files per run and ~1.5 million per day in the v1 feed, against ~70,000 per day in the legacy tree.
- The system was renamed from COSMO-D2-EPS to ICON-D2-EPS on 10 February 2021 as part of DWD's migration from the COSMO model family to the ICON family. Older references and some DWD documentation pages still use the COSMO-D2-EPS name.
- At 2.2 km with 20 members and radar-volume assimilation, ICON-D2-EPS is among the higher-resolution operational convection-allowing ensembles in production internationally. Comparable systems include MeteoSwiss ICON-CH1-EPS (1 km, 11 members) and Météo-France AROME-EPS.
- **DWD's English ensemble-prediction page is stale.** Its ICON-D2-EPS section still describes COSMO-D2-EPS throughout. Prefer the ICON Database Reference and the change notices.

---

## Recent version history

ICON-D2-EPS is upgraded partly through its own ICON-D2 change-notice series (which explicitly covers "ICON-D2 (-EPS)") and partly through the all-configuration ICON notices. Both are reflected below; see the [ICON Global entry](../../../nwp_models/global/germany/icon-global.md#recent-version-history) for the full ICON sequence.

> Model-change notices are covered through 18 February 2026. Later entries are Open Data distribution changes; DWD's ICON-D2 change notices since February have not yet been reviewed for this entry.

### 30 November 2026 (scheduled) — legacy tree, `VIS` and `lib/cdo` withdrawn
Per DWD's 2 September 2026 newsletter: delivery under `/weather/nwp/icon-d2-eps/` stops after 30 November; `VIS` is withdrawn from all ICON Open Data; `/weather/lib/cdo/` is deleted.

### 2 September 2026 — v1 URL scheme
Published additionally under `/weather/nwp/v1/m/icon-d2-eps/` (directory created 24 August 2026): one member and one quarter-hour step per file, plain CCSDS GRIB2, ISO 8601 run/step tokens. `VIS` not carried over; `HHL` reduced to half-levels 56–66. Runs complete about half an hour earlier than in the legacy tree.

### 16 June 2026 — CCSDS compression (effective 09 UTC run)
All ICON, ICON-EPS, ICON-D2, and ICON-D2-EPS GRIB2 output switched to lossless CCSDS packing.

### 18 February 2026 — model version `icon-2025.04-dwd-4.0` (effective 09 UTC run)
- **Revised ceiling diagnostic:** now uses a cloud-overlap assumption consistent with the layer-wise cloud cover fractions (`CLCL`, `CLCM`, …), diagnosing ceiling as the lowest layer where upward-integrated cloud fraction exceeds 50%. The fill value for grid points with too little cloud changed to **16 km above ground**, matching observation reports, rather than the height above sea level of the uppermost model level.
- **Revised visibility diagnostic:** the humidity contribution (relevant absent fog and precipitation) was reworked.
- **ICON-D2-specific:** additional output field for SSO-corrected 10 m winds; adaptive surface friction restricted to grid points with small SSO standard deviation.

### 23 July 2025 — model version `icon-2025.04-dwd-1.0` (effective 06 UTC run, all ICON configurations including all EPS)
Dissipative heating parameterization based on grid-scale kinetic energy loss (reduces the boundary-layer winter cold bias); ocean warm-layer parameterization introducing a diurnal SST cycle; bug fix for rime deposition on snow-free ground; retuning of interception storage and ozone–tropopause coupling.

### 26 February 2025 — KENDA: full French radar network (effective 06 UTC assimilation / 09 UTC forecast)
DACE 2.23 removed the computational limit that had capped French radar assimilation at 9 sites; **all 14 French radar sites within the ICON-D2/RUC domain** are now assimilated. Measurable Fraction Skill Score improvement for precipitation in the south-western quarter of the domain.

### 22 May 2024 — KENDA: French radar volumes introduced (effective 06 UTC assimilation / 09 UTC forecast)
First assimilation of volumetric reflectivity and radial wind from **9 French radar stations** via EUMETNET OPERA, extending beyond the German C-band network. Targeted at heavy-precipitation systems approaching Germany from the south-west. Also: removal of a rudimentary bias correction for near-saturated humidity observations, and correction of the vertical influence of SYNOP observations in the LETKF.

### 24 January 2024 — sea-ice bottom heat flux, model version `icon-2.6.6-nwp2` (effective 12 UTC run)
Sea-ice scheme revised to account for ocean-to-ice heat flux; external parameter bugfix for false glacier points; adaptive time-step reduction extended to horizontal CFL exceedances.

### 10 February 2021 — COSMO-D2-EPS → ICON-D2-EPS
The convection-permitting ensemble migrated from the COSMO core to the ICON limited-area configuration, alongside the deterministic model. All aviation weather products migrated at the same time. Forecast length subsequently settled at 48 h (the pre-operational ICON-D2-EPS configuration ran to 27 h, with 45 h at 03 UTC).

### Earlier history
The lineage runs COSMO-DE-EPS (pre-operational December 2010, 2.8 km, boundaries from **four different global models**) → COSMO-D2-EPS (May 2018, higher resolution and larger domain; boundaries switched to ICON-EPS in 2017, initial conditions from KENDA) → ICON-D2-EPS (February 2021).

---

## Official documentation
- DWD ICON-D2 model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_d2/icon_d2_node.html
- DWD NWP forecast data overview (ICON-D2-EPS product description): https://www.dwd.de/EN/ourservices/nwp_forecast_data/nwp_forecast_data.html
- DWD ensemble prediction overview (⚠️ still describes COSMO-D2-EPS — see Notes): https://www.dwd.de/EN/research/weatherforecasting/num_modelling/04_ensemble_methods/ensemble_prediction/ensemble_prediction_node.html
- DWD ICON-D2 change notices: https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_d2_gesamt.html
- DWD ICON change notices (all-configuration changes): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_gesamt.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD Open Data newsletter, 2 September 2026 (v1 URL scheme, legacy shutdown, VIS, lib/cdo): https://www.dwd.de/DE/leistungen/opendata/neuigkeiten/opendata_september2026_1.html
- DWD Open Data v1 tree: https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-eps/
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- ICON grid definition files (MPI-M): https://icon-downloads.mpimet.mpg.de/dwd_grids.xml
- DWD regridding guide for native ICON grids: https://gitlab.opencode.de/dwd/open-data/regrid-native-icon-grid

### Key references
- Reinert, D., et al. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*
- Schraff, C., Reich, H., Rhodin, A., Schomburg, A., Stephan, K., Periáñez, A., and Potthast, R. (2016). *Kilometre-scale ensemble data assimilation for the COSMO model (KENDA).* Quarterly Journal of the Royal Meteorological Society, 142(696), 1453–1472. https://doi.org/10.1002/qj.2748
- Gebhardt, C., Theis, S. E., Paulat, M., and Ben Bouallègue, Z. (2011). *Uncertainties in COSMO-DE precipitation forecasts introduced by model perturbations and variation of lateral boundaries.* Atmospheric Research, 100(2–3), 168–177.
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378

---

*Live verification performed 2026-10-18 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-eps/` (parameter and step census of the 18 UTC run, member 01; all eight cycles in the manifest), the legacy `https://opendata.dwd.de/weather/nwp/icon-d2-eps/grib/` tree, and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.50.0.*
