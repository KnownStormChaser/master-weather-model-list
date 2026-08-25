# TOPAZ6 (Arctic Ocean Tidal and Storm Surge Analysis and Forecast)

## What this model is
TOPAZ6 is the Arctic surface water level and current forecast of the Copernicus Marine **Arctic Monitoring and Forecasting Centre (ARC MFC)**, run by **MET Norway**. The PUM's own summary states it "contains tides, storm surge and wave signals," and the product-use section calls it "the TOPAZ6 **storm surge and tidal** forecast."

**It forecasts total water level, not a surge residual.** `zos` is sea surface height above the geoid with the tidal elevation, the inverse barometer response, and the wave contribution all included in a single field. There is no separate surge, tide, or residual variable — the components are not distributed apart.

Structurally it is a HYCOM ocean model (the same code as [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md), at 3 km instead of 6.25 km) run **without data assimilation** but **with explicit tides, atmospheric pressure, and wave forcing**, from which only three surface fields are published: sea surface height and the two surface velocity components, at **quarter-hourly** resolution.

> **Why this is filed under storm surge rather than ocean physics.** The delivered product is three surface variables at 15-minute cadence resolving tides and surge — no 3D state, no temperature, no salinity, no ice. The ocean-model template's core sections would all be empty. The operator's own characterisation in the [TOPAZ5 PUM](../../../ocean_models/regional/norway/topaz5.md) is "storm surge and tidal forecast." The Copernicus product title says "Tidal Analysis and Forecast," the STAC description says "Surface Currents Analysis and Forecast system," and the file `title` attribute says "Arctic Ocean Physics Analysis and Forecast, 3 km quarter-hourly instantaneous" — four names across three categories. The categorisation here follows the delivered content and the operator's functional description.

The official Copernicus Marine product identifier is `ARCTIC_ANALYSISFORECAST_PHY_TIDE_002_015`. The system name is **TOPAZ6**.

*Verified 2026-08-25 from the PUM (Issue 1.0, November 2025), Marine Data Store STAC records, and live ARCO Zarr metadata.*

---

## Who runs it
- **Organization:** Norwegian Meteorological Institute (**MET Norway**) — Copernicus Marine producer of record
- **Country / region:** Norway
- **Programme:** Copernicus Marine Service — Arctic Monitoring and Forecasting Centre (ARC MFC)
- **Model development:** the TOPAZ line originates at **NERSC**; files carry `source = "NERSC-HYCOM model fields"` and `institution = "NERSC, Thormoehlens gate 47, N-5006 Bergen, Norway"`. The PUM contributor list spans both institutes.

> **The institution attribute names NERSC, the Copernicus producer names MET Norway.** The same institute-versus-operator split appears in [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md), where the file `institution` reads *Met Norway* instead. The two sibling products therefore disagree with each other about which institute to stamp — worth stating consistently across both entries rather than following each file.

---

## What area it covers
- **Coverage:** Arctic Ocean, Nordic Seas, and the ice-covered North Atlantic — documented as "north of 63°N and ice-covered North Atlantic Ocean"
- **Domain bounds (catalogue):** the STAC record reports a southern bound of **41.13°N**, the corner extent of the polar stereographic grid rather than the practical forecast domain. This is the same figure the [ARCWAM](../../../wave_models/regional/norway/arcwam.md) entry documents, and the same distinction flagged in [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md).
- **Grid dimensions (live-verified, native):** **2467 × 2367** polar stereographic at **3 km**
- **Inundation coverage:** **none.** The domain stops at the coastline; there is no wetting and drying and no overland cell. This is a shelf-and-open-ocean water level product, not a coastal inundation model.

> **All three ARC MFC 3 km products share one grid.** TOPAZ6, [ARCWAM](../../../wave_models/regional/norway/arcwam.md), and the neXtSIM-F sea ice product (`ARCTIC_ANALYSISFORECAST_PHY_ICE_002_011`) are all delivered on the identical 2467 × 2367 polar stereographic grid — live-verified. They can be combined without regridding, which is not true of [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md) at 6.25 km (1185 × 1137).

---

