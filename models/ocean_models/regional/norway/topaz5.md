# TOPAZ5 (Arctic Ocean Physics Analysis and Forecast — MET Norway hourly distribution)

## What this model is
**TOPAZ5** is the operational Arctic Ocean physics and sea ice analysis and forecast system of the **Arctic Monitoring and Forecasting Centre (ARC MFC)**. It is a HYCOM–CICE coupled system with a **100-member Ensemble Kalman Filter**, producing daily 10-day forecasts of the three-dimensional Arctic ocean state and sea ice.

This entry documents **MET Norway's own THREDDS distribution** of the hourly product, which is openly accessible without registration. The same system is distributed through Copernicus Marine as `ARCTIC_ANALYSISFORECAST_PHY_002_001`.

**The MET Norway distribution carries more data than the Copernicus hourly product.** MET Norway states it directly on the catalog page: *"this product is also available from marine.copernicus.eu as ARCTIC_ANALYSISFORECAST_PHY_002_001 but hourly resolution has only surface parameters."* The THREDDS version publishes **all 40 depth levels at hourly resolution**. For anyone needing hourly Arctic subsurface fields, this channel is both richer and registration-free.

The trade-off is retention: MET Norway keeps **only the latest bulletin** — one 10-day run, no archive. Copernicus retains a rolling multi-year window.

*Live-verified against the operational THREDDS distribution on 2026-07-28 (bulletin 2026-07-27). Model internals below are drawn from Copernicus Marine product documentation and are marked as such.*

---

## Who runs it
- **Production Unit:** Arctic Monitoring and Forecasting Centre (ARC MFC)
- **Distributing institution (this channel):** Norwegian Meteorological Institute — `institution = "Met Norway, Henrik Mohns plass 1, 0313 Oslo, Norway"`
- **Country:** Norway
- **System development:** the TOPAZ line originates at the **Nansen Environmental and Remote Sensing Center (NERSC)**, which ran the first operational EnKF application in 2003. ARC MFC is a collaboration between NERSC, MET Norway, IMR, and DMI.
- **Programme:** Copernicus Marine Service — `credit = "E.U. Copernicus Marine Service Information (CMEMS)"`, `references = "https://marine.copernicus.eu/"`, `contact = "<servicedesk.cmems@mercator-ocean.eu>"`
- **Role in larger system:** supplies sea ice concentration and thickness to [ARCWAM](../../../wave_models/regional/norway/arcwam.md); supplies ocean forcing to the Arctic sea ice system (neXtSIM) and to the TOPAZ5-ECOSMO biogeochemistry product

---

## What area it covers
- **Coverage:** Arctic Ocean, Nordic Seas, and the northern North Atlantic
- **Grid dimensions:** 1185 (`x`) × 1137 (`y`)
- **Corner coordinates (verified from `latitude`/`longitude` arrays):**

| Grid corner | Latitude | Longitude |
|---|---|---|
| (y=0, x=0) | 42.538°N | 84.936°W |
| (y=0, x=1184) | 41.559°N | 3.532°W |
| (y=1136, x=0) | 50.654°N | 172.875°W |
| (y=1136, x=1184) | 49.389°N | 81.384°E |

Southern extent ~41.5°N at the corners, reaching the pole in the interior — consistent with [ARCWAM](../../../wave_models/regional/norway/arcwam.md)'s documented 41.12°N southern bound.

- **Projection:** polar stereographic, **true at the pole**
  - `grid_mapping_name = polar_stereographic`, `latitude_of_projection_origin = 90.0`, `straight_vertical_longitude_from_pole = -45.0`
  - `scale_factor_at_projection_origin = 1.0` — **no standard parallel**; scale is true at the pole, not at 60°N
  - `earth_radius = 6378273.0` (spherical)
  - `false_easting = 0.0`, `false_northing = 0.0`
  - proj4: `+proj=stere +lon_0=-45 +lat_0=90 +k=1 +R=6378273 +no_defs`
- **Horizontal resolution:** **6.25 km**, verified from `x`/`y` spacing
- **Projected extent:** `x` −3600 km → +3800 km; `y` −4300 km → +2800 km
- **Georeferencing:** 2-D `latitude` / `longitude` auxiliary variables
- **Bathymetry field:** `model_depth` (`sea_floor_depth_below_sea_level`)

> **The `x`/`y` axis units are `"100  km"`** — literally, with a double space, not metres. Values run −36.0 → 38.0 and −43.0 → 28.0 with 0.0625 spacing. Multiply by 100,000 to get metres before applying the proj4 string. Code that assumes CF projection coordinates are in metres will place the grid 100,000× too small.

