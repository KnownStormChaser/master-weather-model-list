# STOFS-3D-Atlantic (Surge and Tide Operational Forecast System, 3-D Atlantic component)

## What this model is
**STOFS-3D-Atlantic** is NOAA's operational three-dimensional coastal ocean and total water level forecast system for the U.S. East Coast, Gulf of America and Puerto Rico. It runs once daily at 12Z, producing a 24-hour nowcast followed by a 96-hour forecast.

Unlike the depth-averaged [STOFS-2D-Global](../../global/usa/stofs-2d-global.md), STOFS-3D-Atlantic resolves the vertical structure of the water column and publishes temperature, salinity and three-dimensional currents alongside water level. The hydrodynamic core is **SCHISM** (Semi-implicit Cross-scale Hydroscience Integrated System Model), run on an unstructured mixed triangular/quadrilateral mesh of 3,052,121 nodes with up to 49 vertical layers.

Its defining capability is **one-way coupling to the National Water Model (NWM)**, which lets it simulate rainfall-driven overland flow, river flow and storm surge simultaneously — the compound-flooding problem that a coastal-only surge model cannot address.

STOFS-3D-Atlantic was introduced in December 2022 as the first 3-D component of STOFS, at the same time the system was renamed from ESTOFS. A second 3-D component, [STOFS-3D-Pacific](./stofs-3d-pacific.md), became operational alongside the STOFS v3.1 upgrade on 17 August 2026.

---

## Who runs it
- **Organization:** NOAA / National Ocean Service (NOS) / Office of Coast Survey / Coast Survey Development Laboratory (CSDL), with NOAA/NWS/NCEP Central Operations (NCO) running the operational suite on WCOSS2
- **Model core developer:** Virginia Institute of Marine Science (VIMS) — SCHISM
- **Country / region:** United States (U.S. East Coast, Gulf of America, Puerto Rico)

---

## What area it covers
- **Coverage:** Northwest Atlantic — U.S. East Coast, Gulf of America and Puerto Rico
- **Mesh resolution:** about 2–7 km in the open ocean, 1.5–2 km near the shoreline, about 600 m on the floodplain, down to 8 m for watershed rivers (at least three nodes across each river cross-section), and 2–10 m for levees
- **Inundation coverage:** **Yes.** Along the U.S. coasts the land boundary follows the 10 m contour above xGEOID20B, covering the coastal transitional zone most exposed to combined coastal and inland flooding. Wetting and drying is active.
- **Inland extent:** The mesh runs well up into watershed rivers, which is why a large fraction of nodes carry water surface elevations far above any plausible coastal surge — see the caution under *What it provides*.

---

## Basic details
- **Model type:** Deterministic 3-D baroclinic coastal ocean / total water level model
- **Core hydrodynamic model:** SCHISM (`source = SCHISM model output version v10`, live-verified)
- **Dimensionality:** **3D baroclinic**, with a variable number of layers per node: up to **49** in the deepest Atlantic areas, down to 1 on floodplains, about 9 on average. The operator's portal gives 30,907,434 3-D grid points.
- **Forecast length:** 24-hour nowcast plus **96-hour forecast** (120-hour total span). Live-verified on the 2026-11-17 12Z run: station series run from 2026-11-16 12:06 to 2026-11-21 12:00 UTC (1,200 six-minute records).
- **Update frequency / cycles:** **1× daily, 12Z only.** Live-verified — no other cycle appears in any day directory. This is a major difference from [STOFS-2D-Global](../../global/usa/stofs-2d-global.md), which runs four cycles daily.
- **Model time step:** 150 s
- **Temporal output resolution:**
  - GRIB2 water level: **hourly** (`f000`–`f096`)
  - Native-mesh field NetCDF: **hourly**, bundled in 12-hour chunks
  - Station NetCDF: **6-minute**
  - Station vertical profiles: **hourly means** (`cell_methods = time: mean`)
  - SHEF bulletins: **30-minute**
