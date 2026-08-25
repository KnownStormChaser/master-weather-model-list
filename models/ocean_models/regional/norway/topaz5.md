# TOPAZ5 (Arctic Ocean Physics Analysis and Forecast)

## What this model is
**TOPAZ5** is the operational Arctic Ocean physics and sea ice analysis and forecast system of the **Arctic Monitoring and Forecasting Centre (ARC MFC)**, run by the **Norwegian Meteorological Institute (MET Norway)**. It couples HYCOM 2.2.98 to CICE 5.1 on a hybrid isopycnal/sigma/z vertical coordinate and is distinguished among operational ocean systems by its data assimilation: a **100-member deterministic Ensemble Kalman Filter** run weekly, rather than the variational schemes used by every other Copernicus Marine physics product.

The system is distributed through **two independent channels** covering different parts of its output:

- **Copernicus Marine** as `ARCTIC_ANALYSISFORECAST_PHY_002_001` — four datasets (hourly surface, 6-hourly / daily / monthly 3D), registration required, ~2-year tiered archive
- **MET Norway's own THREDDS server** as `fou-hi/topaz5-arc-1hr` — hourly output at **all 40 depth levels**, no registration, latest bulletin only

**Neither channel is a superset of the other**, and MET Norway says so explicitly on its catalog page: *"this product is also available from marine.copernicus.eu as ARCTIC_ANALYSISFORECAST_PHY_002_001 but hourly resolution has only surface parameters."* Anyone needing hourly Arctic subsurface fields must use MET Norway; anyone needing anything retrospective, vertical velocity, ice age, or ice classification must use Copernicus. Both are documented below.

*Copernicus channel live-verified 2026-08-25 (STAC records and ARCO Zarr metadata). MET Norway channel live-verified 2026-07-28 (bulletin 2026-07-27) and re-checked 2026-08-25.*

---

## Who runs it
- **Production Unit:** Norwegian Meteorological Institute (MET Norway) — Copernicus Marine producer of record; metadata contact `ARC-METNO-OSLO-NO`. Files carry `institution = "Met Norway, Henrik Mohns plass 1, 0313 Oslo, Norway"`.
- **Country:** Norway
- **Programme or coordinating body:** Copernicus Marine Service — Arctic Monitoring and Forecasting Centre (ARC MFC), a collaboration between NERSC, MET Norway, IMR, and DMI
- **System development:** the TOPAZ line originates at the **Nansen Environmental and Remote Sensing Center (NERSC)**, which ran the first operational EnKF application in 2003. Files carry `source = "NERSC-HYCOM model fields"`. The PUM contributor list spans both institutes.
- **Role in any larger system:** supplies sea ice concentration and thickness to [ARCWAM](../../../wave_models/regional/norway/arcwam.md); supplies the ocean state that the ARC MFC biogeochemistry product (`ARCTIC_ANALYSISFORECAST_BGC_002_004`) runs inside; is the nudging target for [Barents-2.5km EPS](./barents-25km-eps.md)

---

## What area it covers
- **Coverage:** Arctic Ocean, Nordic Seas, and the ice-covered northern North Atlantic
- **Domain bounds (documented):** north of 63°N plus the ice-covered North Atlantic (PUM). This is the practical forecast domain, not the grid extent.
- **Grid dimensions (native):** 1185 (`x`) × 1137 (`y`) polar stereographic
- **Corner coordinates (verified from `latitude`/`longitude` arrays):**

| Grid corner | Latitude | Longitude |
|---|---|---|
| (y=0, x=0) | 42.538°N | 84.936°W |
| (y=0, x=1184) | 41.559°N | 3.532°W |
| (y=1136, x=0) | 50.654°N | 172.875°W |
| (y=1136, x=1184) | 49.389°N | 81.384°E |

Southern extent ~41.5°N at the corners, reaching the pole in the interior — consistent with [ARCWAM](../../../wave_models/regional/norway/arcwam.md)'s documented 41.12°N southern bound, and with the Marine Data Store STAC record's reported 41.559°N. **The 63°N figure in the PUM and the ~41.5°N figure in the catalogue are not in conflict**; the first is the domain of interest, the second is the corner extent of the grid.

- **Projection:** polar stereographic, **true at the pole**
  - `grid_mapping_name = polar_stereographic`, `latitude_of_projection_origin = 90.0`, `straight_vertical_longitude_from_pole = -45.0`
  - `scale_factor_at_projection_origin = 1.0` — **no standard parallel**; scale is true at the pole, not at 60°N
  - `earth_radius = 6378273.0` (spherical, **not WGS84**)
  - `false_easting = 0.0`, `false_northing = 0.0`
  - proj4 in file: `+proj=stere +lon_0=-45 +lat_0=90 +k=1 +R=6378273 +no_defs`
  - proj4 in PUM text: `+units=m +proj=stere +a=6378273.0 +b=6378273.0 +lon_0=-45.0 +lat_0=90.0 +lat_ts=90.0 +ellps=sphere` — equivalent, but the PUM adds `+lat_ts=90` and `+ellps=sphere` where the file uses `+k=1`
