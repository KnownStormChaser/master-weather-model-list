# UKMO Global (Unified Model — Global Deterministic)

## What this model is
The UKMO Global model is the global deterministic numerical weather prediction system operated by the UK Met Office.

It is a global configuration of the **Unified Model (UM)** — the Met Office's flagship NWP and climate modelling suite, named because the same model code is used across timescales from nowcasting to climate projections, and across spatial scales from kilometre-scale convective forecasting to centennial Earth system runs.

UKMO Global provides medium-range deterministic forecasts of atmospheric conditions worldwide and is the parent model that supplies lateral boundary conditions for the Met Office's regional UK Variable-resolution model ([UKV](../../regional/uk/ukv.md)), as well as for several Unified Model deployments operated by partner agencies (notably the Australian Bureau of Meteorology and the Korea Meteorological Administration).

The current operational version is part of **Operational Suite 47 (OS47)**, implemented on 21 January 2026 — the Met Office's first major science upgrade in over three years and the first run on its new Microsoft Azure-based supercomputer.

---

## Who runs it
- **Organization:** Met Office
- **Country / region:** United Kingdom

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global deterministic NWP
- **Model system / core:** Unified Model (UM), in its Global Coupled 5 (GC5) science configuration as of OS47
- **Dynamical formulation:** Non-hydrostatic, fully compressible deep-atmosphere equations on a regular latitude–longitude grid (ENDGame dynamical core); semi-Lagrangian, semi-implicit time integration
- **Convection-allowing:** No (deep convection is parameterized at ~10 km resolution)
- **Native horizontal grid:** N1280 — 2560 longitudes × 1920 latitudes, regular latitude–longitude
  - **Grid spacing:** 0.140625° longitude × 0.09375° latitude — the grid is **anisotropic**. ~10 km in mid-latitudes; zonal spacing is ~15.6 km at the equator and ~10.1 km at 50°N. The Met Office's published "approximately 0.09 degrees" describes the latitudinal spacing only.
  - **Cell-centred:** latitudes run −89.953125° to +89.953125°, longitudes −179.929688° to +179.929688°. The poles are not grid points; cell bounds are supplied via `latitude_bnds` / `longitude_bnds`.
  - **Earth model:** sphere, radius 6 371 229 m, prime meridian 0° (CF `latitude_longitude` grid mapping)
- **Vertical levels:** 70 — the L70(50t,20s)80 hybrid-eta set (50 levels below 18 km, 20 above)
- **Model top:** ~80 km
- **Forecast length:**
  - 168 hours (7 days) for 00 and 12 UTC cycles
  - **69 hours** for 06 and 18 UTC cycles (extended from 66 h at the OS47 upgrade; `radiation_flux_in_longwave_downward_at_surface` is the one exception, still ending at T+66)
  - **Documentation caveat:** the AWS Open Data Registry entry and all four Planetary Computer collection descriptions state 67 hours for the 06/18 UTC cycles. This is incorrect and appears to predate OS47 — live S3 listings for the 06Z cycle terminate at `PT0069H00M`, and the Met Office's own parameter-table documentation gives 66 h pre-January-2026 and 69 h after. Verified 22 July 2026.
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (Open Data):**
  - Hourly to +54 h
  - 3-hourly from +57 to +144 h
  - 6-hourly from +150 to +168 h
- **Data latency:** Documented as 3–6 hours after model run time. Observed upload times on 22 July 2026 sat at the top of or slightly beyond that range: the 00Z cycle uploaded between T+5h30m and T+6h31m, the 06Z cycle between T+5h30m and T+6h01m. Plan for ~6 h rather than 3 h.

---

## Data assimilation
UKMO Global uses **hybrid 4D-Var** data assimilation, combining a static background error covariance with flow-dependent covariances derived from the Met Office's global ensemble (MOGREPS-G).

OS47 introduced the **JOPA** (Joint Observation Processing Approach) code, the new JEDI-based observation processing system from the Met Office's Next Generation Modelling System (NGMS), replacing the legacy OPS code. OS47 also added assimilation of new **Mode-S aircraft observations** for improved upper-air wind coverage.

---

## What it provides
**Open-data subset (70 parameters as of the OS47 configuration, verified 22 July 2026).** The public distribution is a curated subset of full operational output — notably it contains no specific humidity, no precipitation type, and no cloud base or ceiling fields, all of which the Met Office distributes only through Weather DataHub or direct customer feeds. Post-OS47 additions to the open-data set include `cloud_amount_on_height_levels` and `radiation_flux_in_shortwave_total_downward_at_surface`, neither of which appears in the Met Office's own published parameter table.