## Basic details
- **Model type:** Deterministic tidal and storm surge forecast (surface extraction from a full 3D ocean model)
- **Core hydrodynamic model:** **HYCOM v2.2.98**, with **CICE v5.1** (5 ice thickness categories) coupled via **ESMF**
- **Dimensionality:** the model runs **full 3D baroclinic** on 50 hybrid isopycnal/terrain-following/z layers; **only surface fields are published**. This distinguishes TOPAZ6 from the barotropic 2D solvers typical of this model class — the delivered water level carries a baroclinic and mesoscale signal that a depth-averaged surge model would not produce.
- **Forecast length:** **10 days**
- **Update frequency / cycles:** daily, one cycle
- **Target delivery time:** **01:00 UTC** the following day
- **Temporal output resolution:** **15 minutes, instantaneous.** No temporal averaging is applied.
- **Initial conditions (spin-up):** WOA18 followed by **4 years** of spin-up — against TOPAZ5's 20 years

---

## Grid and bathymetry
- **Grid type:** structured polar stereographic, interpolated from the native Arakawa C grid. Delivered variables sit at the output cell centre.
- **Horizontal resolution:** **3 km**, near-uniform — the projection is conformal, giving very even spacing across the domain
- **Projection:** `+units=m +proj=stere +a=6378273.0 +b=6378273.0 +lon_0=-45.0 +lat_0=90.0 +lat_ts=90.0 +ellps=sphere` — a **sphere of radius 6 378 273 m, not WGS84**
- **Mesh size:** 2467 × 2367 = ~5.84 M cells
- **Bathymetry source:** **GEBCO14**
- **Wetting and drying:** **No.** Overland inundation is not represented and the domain does not extend onto land.

> **The `x`/`y` axis units are "100 km", not metres**, as in [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md). The PUM notes that ArcGIS and QGIS users must convert. Code assuming CF projection coordinates are in metres will place the grid 100 000× too small.

---

## Vertical datum and reference level
- **Vertical datum:** the **geoid**. `zos` carries `standard_name = sea_surface_height_above_geoid`.
- **What the water level field is measured relative to:** the PUM states it explicitly — *"the difference between the actual sea surface height at any given time and place, and that which it would have if the ocean were at rest."* So this is **total water level above the geoid**, not a residual above a tidal prediction and not an anomaly relative to a model mean.
- **Inverse barometer:** **included.** Surface pressure from ECMWF is applied. This is the key difference from [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md), whose `zos` explicitly **excludes** the inverse barometer effect — the two products' sea level fields are not interchangeable.
- **Datum conversion offsets provided?** **No.** No static datum-offset field and no per-station conversion table is distributed. Users needing chart datum, LAT, or a national datum must supply their own conversion.
- **Mean sea level trend / SLR handling:** not documented (**TBD**). No steric or sea-level-rise offset is mentioned in the PUM.

---

## Tide handling
- **Are tides included?** **Yes**, and only as part of the total. There is no surge-only or tide-only variant.
- **Tidal forcing source:** **FES2014**, applied at the lateral boundaries alongside the global parent system's fields
- **Number of constituents:** **36**, as in FES2014 — by a wide margin the largest tidal set among the products in this repository's Copernicus comparison group (AMM7 has 16, IBI and AMM15 11, Mediterranean and Black Sea 8)
- **Separation of components:** **none.** Tide, surge residual, and total water level are not distributed separately; only the combined `zos` is published. Anyone needing the meteorological residual must compute a harmonic analysis themselves or difference against a tidal prediction.
- **Tide–surge interaction:** modelled **nonlinearly within the run** — tides, pressure, and wind all enter the same HYCOM integration rather than being superposed afterwards

---

