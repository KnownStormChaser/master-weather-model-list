# MOGREPS-UK (Met Office Global and Regional Ensemble Prediction System – UK)

## What this model is
MOGREPS-UK is the **convection-permitting regional ensemble prediction system** operated by the UK Met Office. It represents forecast uncertainty for short-range, high-impact weather over the United Kingdom — convection, heavy rainfall, wind, fog, and temperature extremes — at a scale where individual storms are resolved rather than parameterized.

It is the ensemble counterpart to the deterministic [UKV](../../../nwp_models/regional/uk/ukv.md), sharing the same domain, dynamical core, and Regional Atmosphere/Land 3 (RAL3) science configuration, and receives lateral boundary conditions from the global [MOGREPS-G](../../global/uk/mogreps-g.md) ensemble.

**The distribution is time-lagged and this is the defining feature of the dataset.** Each hourly cycle produces only 3 members, with member identities rotating on a 12-hour cycle. A single cycle is not a usable ensemble — see *Perturbations and design* below.

The current operational version is part of **Operational Suite 47 (OS47)**, implemented on 21 January 2026.

---

## Who runs it
- **Organization:** Met Office
- **Country / region:** United Kingdom

---

## What area it covers
- **Coverage:** United Kingdom and Ireland (forecast focus); the published grid extends considerably further
- **Published open-data grid extent** (measured from live files, 21 July 2026):
  - The `uk_extended` domain, spanning **2082 km × 1938 km**
  - Approximate geographic bounds: **24.5°W to 15.3°E, 44.5°N to 63.0°N**
  - **Identical to the [UKV](../../../nwp_models/regional/uk/ukv.md) distribution grid** — same projection, same 1042 × 970 dimensions, same extents. The two products are directly co-registered and require no regridding to compare.
- Lateral boundary conditions provided by [MOGREPS-G](../../global/uk/mogreps-g.md)

---

## Basic details
- **Model type:** Regional ensemble NWP (convection-permitting)
- **Model system / core:** Unified Model (UM), in its Regional Atmosphere/Land 3 (RAL3) science configuration as of OS47
- **Dynamical formulation:** Non-hydrostatic, fully compressible deep-atmosphere equations (ENDGame dynamical core); semi-Lagrangian, semi-implicit time integration
- **Convection-allowing:** Yes — deep convection is explicitly resolved
- **Ensemble size:**
  - **3 members per cycle** (verified: `realization` dimension of size 3 in every file)
  - **35 distinct members over a 12-hour window** — control (realization `0`) plus perturbed members `1`–`34`, assembled across 12 consecutive hourly cycles (36 member-slots; the control appears twice at different lags)
  - See *Perturbations and design* for the full rotation mapping
- **Native horizontal resolution:** ~2.2 km
- **Public distribution grid:** **Lambert Azimuthal Equal Area projection at exactly 2000 m spacing** — 1042 × 970 grid points
  - **Projection origin:** 54.9°N, 2.5°W; false easting and northing both 0
  - **Ellipsoid:** WGS84 (semi-major 6 378 137 m, semi-minor 6 356 752.314 m)
  - **Extent in projection coordinates:** x from −1 158 000 m to 924 000 m; y from −1 036 000 m to 902 000 m
  - **⚠️ Not a 2.2 km grid and not a degree grid.** The "about 2.2 km" figure in the AWS registry and Met Office parameter table describes the native model resolution. The delivered files contain no latitude or longitude variables — only `projection_x_coordinate` and `projection_y_coordinate` in metres, with a CF `lambert_azimuthal_equal_area` grid mapping. Consumers expecting `lat`/`lon` arrays must reproject.
  - Note: `mosg__grid_version` is `2.4.0` here versus `1.7.0` for UKV, despite the coordinate arrays being identical.