- **Horizontal resolution:** **6.25 km**, verified from `x`/`y` spacing
- **Projected extent:** `x` −3600 km → +3800 km; `y` −4300 km → +2800 km
- **Georeferencing:** 2-D `latitude` / `longitude` auxiliary variables
- **Bathymetry field:** `model_depth` (`sea_floor_depth_below_sea_level`)
> **The ARC MFC operates two grids, not one.** This product is on the 6.25 km grid (1185 × 1137). [ARCWAM](../../../wave_models/regional/norway/arcwam.md), [TOPAZ6](../../../storm_surge_models/regional/norway/topaz6.md), and the neXtSIM-F sea ice product (`ARCTIC_ANALYSISFORECAST_PHY_ICE_002_011`) are all on a common **3 km grid of 2467 × 2367** — live-verified. Those three combine without regridding; this product does not combine with any of them without it.

> **The `x`/`y` axis units are `"100  km"`** — literally, with a double space, not metres. Values run −36.0 → 38.0 and −43.0 → 28.0 with 0.0625 spacing. Multiply by 100,000 to get metres before applying the proj4 string. Code that assumes CF projection coordinates are in metres will place the grid 100,000× too small. The PUM notes that ArcGIS and QGIS users must convert.

> **Different projection from MET Norway's other polar-stereographic products.** [NorKyst v3](./norkyst-v3.md) and [Norshelf](./norshelf.md) use `lon_0=70`, `lat_ts=60`, WGS84 ellipsoid, with non-zero false easting/northing. This uses `lon_0=-45`, no standard parallel, and a spherical earth of radius 6378273 m. The four grids are mutually incompatible without reprojection.

### Reprojected lat-lon grid (Copernicus default datasets)
The Copernicus datasets are served **by default on a regular lat-lon grid**, not the native projection: **4500 × 641**, 0.0625° latitude × 0.08° longitude, cut at **50°N** (live-verified). Vectors are rotated to eastward/northward. The PUM warns that this reprojection slightly smooths the fields and slightly enlarges the land mask. Native-grid variants are available but undocumented in the PUM — see *Data availability*.

---

## Basic details
- **Model type:** Regional coupled ocean + sea ice analysis and forecast, **ensemble-based with data assimilation**
- **Core ocean model:** **HYCOM 2.2.98** — `source = "NERSC-HYCOM model fields"`; version per the PUM production table
- **Sea ice model:** **CICE v5.1** (Los Alamos) with **5 ice thickness categories** — per Copernicus documentation; not stated in file metadata
- **Biogeochemistry:** ECOSMO-II is coupled online in the parent system, distributed as a separate Copernicus product (`ARCTIC_ANALYSISFORECAST_BGC_002_004`); **not included in either physics channel**. Note that the BGC forecast runs on **member 001 only**, not the ensemble mean — see *Relationship to other entries*.
- **System name:** TOPAZ5; Copernicus product `ARCTIC_ANALYSISFORECAST_PHY_002_001`
- **Horizontal resolution:** 6.25 km (verified). *Copernicus product text says "6 km resolution at the North Pole" and the dataset identifiers say `6km`; the file `title` attribute and the grid spacing both say 6.25 km — treat 6.25 km as authoritative.*
- **Vertical levels (native):** **50 hybrid layers**
- **Vertical levels (delivered):** **40 fixed depth (z) levels**, 0 m to 4000 m — 0, 2, 4, 6, 10, 15, 20, 25, 30, 40, 50, 60, 70, 80, 90, 100, 125, 150, 175, 200, 250, 300, 350, 400, 450, 500, 600, 700, 800, 900, 1000, 1200, 1400, 1600, 1800, 2000, 2500, 3000, 3500, 4000
- **Vertical coordinate:** HYCOM runs a **hybrid isopycnal/sigma/z** coordinate natively — isopycnal in the stratified open ocean, terrain-following in shallow coastal areas, z-levels in the mixed layer and unstratified deep ocean. Native hybrid layers are not published in either channel.
- **Vertical interpolation:** cubic spline conserving layer-mean values until April 2024; **linear thereafter** for NRT forecasts, to avoid overshoot in strong vertical gradients. Horizontal interpolation from the native Arakawa C grid is bilinear.
- **Forecast length:** **10 days** (240 hourly steps, verified); **13 days on Thursdays**, when the series starts three days before the bulletin date
- **Update frequency:** daily forecast; **weekly analysis**
- **Bulletin:** daily, identified by `bulletin_date`; `bulletin_type = "Forecast"`. The forecast run is performed in the evening of the bulletin date.
- **Temporal output resolution:** hourly instantaneous, 6-hourly mean, daily mean, monthly mean — availability differs by channel, see *Data availability*
- **Conventions:** CF-1.4
- **Time encoding:** `seconds since 1970-1-1T00:00:00Z`
- **Bathymetry source:** GEBCO14
- **Initial conditions (system spinup):** WOA18 followed by 20 years of spinup
- **Initial conditions (operational cycling):** each daily forecast initialises from the previous day's run, except Thursdays when the weekly analysis is used

