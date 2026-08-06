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
- **Measured domain extent:** **4.16°W – 20.54°E, 43.04°N – 58.17°N** — decoded from the `clat`/`clon` time-invariant cell-centre fields of the 2026-08-06 00 UTC run
- **Topography range:** −51.93 m to 4,080.44 m (`HHL` half-level 66, i.e. the surface, excluding masked cells)

---

## Basic details
- **Model type:** Regional convection-allowing ensemble prediction system
- **Model system / core:** ICON (Icosahedral Nonhydrostatic) limited-area configuration
- **Dynamical formulation:** Non-hydrostatic, triangular icosahedral horizontal grid
- **Convection-allowing:** Yes — deep convection is explicitly resolved, not parameterized
- **Ensemble size:** **20 members, numbered 1–20. There is no separate unperturbed control run.**
- **Native horizontal grid:** R19B07 limited-area triangular grid, **542,040 cells**, 2.2 km
  - Verified from live GRIB2 headers: `gridType = unstructured_grid`, `numberOfDataPoints = 542040`, `numberOfGridUsed = 47`, `uuidOfHGrid = c6b12daa91ad64045b26c1b6452a2a20`. The matching grid definition file is `icon_grid_0047_R19B07_L.nc.bz2` under the CDO library path below (`L` = limited area).
- **Public output grid:** Native triangular grid only. **No regular latitude–longitude output is distributed** — see *Data availability*.
- **Vertical levels:** **65 full levels** (66 `HHL` half-levels)
- **Model top:** **22,000 m** exactly — `HHL` half-level 1, spatially constant (`bitsPerValue = 0`). Half-level 2 is also flat, at 19,401.85 m.
- **Forecast length:** **48 hours**, all eight cycles
- **Update frequency / cycles:** 8× daily — 00, 03, 06, 09, 12, 15, 18, 21 UTC. All eight are published on Open Data, unlike the global and European ensembles where only four cycles reach the public server.
- **Temporal output resolution:** **Hourly, 49 steps (0–48 h), uniform across all eight cycles and all parameters** except `vmax_10m`-family interval maxima, which have no step 000

Verified 2026-08-06: every cycle directory carries the identical 93-parameter, 49-step structure. This is the most regular of DWD's four published ICON ensembles — no cycle-dependent parameter sets, no split step schedules, no reduced off-cycles.

---

## Data assimilation
- **Data assimilation:** Yes — its own, unlike [ICON-EU-EPS](./icon-eu-eps.md)
- **Method / cadence:** **KENDA** (Kilometre-scale ENsemble Data Assimilation), a Local Ensemble Transform Kalman Filter run at the same 2.2 km resolution, cycled hourly. KENDA assimilates a wide observation set including **volumetric radar reflectivity and radial wind** from the German C-band network and, since 2024–2025, from French radars via EUMETNET OPERA.

The first 20 members of the KENDA analysis ensemble supply the initial conditions, including stochastically perturbed soil moisture fields.

This is the structural distinction from [ICON-EU-EPS](./icon-eu-eps.md), which has no regional analysis of its own and is simply the nest portion of the global ensemble integration.

---

## Initial and boundary conditions
- **Initial conditions:** KENDA / LETKF analysis ensemble at 2.2 km (members 1–20), with perturbed soil moisture.
- **Boundary conditions:** ICON-EPS / ICON-EU-EPS forecasts, initialized 3 hours before the ICON-D2-EPS initial time.

> **Sourcing note on "various global models".** DWD's NWP forecast data page states that <cite index="4-1">for varying the lateral boundary conditions and the initial state, forecasts from various global models are used</cite>. Earlier versions of this entry read that as evidence that DWD had diversified boundary sources beyond ICON-EPS. The technical literature does not support that reading: COSMO General Meeting material, EMS 2022 conference abstracts, and published ICON-D2-EPS studies all describe boundaries as coming from ICON-EPS or ICON-EU-EPS alone. The multi-model phrasing appears to be inherited from **COSMO-DE-EPS**, which genuinely did draw boundaries from four different global models before 2017, when it switched to ICON-EPS. Treat "various global models" as legacy website wording rather than a current configuration statement. **TBD** — worth a direct question to DWD if it matters for a given application.

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

