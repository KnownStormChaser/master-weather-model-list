# STOFS-3D-Pacific (Surge and Tide Operational Forecast System, 3-D Pacific component)

## What this model is
**STOFS-3D-Pacific** is NOAA's operational three-dimensional coastal ocean and water level forecast system for the Pacific basin, covering the U.S. West Coast, Alaska, Hawaii, Guam and the wider North Pacific. It runs once daily at 12Z, producing a 24-hour nowcast followed by a **48-hour forecast**.

It predicts **total water level** — combined tide and subtidal (surge, steric, river) elevation in a single field. No separate surge residual or tidal prediction is distributed. Alongside water level it publishes temperature, salinity and three-dimensional currents.

The hydrodynamic core is **SCHISM** (Semi-implicit Cross-scale Hydroscience Integrated System Model), the same core as [STOFS-3D-Atlantic](./stofs-3d-atlantic.md), run here on an unstructured mixed triangular/quadrilateral mesh of 2,961,412 nodes with up to 84 vertical layers. River and inland hydrology inputs come from the National Water Model and from Copernicus GloFAS, together with U.S. and Canadian stream gauges.

STOFS-3D-Pacific became operational with **STOFS v3.1 at the 12Z cycle on 17 August 2026**, the third STOFS component after [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) and STOFS-3D-Atlantic.

---

## Who runs it
- **Organization:** NOAA / National Ocean Service (NOS) / Office of Coast Survey / Coast Survey Development Laboratory (CSDL), Coastal Marine Modeling Branch; NOAA/NWS/NCEP Central Operations (NCO) runs the operational suite on WCOSS2
- **Model core developer:** Virginia Institute of Marine Science (VIMS) — SCHISM
- **Country / region:** United States (Pacific basin)

---

## What area it covers
- **Coverage:** Pacific basin, described officially as **30°S to the Bering Strait**
- **Domain details:** Live-verified mesh extent is wider than the official description: **93.1°E to 70.3°W (289.7°E), 33.0°S to 66.3°N**. The mesh therefore reaches into the Indonesian seas and the Timor Sea in the west and to the South American coast in the east. U.S. coastal refinement covers the West Coast, Alaska, Hawaii, Guam and the U.S.-affiliated Pacific islands.
- **Mesh resolution:** 1.5–2 km at the shoreline, about 300 m on floodplains, and about 60 m in watershed rivers, with resolution as fine as about 50 m in selected locations
- **Inundation coverage:** **Yes, but narrower than STOFS-3D-Atlantic.** Along the U.S. coast the land boundary follows **Mean High Water**, except in the Columbia River Estuary and San Francisco Bay, where it follows the 10 m contour above xGEOID20B. Wetting and drying is active. Outside those two estuaries, overland flooding above MHW is not represented.

---

## Basic details
- **Model type:** Deterministic 3-D baroclinic coastal ocean / total water level model
- **Core hydrodynamic model:** SCHISM (`source = SCHISM model output version v10`, live-verified)
- **Dimensionality:** **3D baroclinic**, with a variable number of layers per node: up to **84** (near the Columbia River entrance), down to 1 on floodplains. The portal gives an average of 34 layers. The published count of 83,839,597 3-D points works out to about 28 per node (TBD which figure is the intended average).
- **Forecast length:** 24-hour nowcast plus **48-hour forecast** (72-hour total span). Live-verified on the 2026-11-17 12Z run: station series run from 2026-11-16 12:06 to 2026-11-19 12:00 UTC (720 six-minute records), and GRIB2 grids run `f000`–`f048`.
- **Update frequency / cycles:** **1× daily, 12Z only** (live-verified; no other cycle in any day directory)
- **Model time step:** 90 s (live-verified in the published `param.nml`: `dt = 90.`, `rnday = 3`)
- **Temporal output resolution:**
  - GRIB2 water level: **hourly** (`f000`–`f048`)
  - Native-mesh field NetCDF: **hourly**, bundled in 12-hour chunks (`n001_012`, `n013_024`, `f001_012` … `f037_048`)
  - Station NetCDF: **6-minute**
  - Station vertical profiles: **hourly means** (`cell_methods = time: mean`)
  - SHEF bulletins: **30-minute**