### This is an ensemble-mean product
TOPAZ5 runs a **100-member EnKF** for the weekly analysis, and the daily forecast integrates the **first 10 members** forward, delivering their mean.

This matters for interpretation: the published fields are ensemble means, not a single deterministic trajectory. Ensemble-mean ocean fields are smoother than any individual member and will under-represent variance — relevant for eddy statistics, extremes, and anything driven by gradients. **No spread or member-level output is distributed through either channel.**

Note also that the two ensemble sizes differ: daily forecast fields are a **10-member** mean, weekly best-estimate fields a **100-member** mean. Effective smoothing therefore differs between the hindcast and forecast portions of a continuous time series.

---

## Forcing
- **Atmospheric forcing:** ECMWF **IFS HRES**, 6-hourly, **1/10°**, with completion of the solar radiation daily cycle. Not named in file metadata; per the PUM production table.
- **River runoff:** daily forecasts with up to 30-day lead time from [GloFAS v4.0](../../../hydrological_models/global/eu/cems-glofas-forecast.md)
- **Greenland freshwater:** ice-sheet mass loss from GRACE (GrIS CCI project), distributed over **35 terminal glaciers**
- **Lateral boundary conditions:** "Global HR analysis and forecasting system" (PUM). **The parent system is not named.** [GLO12](../../global/france/glo12.md) is the obvious candidate but this is not stated — *TBD, worth confirming with ARC MFC.*
- **Tidal forcing:** **none.** TOPAZ5 has no tidal constituents; currents here are subtidal. The tide-resolving Arctic configuration is TOPAZ6 (`ARCTIC_ANALYSISFORECAST_PHY_TIDE_002_015`), a separate ~3 km product with 15-minute output — that is the system [ARCWAM](../../../wave_models/regional/norway/arcwam.md) draws its surface currents from, not this one.

---

## Coupling
- **Ocean–sea ice:** online coupled HYCOM–CICE 5.1 via **ESMF**
- **Ocean–biogeochemistry:** ECOSMO-II coupled online in the parent system; not distributed in either physics channel
- **Ocean–wave:** none. TOPAZ5 is not wave-coupled; ARCWAM receives ice fields one-way and returns nothing.
- **Atmosphere:** one-way forcing from ECMWF IFS HRES; no coupled atmosphere
- **Mixing scheme:** K-Profile Parameterization (KPP). The PUM notes KPP limits mixing of warm Atlantic water to the Central Arctic surface, presented as an advantage for users driving standalone sea ice forecasts.
- **Downstream:** supplies sea ice concentration and thickness to [ARCWAM](../../../wave_models/regional/norway/arcwam.md); ocean forcing to the neXtSIM-F Arctic sea ice product; nudging target for [Barents-2.5km EPS](./barents-25km-eps.md)

---

## Data assimilation
- **DA scheme:** **Ensemble Kalman Filter in its deterministic formulation (DEnKF**, Sakov and Oke 2008), **100 dynamic members**
- **Update cycle:** **weekly, not daily.** An analysis is produced on Thursdays valid for the preceding Monday, and is used to initialise a 100-member 7-day ensemble hindcast run the following Monday, whose ensemble feeds the next EnKF cycle. The ensemble mean is temporally averaged into daily-mean best estimates for that 7-day window. Daily forecasts between analyses are free-running from the most recent analysis.
- **Increment application:** **no Increment Analysis Update.** The assimilation effect appears abruptly at midnight on Tuesday. The DA software is designed to limit increment sharpness, but users doing time-series work should expect a weekly discontinuity.
- **Observation handling:** all observations are assimilated. Rather than a background check, **observation error is inflated** for observations far from the model forecast, moderating their impact.
- **Special handling:** super-observations for high-resolution data; **asynchronous (FGAT) assimilation for SLA and sea ice drift**

### Assimilated observations
- **Sea level anomaly:** AVISO, from **Jason-3, CryoSat-2, Sentinel-3A, Sentinel-3B, Sentinel-6A**. Mean SSH reference **CNES-CLS22**. The SLA reference level moved from a 7-year to a 20-year mean, with incoming observations offset by 2 cm to compensate.
- **Sea surface temperature:** **OSTIA** L4
- **Sea ice concentration:** **OSI SAF**
- **Sea ice drift:** **OSI SAF**
- **Sea ice thickness:** **CS2SMOS** (CryoSat-2 + SMOS merged), **October to April only**
- **In-situ T/S profiles:** Arctic In Situ TAC / CORIOLIS — all profiles **except moorings and surface drifters**

The inverse barometer effect is **not** included in the assimilated sea level.

> **The weekly DA cadence is the most important thing to understand about this product.** A "10-day forecast" issued on a Sunday is running six days from its last analysis before the forecast period even starts. Forecast skill is not uniform across the week. Anyone doing verification or skill work should track bulletin date against the Thursday analysis schedule — and against the Tuesday-midnight increment discontinuity.