- **Nowcast/forecast file convention:** `n###` files cover nowcast hours (`n001_012`, `n013_024`), `f###` cover forecast hours (`f001_012` through `f085_096`).
- **Software package version:** `stofs.v3.1.7` (live-verified from processing paths in the 2026-11-17 output metadata)

---

## Grid and bathymetry
- **Grid type:** Unstructured **mixed triangular and quadrilateral** mesh (`nMaxSCHISM_hgrid_face_nodes = 4`), UGRID-style topology with explicit node, face and edge arrays
- **Mesh size (live-verified 2026-11-17, v3.1):** **3,052,121 nodes**, **5,872,610 elements** (5,733,800 triangles + 138,810 quadrilaterals), **8,924,888 edges**. The v2.1 mesh had 2,926,236 nodes; v3.1 added refinements in watersheds, barrier islands, jetties, the Intracoastal Waterway and community-requested areas.
- **Vertical coordinate:** SCHISM LSC² variable-layer, up to 49 layers. `zCoordinates` is published as a full time-varying 3-D field (`nSCHISM_vgrid_layers = 49`), so read layer depths from the data rather than reconstructing them from a static sigma table.
- **Bathymetry source:** Not documented (TBD)
- **Wetting and drying:** **Yes** — `dryFlagNode`, `dryFlagElement` and `dryFlagSide` are published per timestep, alongside a `minimum_depth` variable
- **Land masking:** Applied in post-processing from a static `idmask` fix file; masked nodes are set to −99999.0

> **Live-verified — `field2d_*` triangulates the mesh by splitting each quadrilateral in two.** `fields.out2d_*` publishes 5,872,610 mixed faces; `field2d_*` publishes 6,011,420 triangles (`nvertex = 3`). The difference is exactly the 138,810 quadrilaterals, each replaced by two triangles. Node count is identical. Under v2.1 the `field2d_*` table instead had *fewer* faces than `out2d` (5,548,650 vs 5,654,157), so quadrilaterals were then dropped rather than split. Read connectivity from the file you are using, and do not reuse a connectivity array saved from a pre-v3.1 file.

---

## Vertical datum and reference level

**Three different datums appear across the products of the same model.** This is the single most consequential thing to get right when using STOFS-3D-Atlantic. STOFS v3.1 moved the station NetCDF from NAVD88 to local mean sea level; the gridded output did not change.

| Product | Vertical datum | Units |
|---|---|---|
| Native-mesh field NetCDF (`fields.out2d_*`, `field2d_*`) | **xGEOID20B** | m |
| Maximum elevation (`fields.cwl.maxele.nc`) | **xGEOID20B** | m |
| Station NetCDF (`points.cwl.nc`) | **LMSL** (labelled `msl`) | m |
| Station profile NetCDF (`ncast`/`fcast.station.profile.nc`) | **LMSL** (still **labelled `navd88`**) | m |
| GRIB2 regional grids | xGEOID20B (inherited; **not stated in the GRIB header**) | m |
| SHEF bulletins | **MLLW** | **feet** |

Live-verified labels on the water level variables (2026-11-17 12Z):
- Gridded: `water surface elevation above xgeoid20b`; `field2d_*` `standard_name = sea_surface_height_above_xgeoid20b`
- Station NetCDF: `water surface elevation above msl`, `standard_name = sea_surface_height_above_msl`
- Station profile NetCDF: `water surface elevation above navd88`

- **Datum conversion offsets provided?** No. No conversion grid or per-station offset table is published.
- **Mean sea level trend / SLR handling:** Not documented (TBD)

> **Caution — the gridded and station products are on different datums.** Both are labelled "water surface elevation" in metres and come from the same model cycle. Live-verified on the 2026-11-17 12Z f012 step, the nearest GRIB2 grid point sits **0.02–0.49 m below** the station series. Examples: −0.02 m at Galveston, −0.12 m at Key West, −0.32 m at Charleston, −0.38 m at Sewells Point, −0.46 m at Boston, −0.49 m at The Battery. The offset varies geographically, so a single constant will not convert between them.