- **Software package version:** `stofs.v3.1.7` (live-verified from processing paths in the 2026-11-17 output metadata)

---

## Grid and bathymetry
- **Grid type:** Unstructured **mixed triangular and quadrilateral** mesh (`nMaxSCHISM_hgrid_face_nodes = 4`), UGRID-style topology with node, face and edge arrays
- **Mesh size (live-verified):** **2,961,412 nodes**, **5,774,631 elements** (5,736,195 triangles + 38,436 quadrilaterals), **8,736,794 edges**
- **Vertical coordinate:** SCHISM LSC² variable-layer. `zCoordinates` is published as a full time-varying 3-D field (`nSCHISM_vgrid_layers = 84`), so read layer positions from the data rather than reconstructing them. The vertical grid definition (`stofs_3d_pac_vgrid.in`, 3.76 GB) is published under `staticfiles/v3.1/` on S3.
- **Bathymetry source:** Not documented (TBD)
- **Wetting and drying:** **Yes** — `dryFlagNode`, `dryFlagElement` and `dryFlagSide` are published per timestep, with `minimum_depth`
- **Land masking:** Applied in post-processing from a static `idmask` fix file (`stofs_3d_pac_mask_land_ocean_bnd_out2d.nc`); masked nodes are set to −99999.0
- **Static model inputs published:** S3 `staticfiles/v3.1/` carries the horizontal grid (`hgrid.gr3`, `.ll`, `.2dm`), vertical grid, `param.nml`, `station.in`, and the drag, diffusivity, Shapiro-filter, albedo, water-type and estuary property files. This is unusually complete — enough to inspect the configuration directly.

> **Live-verified — `field2d_*` triangulates the mesh by splitting each quadrilateral in two.** `fields.out2d_*` publishes 5,774,631 mixed faces; `field2d_*` publishes 5,813,067 triangles (`nvertex = 3`). The difference is exactly the 38,436 quadrilaterals, each replaced by two triangles. Node count is identical. Read connectivity from the file you are using.

---

## Vertical datum and reference level (important)

**Unlike STOFS-3D-Atlantic, the gridded and station water levels share one datum: Local Mean Sea Level (LMSL).** NOAA's dataset description states that STOFS-3D-Pacific water levels are uniformly referenced to LMSL, and the live data agrees.

| Product | Vertical datum | Units |
|---|---|---|
| Native-mesh field NetCDF (`fields.out2d_*`, `field2d_*`) | **LMSL** | m |
| Maximum elevation (`fields.cwl.maxele.nc`, `fields.cwl.maxele.lmsl.nc`) | **LMSL** | m |
| Station NetCDF (`points.cwl.nc`) and station profiles | **LMSL** | m |
| GRIB2 regional grids | LMSL (**not stated in the GRIB header**; live-verified, see below) | m |
| SHEF bulletins | **MLLW** | **feet** |

- **What the water level field is measured relative to:** total water level (tide + subtidal) above LMSL. The conversion is applied in post-processing: the `history` of `fields.out2d_*` shows the raw SCHISM elevation (computed relative to xGEOID20B) minus a static `stofs_3d_pac_mlmsl.nc` field.
- **Live-verified GRIB2 datum check (2026-11-17 12Z, f012):** the nearest GRIB2 grid point agrees with the LMSL station series within 0.001 m at Honolulu, Hilo, Apra Harbor (Guam), San Francisco and Seattle. Coarser cells give larger gaps, e.g. 0.034 m at San Diego and 0.083 m at Anchorage. A geoid-referenced field would show offsets of tenths of a metre.
- **Datum conversion offsets provided?** Not as a published product. The fix files used for the conversion (`stofs_3d_pac_mlmsl.nc`, `stofs_3d_pac_field_geoid_lmsl.nc`) appear in metadata paths but are not distributed.
- **Mean sea level trend / SLR handling:** Not documented (TBD)