---

## What it provides

**The variable set differs by channel and by dataset.** The table below is live-verified against all five delivered datasets.

| Variable | Standard name | Units | MET Norway hourly | CMEMS hourly | CMEMS 6h / daily / monthly |
|---|---|---|---|---|---|
| `thetao` | `sea_water_potential_temperature` | °C | **3D (40 lev)** | surface only | **3D (40 lev)** |
| `so` | `sea_water_salinity` | 1e-3 | **3D (40 lev)** | surface only | **3D (40 lev)** |
| `ubaroclin` / `vbaroclin` | `baroclinic_{x,y}_sea_water_velocity` | m s⁻¹ | **3D (40 lev)** | — | — |
| `ubarotrop` / `vbarotrop` | `barotropic_{x,y}_sea_water_velocity` | m s⁻¹ | 2D | — | — |
| `vxo` / `vyo` | `sea_water_{x,y}_velocity` | m s⁻¹ | — | surface | **3D (40 lev)** |
| `wo` | `upward_sea_water_velocity` | m s⁻¹ | — | — | **3D (40 lev)** |
| `zos` | `sea_surface_height_above_geoid` | m | ✓ | ✓ | ✓ |
| `mlotst` | `ocean_mixed_layer_thickness_defined_by_sigma_theta` | m | ✓ | — | ✓ |
| `bottomT` | `sea_water_potential_temperature_at_sea_floor` | °C | — | — | ✓ |
| `stfbaro` | `ocean_barotropic_streamfunction` | m³ s⁻¹ | — | — | ✓ |
| `siconc` | `sea_ice_area_fraction` | 1 | ✓ | ✓ | ✓ |
| `sithick` | `sea_ice_thickness` | m | ✓ | ✓ | ✓ |
| `vxsi` / `vysi` | `sea_ice_{x,y}_velocity` | m s⁻¹ | ✓ | ✓ | ✓ |
| `sisnthick` | `surface_snow_thickness` | m | — | ✓ | ✓ |
| `siage` | `age_of_sea_ice` | day | — | — | ✓ |
| `siconc_fy` | `sea_ice_classification` (first-year ice fraction) | 1 | — | — | ✓ |
| `sialb` | `sea_ice_albedo` | 1 | — | — | ✓ |

**Static fields (both channels):** `model_depth` (bathymetry), `latitude`, `longitude`, `stereographic` (grid mapping), `x`, `y`.

The **first-year ice fraction, ice age, and ice albedo** set is unusually rich for an ocean physics product and is the practical reason to prefer the Copernicus 3D datasets when ice classification matters. Conversely **vertical velocity is Copernicus-only** and available at no better than 6-hourly.

> **Currents require two steps on the MET Norway channel that most ocean products don't.** There is **no total velocity variable** there. Total current = `ubaroclin` + `ubarotrop` (and likewise for v). The Copernicus datasets instead publish total `vxo`/`vyo` directly, so the two channels are **not drop-in substitutes for current work**.
>
> **Both channels deliver velocity in grid x/y directions on the native grid**, not geographic east/north — the standard names say `_x_` and `_y_`, and the grid is rotated relative to true north everywhere except along the −45° meridian. Rotation must be derived from the projection; no `angle` variable is supplied. The PUM provides both MATLAB and Python rotation examples. **The Copernicus default (reprojected) datasets have already been rotated to eastward/northward**; the `originalGrid` variants have not.
>
> This is a real difference from every other ocean entry in this repository. [NorKyst v3](./norkyst-v3.md) and [Norshelf](./norshelf.md) publish `u_eastward`/`v_northward` directly; Copernicus global products publish total `uo`/`vo`. Code written against those will silently produce wrong currents here.

---

## Data availability

- **Product identifier:** `ARCTIC_ANALYSISFORECAST_PHY_002_001`
- **DOI:** https://doi.org/10.48670/moi-00001
- **Data formats:** NetCDF-4 (CF-1.4) on both channels; **Zarr** additionally for the Copernicus ARCO copies

### Channel comparison

| | **MET Norway THREDDS** | **Copernicus Marine** |
|---|---|---|
| Registration | None | Required (free) |
| Hourly vertical coverage | **All 40 depth levels** | **Surface only** |
| 3D output frequency | Hourly | 6-hourly at best |
| Velocity representation | Baroclinic + barotropic, grid-relative | Total `vxo`/`vyo`; rotated by default |
| Vertical velocity, ice age, ice class, albedo | — | ✓ |
| Retention | **Latest bulletin only** | ~2 years, tiered |
| Hindcast series | None | Continuous weekly 7-day hindcasts |
| Licence clarity | Ambiguous (see below) | Copernicus Marine licence |

Use MET Norway for near-real-time hourly subsurface fields; use Copernicus for anything retrospective, for vertical velocity, or for sea ice classification.