Deterministic global forecasts of:
- Temperature (surface, screen-level, and upper-air)
- Wind components (10 m and upper-air on pressure and height levels)
- Specific and relative humidity
- Surface and mean sea level pressure
- Total precipitation, precipitation rate, and precipitation type
- Cloud cover (total, high, mid, low) and cloud base / ceiling
- Surface fluxes, radiation components, and boundary-layer diagnostics
- Visibility, wind gusts, and aviation-relevant fields

UKMO Global output also serves as boundary forcing for the Met Office's regional UKV deterministic and MOGREPS-UK ensemble systems.

---

## Data availability

### Free Open Data — AWS Open Data Registry
- **Free?** Yes
- **License:** Creative Commons Attribution-ShareAlike 4.0 (CC BY-SA 4.0). British Crown copyright, the Met Office. Attribution and ShareAlike both required — note this differs from ECMWF's CC-BY-4.0 by adding the ShareAlike obligation.
- **Resolution:** Native N1280 grid, no downsampling or reprojection
- **Format:** NetCDF4/HDF5, CF-1.7 + UKMO-1.0 conventions
- **Retention:** **2-year rolling archive** — significantly longer than typical operational open-data services, which usually retain only the most recent few days. This is a true rolling window with no fixed start: the earliest cycle present on 22 July 2026 was `20240721T1800Z`, exactly two years back. Older data is deleted as it ages out.
- **Bucket:** `s3://met-office-atmospheric-model-data` (region `eu-west-2`)
  - Anonymous access, no AWS account or credentials required: `aws s3 ls --no-sign-request s3://met-office-atmospheric-model-data/`
  - **Path template:** `global-deterministic-10km/{YYYYMMDD}T{HH}00Z/`
  - **Filename convention:** `{validity YYYYMMDD}T{HHMM}Z-PT{HHHH}H{MM}M-{parameter}.nc` — e.g. `20260722T0000Z-PT0000H00M-temperature_at_screen_level.nc`. Note the leading timestamp is the **validity time**, not the run time; the run time appears only in the directory prefix.
  - **One file per parameter per timestep.** A 00/12 UTC cycle is ~4,530 files and ~116 GB; a 06/18 UTC cycle is ~3,035 files and ~78 GB (measured 22 July 2026).
  - The parallel UKV dataset shares the same bucket under the `uk-deterministic-2km/` prefix.
- **New-object notifications:** SNS topic `arn:aws:sns:eu-west-2:633885181284:met-office-atmospheric-model-data-object_created` — useful for event-driven pipelines rather than polling.
- **Official location:**
  - https://registry.opendata.aws/met-office-global-deterministic/
  - Browsable index: https://met-office-atmospheric-model-data.s3.eu-west-2.amazonaws.com/index.html

### Microsoft Planetary Computer
A second, parallel distribution of the same data, hosted on Azure and indexed as STAC. The same CC BY-SA 4.0 licence applies. The data is split across **four** thematic collections rather than delivered as a single stream:

| Collection | Variables | URL |
|---|---|---|
| Near-surface | 24 | https://planetarycomputer.microsoft.com/dataset/met-office-global-deterministic-near-surface |
| Pressure levels | 7 | https://planetarycomputer.microsoft.com/dataset/met-office-global-deterministic-pressure |
| Whole atmosphere | 12 | https://planetarycomputer.microsoft.com/dataset/met-office-global-deterministic-whole-atmosphere |
| Height levels | 1 | https://planetarycomputer.microsoft.com/dataset/met-office-global-deterministic-height |

- **Dataset group landing page:** https://planetarycomputer.microsoft.com/dataset/group/met-office-global-deterministic
- **STAC API:** `https://planetarycomputer.microsoft.com/api/stac/v1/collections/met-office-global-deterministic-{theme}`. Items carry the `forecast` STAC extension — `forecast:reference_datetime` is the run time and `forecast:horizon` the lead time, while `datetime` is the validity time.
- **Access:** item assets point at `https://ukmoeuwest.blob.core.windows.net/deterministic/...`. Anonymous requests return HTTP 409; a **SAS token is required**, obtained without sign-in or registration from `https://planetarycomputer.microsoft.com/api/sas/v1/token/ukmoeuwest/deterministic` (~45 min validity). Since the token is issued anonymously and free, this is a technical step rather than an approval gate, but it does mean PC access cannot be a plain unauthenticated `curl` the way S3 can.
- **Archive depth:** deeper than AWS. The oldest indexed item is `20231204T0600Z` (4 December 2023) — roughly 2.6 years of history versus AWS's strict 2-year window. The collection metadata states forecasts remain available for at least two years from their data date, so this surplus may not be permanent.

