# UKV (UK Variable Resolution Model)

## What this model is
UKV is the **high-resolution regional deterministic weather prediction system** operated by the UK Met Office.

It is a **regional configuration of the Unified Model (UM)** — the Met Office's flagship NWP and climate modelling suite — covering the UK and Ireland. UKV is named for its variable-resolution grid: a fixed-resolution inner domain at 1.5 km grid spacing covers the UK and Ireland, surrounded by a coarser 4 km grid near the lateral boundaries with a smooth variable-resolution transition zone in between. This design lets UKV run at convection-permitting scales over the area of forecast interest while limiting boundary-related artefacts from the coarser parent [UKMO Global](../../global/uk/ukmo-global.md) model that drives it.

UKV is the Met Office's primary short-range, convection-permitting forecast system for the UK and is used operationally for severe weather, aviation, energy, and public forecasting applications. It is also the deterministic counterpart to the [MOGREPS-UK](../../../ensemble_models/regional/uk/mogreps-uk.md) regional ensemble, which shares the same domain and model core.

The current operational version is part of **Operational Suite 47 (OS47)**, implemented on 21 January 2026 — the Met Office's first major science upgrade in over three years and the first run on its new Microsoft Azure-based supercomputer.

---

## Who runs it
- **Organization:** Met Office
- **Country / region:** United Kingdom

---

## What area it covers
- **Coverage:** United Kingdom and Ireland (forecast focus) — but the published grid extends considerably further, see below
- **Model domain details:**
  - Variable-resolution grid with a 1.5 km fixed-resolution inner domain over the UK and Ireland
  - 4 km grid spacing near the lateral boundaries
  - Smooth variable-resolution transition zone between the inner and boundary regions
  - Lateral boundary conditions provided by the parent [UKMO Global](../../global/uk/ukmo-global.md) model
- **Published open-data grid extent** (measured from live files, 21 July 2026):
  - The distributed grid is the `uk_extended` domain, spanning **2082 km × 1938 km** — far larger than the UK and Ireland
  - Approximate geographic bounds: **24.5°W to 15.3°E, 44.5°N to 63.0°N**, reaching the longitude of Iceland in the west, central Europe in the east, and south past Brittany
  - **The grid is fully populated** — zero masked cells and zero fill values were found in a screen-temperature field. Data outside the UK/Ireland inner domain comes from the coarser variable-resolution outer region and should be treated accordingly; the Met Office designs and verifies UKV for the UK, not for the full grid extent.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** Unified Model (UM), in its Regional Atmosphere/Land 3 (RAL3) science configuration as of OS47
- **Dynamical formulation:** Non-hydrostatic, fully compressible deep-atmosphere equations (ENDGame dynamical core); semi-Lagrangian, semi-implicit time integration
- **Convection-allowing:** Yes (deep convection is explicitly resolved at 1.5 km in the inner domain)
- **Native horizontal resolution:** 1.5 km (inner domain), 4 km (boundary region), variable in transition zone
- **Public distribution grid:** **Lambert Azimuthal Equal Area projection at exactly 2000 m spacing** — 1042 × 970 grid points
  - **Projection origin:** 54.9°N, 2.5°W; false easting and northing both 0
  - **Ellipsoid:** WGS84 (semi-major 6 378 137 m, semi-minor 6 356 752.314 m) — note this differs from the spherical earth (r = 6 371 229 m) used by the global model's latitude–longitude grid
  - **Extent in projection coordinates:** x from −1 158 000 m to 924 000 m; y from −1 036 000 m to 902 000 m
  - **⚠️ Not a degree grid.** Met Office documentation, including the AWS Open Data Registry entry, describes the product as "a resolution of 0.018 degrees, projected on to a 2km horizontal grid." This is misleading: the files contain no latitude or longitude variables at all, only `projection_x_coordinate` and `projection_y_coordinate` in metres, with a CF `lambert_azimuthal_equal_area` grid mapping. Consumers expecting `lat`/`lon` arrays must reproject. Verified by decoding live files, 21 July 2026.
- **Vertical levels:** 70 (a different level set from the global model — the UKV lid is ~40 km rather than ~80 km)
- **Model top:** ~40 km
- **Forecast length and update frequency:** UKV runs **hourly, 24 cycles per day**, in three tiers of differing length:

| Tier | Forecast length | Cycles (UTC) | Runs/day |
|---|---|---|---|
| **Nowcast** | T+12 | 01, 02, 04, 05, 07, 08, 10, 11, 13, 14, 16, 17, 19, 20, 22, 23 | 16 |
| **Short** | T+54 | 00, 06, 09, 12, 18, 21 | 6 |
| **Medium** | T+120 | 03, 15 | 2 |

  Verified against live S3 listings: `20260721T0100Z` terminates at T+12, `20260721T0600Z` at T+54, and both `20260721T0300Z` and `20260721T1500Z` at T+120. A full day of cycle prefixes (2026-07-20) contained all 24 hourly runs.
- **Temporal output resolution:** three-tiered, and parameter-dependent
  - **15-minutely from T+0 to T+53:** `precipitation_rate`, `rainfall_rate`, `snowfall_rate`, `hail_fall_rate`
  - **15-minutely from T+0 to T+11, hourly thereafter:** `CAPE_surface`, `pressure_at_mean_sea_level`, `temperature_at_screen_level`, `temperature_of_dew_point_at_screen_level`, `visibility_at_screen_level`, `wind_direction_at_10m`, `wind_gust_at_10m`, `wind_speed_at_10m` — this set gained 15-minute output in the January 2026 upgrade
  - **Hourly** to T+54 for all remaining parameters
  - **3-hourly** from T+57 to T+120 (medium runs only)
- **Data latency:** Documented as 3–6 hours after model run time; observed performance on 21 July 2026 was consistently at the fast end, with first objects appearing at **T+3h15m** for all three tiers and last objects at T+3h15m (nowcast), T+3h46m (short) and T+4h16m (medium). UKV is materially quicker to land than the global model, which ran T+5h30m to T+6h31m the same week.

---

## Data assimilation
UKV uses **hourly cycling 4D-Var** data assimilation, providing high-frequency analysis updates well-suited to the convection-permitting scale of the inner domain. The Met Office's broader hybrid 4D-Var infrastructure underpins the analysis system, with global-scale information flowing in via the UKMO Global lateral boundary conditions.

OS47 introduced the **JOPA** (Joint Observation Processing Approach) code for observational data processing in the global coupled and marine-only models — the first operational deployment of components from the Met Office's Next Generation Modelling System (NGMS). JOPA adoption for UKV is staged separately and is expected in subsequent operational suites.

---

## What it provides
Convection-permitting deterministic forecasts of:
- Temperature (surface, screen-level, and upper-air)
- Wind components (10 m and upper-air on pressure and height levels)
- Specific and relative humidity
- Surface and mean sea-level pressure
- Total precipitation, precipitation rate, and precipitation type
- Cloud cover (total, high, mid, low) and cloud base / ceiling
- Convective indices and explicitly-resolved storm structure
- Visibility, wind gusts, and aviation-relevant fields
- Surface fluxes, radiation components, and boundary-layer diagnostics

UKV is particularly relied upon for short-range forecasts of small-scale and rapidly evolving weather such as showers, thunderstorms, fog, and local wind effects driven by complex coastlines and orography.

---

## Data availability

### Free Open Data — AWS Open Data Registry
- **Free?** Yes
- **License:** Creative Commons Attribution-ShareAlike 4.0 (CC BY-SA 4.0). British Crown copyright, the Met Office. Attribution and ShareAlike both required — note this differs from ECMWF's CC-BY-4.0 by adding the ShareAlike obligation.
- **Resolution:** 2 km on a Lambert Azimuthal Equal Area grid (see Basic details — this is **not** a 0.018° latitude–longitude grid despite the official description)
- **Format:** NetCDF4/HDF5, CF-1.7 + UKMO-1.0 conventions
- **Retention:** **2-year rolling archive** — significantly longer than typical operational open-data services, which usually retain only the most recent few days. This is a true rolling window with no fixed start: the earliest cycle present on 22 July 2026 was `20240721T1800Z`, exactly two years back. Older data is deleted as it ages out.
- **Bucket:** `s3://met-office-atmospheric-model-data` (region `eu-west-2`) — the **same bucket** as the global deterministic dataset, which lives under the `global-deterministic-10km/` prefix
  - Anonymous access, no AWS account or credentials required: `aws s3 ls --no-sign-request s3://met-office-atmospheric-model-data/`
  - **Path template:** `uk-deterministic-2km/{YYYYMMDD}T{HH}00Z/`
  - **Filename convention:** `{validity YYYYMMDD}T{HHMM}Z-PT{HHHH}H{MM}M-{parameter}.nc` — e.g. `20260721T1600Z-PT0001H00M-lightning_flash_accumulation-PT01H.nc`. The leading timestamp is the **validity time**, not the run time; the run time appears only in the directory prefix. The `MM` field is non-zero for the 15-minutely parameters (`00`, `15`, `30`, `45`).
  - **One file per parameter per timestep.** Approximate per-cycle volumes measured 21 July 2026: nowcast ~1,100 files / 4.2 GB; short ~3,790 files / 17.2 GB; medium ~5,020 files / 24.5 GB. At 24 cycles per day this is roughly 200 GB/day.