### Channel 1 — Copernicus Marine
- **Is the data free?** Yes, with registration
- **License:** Copernicus Marine Service licence (free after self-registration)
- **Datasets:**
  - `cmems_mod_arc_phy_anfc_6km_detided_PT1H-i` — hourly instantaneous, surface
  - `cmems_mod_arc_phy_anfc_6km_detided_PT6H-m` — 6-hourly mean, 3D
  - `cmems_mod_arc_phy_anfc_6km_detided_P1D-m` — daily mean, 3D
  - `cmems_mod_arc_phy_anfc_6km_detided_P1M-m` — monthly mean, 3D
  - Current dataset version tag: `_202311`
- **Native vs reprojected grid:** each dataset exists in **two variants**. The default serves the reprojected regular lat-lon grid; an **`--ext--originalGrid`** variant serves the native polar stereographic grid. The PUM's Data Access note warns about the reprojection but **does not mention that the original-grid variants exist**. Users needing the native grid must request them explicitly.
- **File naming:** `{YYYYMMDD}[T{HH}]_{hr|6hm|dm|mm}-metno-MODEL-topaz5-ARC-b{bulletin}-fv02.0.nc`; monthly files omit the bulletin token (`202501_mm-metno-MODEL-topaz5-ARC-fv02.0.nc`)
- **File size:** ~0.45 GB per daily hourly-surface file; ~0.26 GB per daily-mean, 6-hourly-mean, and monthly-mean file
- **ARCO endpoints:** `timeChunked.zarr`, `geoChunked.zarr`, and `downsampled4.zarr` under `https://s3.waw3-1.cloudferro.com/mdl-arco-time-008/arco/…` and `…/mdl-arco-geo-008/arco/…`; native NetCDF under `https://s3.waw3-1.cloudferro.com/mdl-native-10/native/…`. `downsampled4` is offered on the reprojected datasets only.
- **Delivery mechanism:** Copernicus Marine Toolbox; WMTS for rendered layers
- **Target delivery time:** forecast — following day at 00:30 UTC. Analysis — Wednesdays at 00:00 UTC per the product table, though the PUM text says assimilation runs Thursdays; see *Notes*.
- **Retention:** data older than two years is removed automatically; all daily 10-day forecasts kept three months, after which one forecast per week is retained; **all weekly 7-day hindcasts maintained**, giving a continuous best-estimate series.
- **Official access:** https://data.marine.copernicus.eu/product/ARCTIC_ANALYSISFORECAST_PHY_002_001/description

### Channel 2 — MET Norway THREDDS
- **Is the data free?** Yes — no registration, no API key, no approval gate
- **License:** **No `license` attribute is declared in any file.** The files carry `credit = "E.U. Copernicus Marine Service Information (CMEMS)"` and point to `https://marine.copernicus.eu/` for references. Three declarations are in play, none of which agree:
  - The THREDDS **catalog metadata** declares `CC BY 3.0`
  - MET Norway's **server-wide free-data terms** declare NLOD and CC BY 4.0, linked from every catalog page
  - The **files themselves** credit Copernicus Marine Service Information, invoking the Copernicus Marine licence

  In practice, cite both MET Norway as distributor and "E.U. Copernicus Marine Service Information (CMEMS)" as the credit line the files specify. **Open access is established; open licensing is not.** This is the least clear licensing situation of any MET Norway entry in this repository — *TBD, worth a direct question to MET Norway.* Compare [Norshelf](./norshelf.md), which also lacks a per-file licence but has no third-party credit line complicating it.
- **Access methods:** OPeNDAP (`dodsC`), HTTP file server (`fileServer`), WMS, WCS
- **Top-level catalog:** https://thredds.met.no/thredds/catalog/fou-hi/topaz5-arc-1hr.html

**Individual hourly files**
- **Catalog:** https://thredds.met.no/thredds/catalog/fou-hi/topaz5-arc-1hr_files/catalog.html
- **Naming:** `<YYYYMMDD>T<HH>_hr-metno-MODEL-topaz5-ARC-b<YYYYMMDD>-fv02.0.nc` — `hr` hourly product, `b<YYYYMMDD>` bulletin date, `fv02.0` file format version
- **One time step per file**, `time = 1`
- **File size:** ~201 MB each
- **Live-measured: exactly 240 files per bulletin**, hours 0 to +239, ~48 GB total. Verified for bulletin 2026-07-27 (covering 2026-07-27T00:00Z → 2026-08-05T23:00Z) and re-confirmed for bulletin 2026-08-24.
- **Flat catalog, no subdirectories** — verified, zero `catalogRef` entries

**Hourly Aggregation Best Estimate**
- **OPeNDAP:** `https://thredds.met.no/thredds/dodsC/fou-hi/topaz5-arc-1hr_be`
- **Live-measured: 240 hourly steps** — dimensions `x=1185`, `y=1137`, `depth=40`, `time=240`