## Forcing and coupling
- **Meteorological forcing — wind:** **ECMWF IFS HRES**, 6-hourly, 1/10°, with completion of the solar radiation daily cycle
- **Meteorological forcing — pressure:** **ECMWF surface pressure, applied as an inverse barometer** — co-equal with wind stress for surge, and explicitly included here
- **Wave contribution:** **one-way from [ARCWAM](../../../wave_models/regional/norway/arcwam.md)** (`ARCTIC_ANALYSISFORECAST_WAV_002_014`), supplying **momentum, Stokes–Coriolis drift, and vertical mixing** terms into HYCOM (Ali et al. 2019). ARCWAM in turn takes its surface currents from this product — so the relationship is **bidirectional between runs**, not one-way.
- **River discharge / freshwater forcing:** climatology merged from the **Arctic-HYPE and E-HYPE reanalyses** (SMHI, 2014), **more than 100 rivers**. Plus Greenland ice-sheet mass loss from **GRACE** (GrIS CCI) across **35 terminal glaciers**, and Greenland surface mass balance from ECMWF reanalysis.
- **Ocean forcing / boundary conditions:** "Global HR analysis and forecasting system" — **the parent is not named**, the same gap as in the [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md) PUM. [GLO12](../../../ocean_models/global/france/glo12.md) is the obvious candidate but is not stated (**TBD**).
- **Ice forcing:** CICE v5.1 runs online within the system, so ice cover modifies wind stress transfer internally. **No ice fields are published** from this product.
- **Nested inside / parent for:** a sibling of TOPAZ5 rather than a nest of it — both are HYCOM configurations of the same ARC MFC system at different resolutions. **MET Norway intends to nudge TOPAZ6 toward TOPAZ5 in November 2026** — see *Notes*.

---

## Data assimilation
- **Assimilates water level observations:** **No.**
- **Observation sources:** N/A
- **Method / cadence:** N/A

The PUM records both the assimilation scheme and the assimilated observations as **None** / **N/A**. This is the defining trade-off against [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md), which runs a 100-member EnKF: TOPAZ6 has higher resolution, tides, and pressure forcing but no observational constraint at all.

---

## What it provides

**Three variables. That is the entire product.**

| Variable | Standard name | Units | Notes |
|---|---|---|---|
| `zos` | `sea_surface_height_above_geoid` | m | Total water level: tide + surge + wave + baroclinic, IB included |
| `vxo` | `sea_water_x_velocity` | m s⁻¹ | Surface velocity along the polar stereographic x-axis |
| `vyo` | `sea_water_y_velocity` | m s⁻¹ | Surface velocity along the y-axis |

Plus the static `model_depth` (`sea_floor_depth_below_sea_level`), which is carried in every file rather than offered as a separate static dataset.

**Not provided:** surge residual, tidal water level as a separate field, depth-averaged currents, inundation depth, maximum envelope of water, or wave setup as a separate contribution. No 3D fields, no temperature, no salinity, no sea ice.

> ### ⚠ The current variables are deliberately mislabelled, and the PUM says so
> Since **June 2024** the surface currents include **Stokes drift**, making them formally *total surface currents*. The PUM states plainly that the standard name was nonetheless **kept as ocean currents "to avoid disrupting the aggregation server."**
>
> So `vxo` and `vyo` carry `sea_water_x_velocity` / `sea_water_y_velocity` while actually representing the Eulerian current **plus** the Stokes drift. This is a documented, deliberate CF-compliance compromise made for operational convenience. Anyone comparing these currents against a Eulerian product — including [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md), whose currents are Eulerian — is comparing different quantities, and nothing in the file metadata reveals it.
>
> Note also that data before June 2024 in the same archive does *not* include Stokes drift. **The variable's meaning changes mid-archive with no flag.**

**Velocity components are grid-relative.** `vxo` and `vyo` are defined along the polar stereographic axes and must be rotated if reprojected. The PUM supplies MATLAB and Python rotation examples. The low-resolution reprojected variant has already been rotated to eastward/northward — see *Data availability*.

---

## Data availability
- **Is the data free?** Yes, with registration
- **License:** Copernicus Marine Service licence (free after self-registration)
- **Is the data downloadable?** Yes
- **Output geometry:** **Gridded fields only.** No station or tide-gauge point time series are distributed — unusual for this model class, and worth noting given the template's point-output exception exists precisely because most surge systems lead with gauges.
- **Data formats:** NetCDF-4 (**CF-1.4**); Zarr for the ARCO copies
- **Product identifier:** `ARCTIC_ANALYSISFORECAST_PHY_TIDE_002_015`
- **DOI:** https://doi.org/10.48670/moi-00005
- **Dataset identifier:** `dataset-topaz6-arc-15min-3km-be` (version tag `_202003`)