> **Caution — bathymetry and water level in the same file use different references.** In `field2d_*`, `zeta` is labelled `sea_surface_height_above_local_mean_sea_level`, while `depth` is labelled `distance below XGEOID20B`. Do not compute total water depth as `depth + zeta` without converting one to the other's reference.

> **Caution — the CF `standard_name` is invented.** `sea_surface_height_above_local_mean_sea_level` is not in the CF standard name table. Use the `long_name` or the table above.

---

## Tide handling
- **Are tides included?** Yes — tidal forcing at the open boundaries, integrated within SCHISM. Water level fields are combined tidal and subtidal elevation.
- **Tidal forcing source:** **TPXO9** (STOFS-3D-Atlantic uses FES2014)
- **Separation of components:** **No.** Only combined water level is published — no harmonic tidal prediction, surge residual or `noanomaly` variant. Users needing a surge residual must supply their own tidal prediction.
- **Tide–surge interaction:** Modelled nonlinearly within the run (tide and subtidal forcing are integrated together)

---

## Forcing and coupling
- **Meteorological forcing — wind and pressure:** **GFS and HRRR**, with HRRR given priority where available. Forcing is read as SCHISM `sflux` files (`nws = 2`) at hourly intervals (`wtiminc = 3600`), with heat-flux exchange enabled (`ihconsv = 1`). `windSpeedX/Y`, `windStressX/Y`, `precipitationRate` and `evaporationRate` are carried in the 2-D output.
- **Wave contribution:** **None.** The operator's portal states explicitly that STOFS output is not total water level guidance because waves are not included, so actual water levels can be significantly higher.
- **River discharge / freshwater forcing:** **National Water Model**, **Copernicus GloFAS**, and **USGS and Canadian stream gauge** observations, applied as point sources/sinks (`if_source = 1`). How the roles divide between NWM and GloFAS is not documented; GloFAS presumably covers basins outside the NWM domain (TBD). Whether NWM is coupled the same way as in STOFS-3D-Atlantic is TBD.
- **Ocean forcing / boundary conditions:** **G-RTOFS** supplies subtidal 3-D temperature, salinity and currents. Subtidal water level at the boundary combines G-RTOFS with **Copernicus gridded absolute dynamic topography (ADT) altimetry**.
- **Ice forcing (if applicable):** Not documented (TBD) — relevant for the Bering Sea and Alaskan coasts
- **Nested inside / parent for:** Not nested; boundary forcing comes from G-RTOFS rather than from STOFS-2D-Global

---

## Data assimilation
- **Assimilates water level observations:** Not documented (TBD). The open-boundary bias correction added to STOFS-3D-Atlantic in v3.1 is **not** described for the Pacific component.
- **Station bias correction:** No `noanomaly` variant is published, unlike [STOFS-2D-Global](../../global/usa/stofs-2d-global.md). Whether any correction is applied is TBD.
- **Altimetry:** Copernicus ADT enters only through the boundary water level, not as interior assimilation (as documented)

---

## What it provides

**Water level**
- Combined water level (tide + subtidal) — hourly on the native mesh (`fields.out2d_*` `elevation`, `field2d_*` `zeta`) and on five regional GRIB2 grids
- Maximum water level envelope (`fields.cwl.maxele.nc`; duplicate copy `fields.cwl.maxele.lmsl.nc` on S3)
- Water level "disturbance" as vector GeoPackage polygons (S3 only)

**Ocean state, full 3-D fields** (S3 only)
- `fields.temperature_*`, `fields.salinity_*`
- `fields.horizontalVelX_*`, `fields.horizontalVelY_*`, `fields.verticalVelocity_*`
- `fields.diffusivity_*`
- `fields.zCoordinates_*` — time-varying vertical layer positions