> **Despite the name, this is not a long best-estimate time series.** It aggregates exactly the 240 files of the current bulletin and nothing more — its `bulletin_date` is the same single date. This is a convenience wrapper over one run, not a multi-year record like the aggregations offered by [NorKyst v3](./norkyst-v3.md), [Norshelf](./norshelf.md), or [WW3 4 km](../../../wave_models/regional/norway/met-norway-ww3.md). The naming is misleading if you're used to those.

**Retention — latest bulletin only.** The catalog is explicit: *"only contains the forecast from the latest model run."* Verified — every file in a given listing carries the same `b<date>` token, and there are no year or month subdirectories. **There is no archive on this channel.**

**Publication latency (live-measured, bulletin 2026-07-27).** All 240 files published between **21:00:52 and 21:03:41 UTC on the bulletin date** — approximately **+21 hours** relative to the first field time, with the whole set landing in under three minutes. Re-checked for bulletin 2026-08-24: the T00 file carries `last-modified` 21:15:10 UTC on the bulletin date, consistent. This matches the PUM's statement that the forecast run executes in the evening of the bulletin date.

This is by far the longest latency of any MET Norway marine product in this repository ([NorKyst v3](./norkyst-v3.md) +4 h 20 m, [Norshelf](./norshelf.md) +10 h 13 m). Expected for a 100-member ensemble system with a Copernicus delivery chain, but it means the "analysis hour" of a bulletin is nearly a day old when it appears.

**Terms of service.** MET Norway asks users **not to spawn parallel OPeNDAP sessions or file downloads**, reserving the right to block IPs causing traffic overload. With 240 files per bulletin this is a real constraint — sequential fetching or OPeNDAP subsetting is required. WMS is not recommended beyond simple demonstration. Status: https://status.met.no

> **Verified 2026-08-25** (Copernicus channel): grid dimensions, level list, projection parameters, per-dataset variable inventory, and the `originalGrid` variant structure confirmed from Marine Data Store STAC records and live ARCO Zarr metadata. **Verified 2026-07-28 and re-checked 2026-08-25** (MET Norway channel): grid, variables, file inventory, sizes, latency, and retention confirmed from live NetCDF headers, OPeNDAP DDS, and catalog XML.

---

## Relationship to other entries

*The two distribution channels for this system are documented above under* Data availability *rather than here, since both describe the same entry.*

- **Coupled counterpart:** [ARCWAM](../../../wave_models/regional/norway/arcwam.md) (`ARCTIC_ANALYSIS_FORECAST_WAV_002_014`) takes sea ice concentration and thickness from this system one-way, so ice fields in ARCWAM files are re-distributed TOPAZ5 output and should be cited to this system. ARCWAM's **surface currents** come from TOPAZ6, not from here. There is no return path — TOPAZ5 is not wave-coupled.

- **Downstream nesting:** [Barents-2.5km EPS](./barents-25km-eps.md) weakly nudges all 24 of its members toward this system, inheriting observational constraint indirectly rather than assimilating anything itself.

- **Deliberate overlap with sibling ARC MFC products.** The PUM is unusually explicit that variables are duplicated across the Arctic portfolio, and states which product to prefer:
    - **Surface currents and sea surface height** are duplicated in **[TOPAZ6](../../../storm_surge_models/regional/norway/topaz6.md)** (`ARCTIC_ANALYSISFORECAST_PHY_TIDE_002_015`), a 3 km HYCOM system with 36 FES2014 tidal constituents and atmospheric pressure, at 15-minute output. The operator expects TOPAZ6 to be better near coasts and TOPAZ5 better in the open ocean, where data assimilation helps; **MET Norway intends to nudge TOPAZ6 toward TOPAZ5 in November 2026**. Two differences make the duplicated fields non-interchangeable rather than merely differently-resolved:
    - **TOPAZ6 includes the inverse barometer effect; this product excludes it** (see *Notes*). The sea level fields differ systematically for that reason alone.
    - **TOPAZ6's surface currents have included Stokes drift since June 2024**, making them total rather than Eulerian, while retaining the Eulerian CF standard name. This product's currents are Eulerian. Comparing the two is comparing different quantities.
  - **Sea ice variables** are duplicated in **neXtSIM-F** (`ARCTIC_ANALYSISFORECAST_PHY_ICE_002_011`). Users wanting ocean–ice consistency are directed here; users wanting more accurate ice drift are directed to neXtSIM-F. MET Norway intends to close the gap by coupling neXtSIM to HYCOM.
  - **Biogeochemistry** (`ARCTIC_ANALYSISFORECAST_BGC_002_004`) runs inside this system's forecast, but on **member 001 only** rather than the ensemble mean, for performance reasons. Out of scope for this repository, but the asymmetry matters: the BGC forecast is not consistent with the published physics ensemble mean.

- **Which to use when:** for the Arctic ocean state with observational constraint, this entry. For coastal water level, surge, and tidal currents, TOPAZ6. For sea ice drift specifically, neXtSIM-F. MET Norway's own regional systems — [Norshelf](./norshelf.md) and [NorKyst v3](./norkyst-v3.md) — cover Norwegian coastal waters at higher resolution without ice assimilation. MET Norway's `fou-hi` catalog also carries **AICE**, not yet documented here; published evaluation of the data-driven AICE system benchmarks it against TOPAZ5 and Barents-2.5 km.

