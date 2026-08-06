# ICON-D2-RUC-EPS (ICON-D2 Rapid Update Cycle Convection-Permitting Ensemble)

## What this model is
ICON-D2-RUC-EPS is DWD's hourly-updating convection-permitting regional ensemble for Germany and neighbouring countries. It is the ensemble counterpart of the deterministic [ICON-D2-RUC](../../../nwp_models/regional/germany/icon-d2-ruc.md), running 20 members at 2.2 km with a fresh initialisation every hour.

With hourly initialisation and dense radar data assimilation, ICON-D2-RUC-EPS sits at the boundary between nowcasting and short-range NWP, where probabilistic guidance is operationally most valuable. Its output cadence reflects that positioning: precipitation fields are published **every 5 minutes** and convective diagnostics every 15 minutes, against hourly for the standard field set.

ICON-D2-RUC-EPS is the operational descendant of work carried out within DWD's SINFONY (Seamless INtegrated FOrecastiNg sYstem) project, which targeted seamless ensemble prediction across the nowcasting-to-NWP transition. Earlier versions appear in DWD literature under the name "SINFONY-RUC-EPS."

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country:** Germany

---

## What area it covers
- **Coverage:** Germany, Benelux (Belgium, the Netherlands, Luxembourg), Switzerland, Austria, and parts of neighbouring countries — the same domain as ICON-D2-RUC and [ICON-D2](../../../nwp_models/regional/germany/icon-d2.md)
- **Domain extent:** 4.16°W – 20.54°E, 43.04°N – 58.17°N (identical mesh to [ICON-D2-EPS](./icon-d2-eps.md))
- **Topography range:** −51.93 m to 4,080.44 m (`HHL` half-level 66, excluding masked cells)

---

## Basic details
- **Model type:** Regional convection-permitting ensemble prediction system, rapid update cycle
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) limited-area configuration
- **Dynamical formulation:** Non-hydrostatic, triangular icosahedral horizontal grid
- **Convection-allowing:** Yes
- **Ensemble size:** **20 members, numbered 1–20. There is no separate unperturbed control run.**
- **Native horizontal grid:** R19B07 limited-area triangular grid, **542,040 cells**, 2.2 km — byte-identical grid identity to [ICON-D2-EPS](./icon-d2-eps.md)
  - Verified from live GRIB2 headers: `gridType = unstructured_grid`, `numberOfDataPoints = 542040`, `numberOfGridUsed = 47`, `uuidOfHGrid = c6b12daa91ad64045b26c1b6452a2a20`. Matching grid file: `icon_grid_0047_R19B07_L.nc.bz2` under the CDO library path below.
- **Vertical levels:** 65 full levels in the model; **only the lowest 10 (56–65) are published**, plus 11 `HHL` half-levels (56–66)
- **Cloud microphysics:** Two-moment bulk scheme (Seifert and Beheng), inherited from the deterministic ICON-D2-RUC — the distinguishing physics choice against ICON-D2-EPS
- **Forecast length:** **27 hours**, every run
- **Update frequency / cycles:** **Hourly, 24× daily.** All 24 cycles are published.
- **Temporal output resolution:** three cadences — see below
- **Operational lineage:** developed within the SINFONY project as SINFONY-RUC-EPS; deployed alongside ICON-D2-RUC's operational implementation in mid-2024

> **Correction to earlier versions of this entry.** This entry previously gave the forecast length as **+14 hours**, "matching the deterministic ICON-D2-RUC". Live enumeration shows **27 hours** for every run — verified across ten consecutive cycles on 2026-08-05/06, each reaching `PT027H00M`. The deterministic ICON-D2-RUC also runs to 27 h, so the cross-reference was wrong in both halves.
>
> The 14-hour figure is not arbitrary, though: **+14 h is exactly where the high-cadence nowcasting window ends** for the sub-hourly upper-air fields (see below). It appears to be the SINFONY nowcasting horizon mistaken for the forecast length. The deterministic [ICON-D2-RUC entry](../../../nwp_models/regional/germany/icon-d2-ruc.md) carries the same claim and needs the same correction.

### Output cadence
Three distinct schedules, reflecting the nowcasting orientation. Verified from the 2026-08-06 00 UTC run:

| Cadence | Steps per member | Fields |
|---|---|---|
| **5-minutely**, 0–27 h | 325 | `TOT_PREC`, `TOT_PR`, `PREC_GSP`, `PR_GSP` — instantaneous and accumulated precipitation |
| **15-minutely**, 0–27 h | 109 | The convective/severe-weather set: `DBZ_850`/`DBZ_CMAX`/`DBZ_CTMAX`, `DBZLMX_LOW`, `ECHOTOP`, `ECHOTOPinM`, `LPI`/`LPI_MAX`, `CAPE_ML`/`CIN_ML`, `UH_MAX`/`UH_MAX_LOW`/`UH_MAX_MED`, `SDI_2`, `MCONV`, `TCOND_MAX`/`TCOND10_MX`, `W_CTMAX`, `VORW_CTMAX`, hail fields (`HAIL_GSP`, `KE_HAIL_S`, `KEF_HAIL_MAX_S`, `DEMAX_HAIL_S`, `DEMAX_HAIL_TMAX_S`), `GRAU_GSP`, `RAIN_GSP`, `SNOW_GSP`, the `PR*_GSP` rate family, column-integrated `TQ*`, `VIS`, `CEILING`, `ASOB_S`/`ASWDIR_S`/`ASWDIFD_S` |
| **Hourly**, 0–27 h | 28 | Standard surface and upper-air: `T_2M`, `TD_2M`, `RELHUM_2M`, `QV_2M`, `U_10M`/`V_10M`, `VMAX_10M`, `PMSL`, `PS`, `T_G`, `CLCT`/`CLCH`/`CLCM`/`CLCL`, `CAPE_MU`/`CIN_MU`, `LAPSE_RATE`, `H_SNOW`, `TOT_PREC_D`, and the model-level and pressure-level fields |

**Three specific levels break the pattern.** `QV` on model level **63**, and `W` and `RELHUM` at **500 and 700 hPa**, are published **15-minutely from 0 to 14 h and hourly from 15 to 27 h** (70 steps rather than 28) — while every other level of those same parameters is hourly throughout. This is a deliberate nowcasting subset for mid-level moisture and vertical motion, and it is the origin of the mistaken "+14 hours" forecast length. Code that assumes a uniform step list per parameter will find extra files at these three levels.

Time-invariant fields (`CLAT`, `CLON`, `ELAT`, `ELON`, `HSURF`, `FR_LAND`, `FR_LAKE`, `FR_ICE`, `DEPTH_LK`, `SOILTYP`, `PLCOV`, `LAI`, `ROOTDP`) carry one file per run; `HHL` carries 11 (one per published half-level).

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** **KENDA** (Kilometre-scale ENsemble Data Assimilation) LETKF at 2.2 km, cycled **hourly** to match the forecast cadence.

The SINFONY development line uses extensive remote-sensing assimilation: 3D radar volume scans of radial winds and reflectivity, cell objects, Meteosat VIS channels, and lightning observations. As with [ICON-D2-EPS](./icon-d2-eps.md), the radar input expanded beyond the German C-band network to include French radars via EUMETNET OPERA — 9 sites from May 2024, all 14 in-domain sites from February 2025.

---

## Initial and boundary conditions
- **Initial conditions:** hourly KENDA-LETKF analysis ensemble at 2.2 km, with stochastically perturbed soil moisture.
- **Boundary conditions:** forecasts from the larger-domain ICON ensembles, typically [ICON-EU-EPS](./icon-eu-eps.md).