> **Different projection from MET Norway's other polar-stereographic products.** [NorKyst v3](./norkyst-v3.md) and [Norshelf](./norshelf.md) use `lon_0=70`, `lat_ts=60`, WGS84 ellipsoid, with non-zero false easting/northing. This uses `lon_0=-45`, no standard parallel, and a spherical earth of radius 6378273 m. The four grids are mutually incompatible without reprojection.

---

## Basic details
- **Model type:** Regional coupled ocean + sea ice analysis and forecast, **ensemble-based with data assimilation**
- **Core ocean model:** **HYCOM** — `source = "NERSC-HYCOM model fields"`. Copernicus documentation and the peer-reviewed literature identify the version as **HYCOM 2.2.98**.
- **Sea ice model:** **CICE v5.1** (Los Alamos) — per Copernicus product documentation; not stated in file metadata
- **Biogeochemistry:** ECOSMO-II is coupled online in the parent system, distributed as a separate Copernicus product (`ARCTIC_ANALYSISFORECAST_BGC_002_004`); **not included in this distribution**
- **System name:** TOPAZ5; Copernicus product `ARCTIC_ANALYSISFORECAST_PHY_002_001`
- **Horizontal resolution:** 6.25 km (verified). *Copernicus product text says "6 km resolution at the North Pole"; the file title and grid both say 6.25 km — treat 6.25 km as authoritative.*
- **Vertical levels:** **40 fixed depth (z) levels**, 0 m to 4000 m — 0, 2, 4, 6, 10, 15, 20, 25, 30, 40, 50, 60, 70, 80, 90, 100, 125, 150, 175, 200, 250, 300, 350, 400, 450, 500, 600, 700, 800, 900, 1000, 1200, 1400, 1600, 1800, 2000, 2500, 3000, 3500, 4000
- **Vertical coordinate:** HYCOM runs a **hybrid isopycnal/sigma/z** coordinate natively; the distributed product is interpolated to the 40 fixed z-levels above. Native hybrid layers are not published.
- **Forecast length:** **10 days** — `Forecast_range = "10 days"`, verified as 240 hourly steps
- **Update frequency:** once daily
- **Bulletin:** daily, identified by `bulletin_date`; `bulletin_type = "Forecast"`
- **Temporal output resolution:** **hourly**
- **Conventions:** CF-1.4
- **Time encoding:** `seconds since 1970-1-1T00:00:00Z`
- **Archive availability:** **none on this channel** — latest bulletin only

### This is an ensemble-mean product
Per Copernicus documentation, TOPAZ5 runs a **100-member EnKF**, and the daily forecast delivered is the **average of 10 ensemble members**. The weekly analysis is likewise an ensemble average.

This matters for interpretation: the published fields are ensemble means, not a single deterministic trajectory. Ensemble-mean ocean fields are smoother than any individual member and will under-represent variance — relevant for eddy statistics, extremes, and anything driven by gradients. No spread or member-level output is distributed through this channel.

---

## Forcing
- **Atmospheric forcing:** ECMWF (per Copernicus and MET Norway documentation). Not named in file metadata.
- **Lateral boundary conditions:** not documented for this distribution (**TBD**)
- **River runoff:** not documented (**TBD**)
- **Tidal forcing:** **none** in this product. A separate ~3 km tide-forced TOPAZ configuration exists as `ARCTIC_ANALYSISFORECAST_PHY_TIDE_002_015`, delivering 15-minute instantaneous profiles — that is the system [ARCWAM](../../../wave_models/regional/norway/arcwam.md) draws its surface currents from, not this one.
- **Initial conditions:** from the weekly EnKF analysis, propagated forward daily

---

## Coupling
- **Ocean–sea ice:** online coupled HYCOM–CICE 5.1 within the parent system
- **Ocean–biogeochemistry:** ECOSMO-II coupled online in the parent system; not distributed here
- **Atmosphere:** one-way forcing from ECMWF; no coupled atmosphere
- **Downstream:** supplies sea ice concentration and thickness to [ARCWAM](../../../wave_models/regional/norway/arcwam.md), and ocean forcing to the neXtSIM-based Arctic sea ice forecast product

---