- **Programme context:** see [`COPERNICUS.md`](../../../../COPERNICUS.md) for the full Copernicus Marine portfolio.

---

## Notes

- **`x`/`y` units are "100 km", not metres.** See *What area it covers*. This is the most likely source of silent georeferencing failure.

- **Velocity representation differs between the two channels.** MET Norway publishes baroclinic and barotropic components that must be summed; Copernicus publishes total velocity. Both are grid-relative on the native grid. See *What it provides* — the second most likely source of silent error.

- **The resolution is 6.25 km, not 6 km.** The dataset identifiers (`…_6km_…`) and the Copernicus product description both say 6 km. The delivered grid spacing is 0.0625 in units of 100 km = 6.25 km, and the files' own `title` attribute reads "6.25 km". The 6 km figure is a rounding now frozen into the dataset identifiers.

- **The datasets are labelled `detided` but the model has no tides.** The PUM production table gives tidal constituents as none. The `detided` token appears in all four Copernicus dataset identifiers with no explanation — most plausibly a convention distinguishing these from the tidal TOPAZ6 product rather than a description of processing applied. **Unexplained; flagged, not resolved.**

- **Weekly, not daily, assimilation, with no IAU.** See *Data assimilation*. The Tuesday-midnight discontinuity is a genuine artefact in continuous time series.

- **Ensemble mean, not deterministic — and two different ensemble sizes.** Forecast fields average 10 members, best-estimate fields average 100. Variance is suppressed and the degree of suppression differs across the hindcast/forecast join.

- **Sea surface height reference.** `zos` carries `standard_name = sea_surface_height_above_geoid`. The inverse barometer effect is not included. For total Arctic water level, TOPAZ6 is the intended product.

- **`bottomT` is not the seafloor value.** It is interpolated to **10 m above the seabed** and appears discontinuous where the interpolation crosses between isopycnic layers. Copernicus 3D datasets only.

- **`wo` is in m s⁻¹, not m day⁻¹**, and is diagnosed from the displacement of hybrid vertical coordinates rather than computed directly. The PUM flags the unit explicitly because the SI value is small enough to look wrong.

- **Bottom grid cells flicker between ocean and land.** Isopycnic layers can outcrop and become vanishingly thin at the bottom of the water column; those layers are dropped from the vertical interpolation, so a few bottom cells intermittently mask as land. The operator suggests carrying forward the masked values at affected pixels and has promised a fix in a future upgrade. This matters for anyone using TOPAZ5 for downscaling.

- **Vertical coordinate naming differs between distribution formats.** Native NetCDF uses `depth`, positive down, 0 → 4000 m. The Copernicus ARCO Zarr copies use `elevation`, **negative**, −4000 → 0 m. Same levels, opposite sign convention.

- **Thursday forecasts are 13 days, not 10.** Because Thursday runs initialise from the weekly analysis rather than the previous day's forecast, the delivered series starts three days before the bulletin date. Anyone concatenating bulletins into a continuous series must handle the overlap.

- **Analysis schedule is stated inconsistently in the PUM.** The dataset table gives analysis target delivery as Wednesdays at 00:00 UTC; the processing and catalogue-updating sections both say assimilation runs Thursdays; the product-use section says the assimilation effect appears at midnight on Tuesday. These cannot all be right. **Flagged, not resolved** — the Thursday statement appears twice and is the more likely production day, but the delivery day is genuinely ambiguous.

- **The 6-hourly dataset is described in the future tense.** The PUM summary says a 6-hourly 3D forecast with 3-day lead "will also be included." It exists and is live, carrying the full 17-variable 3D set. Stale text.

- **The PUM annex is stale.** The sample header reproduced in the annex is from a 2023 file and carries `title = "Arctic Ocean Physics Reanalysis"` — wrong for an NRT analysis-and-forecast product. Live files correctly read "Arctic Ocean Physics Analysis and Forecast, 6.25 km …". Do not cite the annex for current metadata.

- **Naming.** MET Norway's catalog title is *"Topaz5 Arctic Physical ocean and sea ice forecasting system (hourly forecast only)"*; the page heading is *"Topaz 5 forecast with hourly resolution"*; file titles read *"Arctic Ocean Physics Analysis and Forecast, 6.25 km 1-hourly frequency"*. The Copernicus product name is *"Arctic Ocean Physics Analysis and Forecast"*. This entry uses **TOPAZ5**. Note "TOPAZ" has a long version history (TOPAZ4 was 12.5 km) — always qualify with the version number.

---

## Version history