**Ocean state, 2-D slices** (`field2d_*`, NOMADS and S3)
- `temp_surface`, `temp_bottom`, `salt_surface`, `salt_bottom`
- `uvel_surface`, `uvel_bottom`, `uvel0.5`, `uvel4.5` and the matching `vvel_*` — velocity at the surface, at the bottom, and at 0.5 m and 4.5 m below the free surface

**2-D diagnostics** (`fields.out2d_*`)
- `windSpeedX/Y`, `windStressX/Y`, `precipitationRate`, `evaporationRate`
- `dryFlagNode`, `dryFlagElement`, `dryFlagSide`, `bottom_index_node`, `minimum_depth`, `idmask`
- No `depthAverageVelX/Y` (STOFS-3D-Atlantic publishes these; the Pacific `out2d` files do not)

**Station products**
- 6-minute surface time series at **850 output points**: water level, u, v, salinity, temperature. Only **468 are real stations or buoy locations**. The other **382 are virtual "Satellite Altimetry Station" points** (IDs `SA469`–`SA850`, WMO header `SOUS00`) spread across the basin from 32°S to 59°N.
- Hourly vertical profiles at the same 850 points: temperature, salinity, u, v and `zCoordinates` through up to 84 layers, plus 10 m wind
- `points.autoval.cwl.nc` (S3 only): a 467-station subset used for NOAA's automated validation — see *Notes*
- 30-minute SHEF water level bulletins for **78 stations** (79 blocks — one station appears twice; see *Notes*)

> **Live-verified — `fields.cwl.maxele.nc` covers the forecast only.** `zeta_max` is one-dimensional over 2,961,412 nodes. Its two `time` values (`[90000, 432000]` s since the nowcast start) give the **window over which the maximum was taken**, not two data slices. These are the same values STOFS-3D-Atlantic uses for its 96-hour window, so the stated end (+120 h) runs 48 hours past the end of the Pacific run. Treat the envelope as covering f001–f048 (TBD whether the time attribute is simply stale).

> **Live-verified — the Pacific maximum-elevation field is clean, unlike the Atlantic one.** On the 2026-11-17 12Z run, 99.87% of nodes are valid. All valid values fall within −3 to +5 m except three nodes below −10 m (minimum −22.69 m in the Timor Sea, with others near 9.5°S 123.8°E and off Fiji). No node exceeds 20 m. The maximum is 6.72 m, also in the Timor Sea; the median is 0.433 m and the 99th percentile 1.93 m. Every outlier lies outside U.S. waters. The MHW land boundary keeps most inland river nodes out of the field, unlike STOFS-3D-Atlantic.

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS and anonymous S3)
- **License:** **U.S. Government work — public domain.** Distributed through NOAA Open Data Dissemination (NODD). NOAA requests attribution for use or dissemination of unaltered data, prohibits stating or implying NOAA endorsement, and prohibits presenting modified data as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** **Both.** Gridded (native unstructured mesh plus five regridded GRIB2 domains) and station point time series and vertical profiles.
- **Data formats:** **NetCDF-4 / HDF5** (fields, stations, profiles; UGRID-style mesh topology in `fields.*`), **GRIB2** (regional grids), **SHEF** (station bulletins), **GeoPackage** (`.gpkg` disturbance polygons)
- **Station list:** Embedded in the `station_name` variable of the point NetCDF files as fixed-width records: NWS Handbook-5 ID, WMO header, NOS/buoy ID, state and site name (e.g. `SHNO3 SOUS43 9439201 OR St Helens OR`). Records are **null-padded** — strip `\x00`. A plain-text `station.in` (coordinates and short IDs) is published under S3 `staticfiles/v3.1/`.

### GRIB2 regional grids (live-verified from the 2026-11-17 12Z f012 files)