> **Caution — the station profile files carry a stale datum label.** Under v3.1 the profile `zeta` matches the station `zeta` (median difference below 10⁻⁷ m once both are on the same hourly steps), so it is on LMSL like the station file. Its `long_name` still reads `above navd88`. Trust the station-file label, not the profile label.

> **Caution — the CF `standard_name` values are invented and will not validate.** Neither `sea_surface_height_above_msl` nor `sea_surface_height_above_xgeoid20b` exists in the CF standard name table. Tools that resolve datum from `standard_name` will fail or silently fall through. Hard-code the datum per product from the table above.

> **Caution — the GRIB2 files carry no datum information at all.** The regional GRIB2 grids are the most convenient product for most users and the easiest to misuse: nothing in the message identifies the reference surface. See *Notes* for the related problem that the GRIB2 parameter is shared with the other STOFS components.

---

## Tide handling
- **Are tides included?** Yes — tides are forced at the open ocean boundaries and integrated within SCHISM. The water level fields represent combined tidal and subtidal elevation.
- **Tidal forcing source:** **FES2014** ([STOFS-3D-Pacific](./stofs-3d-pacific.md) uses TPXO9)
- **Separation of components:** **No.** [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) publishes combined water level, harmonic tidal prediction and sub-tidal residual as three separate products; STOFS-3D-Atlantic publishes **only** the combined water level. There is no `htp` or `swl` product and no way to isolate the surge from the distributed output. Users needing a surge residual must supply their own tidal prediction.

---

## Forcing and coupling
- **Meteorological forcing:** **GFS and HRRR** surface fields, with HRRR given priority. `windSpeedX`, `windSpeedY`, `windStressX`, `windStressY`, `precipitationRate` and `evaporationRate` are carried in the 2-D output.
- **River discharge / freshwater forcing:** **Yes — one-way coupling to the National Water Model (NWM).** This lets the system represent rainfall-induced overland flow, river flow and storm surge in a single simulation, and is the principal reason to use STOFS-3D-Atlantic over the 2-D global system for compound coastal-inland flooding.
- **Ocean boundary conditions:** **G-RTOFS** supplies subtidal 3-D temperature, salinity and currents. Subtidal water level at the boundary combines G-RTOFS with **Copernicus gridded absolute dynamic topography (ADT) altimetry**. Since v3.1 the boundary water level is also bias-adjusted (see *Data assimilation*).
- **Wave contribution:** **None.** The operator's portal states explicitly that STOFS output is not total water level guidance because waves are not included.
- **Ice forcing:** Not documented (TBD)

---

## Data assimilation
- **Assimilates water level observations:** Not in the interior. Since v3.1 there is an **open-boundary bias correction**: the mean nowcast bias over the past two days at 11 U.S. East Coast open-coast NOS/CO-OPS stations is applied uniformly to the open boundaries, to account for missing physics such as steric effects.
- **Station bias correction:** No `noanomaly` variant is published, unlike [STOFS-2D-Global](../../global/usa/stofs-2d-global.md), where that variant reveals a station-level bias correction. Beyond the boundary adjustment above, no station-level correction is documented.

---

## What it provides

**Water level**
- Combined water level (tide + surge) — hourly on the native mesh (`fields.out2d_*` `elevation`, `field2d_*` `zeta`) and on two regional GRIB2 grids
- Maximum water level envelope (`fields.cwl.maxele.nc`)
- Water level "disturbance" as vector GeoPackage polygons

**Ocean state, full 3-D fields** (S3 only — see *Data availability*)
- `fields.temperature_*`, `fields.salinity_*`
- `fields.horizontalVelX_*`, `fields.horizontalVelY_*`
- `fields.verticalVelocity_*`, `fields.diffusivity_*` (new with v3.1; absent from the S3 archive before 2026-08-17)
- `fields.zCoordinates_*` — time-varying vertical layer positions