- **Vertical levels:** 70 (UK level set; lid ~40 km, distinct from the global model's ~80 km)
- **Model top:** ~40 km
- **Forecast length:** **126 hours (5 d 6 h) from every cycle.** Verified live — there is no cycle-to-cycle variation in forecast length, unlike UKV.
- **Update frequency / cycles:** **24× daily, hourly** (00–23 UTC)
- **Temporal output resolution:** three tiers, parameter-dependent (verified against the 2026-07-21 00Z cycle)
  - **15-minutely to T+125 h** — the precipitation family: `precipitation_rate`, `rainfall_rate`, `snowfall_rate`, `hail_fall_rate`, and the four matching `*_accumulation-PT15M` parameters (504 steps each)
  - **15-minutely to T+11h45m, hourly thereafter** — screen-level and 10 m diagnostics: `CAPE_surface`, `CIN_surface`, `pressure_at_mean_sea_level`, `temperature_at_screen_level`, `temperature_of_dew_point_at_screen_level`, `visibility_at_screen_level`, `wind_direction_at_10m`, `wind_speed_at_10m`, `wind_gust_at_10m`
  - **Hourly to T+126** for the remaining parameters
- **Data latency:** Documented as 10–11 hours after run time; **observed at T+3h46m to T+4h47m** across six cycles spanning 21–22 July 2026. The official figure is wrong by more than a factor of two — MOGREPS-UK is the fastest-landing of the four Met Office open datasets.

---

## Data assimilation
MOGREPS-UK inherits the Met Office's hybrid 4D-Var framework via the UKV analysis chain, with global-scale ensemble information arriving through MOGREPS-G lateral boundary conditions.
- **Method / cadence:** TBD — the precise per-member analysis scheme for the regional ensemble is not documented in the open-data metadata

---

## Initial and boundary conditions
- **Initial conditions:** Derived from the UKV/UK regional analysis with per-member perturbations; exact scheme TBD
- **Boundary conditions:** [MOGREPS-G](../../global/uk/mogreps-g.md) global ensemble. Boundary perturbation methodology TBD.

---

## Perturbations and design

### Time-lagged distribution (verified 21 July 2026)
**This is the defining structural feature of the dataset and is absent from official documentation.** Each hourly cycle contains 3 members, and the `realization` IDs advance on a **12-hour rotation**. Verified by reading the `realization` coordinate from all 24 cycles of a single day; the pattern repeats exactly across the two halves of the day.

| H mod 12 | Cycles (UTC) | Realizations present |
|---|---|---|
| 5 | 05, 17 | **0** (control), 1, 2 |
| 6 | 06, 18 | 3, 4, 5 |
| 7 | 07, 19 | 6, 7, 8 |
| 8 | 08, 20 | 9, 10, 11 |
| 9 | 09, 21 | 12, 13, 14 |
| 10 | 10, 22 | 15, 16, 17 |
| 11 | 11, 23 | **0** (control), 18, 19 |
| 0 | 00, 12 | 20, 21, 22 |
| 1 | 01, 13 | 23, 24, 25 |
| 2 | 02, 14 | 26, 27, 28 |
| 3 | 03, 15 | 29, 30, 31 |
| 4 | 04, 16 | 32, 33, 34 |

**To build the full ensemble, combine 12 consecutive hourly cycles**, accepting a lag of up to 12 hours on the oldest members. This yields 36 member-slots covering 35 distinct realizations: the control (`0`) is rerun every 6 hours and therefore appears twice per window at different initialization times, alongside 34 perturbed members.

**A single cycle is not a complete ensemble.** Treating one cycle's three members as the ensemble will severely under-sample the forecast distribution.

**Control identification:** realization `0` is the conventional control designation and is consistent with its 6-hourly rerun cadence, but the files carry no attribute confirming this — treat as **TBD** if the distinction is load-bearing.

- **Initial condition perturbations:** TBD
- **Model/physics perturbations:** OS47 introduced new stochastic perturbations consistent with the RAL3 configuration; specific schemes TBD
- **Stochastic schemes:** TBD

---

## What it provides

**Raw ensemble member forecasts only.** The open-data distribution contains **no ensemble-derived products** — no ensemble mean, no spread, no ensemble probabilities, no ensemble percentiles. Every one of the 89 parameters carries a `realization` dimension; users must derive ensemble statistics themselves after assembling members across cycles.

### ⚠️ The "percentile" and "probability" parameters are not ensemble statistics
Two parameters look like ensemble products but are not:
- `visibility_at_screen_level_percentile` — dimensions `(realization, percentile, y, x)` with percentiles `[1, 10, 50, 90, 99]`
- `visibility_at_screen_level_probability` — dimensions `(realization, threshold, y, x)`, variable `probability_of_visibility_in_air_below_threshold`, with 13 thresholds in metres: `[50, 100, 200, 400, 600, 800, 1000, 1500, 2000, 5000, 10000, 20000, 40000]` and `spp__relative_to_threshold = less_than`

Because `realization` is the leading dimension on both, these are **per-member, within-grid-cell** visibility distributions — sub-grid diagnostics computed inside each individual member — not statistics across the ensemble. Misreading them as ensemble percentiles or ensemble probabilities is an easy and consequential mistake.

### Parameter set (89 parameters, verified 21 July 2026)
Convection-permitting and probabilistic-relevant highlights:
- **Simulated radar reflectivity** — `total_radar_reflectivity_max_in_column` (dBZ, column maximum) and `total_radar_reflectivity_on_pressure_levels` (dBZ on 33 pressure levels). The sum of graupel, ice aggregate, ice crystal, rain and liquid cloud contributions. **Unique to MOGREPS-UK across the four Met Office open datasets** and directly comparable with the UK radar composites in [`observations/radar/`](../../../../observations/radar/).
- **Lightning** — `lightning_flash_accumulation-PT01H` (flashes m⁻², total cloud-to-cloud and cloud-to-ground)
- **Hail** — `hail_fall_rate`, `hail_fall_rate_max-PT01H`, `hail_fall_accumulation-PT01H` and `-PT15M`
- **Convective structure** — CAPE and CIN in three parcel variants, convective inflow base and top heights, mixed-layer and most-unstable CAPE equilibrium and initiation levels
- **Cloud base heights** at 2.5 and 4.5 okta thresholds, `cloud_amount_on_height_levels` (33 levels) and `on_pressure_levels` (33 levels)
- **Soil** — temperature and water mass concentration on 4 depth levels (0.05, 0.225, 0.675, 2.0 m)
- **Full profiles** — temperature, relative humidity, specific humidity, wind speed/direction and vertical velocity on both 33 height levels (5–6000 m) and 33 pressure levels (100000–1000 Pa)
- **Surface energy budget** — sensible and latent heat flux, evaporation flux, five radiation components including net shortwave

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution-ShareAlike 4.0 (CC BY-SA 4.0). British Crown copyright, the Met Office. Attribution and ShareAlike both required — note this differs from ECMWF's CC-BY-4.0 by adding the ShareAlike obligation.
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF4/HDF5, CF-1.7 + UKMO-1.0 conventions
- **Retention:** **~30-day rolling archive.** Measured 22 July 2026: 33 date directories spanning 2026-06-20 to 2026-07-22, with the oldest surviving cycle being `2026/06/20/T2200Z`. Deletion is at **cycle granularity**, giving roughly 32 days of data at any moment.
- **Bucket:** `s3://met-office-uk-ensemble-model-data` (region `eu-west-2`) — a **fourth distinct bucket**, separate from the deterministic pair (`met-office-atmospheric-model-data`) and from MOGREPS-G (`met-office-global-ensemble-model-data`)
  - Anonymous access, no AWS account or credentials required: `aws s3 ls --no-sign-request s3://met-office-uk-ensemble-model-data/`
  - **Path template:** `uk-ensemble/{YYYY}/{MM}/{DD}/T{HHMM}Z/` — date-hierarchical, matching MOGREPS-G but differing from the flat prefix used by the deterministic datasets
  - **Filename convention:** `{validity YYYYMMDD}T{HHMM}Z-PT{HHHH}H{MM}M-{parameter}.nc`. The leading timestamp is the **validity time**; the run time appears only in the path. The `MM` field is non-zero for 15-minutely parameters.
  - **No member index in the filename** — all 3 members of that cycle are inside each file
- **Volume:** each cycle is **14,330 files totalling ~247 GB**; at 24 cycles per day this is roughly **5.9 TB/day**. Uniform across all cycles.
- **New-object notifications:** SNS topic `arn:aws:sns:eu-west-2:633885181284:met-office-uk-ensemble-model-data-object_created`
- **Not on the Planetary Computer.** Like MOGREPS-G, the UK ensemble is distributed only through AWS; there are no `met-office-uk-ensemble-*` collections in the Planetary Computer STAC catalog.
- **Caveat:** offered on a free, **unsupported**, non-operational basis. The Met Office does not recommend it for critical business purposes.
- **Official location:**
  - https://registry.opendata.aws/met-office-uk-ensemble/
  - Browsable index: https://met-office-uk-ensemble-model-data.s3.eu-west-2.amazonaws.com/index.html

### File internals (verified 21 July 2026)
| Attribute | Value |
|---|---|
| `Conventions` | `CF-1.7, UKMO-1.0` |
| `title` | `MOGREPS-UK Model Forecast on UK 2 km Standard Grid` |
| `mosg__model_configuration` | `uk_ens` |
| `mosg__grid_domain` | `uk_extended` |
| `mosg__grid_type` / `mosg__grid_version` | `standard` / `2.4.0` |
| `mosg__forecast_run_duration` | `PT126H` |
| `um_version` | `13.8` |

- **Dimension ordering:** `realization` is always the leading dimension — `(realization, y, x)` for surface fields, `(realization, level, y, x)` for level fields, `(realization, percentile|threshold, y, x)` for the visibility diagnostics.
- **Compression:** zlib level 1, shuffle off, with lossy precision truncation via `least_significant_digit`. As with MOGREPS-G (and unlike the deterministic products), this is a **global** attribute rather than a per-variable one.
- **Status flags:** pressure-level files carry a `flag` ancillary variable (`standard_name = status_flag`, `flag_meanings = above_surface_pressure below_surface_pressure`), itself per-member at `(realization, pressure, y, x)`. Sub-surface points are **not masked** and must be screened per member.
- **Fill values:** some parameters (e.g. `total_radar_reflectivity_on_pressure_levels`, `soil_temperature_on_soil_levels`) declare `_FillValue = nan`.
- **Calendar:** `standard`; HDF5 superblock version 2 as of late January 2026.
- **CF variable names differ from filenames** — e.g. `cloud_amount_of_total_cloud.nc` contains `cloud_area_fraction`.

---

## Relationship to other models
- **[UKV](../../../nwp_models/regional/uk/ukv.md):** deterministic counterpart. Shares the domain, dynamical core, RAL3 configuration and — verified — an identical 2 km LAEA distribution grid, so the two are directly co-registered.
- **[MOGREPS-G](../../global/uk/mogreps-g.md):** global ensemble parent, supplying lateral boundary conditions. Note the contrasting distribution designs: MOGREPS-G delivers a complete 18-member ensemble in every file, while MOGREPS-UK requires time-lagged assembly across 12 cycles.
- **[UKMO Global](../../../nwp_models/global/uk/ukmo-global.md):** the global deterministic model that drives UKV.

---

## Notes
- The time-lagged design trades ensemble size per cycle against update frequency: hourly cycling gives very fresh short-range guidance, but any application needing a well-sampled distribution must accept up to 12 hours of lag on the oldest members. This is a different trade-off from the KNMI HARMONIE-EPS systems, which lag over 6 hours to reach 30 members, and from MOGREPS-G, which does not lag at all in its open distribution.
- The distributed 89 parameters are a subset of full operational output; the Met Office sells the complete ensemble through Weather DataHub and direct customer feeds.
- Output should be interpreted probabilistically. Given that no ensemble-derived products are distributed, users must compute their own statistics — and must first assemble a real ensemble, since three members from one cycle will not support meaningful probabilities.
- MOGREPS-UK runs on the Met Office's Microsoft Azure-based supercomputer, on which OS47 was the first major modelling suite to run.

---

## Recent version history

### OS47 / PS47 / RAL3 — operational 21 January 2026 (current)
Deployed alongside the UKV and global upgrades.
- New **RAL3** science configuration
- **17 new parameters** added to the open-data set
- Status flags added to **all** pressure-level parameters (previously partial), converted to compressed ancillary variables
- `height_ASL_on_pressure_levels` replaced by `geopotential_height_on_pressure_levels`
- Increased number of pressure levels for some parameters, and additional timesteps for selected parameters — notably the expansion of 15-minutely output
- Precision reduced via `least_significant_digit` truncation to offset volume growth
- HDF5 superblock version 0 → 2; calendar metadata `gregorian` → `standard`

### OS46 — operational May 2022 to January 2026
Previous operational configuration, succeeded by OS47.

---

## Official documentation
- Met Office MOGREPS overview: https://www.metoffice.gov.uk/research/weather/ensemble-forecasting/mogreps
- Met Office numerical weather prediction overview: https://www.metoffice.gov.uk/research/approach/modelling-systems/unified-model/weather-forecasting
- Parallel Suite 47 (PS47) overview: https://www.metoffice.gov.uk/services/data/parallel-suite-47-ps47-overview
- MOGREPS-UK PS47 dataset change notice and parameter table: https://www.metoffice.gov.uk/api/assets/file/mogreps-uk-ps47-asdi-pdf-updates-20pdf?prefix=assets
- Met Office external data channels: https://www.metoffice.gov.uk/services/data/external-data-channels
- AWS Open Data Registry: https://registry.opendata.aws/met-office-uk-ensemble/