## Data assimilation
- **DA scheme:** **Ensemble Kalman Filter (EnKF)**, 100 members
- **Update cycle:** **weekly, not daily.** Per the ARC MFC Product User Manual, an analysis is produced on Thursdays valid for the preceding Monday, and is used to initialise a 100-member 7-day ensemble hindcast run the following Monday, whose ensemble feeds the next EnKF cycle. Daily forecasts between analyses are free-running from the most recent analysis.
- **Observation handling:** all observations are assimilated; rather than a background check, observation error is inflated for observations far from the model forecast, moderating their impact

### Assimilated observations (per ARC MFC documentation)
- **Sea ice concentration:** OSI SAF passive microwave
- **Sea level anomaly (altimetry):** Jason-3, CryoSat-2, Sentinel missions
- **Sea surface temperature, in-situ temperature and salinity profiles:** additional streams documented in the PUM

The inverse barometer effect is **not** included in the assimilated sea level.

> **The weekly DA cadence is the most important thing to understand about this product.** A "10-day forecast" issued on a Sunday is running six days from its last analysis before the forecast period even starts. Forecast skill is not uniform across the week. Anyone doing verification or skill work should track bulletin date against the Thursday analysis schedule.

---

## What it provides

### 3D fields (on the 40 z-levels)
| Variable | Standard name | Units |
|---|---|---|
| `thetao` | `sea_water_potential_temperature` | °C |
| `so` | `sea_water_salinity` | 1e-3 |
| `ubaroclin` | `baroclinic_x_sea_water_velocity` | m s⁻¹ |
| `vbaroclin` | `baroclinic_y_sea_water_velocity` | m s⁻¹ |

### 2D ocean fields
| Variable | Standard name | Units |
|---|---|---|
| `zos` | `sea_surface_height_above_geoid` | m |
| `mlotst` | `ocean_mixed_layer_thickness_defined_by_sigma_theta` | m |
| `ubarotrop` | `barotropic_x_sea_water_velocity` | m s⁻¹ |
| `vbarotrop` | `barotropic_y_sea_water_velocity` | m s⁻¹ |

### Sea ice fields
| Variable | Standard name | Units |
|---|---|---|
| `siconc` | `sea_ice_area_fraction` | 1 |
| `sithick` | `sea_ice_thickness` | m |
| `vxsi` | `sea_ice_x_velocity` | m s⁻¹ |
| `vysi` | `sea_ice_y_velocity` | m s⁻¹ |

### Static fields
`model_depth` (bathymetry), `latitude`, `longitude`, `stereographic` (grid mapping)

> **Currents require two steps that most ocean products don't.** There is **no total velocity variable**. Total current = `ubaroclin` + `ubarotrop` (and likewise for v). And both are in **grid x/y directions**, not geographic east/north — the standard names say `_x_` and `_y_`, and the grid is rotated relative to true north everywhere except along the −45° meridian. Rotation must be derived from the projection; no `angle` variable is supplied.
>
> This is a real difference from every other ocean entry in this repository. [NorKyst v3](./norkyst-v3.md) and [Norshelf](./norshelf.md) publish `u_eastward`/`v_northward` directly; Copernicus global products publish total `uo`/`vo`. Code written against those will silently produce wrong currents here.

---

## Data availability
- **Is the data free?** Yes on this channel — no registration, no API key, no approval gate. *(The Copernicus distribution of the same system requires free registration.)*
- **License:** **No `license` attribute is declared in any file.** The files carry `credit = "E.U. Copernicus Marine Service Information (CMEMS)"` and point to `https://marine.copernicus.eu/` for references. Two frameworks are in play:
  - MET Norway's **server-wide free-data terms** (NLOD / CC BY 4.0, attribution required), linked from every THREDDS catalog page
  - The **Copernicus Marine licence**, which the `credit` attribute invokes and which requires the CMEMS attribution statement

  In practice, cite both: MET Norway as distributor and "E.U. Copernicus Marine Service Information (CMEMS)" as the credit line the files themselves specify. **This is the least clear licensing situation of any MET Norway entry in this repository** — worth confirming if you need a definitive answer. Compare [Norshelf](./norshelf.md), which also lacks a per-file licence but has no third-party credit line complicating it.
- **Is the data downloadable?** Yes — direct HTTP and OPeNDAP
- **Data formats:** NetCDF (CF-1.4)
- **Access methods:** OPeNDAP (`dodsC`), HTTP file server (`fileServer`), WMS, WCS
- **Product identifier (Copernicus equivalent):** `ARCTIC_ANALYSISFORECAST_PHY_002_001`
- **DOI (Copernicus product):** https://doi.org/10.48670/moi-00003
- **Top-level catalog:** https://thredds.met.no/thredds/fou-hi/topaz5-arc-1hr.html