**93 parameter directories**, identical in every cycle — by far the richest of DWD's published ensembles (against 22 for [ICON-EPS](./../../global/de/icon-eps.md) and 28 for [ICON-EU-EPS](./icon-eu-eps.md)). Verified live 2026-08-06:

| Level type | Levels | Notes |
|---|---|---|
| Single-level | — | the bulk of the 93 |
| Pressure-level | **500, 700, 850, 950, 975, 1000 hPa** | `fi`, `t`, `u`, `v`, `relhum`, `omega` |
| Model-level | **56–65** (lowest 10 of 65) | `t`, `u`, `v`, `w`, `qv`, `relhum`, `tke`-family |
| Soil-level | 0, 1, 2, 3, 5, 6, 9, 18, 27, 54, 81, 162, 243, 486, 729, 1458 | `t_so`, `w_so`, `w_so_ice`, `smi` |
| Time-invariant | — | `clat`, `clon`, `elat`, `elon`, `hhl`, `hsurf`, `fr_land`, `fr_lake`, `depth_lk`, `soiltyp`, `plcov`, `lai`, `rootdp` |

Convection-allowing and severe-weather diagnostics are the distinguishing content: **`lpi` / `lpi_max`** (Lightning Potential Index), **`uh_max`** (updraught helicity), **`vorw_ctmax`**, **`w_ctmax`**, **`dbz_850` / `dbz_cmax` / `dbz_ctmax`** (simulated radar reflectivity), **`echotop`**, **`ceiling`**, **`vis`**, **`sdi_2`** (supercell detection index), **`tcond_max` / `tcond10_mx`**, **`cape_ml` / `cin_ml`**, **`hbas_sc` / `htop_sc`**, and **`grau_gsp`** (graupel). Aviation-relevant fields migrated from COSMO-D2 in February 2021 are included.

Time-integration conventions, verified by decoding `stepType`/`stepRange` at +12 h:

| Convention | `stepType` | PDT | Examples |
|---|---|---|---|
| Accumulated from run start | `accum` | 11 | `tot_prec`, `rain_gsp`, `rain_con`, `snow_gsp`, `grau_gsp`, `runoff_s` |
| Averaged from run start | `avg` | 11 | `asob_s`, `athb_s`, `aswdir_s`, `alhfl_s` (the `a*` flux family) |
| Max over the preceding hour | `max` | 11 | `vmax_10m` (`11-12`), `lpi_max`, `uh_max`, `w_ctmax` |
| Max/min since last 6 h boundary | `max` / `min` | 11 | `tmax_2m` (`6-12`), `tmin_2m` |
| Instantaneous | `instant` | 1 | `t_2m`, `pmsl`, `cape_ml`, `dbz_cmax`, and all pressure-, model-, and soil-level fields |

Note the two different max conventions: gust and convective maxima cover one hour, while `tmax_2m`/`tmin_2m` reset every six hours.

Probability, percentile, mean, and spread products are **not** distributed — the public feed carries raw member fields only. All ensemble statistics must be computed by the user from the 20 members.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, bzip2-wrapped (`.grib2.bz2`)
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/icon-d2-eps/grib/
  - Layout: `/<cycle>/<parameter>/icon-d2-eps_germany_icosahedral_<leveltype>_<YYYYMMDDHH>_<step>_<level|2d>_<param>.grib2.bz2`
  - Single-level files carry the literal token `2d` in the level position (e.g. `..._012_2d_t_2m.grib2.bz2`), where multi-level files carry the numeric level. Parsers splitting on underscores need to expect a non-numeric token there.
- **Server manifest:** https://opendata.dwd.de/weather/nwp/content.log.bz2 — pipe-delimited `path|bytes|mtime` for the whole `/weather/nwp/` tree.
- **Retention:** each cycle directory holds exactly one run. Verified 2026-08-06: all eight cycle directories contained a single run date each. With eight cycles, that is a ~24 h rolling window.
- **Volume:** **~98 GB and 8,752 files per run**, essentially constant across all eight cycles (97.70–98.90 GB observed). At eight runs per day this is **~785 GB/day** — the heaviest of DWD's published ensembles by daily volume, and comparable to the deterministic ICON Global feed.
- **Publication latency:** first files ~1 h 31 min after cycle time; run completes ~2 h 07–2 h 10 min after cycle time. Measured across all eight cycles of 2026-08-05/06 — e.g. the 00 UTC run ran 01:31:23 → 02:09:03 UTC, the 12 UTC run 13:31:17 → 14:08:47 UTC. Substantially faster than the global systems (ICON Global completes near T+3h45), as expected for a 48 h limited-area forecast.