### Delivery variants — note what is missing
| Variant | Grid | ARCO available? |
|---|---|---|
| default | — | **No ARCO. `native` asset only.** |
| `--ext--originalGrid` | native polar stereographic, 2467 × 2367 at 3 km | Yes |
| `--ext--lowResolution` | regular lat-lon, **6000 × 815 at 0.06°**, 41.13–89.97°N | Yes |

> **There is no full-resolution reprojected lat-lon variant.** Users who want lat-lon must accept **0.06° (~6.7 km)** — coarser than the native 3 km by more than a factor of two, and coarser than [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md)'s reprojected grid. Anyone needing 3 km must work in the native projection and handle the vector rotation themselves. This differs from TOPAZ5, whose *default* datasets are full-resolution reprojected.

- **File naming:** `{YYYYMMDD}_qhr-metno-MODEL-topaz6-tide-ARC-b{bulletin}-fv02.0.nc` — 96 quarter-hourly steps (24 h) per file
- **File size:** **~2.79 GB per daily file**
- **Live archive extent (verified 2026-08-25):** 2018-01-01T00:00Z → 2026-09-02T23:45Z, **304 032 quarter-hourly steps**. The PUM's dataset table gives 19/12/2017 as the start; the General Information table has a mangled "-201/01/2018". The live data begins **2018-01-01**.
- **Official download location:** https://data.marine.copernicus.eu/product/ARCTIC_ANALYSISFORECAST_PHY_TIDE_002_015/description

> **The archive is eight and a half years long.** Far beyond the nominal 2-year rolling window applied to most ARC MFC products, and unusual for a near-real-time surge product. At ~2.79 GB/day the full series is roughly **8.8 TB** — the practical constraint on any retrospective use.

> **Verified 2026-08-25.** Grid dimensions, variable inventory, units and standard names, delivery variants and their availability, low-resolution grid geometry, and archive extent confirmed from Marine Data Store STAC records and live ARCO Zarr metadata.

---

## Notes

- **Operational status: active, with a documented change coming.** The PUM states MET Norway intends to **nudge TOPAZ6 toward TOPAZ5 in November 2026**, to reconcile the duplicated surface currents and sea level between the two products. That will introduce observational constraint into this system indirectly and should produce a discontinuity in the archive. Worth adding to `STATUS.md`.

- **Total water level, not a residual.** The most common source of confusion in surge products, and here the answer is unambiguous: `zos` is the total, tide and pressure and waves included, above the geoid. There is no way to recover the meteorological residual from the distributed data alone.

- **Inverse barometer is included here and excluded in TOPAZ5.** The two sibling products' sea level fields differ systematically for this reason alone, before resolution or assimilation are considered.

- **The currents are total, not Eulerian, and the metadata does not say so.** See the warning under *What it provides*. The change dates from June 2024 and is not flagged in-file, so the variable's meaning is inconsistent across the archive.

- **Choosing between TOPAZ6 and TOPAZ5.** The operator states the trade-off directly: TOPAZ6 should be **more accurate near coasts**, because of the higher resolution and the tidal and pressure processes; TOPAZ5 should be **better in the open ocean**, because of its data assimilation. Neither dominates.

| | **TOPAZ6 (this entry)** | **[TOPAZ5](../../../ocean_models/regional/norway/topaz5.md)** |
|---|---|---|
| Resolution | 3 km | 6.25 km |
| Output | 3 surface variables, 15-min | Full 3D + ice, hourly to monthly |
| Tides | **36 FES2014 constituents** | **None** |
| Inverse barometer | **Included** | **Excluded** |
| Currents | **Total (incl. Stokes drift)** | Eulerian |
| Data assimilation | **None** | 100-member DEnKF |
| Spin-up | 4 years | 20 years |
| Archive | **2018-01-01 →** | 2021-07-05 → |