#### ⚠️ Currency warning (verified 22 July 2026)
The Planetary Computer copy is **not currently keeping pace with AWS**:
- Latest STAC-indexed `forecast:reference_datetime` across all four global collections: **2026-06-26T06:00Z** (~26 days stale)
- Latest cycle with objects present in the Azure blob container: **20260716T0000Z** (~6 days stale) — nothing for 17–22 July

AWS was fully current over the same period (`20260722T1200Z` available). Whether this is a transient outage or the start of a wind-down is **TBD**. Until resolved, **treat AWS S3 as the authoritative channel** and PC as useful mainly for the pre-July-2024 archive that AWS has already rolled off.

Note also that the PC collection descriptions are stale in content, not just currency: they still describe the pre-OS47 67-hour short cycles and state "as of December 2025" for archive extent.

### Caveats for both channels
The data is offered on a free, **unsupported** basis — the Met Office does not recommend either distribution for critical business purposes. Service-desk support is available Mon-Fri 09:00-17:00 UTC with a 3-5 business day target response time, and only for non-operational queries.

### File internals (verified by decoding live files, 22 July 2026)
Global attributes on every file identify the source configuration and are the quickest way to confirm which suite produced a given file:

| Attribute | Value |
|---|---|
| `Conventions` | `CF-1.7, UKMO-1.0` |
| `source` | `Met Office Unified Model` |
| `mosg__model_configuration` | `gl_det` |
| `mosg__grid_domain` / `mosg__grid_type` | `global` / `standard` |
| `mosg__grid_version` | `1.7.0` |
| `um_version` | `13.8` |
| `mosg__forecast_run_duration` | `PT168H` |

- **Compression:** zlib level 1, shuffle off. Combined with a `least_significant_digit` attribute on the data variable (2 for screen-level temperature, 1 for pressure-level temperature), which lossily truncates precision — this is the "reduction in precision to offset data volume increases" from the January 2026 change notice. Decoders will return values already rounded; do not expect full float32 precision.
- **Superblock:** HDF5 superblock version 2 as of late January 2026 (previously version 0). Tools built against HDF5 ≤ 1.8.x may fail to open these files.
- **Calendar:** `standard` (was `gregorian` before the Iris upgrade). Behaviourally identical; values unchanged.
- **Pressure levels:** 37, in Pa — 100000 down to 1000 at the pre-2026 spacing, plus four new stratospheric levels at 500, 200, 100 and 40 Pa. Pre-January-2026 files carry 33.
- **Height levels** (`cloud_amount_on_height_levels`): 33 levels from 5 m to 6000 m.
- **Status flags:** pressure-level files carry a `flag` ancillary variable (`standard_name = status_flag`, `flag_values = [0, 1]`, `flag_meanings = above_surface_pressure below_surface_pressure`) on the same 3-D grid as the data. Post-OS47 this is a compressed ancillary variable named simply `flag`, replacing the earlier per-parameter auxiliary coordinates such as `air_temperature_status_flag`. **Points below the model surface are not masked** — they contain extrapolated values and must be screened using this flag.
- **CF variable names differ from filenames:** e.g. `temperature_at_screen_level.nc` contains `air_temperature`; `cloud_amount_on_height_levels.nc` contains `cloud_volume_fraction_in_atmosphere_layer`. Do not assume the filename token is the variable name.

---

## Notes
- The Unified Model is grid-point (rather than spectral) and is unusual in spanning weather and climate timescales with a single codebase. The same model is used by the Met Office for centennial climate predictions in the IPCC assessment reports as for tomorrow's short-range forecast.
- UKMO Global runs on the Met Office's **new Microsoft Azure-based supercomputer**, which became operational in 2024 and on which OS47 was the first major modelling suite to run.
- The Met Office uses a science configuration scheme for the UM: the global model uses **Global Coupled 5 (GC5)** as of OS47 — earlier configurations included GC2, GC3, and GC4 (operational from May 2022 to January 2026). GC5 is documented in Willett et al. (2025).
- The asymmetric forecast length (168 h at 00/12 UTC vs 67 h at 06/18 UTC) is structurally similar to the equivalent IFS, ICON, and GFS schedules — the longer runs at 00/12 anchor medium-range guidance, while the 06/18 runs target short-range applications.
- The Unified Model is also operated by partner agencies including the **Australian Bureau of Meteorology** (ACCESS-G global at 12.5 km) and the **Korea Meteorological Administration** (KMA Unified Model global at ~10 km).