MET Norway publishes no version history for its distribution. The following is from Copernicus documentation and published literature. Documented change history is thin: the product-specific PUM is at Issue 1.0 (November 2025), recorded as an initial version transferred from the combined ARC manual, so it carries no change log of its own. The catalogue record gives a creation date of 2013-03-13 (the TOPAZ product line, not TOPAZ5) and last modification 2025-11-25. TOPAZ5 data coverage begins **2021-07-05**.

### Current — TOPAZ5
HYCOM 2.2.98 coupled to CICE 5.1 and ECOSMO-II, 100-member DEnKF, 6.25 km, 10-day forecasts from a 10-member average. Distributed files carry format version `fv02.0`.

### April 2024
Vertical interpolation from hybrid layers to fixed levels changed from **cubic spline to linear** for NRT forecasts, to avoid overshoot in regions of strong vertical gradients. The PUM states linear interpolation will become the default in future upgrades.

### Undated changes recorded in the PUM
- Monthly mean dataset (`P1M-m`) added
- SLA reference level moved from a 7-year to a 20-year mean, with a 2 cm offset applied to incoming observations

### Predecessor — TOPAZ4
HYCOM-based with EnKF at ~12.5 km average horizontal resolution, delivering daily-mean fields. The resolution and output-frequency jump from TOPAZ4 to TOPAZ5 (12.5 km daily-mean → 6.25 km hourly) is substantial; the two should not be concatenated for time-series work.

### 2003 — TOPAZ line begins
NERSC's TOPAZ was the first operational application of the Ensemble Kalman Filter, forecasting North Atlantic and Arctic ocean and sea ice with HYCOM-CICE.

*TBD — a fuller version history would need the QUID and the Copernicus Marine User Notification Service archive.*

---

## Official documentation
- **Copernicus product page:** https://data.marine.copernicus.eu/product/ARCTIC_ANALYSISFORECAST_PHY_002_001/description
- **Product User Manual (product-specific, Issue 1.0, November 2025 — authoritative):** https://documentation.marine.copernicus.eu/PUM/CMEMS-ARC-PUM-002-001.pdf
- **Product User Manual (combined ARC MFC, Issue 5.20, June 2025 — still served; covers all seven ARC products):** https://documentation.marine.copernicus.eu/PUM/CMEMS-ARC-PUM-002-ALL.pdf
- **Quality Information Document (QUID):** https://documentation.marine.copernicus.eu/QUID/CMEMS-ARC-QUID-002-001.pdf
- **Synthesis Quality Overview (SQO):** https://documentation.marine.copernicus.eu/SQO/CMEMS-ARC-SQO-002-001.pdf
- **DOI:** https://doi.org/10.48670/moi-00001
- MET Norway hourly TOPAZ5 catalog: https://thredds.met.no/thredds/catalog/fou-hi/topaz5-arc-1hr.html
- Files catalog: https://thredds.met.no/thredds/catalog/fou-hi/topaz5-arc-1hr_files/catalog.html
- MET Norway ocean and sea ice THREDDS root: https://thredds.met.no/thredds/catalog/fou-hi/fou-hi.html
- MET Norway ocean model overview: https://ocean.met.no/models
- NERSC / Norwegian Center for Data Assimilation — TOPAZ: https://www.data-assimilation.no/products/topaz-arctic-ocean-forecasts
- MET Norway licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- MET Norway THREDDS service status: https://status.met.no

### Key references
- Sakov, P. and Oke, P. R. (2008). A deterministic formulation of the ensemble Kalman filter: an alternative to ensemble square root filters. *Tellus A*, 60(2), 361–371.
- Sakov, P., Counillon, F., Bertino, L., Lisæter, K. A., Oke, P. R., and Korablev, A. (2012). TOPAZ4: an ocean-sea ice data assimilation system for the North Atlantic and Arctic. *Ocean Science*, 8, 633–656.
- Melsom, A., Counillon, F., LaCasce, J., and Bertino, L. (2012). Forecasting search areas using ensemble ocean circulation modeling. *Ocean Dynamics*, 62(8), 1245–1257.
- Bleck, R. (2002). An oceanic general circulation model framed in hybrid isopycnic-Cartesian coordinates. *Ocean Modelling*, 4, 55–88.
- Hunke, E. C., et al. (2017). CICE: the Los Alamos Sea Ice Model, version 5.1.
- Yumruktepe, V. Ç., et al. (2022). ECOSMO-II biogeochemical model coupled to TOPAZ.
- Ali, A., et al. (2025). TOPAZ5 Arctic Ocean system description. *(cited in MET-AICE documentation)*
- MET-AICE v1.0 evaluation, benchmarking against TOPAZ5 and Barents-2.5 km: *Geoscientific Model Development*, 18, 9751 (2025). https://gmd.copernicus.org/articles/18/9751/2025/

> **Documentation note:** unlike the rest of the `fou-hi` tree, this system is thoroughly documented — but the documentation lives on the Copernicus side, not MET Norway's. The THREDDS catalog page carries four sentences. Everything about model internals and assimilation in this entry comes from Copernicus product documentation or peer-reviewed literature; everything about grids, variables, timing, and per-channel retention was live-verified from the files.