> **Sourcing note.** As with [ICON-D2-EPS](./icon-d2-eps.md#initial-and-boundary-conditions), DWD's public wording about boundary sources is generic ("forecasts from various global models"), while the technical literature consistently describes ICON-EPS / ICON-EU-EPS. The multi-model phrasing appears to be inherited from the COSMO-DE-EPS era. **TBD** — worth a direct question to DWD if it matters for a given application.

---

## Perturbations and design
Member generation follows the ICON ensemble approach used in [ICON-D2-EPS](./icon-d2-eps.md), adapted to the rapid-update cycle:

- **Initial state perturbations:** from the hourly KENDA-LETKF ensemble data assimilation cycle
- **Lateral boundary conditions:** from larger-domain ensemble forecasts
- **Soil moisture:** stochastically perturbed at initialisation
- **Model physics:** randomized physics parameter perturbations across members, held fixed for each integration. No SPPT, SPP, or SKEB.

Because the system is convection-allowing at 2.2 km and runs hourly, error growth at small spatial and temporal scales is rapid. The hourly cadence partially compensates by forcing the ensemble back to observed reality each hour, while still preserving the spread needed for probabilistic guidance.

---

## What it provides

**99 parameter directories**, the largest published set of any DWD ensemble. Level structure verified live 2026-08-06:

| Level type (`lvt1`) | Meaning | Levels | Parameters |
|---|---|---|---|
| `150` | General vertical (model) layer | **56–65** (66 for `HHL`) | `T`, `U`, `V`, `P`, `QV`, `QC`, `QC_DIA`, `QI_DIA`, `CLC`, `HHL` |
| `100` | Isobaric surface (Pa) | **50000, 70000, 85000, 95000, 97500, 100000** | `FI`, `RELHUM`, `W` |
| `103` | Height above ground (m) | **1000, 3000, 6000** | `SRH`, `WSHEAR_U`, `WSHEAR_V` |
| `25` | Radar reflectivity threshold (dBZ) | **18, 30** | `ECHOTOP`, `ECHOTOPinM` |

The `lvt1/25` level type is unusual and worth noting: `ECHOTOP` and `ECHOTOPinM` are indexed by **reflectivity threshold** (18 and 30 dBZ) rather than by a physical height or pressure — the level value is the dBZ contour whose top is being reported.

Content distinctive to this system relative to [ICON-D2-EPS](./icon-d2-eps.md): a full **hail diagnostic family** (`HAIL_GSP`, `KE_HAIL_S` kinetic energy, `KEF_HAIL_MAX_S`, `DEMAX_HAIL_S`, `DEMAX_HAIL_TMAX_S`), **`TQH`** (column-integrated hail, from the two-moment microphysics), **`WSHEAR_U`/`WSHEAR_V`** and **`SRH`** (storm-relative helicity) on three shear layers, **`DBZLMX_LOW`**, **`MCONV`** (moisture convergence), and **`ECHOTOPinM`** alongside the pressure-based `ECHOTOP`.

Time-integration conventions, verified by decoding at +12 h:

| Convention | `stepType` | PDT | Notes |
|---|---|---|---|
| Accumulated from run start | `accum` | 11 | `TOT_PREC` at `PT012H05M` decodes as `stepRange = 0m-725m` with `stepUnits = 0` — **minutes**, not hours |
| Instantaneous | `instant` | 1 | `T_2M`, `PMSL`, model- and pressure-level fields |

**The minute-resolution step encoding is the key parsing hazard.** Because output is sub-hourly, `stepUnits` is 0 (minutes) on time-processed fields and `stepRange` is expressed in minutes (`0m-725m` = 0 to 12 h 05 min). Tooling that assumes `stepUnits = 1` (hours) will misread accumulation windows by a factor of 60.

Probability, percentile, mean, and spread products are **not** distributed — raw member fields only.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, **uncompressed** — plain `.grib2`, like the other `/weather/nwp/v1/m/` products and unlike the bzip2-wrapped flat trees
- **GRIB2 packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library without libaec/CCSDS support cannot decode these files.
- **GRIB2 tables:** `tablesVersion = 19`, `localTablesVersion = 1`, `centre = edzw`, `missingValue = 9999`
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-ruc-eps/

### Path structure
Three layouts. Note the **`e/` member segment**, which the other `v1/m/` products do not have.

- **Single-level:**
  `/p/{PARAM}/r/{YYYY-MM-DD}T{HH}%3A00/e/{01..20}/s/PT{hhh}H{mm}M.grib2`
- **Multi-level:**
  `/p/{PARAM}/lvt1/{25|100|103|150}/lv1/{level}/r/{run}/e/{member}/s/PT{hhh}H{mm}M.grib2`
- **Time-invariant:** as single-level, with a single `PT000H00M` step

The run token is an ISO-8601 timestamp with the colon percent-encoded as `%3A` and must be sent encoded. Member directories are **zero-padded two-digit** (`01`, not `1`); `e/0/` does not exist.

Example: `https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-ruc-eps/p/T/lvt1/150/lv1/65/r/2026-08-06T00%3A00/e/01/s/PT012H00M.grib2`

### Member packaging (verified)
Opposite convention from every other DWD ensemble in this catalog.

- **Packaging:** **one member per file, one GRIB message per file.** Members are split across separate path segments rather than concatenated. [ICON-EPS](../../global/de/icon-eps.md), [ICON-EU-EPS](./icon-eu-eps.md), and [ICON-D2-EPS](./icon-d2-eps.md) all bundle all members into a single file per parameter-step.
- **Member indexing:** the `e/` path segment matches `perturbationNumber` — `e/01/` → `perturbationNumber = 1`, `e/20/` → 20. There is no member 0.
- **GRIB2 encoding:** `productDefinitionTemplateNumber = 1` for instantaneous fields and `11` for time-processed fields; `numberOfForecastsInEnsemble = 20`; `typeOfProcessedData = cp`.

### Every physical field carries a bitmap
As with [ICON-D2-EPS](./icon-d2-eps.md), messages are bitmapped: `bitmapPresent = 1` with **16,968 of 542,040 cells masked (3.13%)** — the lateral boundary relaxation zone. Verified on `T_2M`, `TOT_PREC`, and `HHL` at levels 56 and 66. Readers that ignore `bitmapPresent` will read 9999 as a physical value around the domain rim, and coordinate arrays from `CLAT`/`CLON` cover the full mesh while the data does not.

### Retention, volume, and timing
- **Retention:** approximately **24–25 runs**, a rolling ~24 h window. Observed 2026-08-06 02:42 UTC: 25 run directories from `2026-08-05T02:00` through `2026-08-06T02:00`.
- **Volume:** **~86.4 GB and 213,660 files per run**. At 24 runs per day this is roughly **2.1 TB and 5.1 million files per day** — by a wide margin the largest product on the DWD Open Data server, and about 2.7× the daily volume of ICON-D2-EPS. Mirroring even a modest subset requires care: the file count, not the byte count, is usually the binding constraint.
- **Publication latency:** first files ~30 minutes after cycle time, run complete ~46 minutes after. Measured on the 2026-08-06 00 UTC run: 00:29:48 → 00:45:54 UTC. This is the fastest turnaround of any DWD ensemble, as required by an hourly cycle.

### The uncompressed format is a deliberate trade-off
Files are served as plain `.grib2` with no bzip2 wrapper. Compressing one externally recovers about 8% (a 757,332-byte `T_2M` message compresses to 693,052 bytes) — consistent with CCSDS having already done the work. Given 213,660 files per run, skipping compression saves DWD meaningful CPU on both ends at negligible transfer cost.

### No regular lat–lon output
The `/weather/nwp/v1/m/icon-d2-ruc-eps/` tree contains a single `p/` branch, and every file decodes as `unstructured_grid` on the R19B07 limited-area mesh. Users needing lat–lon must interpolate; DWD ships the tooling:
- https://opendata.dwd.de/weather/lib/cdo/
  - `icon_grid_0047_R19B07_L.nc.bz2` — grid description matching `numberOfGridUsed = 47`
  - `ICON_D2_002_EASY.tar.bz2` — CDO weights to a 0.02° target grid over the D2 domain

---

## Relationship to other models
- **[ICON-D2-RUC](../../../nwp_models/regional/germany/icon-d2-ruc.md):** deterministic counterpart, sharing model core, two-moment microphysics, domain, hourly cadence, and 27 h forecast length.
- **[ICON-D2-EPS](./icon-d2-eps.md):** the 3-hourly, 48 h sibling on the identical grid. The two ensembles run in parallel and serve complementary forecast windows — RUC-EPS for the 0–27 h nowcasting-to-short-range transition with sub-hourly output, D2-EPS for the full 48 h range. Same 20-member size, same grid UUID, but different microphysics (two-moment vs one-moment), different output cadences, different member packaging, and different distribution tiers.
- **[ICON-EU-EPS](./icon-eu-eps.md) / [ICON-EPS](../../global/de/icon-eps.md):** boundary-condition suppliers.
- **[ICON-CH1-EPS](../../../nwp_models/regional/switzerland/icon-ch-eps.md)** (MeteoSwiss): the closest international peer for rapid-update convection-permitting ensembles, at 1 km with 11 members and 3-hourly cycling — but not hourly, and without the sub-hourly precipitation output.

---

## Notes
- **Distribution tier.** ICON-D2-RUC-EPS lives under `/weather/nwp/v1/m/` rather than alongside ICON-D2-EPS at `/weather/nwp/icon-d2-eps/`. The `v1/m/` tier uses the hierarchical `p/…/r/…/s/` path scheme and uncompressed GRIB2, and also hosts [ICON-D2-RUC](../../../nwp_models/regional/germany/icon-d2-ruc.md), [AICON-Global](../../../nwp_models/global/germany/aicon-global.md), and the ICON-ART family. Structurally this entry has more in common with AICON than with its own ensemble siblings.
- **All 24 cycles are published**, with no reduced off-cycles — the same completeness as ICON-D2-EPS.
- **No control member.** `perturbationNumber` starts at 1 and every member is perturbed.
- **Only the lowest 10 model levels are published** (56–65 of 65). Combined with six pressure levels and three shear layers, upper-air coverage is deliberately shallow — this is a boundary-layer and convective-storm product, not a synoptic one.
- **Sub-hourly steps mean minute-unit encoding.** `stepUnits = 0` on time-processed fields; `stepRange` reads e.g. `0m-725m`. This is the single most likely source of silent errors when adapting tooling built for the hourly ICON products.
- At 2.2 km with hourly updates, 5-minute precipitation output, and dense radar DA, ICON-D2-RUC-EPS occupies an unusual operational niche internationally. Few national weather services currently run a true convection-permitting rapid-update ensemble in production.
- **DWD publishes no dedicated ICON-D2-RUC-EPS change-notice series.** Changes arrive through the ICON-D2 notices (which cover "ICON-D2/ICON-RUC (-EPS)") and the all-configuration ICON notices.

---

## Recent version history

### 18 February 2026 — model version `icon-2025.04-dwd-4.0` (effective 09 UTC run)
Revised ceiling diagnostic (cloud-overlap assumption; fill value now 16 km above ground, matching observation reports) and revised visibility diagnostic (reworked humidity contribution). Applies to all configurations producing these fields.

### 23 July 2025 — model version `icon-2025.04-dwd-1.0` (effective 06 UTC run, all ICON configurations including all EPS)
Dissipative heating parameterization; ocean warm-layer parameterization introducing a diurnal SST cycle; bug fix for rime deposition on snow-free ground; retuning of interception storage and ozone–tropopause coupling.

### 26 February 2025 — KENDA: full French radar network (effective 06 UTC assimilation / 09 UTC forecast)
DACE 2.23 removed the computational cap on French radar assimilation; **all 14 French radar sites within the ICON-D2/RUC domain** are now assimilated. Explicitly covers ICON-RUC as well as ICON-D2.

### Mid-2024 — operational deployment
ICON-D2-RUC-EPS deployed alongside the deterministic ICON-D2-RUC, as the operational descendant of the SINFONY project's SINFONY-RUC-EPS.

### 22 May 2024 — KENDA: French radar volumes introduced (effective 06 UTC assimilation / 09 UTC forecast)
First assimilation of volumetric reflectivity and radial wind from 9 French radar stations via EUMETNET OPERA. The change notice states the configuration was tested for ICON-D2 "but also for the envisaged ICON-RUC system" — this is the RUC line entering DWD's operational change record.

---

## Official documentation
- DWD ICON-D2 model description: https://www.dwd.de/EN/research/weatherforecasting/num_modelling/01_num_weather_prediction_modells/icon_d2/icon_d2_node.html
- DWD NWP forecast data overview: https://www.dwd.de/EN/ourservices/nwp_forecast_data/nwp_forecast_data.html
- DWD ICON-D2 change notices (cover ICON-D2/ICON-RUC (-EPS)): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_d2_gesamt.html
- DWD ICON change notices (all-configuration changes): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_icon_gesamt.html
- DWD ICON Database Reference Manual: https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- DWD SINFONY project page: https://www.dwd.de/EN/research/researchprogramme/sinfony/sinfony_node.html
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD CDO grid description and weight files: https://opendata.dwd.de/weather/lib/cdo/

### Key references
- Schraff, C., Reich, H., Rhodin, A., Schomburg, A., Stephan, K., Periáñez, A., and Potthast, R. (2016). *Kilometre-scale ensemble data assimilation for the COSMO model (KENDA).* Quarterly Journal of the Royal Meteorological Society, 142(696), 1453–1472. https://doi.org/10.1002/qj.2748
- Seifert, A., and Beheng, K. D. (2006). *A two-moment cloud microphysics parameterization for mixed-phase clouds. Part 1: Model description.* Meteorology and Atmospheric Physics, 92, 45–66. https://doi.org/10.1007/s00703-005-0112-4
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378

---

*Live verification performed 2026-08-06 against `https://opendata.dwd.de/weather/nwp/v1/m/icon-d2-ruc-eps/` (25 hourly cycles, 2026-08-05 02 UTC through 2026-08-06 02 UTC) and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.48.0.*