### Member packaging (verified)
- **Packaging:** all 20 members for a given parameter, level, and step are concatenated into a **single GRIB2 file**, one message per member. No per-member file split, no member token in the filename.
- **Member indexing:** `perturbationNumber` runs **1 to 20**. There is no member 0 and no separate control forecast.
- **GRIB2 encoding:** `productDefinitionTemplateNumber = 1` for instantaneous fields and `11` for time-processed fields; `typeOfEnsembleForecast = 192` (DWD local code); `numberOfForecastsInEnsemble = 20`; `typeOfProcessedData = cp`; `centre = edzw`; `tablesVersion = 19`, `localTablesVersion = 1`; `missingValue = 9999`.
- **Packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`. A GRIB library built without libaec/CCSDS support cannot decode these files.

### Every physical field carries a bitmap
Unlike the global and European ICON ensembles, ICON-D2-EPS messages are **bitmapped**. Verified on the 2026-08-06 00 UTC run:

| Field | `bitmapPresent` | Masked cells |
|---|---|---|
| `t_2m`, `cape_ml`, `tot_prec`, `hsurf`, `hhl` (all levels) | 1 | **16,968** of 542,040 (3.13%) |
| `clat`, `clon` | 0 | 0 |

The masked cells are the lateral boundary relaxation zone, where the limited-area solution is nudged toward the driving model and physical fields are not defined. Two consequences:

- **Readers that ignore `bitmapPresent` will read 9999 as a physical value.** A 9999 K 2 m temperature or 9999 mm of precipitation around the domain rim is the signature.
- **`clat`/`clon` cover the full 542,040-cell mesh while the data does not.** Zipping coordinate arrays against value arrays without applying the bitmap silently misaligns 3% of the domain. Use the bitmap, or drop the masked cells from both.

### The `.bz2` wrapper barely compresses
Since DWD switched all ICON-family GRIB2 output to CCSDS packing on **16 June 2026**, the bzip2 wrapper recovers little. Measured on the 2026-08-06 00 UTC run: `..._012_2d_t_2m.grib2.bz2` is 13,801,579 bytes against 15,092,833 decompressed — a ratio of 0.914. Better than the global products (where wrapped files are sometimes marginally *larger* than their contents), but far from the compression the extension implies. Budget transfer volume on the compressed size.

### No regular lat–lon output
Every file under `/weather/nwp/icon-d2-eps/grib/` matches `icon-d2-eps_germany_icosahedral_*` — a scan of all 70,016 manifest entries for this tree returned **zero** `regular-lat-lon` matches. DWD's own product description confirms it: <cite index="1-1">ICON-D2-EPS is provided on the DWD Open Data Server in the native triangular grid</cite>.

> **Correction to earlier versions of this entry.** This entry previously stated that a "regular lat-lon grid [is] also distributed for many element packages". That is true of the **deterministic** [ICON-D2](../../../nwp_models/regional/germany/icon-d2.md), whose tree carries 434,536 `regular-lat-lon` files alongside 434,176 icosahedral ones — an even split — but **not** of the ensemble. The claim appears to have been inherited from the deterministic sibling. Users needing lat–lon for the ensemble must interpolate themselves; DWD ships the tooling:
> - https://opendata.dwd.de/weather/lib/cdo/
>   - `icon_grid_0047_R19B07_L.nc.bz2` — the limited-area grid description matching `numberOfGridUsed = 47`
>   - `ICON_D2_002_EASY.tar.bz2` — CDO weights to a 0.02° target grid over the D2 domain

---

## Relationship to other models
- **[ICON-D2](../../../nwp_models/regional/germany/icon-d2.md):** deterministic counterpart, sharing model core, domain, resolution, vertical structure, and cycle schedule. Unlike the ensemble, the deterministic product **is** published on a regular lat–lon grid in addition to the native mesh.
- **[ICON-EPS](../../global/de/icon-eps.md) / [ICON-EU-EPS](./icon-eu-eps.md):** the global and European ensembles supplying lateral boundary conditions. Note that ICON-D2-EPS has 20 members against their 40 — member *n* of ICON-D2-EPS is not the downscaled member *n* of the global ensemble, because KENDA supplies independent initial conditions.
- **[ICON-D2-RUC-EPS](./icon-d2-ruc-eps.md):** the hourly-updating sibling covering the 0–14 h rapid-update range with two-moment microphysics. The two run in parallel and serve complementary forecast windows.
- **[ICON-CH1-EPS / ICON-CH2-EPS](../../../nwp_models/regional/switzerland/icon-ch-eps.md)** (MeteoSwiss): the closest international peers, also ICON-based limited-area ensembles with KENDA-family assimilation, at 1 km / 11 members and 2.1 km / 21 members respectively. Useful for cross-checking conventions, though MeteoSwiss distributes via a STAC API rather than a directory tree and does publish a control member.

---

## Notes
- ICON-D2-EPS is the ensemble counterpart of the deterministic ICON-D2 — see that entry for the shared model core and operational details.
- **All eight cycles are published.** This is the only DWD ICON ensemble whose full cycle schedule reaches Open Data; the global and European ensembles publish four of eight.
- **The structure is unusually regular.** 93 parameters × 49 hourly steps in every cycle, with no cycle-dependent fields and no split step schedules. Contrast [ICON-EU-EPS](./icon-eu-eps.md), where `fi` and `tqv` appear only at 00/12 UTC and model-level fields carry three extra steps.
- **No control member.** `perturbationNumber` starts at 1 and every member is perturbed. Products expecting a control/perturbed split (common when ingesting ECMWF ENS or GEFS) need adapting.
- The system was renamed from COSMO-D2-EPS to ICON-D2-EPS on 10 February 2021 as part of DWD's migration from the COSMO model family to the ICON family. Older references and some DWD documentation pages still use the COSMO-D2-EPS name — and, as noted above, some of the surrounding descriptive text is also inherited from that era.
- At 2.2 km with 20 members and radar-volume assimilation, ICON-D2-EPS is among the higher-resolution operational convection-allowing ensembles in production internationally. Comparable systems include MeteoSwiss ICON-CH1-EPS (1 km, 11 members) and Météo-France AROME-EPS.
- **DWD's English ensemble-prediction page is stale.** Its ICON-D2-EPS section still describes COSMO-D2-EPS throughout, and its ICON-EPS resolution figures predate the November 2022 upgrade. Prefer the ICON Database Reference and the change notices.

---

## Recent version history

ICON-D2-EPS is upgraded partly through its own ICON-D2 change-notice series (which explicitly covers "ICON-D2 (-EPS)") and partly through the all-configuration ICON notices. Both are reflected below; see the [ICON Global entry](../../../nwp_models/global/germany/icon-global.md#recent-version-history) for the full ICON sequence.

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
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD CDO grid description and weight files: https://opendata.dwd.de/weather/lib/cdo/

### Key references
- Reinert, D., et al. *DWD Database Reference for the Global and Regional ICON and ICON-EPS Forecasting System.*
- Schraff, C., Reich, H., Rhodin, A., Schomburg, A., Stephan, K., Periáñez, A., and Potthast, R. (2016). *Kilometre-scale ensemble data assimilation for the COSMO model (KENDA).* Quarterly Journal of the Royal Meteorological Society, 142(696), 1453–1472. https://doi.org/10.1002/qj.2748
- Gebhardt, C., Theis, S. E., Paulat, M., and Ben Bouallègue, Z. (2011). *Uncertainties in COSMO-DE precipitation forecasts introduced by model perturbations and variation of lateral boundaries.* Atmospheric Research, 100(2–3), 168–177.
- Zängl, G., Reinert, D., Rípodas, P., and Baldauf, M. (2015). *The ICON (ICOsahedral Non-hydrostatic) modelling framework of DWD and MPI-M: Description of the non-hydrostatic dynamical core.* Quarterly Journal of the Royal Meteorological Society, 141(687), 563–579. https://doi.org/10.1002/qj.2378

---

*Live verification performed 2026-08-06 against `https://opendata.dwd.de/weather/nwp/icon-d2-eps/grib/` (all eight cycles, 2026-08-05 03 UTC through 2026-08-06 00 UTC) and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.48.0.*