- **Bidirectional relationship with ARCWAM.** This system takes wave momentum, Stokes–Coriolis, and mixing terms from [ARCWAM](../../../wave_models/regional/norway/arcwam.md); ARCWAM takes its surface currents from here. That is a genuine two-way exchange between runs, and it means the [ARCWAM entry's](../../../wave_models/regional/norway/arcwam.md) description of taking currents from "the separate tidal configuration" should be cross-linked to this entry in both directions.

- **No station output, no inundation.** Both unusual for a surge product. This is a gridded open-ocean and shelf water level field, not a coastal flood forecasting tool, and it should not be compared against gauge-oriented national surge systems on equal terms.

- **Only surface fields are published from a full 3D run.** The PUM is explicit: *"Although the model performs full 3D calculation, the PHY TIDE product provides only surface variables."* The 50 hybrid layers exist internally and are discarded at delivery.

- **The `--ext--lowResolution` variant is the only lat-lon option** and is 0.06°. See *Data availability*.

- **The spherical projection is not WGS84.** Earth radius 6 378 273 m with equal axes. Reprojecting against a WGS84 ellipsoid introduces error.

- **The Copernicus product title says "Tidal", the file title says "Physics", the STAC description says "Surface Currents", and the TOPAZ5 PUM says "storm surge and tidal".** Four names for one product. This entry uses **TOPAZ6** and files it as a surge product; anyone searching the Copernicus catalogue will find it under the tidal name.

---

## Recent version history

### November 2025 — PUM Issue 1.0
Initial product-specific PUM, "transferred from common PUM" — the same split from the combined ARC MFC manual that produced the [TOPAZ5](../../../ocean_models/regional/norway/topaz5.md) PUM. Carries no change log of its own.

### June 2024
**Stokes drift included in the surface currents**, making them total rather than Eulerian. The CF standard name was deliberately left unchanged. This is the most consequential undocumented-in-metadata change in the archive.

### Planned — November 2026
**Nudging of TOPAZ6 toward TOPAZ5**, per the PUM's product-use section.

*A fuller history would need the QUID and the Copernicus Marine User Notification Service archive (**TBD**). The dataset version tag `_202003` suggests a March 2020 catalogue version that has not been bumped since, despite the June 2024 physics change.*

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/ARCTIC_ANALYSISFORECAST_PHY_TIDE_002_015/description
- **Product User Manual (Issue 1.0, November 2025):** https://documentation.marine.copernicus.eu/PUM/CMEMS-ARC-PUM-002-015.pdf
- Quality Information Document (QUID): https://catalogue.marine.copernicus.eu/documents/QUID/CMEMS-ARC-QUID-002-015.pdf
- **Product User Manual (combined ARC MFC, Issue 5.20, June 2025 — still served):** https://documentation.marine.copernicus.eu/PUM/CMEMS-ARC-PUM-002-ALL.pdf
- DOI: https://doi.org/10.48670/moi-00005
- MET Norway: https://www.met.no/
- NERSC: https://www.nersc.no/
- Copernicus Marine Service: https://marine.copernicus.eu/

### Key references
- Ali, A., Christensen, K.H., Breivik, Ø., Malila, M., Raj, R.P., Bertino, L., Chassignet, E. and Bakhoday-Paskyabi, M. (2019). A comparison of Langmuir turbulence parameterizations and key wave effects in a numerical model of the North Atlantic and Arctic Oceans. *Ocean Modelling*, 137, 76–97. https://doi.org/10.1016/j.ocemod.2019.02.005
- Carrere, L., Lyard, F., Cancet, M. and Guillot, A. (2015). FES2014, a new tidal model on the global ocean. *(EGU/OSTST presentation.)*
- Bleck, R. (2002). An oceanic general circulation model framed in hybrid isopycnic-Cartesian coordinates. *Ocean Modelling*, 4, 55–88. *(HYCOM.)*
- Hunke, E.C. et al. (2017). CICE: the Los Alamos Sea Ice Model, version 5.1.
- SMHI (2014). Arctic-HYPE and E-HYPE reanalyses. *(River climatology source cited in the PUM.)*