| Region | Projection | Grid (Ni × Nj) | Spacing | `generatingProcessIdentifier` |
|---|---|---|---|---|
| `conus.west` | Lambert conformal | 2145 × 1377 | 2539.703 m | 22 |
| `alaska` | Polar stereographic | 825 × 553 | 5953.125 m | 22 |
| `hawaii` | Mercator | 321 × 225 | 2500 m | 22 |
| `guam` | Mercator | 193 × 193 | 2500 m | 23 |
| `northpacific` | Mercator | 1473 × 1073 | 10000 m | 23 |

Lambert parameters: LaD 25.0°N, LoV 265.0°E, Latin1 = Latin2 = 25.0°. Alaska polar stereographic: LaD 60.0°N, orientation 210.0°. North Pacific spans 25°S–60.6°N, 110°E–250.9°E. These are the **same grid definitions STOFS-2D-Global uses for its five Pacific grids**, identical in every key except `generatingProcessIdentifier` (live-verified against the 2026-11-17 12Z STOFS-2D-Global files).

Each hourly file holds **exactly one message**: 49 files per grid (`f000`–`f048`). Whole-run files are also published as `<region>.cwl.grib2` (e.g. `northpacific.cwl.grib2` 105 MB, `conus.west.cwl.grib2` 27 MB, `guam.cwl.grib2` 2.6 MB).

### Official download locations