### 1. Individual hourly files
- **Catalog:** https://thredds.met.no/thredds/catalog/fou-hi/topaz5-arc-1hr_files/catalog.html
- **Naming:** `<YYYYMMDD>T<HH>_hr-metno-MODEL-topaz5-ARC-b<YYYYMMDD>-fv02.0.nc`
  - `hr` — hourly product
  - `b<YYYYMMDD>` — **bulletin date** (the production run)
  - `fv02.0` — file format version 2.0
- **One time step per file**, `time = 1`
- **File size:** ~201 MB each
- **Live-measured (bulletin 2026-07-27): exactly 240 files**, covering 2026-07-27T00:00Z through 2026-08-05T23:00Z — hours 0 to +239. Total ~48 GB per bulletin.
- **Flat catalog, no subdirectories** — verified, zero `catalogRef` entries.

### 2. Hourly Aggregation Best Estimate
- **OPeNDAP:** `https://thredds.met.no/thredds/dodsC/fou-hi/topaz5-arc-1hr_be`
- **Live-measured: 240 hourly steps, 2026-07-27T00:00Z → 2026-08-05T23:00Z** — dimensions `x=1185`, `y=1137`, `depth=40`, `time=240`

> **Despite the name, this is not a long best-estimate time series.** It aggregates exactly the 240 files of the current bulletin and nothing more — its `bulletin_date` is the same single date. This is a convenience wrapper over one run, not a multi-year record like the aggregations offered by [NorKyst v3](./norkyst-v3.md), [Norshelf](./norshelf.md), or [WW3 4 km](../../../wave_models/regional/norway/met-norway-ww3.md). The naming is misleading if you're used to those.

### Retention — latest bulletin only
The catalog is explicit: *"only contains the forecast from the latest model run."* Verified — every one of the 240 files carries `b20260727`, and there are no year or month subdirectories.

**There is no archive on this channel.** For historical TOPAZ5 output, use Copernicus Marine, whose PUM documents a very different retention policy: data older than two years is removed automatically; all daily 10-day forecasts are kept for three months, after which one forecast per week is retained; and all weekly 7-day hindcasts are maintained permanently, giving a continuous hindcast series.

**The choice between channels is therefore a genuine trade-off:**

| | **MET Norway THREDDS (this entry)** | **Copernicus Marine** |
|---|---|---|
| Registration | None | Required (free) |
| Hourly vertical coverage | **All 40 depth levels** | **Surface parameters only** |
| Retention | Latest bulletin only | ~2 years, tiered |
| Hindcast series | None | Continuous weekly 7-day hindcasts |

Use MET Norway for near-real-time hourly subsurface fields; use Copernicus for anything retrospective.

### Publication latency (live-measured, bulletin 2026-07-27)
All 240 files published between **21:00:52 and 21:03:41 UTC on the bulletin date** — approximately **+21 hours** relative to the first field time (00:00Z of the bulletin date), with the whole set landing in under three minutes.

This is by far the longest latency of any MET Norway marine product in this repository ([NorKyst v3](./norkyst-v3.md) +4 h 20 m, [Norshelf](./norshelf.md) +10 h 13 m). Expected for a 100-member ensemble system with a Copernicus delivery chain, but it means the "analysis hour" of a bulletin is nearly a day old when it appears.

### Terms of service
MET Norway asks users **not to spawn parallel OPeNDAP sessions or file downloads**, reserving the right to block IPs causing traffic overload. With 240 files per bulletin this is a real constraint — sequential fetching or OPeNDAP subsetting is required. WMS is not recommended beyond simple demonstration. Status: https://status.met.no

---

## Notes

- **`x`/`y` units are "100 km", not metres.** See *What area it covers*. This is the most likely source of silent georeferencing failure.

- **No total-velocity variable, and velocities are grid-relative.** See *What it provides*. The second most likely source of silent error.

- **Weekly, not daily, assimilation.** Unlike most operational ocean systems, the analysis updates once a week. See *Data assimilation*.

- **Ensemble mean, not deterministic.** The published fields average 10 members. Variance is suppressed; no spread is distributed.

- **Sea surface height reference.** `zos` carries `standard_name = sea_surface_height_above_geoid`. The inverse barometer effect is not included in the assimilated altimetry, per ARC MFC documentation.

- **No tides.** Currents here are subtidal. The tide-resolving Arctic configuration is a separate Copernicus product at ~3 km with 15-minute output.