- **New-object notifications:** SNS topic `arn:aws:sns:eu-west-2:633885181284:met-office-atmospheric-model-data-object_created` — shared with the global dataset; subscribers must filter by prefix.
- **Official location:**
  - https://registry.opendata.aws/met-office-uk-deterministic/
  - Browsable index: https://met-office-atmospheric-model-data.s3.eu-west-2.amazonaws.com/index.html

### Microsoft Planetary Computer
A second, parallel distribution of the same data was launched on the Microsoft Planetary Computer in February 2026, alongside the equivalent UKMO Global release:
- **Near-surface collection:** https://planetarycomputer.microsoft.com/dataset/met-office-uk-deterministic-near-surface
- **Height levels collection:** https://planetarycomputer.microsoft.com/dataset/met-office-uk-deterministic-height
- **Pressure levels collection:** https://planetarycomputer.microsoft.com/dataset/met-office-uk-deterministic-pressure
- **Whole atmosphere collection:** https://planetarycomputer.microsoft.com/dataset/met-office-uk-deterministic-whole-atmosphere
- **Dataset group landing page:** https://planetarycomputer.microsoft.com/dataset/group/met-office-uk-deterministic
- The Planetary Computer release includes a 2-year historical archive (>600 TB combined with the UKMO Global dataset), pitched at researchers training and evaluating AI weather models against authoritative high-resolution forecasts.
- **STAC API:** `https://planetarycomputer.microsoft.com/api/stac/v1/collections/met-office-uk-deterministic-{theme}`. Items use the `forecast` STAC extension — `forecast:reference_datetime` is the run time, `forecast:horizon` the lead time, `datetime` the validity time.
- **Access:** assets resolve to `https://ukmoeuwest.blob.core.windows.net/deterministic/uk/...`. Anonymous requests are rejected; a **SAS token is required**, obtained without sign-in or registration from `https://planetarycomputer.microsoft.com/api/sas/v1/token/ukmoeuwest/deterministic` (~45 min validity). Free and unauthenticated to obtain, so not an approval gate, but it is an extra step relative to plain S3 access.
- **Archive depth:** oldest indexed run is `2023-12-01T00:00Z` — deeper than the AWS rolling window, which had rolled off everything before 21 July 2024 as of this check.
- **Variables per collection:** near-surface 28, whole-atmosphere 11, pressure 6, height 4.
- **⚠️ Note:** the STAC collection bounding box is given as approximately 13.7°W–4.3°E, 48.9°N–61.6°N. This is narrower than the actual grid extent (see "What area it covers") and appears to describe the UK region of interest rather than the delivered `uk_extended` domain.

#### ⚠️ Currency warning (verified 22 July 2026)
The Planetary Computer copy is **not currently keeping pace with AWS**:
- Latest STAC-indexed `forecast:reference_datetime` across all four UKV collections: **2026-06-26T08:00Z** (~26 days stale)
- Latest cycle with objects present in the Azure blob container: **20260716T0000Z** (~6 days stale)

The [UKMO Global](../../global/uk/ukmo-global.md) collections stall at almost exactly the same two points (2026-06-26T06:00Z indexed, 20260716T0000Z in blob storage), which suggests a platform-level ingest failure rather than a Met Office or dataset-specific problem. No Planetary Computer outage or retirement announcement covering this period was found; the 2024 announcement retired only the compute Hub and explicitly committed to keeping data and APIs available. Cause is **TBD**. Until resolved, **treat AWS S3 as the authoritative channel** and the Planetary Computer as useful mainly for the pre-July-2024 archive that AWS has already rolled off.