**Ocean state, 2-D slices** (`field2d_*`, on NOMADS and S3)
- `temp_surface`, `temp_bottom`, `salt_surface`, `salt_bottom`
- `uvel_surface`, `uvel_bottom`, `uvel0.5`, `uvel4.5` and the matching `vvel_*` — velocity at the surface, at the bottom, and at 0.5 m and 4.5 m below the free surface

**2-D diagnostics** (`fields.out2d_*`)
- `depthAverageVelX`, `depthAverageVelY`
- `windSpeedX/Y`, `windStressX/Y`, `precipitationRate`, `evaporationRate`
- `dryFlagNode`, `dryFlagElement`, `dryFlagSide`, `bottom_index_node`, `minimum_depth`, `idmask`

**Station products**
- 6-minute surface time series at **166 sites**: water level, u, v, salinity, temperature (108 under v2.1)
- Hourly vertical profiles at the same 166 sites: temperature, salinity, u, v, `zCoordinates` through up to 49 layers, plus 10 m wind (`uwind_speed`, `vwind_speed`)
- 30-minute SHEF water level bulletins at **150 sites** (105 under v2.1)

> **Live-verified — `fields.cwl.maxele.nc` covers the forecast only, and its `time` dimension does not index the data.** `zeta_max` is one-dimensional over the mesh. `time` has length 2, with values `[90000, 432000]` seconds since the nowcast start. The pair encodes the **window over which the maximum was taken** (forecast hours f001–f096), not two data slices. The 24-hour nowcast is excluded from the envelope.

> **Live-verified — sanity-bound the maximum-elevation field before use; the v3.1 mesh did not remove the artifacts.** On the 2026-11-17 12Z run, `zeta_max` spans **−403.54 m to +792.38 m** across 2,852,568 valid nodes (93.46% of the mesh). Most of that range is legitimate: 85.6% of valid nodes fall in −3 to +5 m, and the 14.3% in the 5–20 m band largely reflects inland river nodes whose surface genuinely sits well above the geoid. But **409 nodes exceed 20 m and 145 fall below −10 m**. The >20 m nodes all lie between 38.9°N and 44.7°N (Chesapeake to Maine), with the extremes on the Massachusetts and Maine coasts (+792.38 m near 70.43°W, 41.73°N; −403.54 m near 70.76°W, 43.08°N) — a signature of isolated numerical artifacts rather than physical signal. Median is 0.759 m, 99th percentile 11.04 m. The v2.1 run of 2026-07-22 showed the same pattern (420 nodes > 20 m, 84 < −10 m).

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS and anonymous S3)
- **License:** **U.S. Government work — public domain.** Distributed through NOAA Open Data Dissemination (NODD). NOAA requests attribution for use or dissemination of unaltered data; it is not permissible to state or imply NOAA endorsement, and modified data may not be presented as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** **Both.** Gridded (native unstructured mesh plus two regridded regional GRIB2 domains) and station point time series and vertical profiles.
- **Data formats:** **NetCDF-4 / HDF5** (fields, stations, profiles), **GRIB2** (regional grids), **SHEF** (station bulletins), **GeoPackage** (`.gpkg` disturbance polygons)
- **Station list:** Embedded in the `station_name` variable of the point NetCDF files as fixed-width 50-character records combining NWS Handbook-5 ID, WMO header, NOS station ID, state and site name — e.g. `FMRF1 SOUS42 8725520 FL Fort Myers`. Records are **null-padded**, not space-padded; strip `\x00` when parsing. 16 of the 166 records carry the placeholder `XXXXX XXXXXX` in place of the Handbook-5 ID and WMO header; these are exactly the 16 stations missing from the SHEF bulletins (166 − 16 = 150). No separate station metadata file is published.

### GRIB2 regional grids (live-verified from the 2026-11-17 12Z f012 files)

| Region | Projection | Grid (Ni × Nj) | Spacing | `generatingProcessIdentifier` |
|---|---|---|---|---|
| `conus.east` | Lambert conformal | 2145 × 1377 | 2539.703 m | 21 |
| `puertori` | Mercator | 339 × 225 | 1250 m | 21 |