- **Relationship to MET Norway's other Arctic systems.** [ARCWAM](../../../wave_models/regional/norway/arcwam.md) takes its sea ice concentration and thickness from this product and its surface currents from the separate tidal configuration — so ice fields in ARCWAM files are re-distributed TOPAZ5 output and should be cited to this system. MET Norway's `fou-hi` catalog also carries **Barents-2.5 km (ROMS EPS)** and **AICE**, neither yet documented here; published evaluation of the data-driven AICE system benchmarks it against TOPAZ5 and Barents-2.5 km, so all three are natural companions.

- **Naming.** MET Norway's catalog title is *"Topaz5 Arctic Physical ocean and sea ice forecasting system (hourly forecast only)"*; the page heading is *"Topaz 5 forecast with hourly resolution"*; file titles read *"Arctic Ocean Physics Analysis and Forecast, 6.25 km 1-hourly frequency"*. The Copernicus product name is *"Arctic Ocean Physics Analysis and Forecast"*. This entry uses **TOPAZ5**. Note "TOPAZ" has a long version history (TOPAZ4 was 12.5 km) — always qualify with the version number.

---

## Version history

MET Norway publishes no version history for this distribution. The following is from Copernicus and published literature.

### Current — TOPAZ5
HYCOM 2.2.98 coupled to CICE 5.1 and ECOSMO-II, 100-member EnKF, 6.25 km, hourly output, 10-day forecasts from a 10-member average. Distributed files carry format version `fv02.0`.

### Predecessor — TOPAZ4
HYCOM-based with EnKF at ~12.5 km average horizontal resolution, delivering daily-mean fields. The resolution and output-frequency jump from TOPAZ4 to TOPAZ5 (12.5 km daily-mean → 6.25 km hourly) is substantial; the two should not be concatenated for time-series work.

### 2003 — TOPAZ line begins
NERSC's TOPAZ was the first operational application of the Ensemble Kalman Filter, forecasting North Atlantic and Arctic ocean and sea ice with HYCOM-CICE.

---

## Official documentation
- MET Norway hourly TOPAZ5 catalog: https://thredds.met.no/thredds/fou-hi/topaz5-arc-1hr.html
- Files catalog: https://thredds.met.no/thredds/catalog/fou-hi/topaz5-arc-1hr_files/catalog.html
- MET Norway ocean and sea ice THREDDS root: https://thredds.met.no/thredds/fou-hi/fou-hi.html
- MET Norway ocean model overview: https://ocean.met.no/models
- **Copernicus product page:** https://data.marine.copernicus.eu/product/ARCTIC_ANALYSISFORECAST_PHY_002_001/description
- **ARC MFC Product User Manual (covers all ARC products):** https://documentation.marine.copernicus.eu/PUM/CMEMS-ARC-PUM-002-ALL.pdf
- **DOI:** https://doi.org/10.48670/moi-00003
- NERSC / Norwegian Center for Data Assimilation — TOPAZ: https://www.data-assimilation.no/products/topaz-arctic-ocean-forecasts
- MET Norway licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- MET Norway THREDDS service status: https://status.met.no

### Key references
- Sakov, P., Counillon, F., Bertino, L., Lisæter, K. A., Oke, P. R., and Korablev, A. (2012). TOPAZ4: an ocean-sea ice data assimilation system for the North Atlantic and Arctic. *Ocean Science*, 8, 633–656.
- Bleck, R. (2002). An oceanic general circulation model framed in hybrid isopycnic-Cartesian coordinates. *Ocean Modelling*, 4, 55–88.
- Hunke, E. C., et al. (2017). CICE: the Los Alamos Sea Ice Model, version 5.1.
- Yumruktepe, V. Ç., et al. (2022). ECOSMO-II biogeochemical model coupled to TOPAZ.
- Ali, A., et al. (2025). TOPAZ5 Arctic Ocean system description. *(cited in MET-AICE documentation)*
- MET-AICE v1.0 evaluation, benchmarking against TOPAZ5 and Barents-2.5 km: *Geoscientific Model Development*, 18, 9751 (2025). https://gmd.copernicus.org/articles/18/9751/2025/

> **Documentation note:** unlike the rest of the `fou-hi` tree, this system is thoroughly documented — but the documentation lives on the Copernicus side, not MET Norway's. The THREDDS catalog page carries four sentences. Everything about model internals, assimilation, and retention in this entry comes from Copernicus product documentation or peer-reviewed literature; everything about grid, variables, timing, and this channel's retention was live-verified from the files.