### Caveats for both channels
The data is offered on a free, **unsupported** basis — the Met Office does not recommend either distribution for critical business purposes. Service-desk support is available Mon-Fri 09:00-17:00 UTC with a 3-5 business day target response time, and only for non-operational queries.

---

### File internals and parameter set (verified by decoding live files, 21 July 2026)
Global attributes identifying the source configuration:

| Attribute | Value |
|---|---|
| `Conventions` | `CF-1.7, UKMO-1.0` |
| `title` | `UKV Model Forecast on UK 2 km Standard Grid` |
| `mosg__model_configuration` | `uk_det` |
| `mosg__grid_domain` | `uk_extended` |
| `mosg__grid_type` / `mosg__grid_version` | `standard` / `1.7.0` |
| `um_version` | `13.8` |
| `mosg__forecast_run_duration` | `PT12H`, `PT54H` or `PT120H` depending on tier |

- **Parameter counts:** 52 for nowcast and short runs; 56 for medium runs. The four medium-only parameters are `precipitation_accumulation-PT03H`, `rainfall_accumulation-PT03H`, `snowfall_accumulation-PT03H` and `wind_gust_at_10m_max-PT03H` — all tied to the 3-hourly T+57–T+120 segment that only medium runs reach.
- **Pressure levels: 33** (100000 Pa down to 1000 Pa). Unlike the global model, UKV did **not** gain the four upper-stratospheric levels (500, 200, 100, 40 Pa) at OS47 — a genuine asymmetry between the two entries.
- **Height levels grew asymmetrically at OS47:** `temperature_on_height_levels`, `wind_speed_on_height_levels` and `wind_direction_on_height_levels` now carry **56 levels** from 5 m to 7500 m (up from 33), while `cloud_amount_on_height_levels` remains at **33 levels** from 5 m to 6000 m. Do not assume a shared height coordinate across parameters.
- **Status flags:** pressure-level files carry a `flag` ancillary variable (`standard_name = status_flag`, `flag_values = [0, 1]`, `flag_meanings = above_surface_pressure below_surface_pressure`) on the same 3-D grid. Post-OS47 this is a single compressed ancillary variable named `flag`, replacing earlier per-parameter auxiliary coordinates such as `air_temperature_status_flag`. **Sub-surface points are not masked** — they contain extrapolated values and must be screened using this flag.
- **Precision:** a `least_significant_digit` attribute lossily truncates values before zlib compression (2 for screen temperature, 1 for pressure-level temperature). Decoded values are already rounded; full float32 precision is not recoverable.
- **Superblock and calendar:** HDF5 superblock version 2 as of late January 2026 (previously 0) — tools built against HDF5 ≤ 1.8.x may fail to open these files. Calendar metadata is now `standard` rather than `gregorian`; behaviourally identical.
- **CF variable names differ from filenames:** `temperature_at_screen_level.nc` contains `air_temperature`, and so on. Do not assume the filename token is the variable name.

#### Convection-permitting parameters absent from the global model
UKV's open-data set includes several fields that only make sense at convection-permitting scale and have no counterpart in the [UKMO Global](../../global/uk/ukmo-global.md) distribution:
- `lightning_flash_accumulation-PT01H` (flashes m⁻², hourly)
- `hail_fall_rate` and `hail_fall_accumulation-PT01H` (includes graupel)
- `height_AGL_at_cloud_base_where_cloud_cover_2p5_oktas`
- `height_AGL_at_freezing_level` and `height_AGL_at_wet_bulb_freezing_level`
- `radiation_flux_in_shortwave_diffuse_downward_at_surface`
- `landsea_mask` and `pressure_at_surface`

Conversely, UKV has no CAPE/CIN mixed-layer variants, no tropopause fields and no convection-scheme-partitioned precipitation, since UKV resolves convection explicitly rather than parameterizing it.

---