Lambert parameters: LaD 25.0°N, LoV 265.0°E, Latin1 = Latin2 = 25.0°. These are the same NDFD-family grid definitions used by [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) — identical projection parameters and dimensions. The grids are unchanged by v3.1.

Each hourly file contains **exactly one message**. Per-cycle whole-run files are also published as `conus.east.cwl.grib2` (109 MB) and `puertori.cwl.grib2` (9.0 MB).

### Official download locations

**NOMADS (operational, 24/7 supported):**
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/stofs_3d_atl.YYYYMMDD/
ftp://ftp.ncep.noaa.gov/data/nccf/com/stofs/prod/stofs_3d_atl.YYYYMMDD/
```
The `stofs/prod` directory holds all operational STOFS components side by side (`stofs_2d_glo.YYYYMMDD/`, `stofs_3d_atl.YYYYMMDD/` and `stofs_3d_pac.YYYYMMDD/`), with roughly two days of retention.

**AWS Open Data (NODD) — the long-term archive:**
```
s3://noaa-nos-stofs3d-pds/STOFS-3D-Atl/stofs_3d_atl.YYYYMMDD/
aws s3 ls --no-sign-request s3://noaa-nos-stofs3d-pds/STOFS-3D-Atl/
```
- AWS region: `us-east-1`. [STOFS-3D-Pacific](./stofs-3d-pacific.md) shares the bucket under `STOFS-3D-Pac/`.
- **Archive depth (live-verified 2026-11-18):** 1,397 daily directories from **2023-01-12** to 2026-11-17. Nine days are missing from the 1,406-day span: 2023-02-26 and an eight-day run from 2025-11-04 to 2025-11-11. Do not assume continuity when building time series.
- **v3.1 boundary in the archive:** directories up to 2026-08-16 are v2.1 output; from **2026-08-17** they are v3.1. The file set changes visibly at that boundary (`verticalVelocity` and `diffusivity` appear, and the maxele and station files grow).

> **S3 carries substantially more than NOMADS.** NOMADS publishes the GRIB2 grids, `field2d_*`, `fields.out2d_*`, `fields.cwl.maxele.nc`, the two station profile files, `points.cwl.temp.salt.vel.nc` and the SHEF bulletins. The full 3-D state variables — `fields.temperature_*`, `fields.salinity_*`, `fields.horizontalVelX/Y_*`, `fields.verticalVelocity_*`, `fields.diffusivity_*` and `fields.zCoordinates_*` — appear **only on S3**. So do `points.cwl.nc`, the `.gpkg` disturbance files and the raw `schout_adcirc_*.nc` output. If you need the 3-D ocean state, NOMADS is not sufficient.

---

## Notes

> **Live-verified caution — the GRIB2 `shortName` is `unknown`, and every STOFS component shares the same parameter.** Each hourly GRIB2 file contains exactly one message, encoded as discipline 10 / category 3 / **parameter 250**, which ecCodes 2.50.0 cannot resolve (`shortName = unknown`, `name = unknown`). Parameter 10/3/250 falls in the NCEP local-use range 192–254. Apply the standard fallback: read `discipline`, `parameterCategory` and `parameterNumber` directly and select on `parameterNumber == 250`.
>
> The compounding problem is that **[STOFS-2D-Global](../../global/usa/stofs-2d-global.md) uses the identical parameter on the identical `conus.east` and `puertori` grid definitions**, and [STOFS-3D-Pacific](./stofs-3d-pacific.md) does the same on the five Pacific grids. Messages from these systems are not distinguishable by data content. `generatingProcessIdentifier` is 21 for STOFS-3D-Atlantic, 14/17/20 for STOFS-2D-Global (varying by grid) and 22/23 for STOFS-3D-Pacific. The value sets do not overlap, but the per-grid variation makes the key easy to misuse. **Track provenance by filename.** The systems are also on different vertical references (xGEOID20B here, LMSL for STOFS-3D-Pacific; see the STOFS-2D-Global entry for its own), so a mix-up is not merely a labelling error.

- **No bitmap; land is the literal value 9999.0.** GRIB2 messages carry `bitmapPresent = 0` and `missingValue = 9999`, with land and dry points encoded in-band. Filter explicitly or 9999 will enter your statistics. NetCDF products use −99999.0 instead, applied consistently — unlike the four-way inconsistent sentinel values documented for [STOFS-2D-Global](../../global/usa/stofs-2d-global.md).
- **`points.cwl.nc` and `points.cwl.temp.salt.vel.nc` are the same file under two names.** Re-verified on the 2026-11-17 12Z run: identical dimensions (1,200 × 166) and all five data variables (`zeta`, `u`, `v`, `salinity`, `temperature`) bit-identical. Download one, not both. NOMADS publishes only `points.cwl.temp.salt.vel.nc`, while S3 publishes both, so the same data reaches users under different names depending on the source.
- **The v3.1 station list does not match the SCN.** SCN 26-51 lists Duck NC, Fort Point NH, Money Point VA and Springmaid Pier SC as redundant stations to be removed. All four are still in the 2026-11-17 station NetCDF. (South Amboy NJ is correctly gone; the active `S Amboy Raritan River` station remains, as the SCN intends.) Of the three stations listed as added, only Kings Bay MSF Pier GA is present; Bogue Sound at Emerald Isle NC and White Oak River at Swansboro NC are not. For Naples, the new ID (8725114) is present, but the old ID (8725110) also remains, as one of the 16 placeholder `XXXXX` records. The station count rose from 108 to 166, a change the SCN does not mention. The operator portal still gives 108.
- **`depth` metadata is inconsistent between products.** In `fields.cwl.maxele.nc`, bathymetry still carries `units = mm` with `standard_name = depth below geoid`. In `fields.out2d_*` and `field2d_*` the units are `m`; under v2.1 `out2d` also carried `mm`, so v3.1 fixed one product and not the other. SCHISM depth is conventionally metres; treat the `mm` label as a post-processing error.
- **The `depth` long_name in the maxele file is a duplicated string.** `distance  below geoiddistance  below geoid` — a concatenation bug. Cosmetic, but a useful signal of how much metadata is applied after the fact.
- **Metadata is applied post-hoc by NCO, not written by the model.** The `history` attribute in every field file is a long chain of `ncks`, `ncatted`, `ncap2`, `ncra` and `ncbo` calls. Datum labels, fill values and even dimension definitions are attached in post-processing, independently per product. This is the mechanism behind the datum and units inconsistencies above, and it means a fix to one product does not propagate to the others — as the stale `navd88` label on the v3.1 profile files shows.
- **Time epochs are cycle-relative, and differ from STOFS-2D-Global.** Files use `seconds since YYYY-MM-DD 12:00:00 UTC` anchored to **the day before the cycle date** — the start of the 24-hour nowcast. STOFS-2D-Global instead uses a fixed cold-start epoch that never changes. STOFS-3D-Pacific uses the same convention as this system.
- **Disturbance products are vector GeoPackage, S3 only.** `stofs_3d_atl.t12z.disturbance.f###.gpkg` and `.n###.gpkg` are published hourly at roughly 34–40 MB each, totalling about 4.6 GB per cycle under v3.1. These are derived polygon products rather than raw gridded output; the NetCDF and GRIB2 are the primary data.
- **Volume warning.** A full v3.1 cycle on S3 is about **195 GB**. `field2d_*` chunks are 3.95 GB each (10 files) and `fields.out2d_*` about 1.6 GB each (10 files). Each of the seven 3-D variables (`temperature`, `salinity`, `horizontalVelX/Y`, `verticalVelocity`, `diffusivity`, `zCoordinates`) runs 1.6–1.85 GB per chunk across 10 chunks. The archive also carries five rolling `schout_adcirc_YYYYMMDD.nc` files at 1.98 GB each. The regional GRIB2 (about 1.1 MB per hour for CONUS East) and the station files (8.3 MB) are the practical products for most users.
- **Station counts differ between products.** 166 sites in the NetCDF station and profile files, but only 150 in the SHEF bulletins. The difference is exactly the 16 records without a Handbook-5 ID (see *Data availability*).
- **The operator portal is labelled experimental.** It states that STOFS output is not total water level guidance (waves are not included) and directs users to regional NWS forecast offices for official water level forecasts.
- **Relationship to other systems:**
  - Sibling component: [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) — same STOFS umbrella and version numbering, but a different model core (ADCIRC), global coverage, 2-D barotropic physics, four cycles daily, and a three-way tide/surge product split that this system does not offer.
  - Sibling component: [STOFS-3D-Pacific](./stofs-3d-pacific.md) — same SCHISM core, S3 bucket and version numbering, operational since 17 August 2026. Differences: a 48 h forecast against 96 h here, TPXO9 tides, an MHW land boundary over most of the U.S. coast, up to 84 layers, and LMSL across all of its gridded and station products.
  - Driving hydrology: [National Water Model](../../../hydrological_models/regional/usa/nwm.md) (NOAA/NWS Office of Water Prediction). NWM also runs its own SCHISM-based coastal total water level configurations for the same coast, hourly and on NAVD88, with STOFS-2D-Global at the ocean boundary. That is a separate product from this one, despite the shared core.
  - Atmospheric drivers: GFS and HRRR. Ocean boundary: G-RTOFS.
  - Comparable regional surge system: [RESPS](../canada/resps.md) (ECCC) — but RESPS is 2-D barotropic with no overland domain and no hydrological coupling, so the two are not equivalent products.
  - SCHISM model documentation: http://ccrm.vims.edu/schismweb/