---

## Recent version history

### OS47 / PS47 / GC5 — operational 21 January 2026 (current)
First major science upgrade in over three years, and the first running on the Met Office's new Microsoft Azure supercomputer. Per ECMWF's TIGGE model-change log, the global model switched to OS47 **from the 12 UTC cycle on 21 January 2026**. Highlights for the global deterministic model:
- New **GC5** science configuration (replacing GC4 which had been operational since May 2022)
- Stronger performance in temperature, winds, convection, and monsoon representation
- More realistic tropical cyclone structure (deeper, better-defined storms)
- Reduction in unrealistic precipitation "spikes"
- Improved near-surface temperature
- New **Mode-S aircraft observation** assimilation for better upper-air winds
- New **JOPA** (JEDI-based) observation processing replacing the legacy OPS code — the first operational deployment of components from the Met Office's Next Generation Modelling System (NGMS)
- Updated ocean data assimilation
- Companion change: MOGREPS-G global ensemble extended from 7 to 10 days (see [MOGREPS-G entry](../../../ensemble_models/global/uk/mogreps-g.md))
- Open-data product changes (AWS / Planetary Computer NetCDF):
  - `height_ASL_on_pressure_levels` replaced by `geopotential_height_on_pressure_levels` (confirmed absent / present respectively in live listings)
  - Pressure-level count raised from 33 to 37, adding 500, 200, 100 and 40 Pa
  - Status flags on pressure-level parameters renamed to a single `flag` ancillary variable and converted from auxiliary coordinate to compressed ancillary variable
  - Precision reduced via `least_significant_digit` truncation to offset volume growth
  - HDF5 superblock version 0 → 2; calendar metadata `gregorian` → `standard`
  - 06/18 UTC cycles extended from T+66 to T+69 (except `radiation_flux_in_longwave_downward_at_surface`, still T+66)
  - `precipitation_accumulation-PT01H` standardised in line with other accumulations: hourly steps extended to T+54, 3-hourly beginning at T+57
- Operational data availability is approximately 10–20 minutes later than under OS46
- **Note:** the freezing-rain accumulation parameter ID change announced alongside OS47 applies to the Weather DataHub **Atmospheric API**, not to the AWS/Planetary Computer NetCDF distribution. No freezing-rain parameter exists in either the global or UKV open-data listings.

### OS46 / GC4 — operational May 2022 to January 2026
Previous operational configuration with GC4 science, succeeded by OS47. GC4 was the first global coupled configuration with the new ENDGame dynamical core matured for production use.

### Earlier history
The Unified Model entered Met Office operational service in 1991 as the world's first NWP model designed to span weather-and-climate timescales with a single codebase. The current ENDGame dynamical core replaced the older "New Dynamics" formulation in 2014. The horizontal resolution upgrade to N1280 (~10 km) was implemented as part of the 2018 supercomputer transition.

---

## Official documentation
- Met Office numerical weather prediction overview: https://www.metoffice.gov.uk/research/approach/modelling-systems/unified-model/weather-forecasting
- Parallel Suite 47 (PS47) overview: https://www.metoffice.gov.uk/services/data/parallel-suite-47-ps47-overview
- Met Office Weather DataHub upcoming changes: https://datahub.metoffice.gov.uk/support/changes-and-updates
- Met Office services data pages: https://www.metoffice.gov.uk/services/data
- AWS Open Data Registry: https://registry.opendata.aws/met-office-global-deterministic/

### Key references
- Willett, M. R., et al. (2025). *The Met Office Unified Model Global Atmosphere 8.0 and JULES Global Land 9.0 configurations.* EGUsphere preprint. https://doi.org/10.5194/egusphere-2025-1829
- Walters et al. (2019). *The Met Office Unified Model Global Atmosphere 7.0/7.1 and JULES Global Land 7.0 configurations.* GMD, 12, 1909–1963.
- Williams et al. (2018). *The Met Office Global Coupled model 3.0 and 3.1 (GC3.0 and GC3.1) configurations.* JAMES, 10, 357–380.
- Wood et al. (2014). *An inherently mass-conserving semi-implicit semi-Lagrangian discretization of the deep-atmosphere global non-hydrostatic equations.* QJRMS, 140, 1505–1520. (Documents the ENDGame dynamical core)