## Notes
- UKV's native 1.5 km inner-domain resolution is finer than the public 2 km distribution grid. Forecasts on the open-data servers are regridded from the native variable-resolution rotated-pole grid onto a Lambert Azimuthal Equal Area projection; users requiring full native resolution should consult the Met Office about commercial/research data services. Note that the widely repeated "0.018 degrees" figure in Met Office and AWS documentation does not describe the delivered grid, which is metre-based and projected.
- The Met Office uses a science configuration scheme for the UM: UK NWP systems use **Regional Atmosphere/Land 3 (RAL3)** as of OS47 (Bush et al., 2025), while global systems use **Global Coupled 5 (GC5)**. RAL3 was operationalised with OS47 in January 2026, replacing the previous regional configuration that had been in use under OS46.
- UKV runs on the Met Office's **new Microsoft Azure-based supercomputer**, which became operational in 2024 and on which OS47 was the first major modelling suite to run.
- Defence Regional Models and Crisis Area Models — used by the Met Office to support military operations and disaster relief — share the same RAL3 science configuration as UKV but can be deployed rapidly to other domains. These are not part of the open-data UKV distribution.
- The asymmetric forecast length (extended to 120 h at the 03 and 15 UTC cycles, 54 h at the others) gives UKV both a short-range update-heavy schedule and a medium-range capability via the extended cycles. Most users consume the 54-hour cycles.
- The companion ensemble system is [MOGREPS-UK](../../../ensemble_models/regional/uk/mogreps-uk.md), which shares UKV's domain and dynamical core.

---

## Recent version history

### OS47 / PS47 / RAL3 — operational 21 January 2026 (current)
First major science upgrade in over three years, and the first running on the Met Office's new Microsoft Azure supercomputer. Highlights for UKV:
- New **RAL3** science configuration replacing the previous regional configuration used under OS46
- **Improved UK cloud forecasts (including fog)**, with measurable benefits for aviation
- **Improved UK winter temperature forecasts**, benefiting energy and gritting/de-icing applications
- Better modelling of light rain in fronts, and improved cloud base heights
- Introduction of **CASIM cloud microphysics** — note that cloud ice water content is now calculated differently and now requires the sum of two STASH fields (0-012 and 0-271) rather than the single legacy field; this is handled transparently in StaGE for users consuming gridded products via Weather DataHub
- Companion change: MOGREPS-UK regional ensemble upgraded under the same OS47 release
- Open-data product changes: the `height_asl_on_pressure_levels` parameter was replaced by `geopotential_height_on_pressure_levels`; precision changes; new parameters; new vertical levels and timesteps for some fields
- Operational data availability is approximately 10–20 minutes later than under OS46

### OS46 — operational May 2022 to January 2026
Previous operational configuration, succeeded by OS47.

### Earlier history
The Unified Model entered Met Office operational service in 1991 as the world's first NWP model designed to span weather-and-climate timescales with a single codebase. The current ENDGame dynamical core replaced the older "New Dynamics" formulation in 2014. UKV's variable-resolution design with a 1.5 km inner domain was introduced as part of the Met Office's transition to convection-permitting operational forecasting in the 2010s, replacing earlier 4 km UK-area configurations.

---

## Official documentation
- Met Office numerical weather prediction overview: https://www.metoffice.gov.uk/research/approach/modelling-systems/unified-model/weather-forecasting
- Parallel Suite 47 (PS47) overview: https://www.metoffice.gov.uk/services/data/parallel-suite-47-ps47-overview
- Met Office Weather DataHub upcoming changes: https://datahub.metoffice.gov.uk/support/changes-and-updates
- Met Office services data pages: https://www.metoffice.gov.uk/services/data
- AWS Open Data Registry: https://registry.opendata.aws/met-office-uk-deterministic/
- Microsoft Planetary Computer dataset group: https://planetarycomputer.microsoft.com/dataset/group/met-office-uk-deterministic
- CEDA UKV dataset record: https://catalogue.ceda.ac.uk/uuid/f47bc62786394626b665e23b658d385f/

### Key references
- Bush, M., et al. (2025). *The Met Office Unified Model Regional Atmosphere 3 and JULES Regional Land 3 configurations.* (RAL3 documentation reference cited in the Met Office NWP overview.)
- Wood et al. (2014). *An inherently mass-conserving semi-implicit semi-Lagrangian discretization of the deep-atmosphere global non-hydrostatic equations.* QJRMS, 140, 1505–1520. (Documents the ENDGame dynamical core)
- Tang et al. (2013). *The benefits of the Met Office variable resolution NWP model for forecasting convection.* Meteorological Applications, 20, 417–426. (Documents the UKV variable-resolution design)