---

## Recent version history

| Version | Date | Notes |
|---|---|---|
| STOFS v3.1 (package `stofs.v3.1.7`) | live-verified 2026-11-17 | Current production package. |
| STOFS v3.1 | 17 Aug 2026, 12Z | SCN 26-51 (Updated); first announced for 7 July 2026, then 11 August. Mesh refined to 3,052,121 nodes, open-boundary bias correction added, and station NetCDF moved from NAVD88 to LMSL. Station list revised (108 → 166 sites live-verified). `verticalVelocity` and `diffusivity` 3-D fields added on S3, and `field2d_*` now splits quadrilaterals instead of dropping them. Go-live date confirmed by the S3 archive. |
| STOFS v2.1.18 | 2026 | Package version live-verified in the 2026-07-22 12Z output (last v2.1 cycle on S3: 2026-08-16). |
| STOFS v1.1.1 | 10 Jan 2023 | PNS 23-01. S3 bucket established; archive begins 2023-01-12. |
| STOFS v1.0.1 | Dec 2022 | ESTOFS renamed to STOFS; STOFS-3D-Atlantic introduced as the first 3-D component. |

Full version history TBD.

---

## Official documentation
- Operator portal (configuration, validation): https://polar.ncep.noaa.gov/estofs/atl.htm
- AWS Open Data Registry: https://registry.opendata.aws/noaa-nos-stofs3d/
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/
- NOS Office of Coast Survey storm surge modeling overview: https://nauticalcharts.noaa.gov/learn/storm-surge-modeling.html
- STOFS product page: https://polar.ncep.noaa.gov/stofs/
- SCN 26-51 (Updated) — STOFS v3.1: https://www.weather.gov/media/notification/pdf_2026/SCN26-51_STOFS.v3.1_Updated.aaa.pdf
- PNS 23-01 — STOFS v1.1.1 known issues: https://www.weather.gov/media/notification/pdf_2023_24/pns23-01_stofs_v1.1.1.pdf
- PNS 22-37 — ESTOFS→STOFS transition: https://www.weather.gov/media/notification/pdf2/pns22-37_stofs_dec_2022_upgrade_aaa.pdf
- NOAA InPort — STOFS: https://www.fisheries.noaa.gov/inport/item/79430
- SCHISM model documentation: http://ccrm.vims.edu/schismweb/