**NOMADS (operational, 24/7 supported):**
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/stofs_3d_pac.YYYYMMDD/
```
All operational STOFS components share `stofs/prod` (`stofs_2d_glo.YYYYMMDD/`, `stofs_3d_atl.YYYYMMDD/`, `stofs_3d_pac.YYYYMMDD/`), with about two days of retention. The Pacific day directory appears at about 18:00 UTC, roughly 25 minutes before the Atlantic one.

**AWS Open Data (NODD) — the long-term archive:**
```
s3://noaa-nos-stofs3d-pds/STOFS-3D-Pac/stofs_3d_pac.YYYYMMDD/
aws s3 ls --no-sign-request s3://noaa-nos-stofs3d-pds/STOFS-3D-Pac/
```
- AWS region: `us-east-1`. Same bucket as STOFS-3D-Atlantic, under a separate `STOFS-3D-Pac/` prefix.
- **Archive depth (live-verified 2026-11-18):** 94 daily directories from **2026-08-16** to 2026-11-17, with no gaps. The 2026-08-16 directory was written on 19 August and contains a `rerun/` subdirectory: it is a backfilled run, not a live operational cycle. Live production starts with **2026-08-17**.
- **Pre-operational archives (same bucket):** `para/` (2023-09-24 onward, still updated after go-live), `para1/` (2025-06-04 to 2026-05-23) and `para1_pro/` (2026-04-30 to 2026-09-21). These are development and parallel runs, not operational output.

> **S3 carries substantially more than NOMADS.** NOMADS publishes the GRIB2 grids, `field2d_*`, `fields.out2d_*`, `fields.cwl.maxele.nc`, the two station profile files, `points.cwl.temp.salt.vel.nc` and the SHEF bulletin. Only S3 carries the full 3-D fields (`temperature`, `salinity`, `horizontalVelX/Y`, `verticalVelocity`, `diffusivity`, `zCoordinates`), `points.cwl.nc`, `points.autoval.cwl.nc`, `fields.cwl.maxele.lmsl.nc`, the `.gpkg` disturbance files, the raw `schout_adcirc_*.nc` and `staout_1` files, and a 43 GB `hotstart.stofs3d.nc` restart file.

---

## Notes

> **Live-verified caution — Pacific GRIB2 cannot be told apart from STOFS-2D-Global by content.** Every message is discipline 10 / category 3 / **parameter 250**, which ecCodes 2.50.0 does not resolve (`shortName = unknown`). Select on `parameterNumber == 250`. STOFS-2D-Global publishes **the same parameter on the same five grid definitions under the same region names** (`conus.west`, `alaska`, `hawaii`, `guam`, `northpacific`). The only differing key is `generatingProcessIdentifier`: 22 (CONUS West, Alaska, Hawaii) and 23 (Guam, North Pacific) here, against 17 and 20 for the same grids in STOFS-2D-Global, and 21 for [STOFS-3D-Atlantic](./stofs-3d-atlantic.md). The three sets do not overlap, so the key can separate the systems, but it varies by grid within one run. **Track provenance by filename.** The systems also differ in vertical reference — see the [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) entry.

- **Operational status:** Operational since the 12Z cycle of 17 August 2026 (STOFS v3.1). The operator's portal is labelled experimental and directs users to regional NWS forecast offices for official water level forecasts.
- **No bitmap; land is the literal value 9999.0** in GRIB2 (`bitmapPresent = 0`, `missingValue = 9999`). NetCDF products use −99999.0.
- **`points.cwl.nc` and `points.cwl.temp.salt.vel.nc` are the same file under two names.** Live-verified on 2026-11-17: identical dimensions (720 × 850) and bit-identical data variables. NOMADS publishes only the second name. STOFS-3D-Atlantic has the same duplication.
- **`fields.cwl.maxele.nc` and `fields.cwl.maxele.lmsl.nc` are bit-identical.** Both carry `long_name = water surface elevation above local mean sea level`, with identical masks and values on the 2026-11-17 run. The `.lmsl` file's `history` records adding a geoid-to-LMSL field. The input was already LMSL-referenced (the conversion happens upstream in `out2d`), and the addition changed nothing, for reasons TBD. Download either one; only the first is on NOMADS.
- **`points.autoval.cwl.nc` appears to drop one real station.** Its `history` records `ncks -F -d station,1,467`, a 1-based slice of the first 467 points. The real stations occupy points 1–468 (469–850 are the altimetry points), so station 468 (`BMTW1`, Bremerton, WA) is excluded. Whether this is deliberate is TBD.
- **Astoria, OR appears twice.** `ASTO3 / 9439040` occupies points 115 and 211, about 70 m apart (236.2317°E 46.2073°N vs 236.2309°E 46.2081°N). The SHEF bulletin therefore carries two `ASTO3` blocks whose values differ by up to 0.01 ft. De-duplicate by ID before ingesting SHEF.
- **Station list quirks.** 70 points carry the placeholder Handbook-5 ID `ZZZZZ` (NOS stations and buoy locations without an NWS ID). Seven international gauges (e.g. `Rosslyn_Bay, Australia`, `Apia_B, Samoa`) lack the Handbook-5 field entirely, so the WMO header lands in the first column. Parse by position with care.
- **SHEF units were wrong before 2 November 2026.** SCN 26-98 corrected the Pacific SHEF bulletins to **feet above MLLW** from the 12Z cycle of 2 November 2026. Bulletins before that date carry the same `REFERENCED TO MLLW IN FEET` header but different values. Do not mix pre- and post-fix SHEF bulletins in one series.
- **`depth` metadata is inconsistent between products**, as in STOFS-3D-Atlantic. In `fields.cwl.maxele*.nc`, `depth` carries `units = mm` and the duplicated long_name `distance  below geoiddistance  below geoid`. In `fields.out2d_*` and `field2d_*` the units are `m`. Treat the `mm` label as a post-processing error.
- **Metadata is applied post-hoc by NCO.** Datum conversion, masking, fill values and attribute labels are attached through chains of `ncbo`, `ncap2`, `ncks` and `ncatted` calls, independently per product. This is why labels and values can drift apart between products of the same run.
- **Time epochs are cycle-relative**: `seconds since <day before cycle> 12:00:00 UTC`, i.e. the nowcast start. This matches STOFS-3D-Atlantic and differs from STOFS-2D-Global's fixed cold-start epoch.
- **Volume warning.** A full S3 cycle is about **253 GB**: six chunks each of seven 3-D variables at 3.4–4.3 GB per chunk, `field2d_*` at 3.84 GB per chunk, `out2d` at 1.36 GB per chunk, three rolling `schout_adcirc_*.nc` files at 1.92 GB each, and the 43 GB hotstart. The regional GRIB2 (about 0.55 MB per hour for CONUS West, 105 MB for the whole North Pacific run) and station files (25 MB) are the practical products for most users.
- **GloFAS input files are mirrored in the same bucket.** `STOFS-3D-Pac/_data/GloFAS/` holds daily `GLOFAS-global-30day_YYYYMMDD.nc` files (about 200 MB each, from 2026-06-22). These are the Copernicus river forecasts used as forcing, not STOFS output. For GloFAS itself see [GloFAS Forecast](../../../hydrological_models/global/eu/cems-glofas-forecast.md); its Copernicus licence terms, not NOAA's, apply to those files.
- **Relationship to other systems:**
  - Sibling component: [STOFS-3D-Atlantic](./stofs-3d-atlantic.md). Same SCHISM core, S3 bucket and version numbering. Differences: 48 h vs 96 h forecast, TPXO9 vs FES2014 tides, an MHW vs 10 m-contour land boundary, up to 84 vs up to 49 layers, and a single LMSL datum across products where the Atlantic component mixes datums.
  - Sibling component: [STOFS-2D-Global](../../global/usa/stofs-2d-global.md). Different core (ADCIRC), 2-D barotropic, four cycles daily, separate tide/surge products. It shares the five Pacific GRIB2 grids and the GRIB2 parameter, and covers the same U.S. Pacific coasts, so the two are alternative guidance for the same locations.
  - River forcing: [National Water Model](../../../hydrological_models/regional/usa/nwm.md) and [GloFAS Forecast](../../../hydrological_models/global/eu/cems-glofas-forecast.md). NWM's own coastal total water level configurations cover the U.S. West Coast, Hawaii and Puerto Rico with SCHISM too, but on separate meshes and datums.
  - Atmospheric drivers: GFS and HRRR. Ocean boundary: G-RTOFS.
  - SCHISM model documentation: http://ccrm.vims.edu/schismweb/

---

## Recent version history

| Version | Date | Notes |
|---|---|---|
| STOFS v3.1 (package `stofs.v3.1.7`) | live-verified 2026-11-17 | Current production package. |
| STOFS v3.1 bug fix | 2 Nov 2026 | SCN 26-98. Pacific SHEF water levels corrected to feet above MLLW. |
| STOFS v3.1 | 17 Aug 2026, 12Z | SCN 26-51 (Updated). **STOFS-3D-Pacific introduced as an operational component.** Originally announced for 7 July 2026, then 11 August; the go-live date is confirmed by the S3 archive and the operator's portal. |
| Pre-operational | Sep 2023 – Aug 2026 | Parallel and development runs published on S3 under `para/`, `para1/` and `para1_pro/`. |

---

## Official documentation
- Operator portal (configuration, validation): https://polar.ncep.noaa.gov/estofs/pac.htm
- SCN 26-51 (Updated) — STOFS v3.1: https://www.weather.gov/media/notification/pdf_2026/SCN26-51_STOFS.v3.1_Updated.aaa.pdf
- SCN 26-98 — STOFS v3.1 SHEF bug fix: https://www.weather.gov/media/notification/pdf_2026/scn26-98_STOFS_SHEF__bug_fix.pdf
- NOAA Institutional Repository dataset record: https://repository.library.noaa.gov/view/noaa/76397
- NOAA InPort — STOFS: https://www.fisheries.noaa.gov/inport/item/79430
- AWS Open Data Registry: https://registry.opendata.aws/noaa-nos-stofs3d/
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/
- SCHISM model documentation: http://ccrm.vims.edu/schismweb/
