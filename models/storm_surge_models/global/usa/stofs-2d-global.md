# STOFS-2D-Global (Global Surge and Tide Operational Forecast System, 2-D component)

## What this model is
**STOFS-2D-Global** is NOAA's operational global water level forecast system, run four times daily by NCEP Central Operations on behalf of the NOS Office of Coast Survey. It produces a 6-hour nowcast followed by a 180-hour (7.5-day) forecast of coastal water levels for the entire globe.

The hydrodynamic core is **ADCIRC**, a 2D depth-averaged finite element model, run on a single global unstructured triangular mesh of **12,785,004 nodes and 24,875,336 elements** (live-verified in the file headers). Tides are integrated explicitly inside the model rather than added afterward.

Three water level products are distributed, and this three-way split is the defining feature of the system:
- **`cwl`** — combined water level (tide + surge), the total
- **`htp`** — harmonic tidal prediction (astronomical tide only)
- **`swl`** — sub-tidal water level (the isolated storm surge)

The system was renamed from **ESTOFS** (Extratropical Surge and Tide Operational Forecast System) in the STOFS v1.0.1 upgrade of December 2022, when a separate 3-D Atlantic component was added — see [STOFS-3D-Atlantic](../../regional/usa/stofs-3d-atlantic.md). A 3-D Pacific component, [STOFS-3D-Pacific](../../regional/usa/stofs-3d-pacific.md), followed with STOFS v3.1. Despite the "surge and tide" naming and the ETSRG parameter label inherited from ESTOFS, STOFS-2D-Global is a general water level model, not an extratropical-only one.

The current operational release is **STOFS v3.1**, operational since the **12Z cycle of 17 August 2026** (SCN 26-51 Updated). Its main change to this component is a **gridded bias correction**: since v3.1 the primary gridded `cwl` product, including the GRIB2, is corrected towards tide-gauge observations, and the uncorrected field is published separately as `noanomaly` NetCDF. See *Data assimilation*.

---

## Who runs it
- **Organization:** NOAA / National Ocean Service (NOS) / Office of Coast Survey / Coast Survey Development Laboratory (CSDL), with NOAA/NWS/NCEP Central Operations (NCO) running the operational suite
- **Development partners:** University of Notre Dame, University of North Carolina, The Water Institute of the Gulf
- **Country / region:** United States (global coverage)

---

## What area it covers
- **Coverage:** Global ocean and coastal floodplain
- **Domain details:** Single global unstructured mesh, no open boundaries. Station coverage spans 69.0°S to 82.5°N and the full longitude range (live-verified on the 2026-07-23 00Z run).
- **Mesh resolution:** At least **1.5 km globally**, refining to:
  - up to **80 m** — U.S. West Coast and Alaska
  - up to **90–120 m** — Pacific Islands (Hawaii, Guam, American Samoa, Marianas, Wake, Marshall Islands, Palau)
  - up to **120 m** — U.S. East Coast and Puerto Rico
- **Inundation coverage:** **Yes.** The floodplain extends overland to approximately **10 m elevation above MSL** for the U.S. East Coast and up to **20 m above MSL** for the Pacific Islands. Wetting and drying is active — this is a genuine overland flooding domain, unlike the Canadian [GDSPS](../canada/gdsps.md) and [RESPS](../../regional/canada/resps.md), which both stop at the coastline.

---

## Basic details
- **Model type:** Deterministic storm surge / total water level model
- **Core hydrodynamic model:** ADCIRC (ADvanced CIRCulation), finite element, GWCE formulation
- **Dimensionality:** **2D depth-averaged (barotropic)**
- **Forecast length:** 180 hours, preceded by a **6-hour nowcast** (186-hour total span per cycle). Live-verified on the 2026-11-17 12Z run: station time series run from 2026-11-17 06:06 UTC to 2026-11-25 00:00 UTC (1,860 six-minute records).
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** varies by product —
  - GRIB2 regional grids: **hourly** (f000–f180, 181 records)
  - Native-mesh field NetCDF: **hourly**
  - Station NetCDF: **6-minute** (1,860 records)
  - SHEF station bulletins: **30-minute**
- **Computational timestep:** 6 s (implicit)
- **Operational footprint:** 4,064 cores on WCOSS2, per the bucket README updated in August 2026 (the pre-v3.1 figure was about 3,968). The `source` attribute reads `Dogwood/Cactus`.

---

## Grid and bathymetry
- **Grid type:** Unstructured triangular mesh (`grid_type = Triangular`, `Conventions = UGRID-0.9.0`)
- **Horizontal resolution:** see *What area it covers* above
- **Mesh size:** 12,785,004 nodes / 24,875,336 triangular elements, unchanged by v3.1 (live-verified from the 2026-11-17 12Z `fields.cwl.maxele.nc`)
- **Mesh generator:** **OceanMesh2D** (live-verified `agrid` global attribute)
- **Internal barriers (new in v3.1 output):** the native-mesh files now carry ADCIRC boundary and barrier arrays — `nvell`, `ibtype`, `nbvv`, `barsp`, `barht`, `ibconn`, `barsb` (262 boundary segments, 12,421 boundary nodes) — which were absent from v2.1 output. This suggests levee and weir structures were added along existing mesh edges, since node and element counts are unchanged (TBD — mechanism unconfirmed).
- **Bathymetry source:** Not documented. The `depth` variable is present on the native mesh with `long_name = "distance below geoid"`, but no source dataset is named. (TBD)
- **Wetting and drying:** **Yes.** Live-verified from the embedded ADCIRC `fort.15` attributes: `nolifa = 2` (wetting/drying with finite amplitude terms) and `h0 = 0.1` m minimum water depth.
- **Other configuration** (live-verified from `fort.15` attributes carried in every output file): `nolibf = 1` (quadratic bottom friction), `cf = 0.0005` (bottom friction coefficient), `ncor = 1` (spatially variable Coriolis), `nolica = nolicat = 1` (advective terms active), `eslm = -0.2` (Smagorinsky-type horizontal eddy viscosity), `nwp = 7` (seven nodal attributes).
- **Static files:** grid, nodal attributes, station lists and control files for each version are published under `s3://noaa-gestofs-pds/staticfiles/{v1.1,v2.1,v3.1}/`.

---

## Vertical datum and reference level
- **Vertical datum:** Differs by product —
  - **Gridded output (GRIB2 and native NetCDF):** global mean sea level (**GMSL**)
  - **Station NetCDF:** local mean sea level (**LMSL**)
  - **SHEF station bulletins:** **MLLW**, and in **feet**, not metres. The bucket README describes the SHEF files as "(m, MLLW)"; the bulletin header says `REFERENCED TO MLLW IN FEET`, and the values agree with feet.
- **What the water level field is measured relative to:**
  - `cwl` = total water level (tide + surge) above the stated datum
  - `htp` = astronomical tidal prediction above the stated datum
  - `swl` = the isolated surge residual
- **Datum conversion offsets provided?** **For stations only.** `staticfiles/v3.1/stofs_2d_glo_msl2mllw` (also in `v2.1/`) is a plain-text table of 1,687 NOS station IDs with an MSL-to-MLLW offset in metres (e.g. `8410140 2.958` for Eastport, ME). This appears to be the table used to produce the SHEF bulletins. No gridded conversion field is published.
- **Mean sea level trend / SLR handling:** Not documented. (TBD)

> **Live-verified caution — misleading geoid metadata, again.** The official documentation states unambiguously that output is referenced to **MSL** (the bucket README labels every gridded product "(m, MSL)"). But the file metadata says geoid throughout: the station NetCDF `zeta` variable carries `standard_name = sea_surface_height_above_geoid` and `long_name = "water surface elevation above geoid"`, the native-mesh `zeta_max` carries `standard_name = maximum_sea_surface_height_above_geoid`, and the GRIB2 tidal field is literally named *"Ocean Surface Elevation Relative to Geoid"*. This is the same defect documented for [GDSPS](../canada/gdsps.md) and [RESPS](../../regional/canada/resps.md) — and here, unlike GDSPS, **no attribute anywhere in the file corrects the record.** Still true under v3.1. Trust the documentation, not the CF `standard_name`.

---

## Tide handling
- **Are tides included?** Yes — tides are integrated explicitly inside the ADCIRC run, and `cwl` is a genuine total water level.
- **Tidal forcing source:** Astronomical tidal potential applied directly in the interior. Live-verified from `fort.15`: `ntif = 8` (eight tidal constituents in the tidal potential), `ntip = 2` (tidal potential **plus** self-attraction and loading), `nbfr = 0` (**no** boundary tidal forcing — the global domain has no open boundaries). The system also includes **internal tides**.
- **Separation of components:** All three components are distributed directly as separate products (`cwl`, `htp`, `swl`) — a more complete separation than most operational surge systems, which typically publish only two of the three.
- **Tide–surge interaction:** Modelled nonlinearly within the run.

> **Live-verified — the three products are exactly additive, so only two are independent.** On the 2026-11-17 12Z f012 CONUS East GRIB2, `cwl − (htp + swl)` has a maximum absolute residual of **2 × 10⁻⁵ m** over 628,542 valid points — i.e. zero to the GRIB2 packing precision, as it was under v2.1. Because `cwl` is now bias-corrected (see *Data assimilation*), exact additivity means the correction is carried in `swl` as well. Downloading all three triples your transfer volume for no additional information. Fetch `cwl` and `htp` and subtract, or `swl` and `htp` and add.

---

## Forcing and coupling
- **Meteorological forcing — wind:** 10 m winds from [GFS](../../../nwp_models/global/usa/gfs.md) (`nws = 10`, gridded meteorological file forcing)
- **Meteorological forcing — pressure:** GFS surface pressure
- **Forcing frequency:** Hourly GFS forcing through +120 h, then 3-hourly from +120 h to +180 h (introduced in STOFS v2.0.0; TBD whether unchanged at v3.1)
- **Ice forcing:** GFS **sea ice**, used to modify wind drag transfer. This is unusual for a surge model and matters at high latitudes.
- **Forcing files published (new with v3.1, S3 only):** each cycle now includes the GFS forcing as used, on the native mesh: `icec.nc` (sea ice concentration, 11.4 GB), `pressfc.nc` (surface pressure, 12.6 GB) and `uvgrd10m.nc` (10 m wind, 15.5 GB).
- **Wave contribution:** None documented. STOFS-2D-Global is not coupled to a wave model and no wave setup term is published. (TBD)
- **River discharge / freshwater forcing:** None. Inland hydrology coupling to the National Water Model is a feature of [STOFS-3D-Atlantic](../../regional/usa/stofs-3d-atlantic.md) and [STOFS-3D-Pacific](../../regional/usa/stofs-3d-pacific.md), not of the global 2-D component.
- **Ocean forcing / boundary conditions:** None — the domain is global and has no open ocean boundaries.
- **Run continuity:** Hot-started continuously. Under v3.1 the run was cold-started afresh: the time epoch moved to **2026-05-26 12:00 UTC**, and the 2026-11-17 12Z files carry `rnday = 175.0` and `ihot = 368` (v2.1 had a 2024-04-04 epoch and `rnday = 839.5`). `rnday` is the cumulative run length, not a per-cycle value.

---

## Data assimilation
- **Assimilates water level observations:** **No** interior data assimilation. Observations enter only through post-processing bias corrections.
- **Station bias correction (since v2.0.0):** at stations where NOS/CO-OPS observations exist, the forecast is corrected by the bias between observed water levels and the most recent five days of nowcast water levels.
- **Gridded bias correction (new in v3.1):** the station biases are spread across the global mesh by graph diffusion, so the gridded output is corrected too. **This changes the meaning of the primary gridded product.** `fields.cwl.nc`, `fields.cwl.maxele.nc`, `fields.swl.nc` and all GRIB2 output are now bias-corrected. The uncorrected model output is published only as NetCDF: `fields.cwl.noanomaly.nc`, `fields.cwl.maxele.noanomaly.nc` and `points.cwl.noanomaly.nc`. **There is no uncorrected GRIB2.**

> **Live-verified — the GRIB2 carries the corrected field.** On the 2026-11-17 12Z run, the maximum of the CONUS East `cwl` GRIB2 over f001–f180 was compared with the nearest native-mesh node in both maxele files at 48,248 grid points. The median difference from the corrected `fields.cwl.maxele.nc` is **0.0002 m**; from `fields.cwl.maxele.noanomaly.nc` it is **0.172 m**. 99.6% of points are closer to the corrected field.

> **Live-verified — the gridded correction is broad and regionally biased.** Comparing the two maxele files on the same run, **96.3% of the 9,221,579 valid nodes** are changed by the correction, by −1.54 m to +0.55 m (5th–95th percentile −0.04 to +0.25 m). The median correction is **+0.147 m** on the U.S. East and Gulf coasts and **+0.107 m** on the U.S. West Coast, but close to zero in Alaska (−0.002 m), Hawaii (−0.023 m) and Europe (−0.001 m). Corrected and uncorrected fields are therefore not interchangeable for U.S. coastal work. The corrected file also masks 3,103 nodes that are valid in the uncorrected one.

> **Live-verified — station correction.** Comparing `points.cwl.nc` with `points.cwl.noanomaly.nc` on the 2026-11-17 12Z run, **208 of 1,687 stations (12.3%)** carry a non-zero correction. Among those, the median mean offset is **+0.260 m**, spanning −1.169 m to +0.563 m. The offset is close to constant through a cycle: median temporal standard deviation 0.011 m, maximum 0.064 m. That is much steadier than under v2.1, when one site varied by 1.85 m. Use `points.cwl.nc` for guidance and `points.cwl.noanomaly.nc` for raw model verification.

---

## What it provides
- **Combined water level** (`cwl`) — total, tide + surge; bias-corrected since v3.1
- **Harmonic tidal prediction** (`htp`) — astronomical tide only
- **Sub-tidal water level** (`swl`) — isolated storm surge; carries the bias correction
- **Uncorrected combined water level** (`fields.cwl.noanomaly.nc`, `fields.cwl.maxele.noanomaly.nc`, `points.cwl.noanomaly.nc`)
- **Maximum water level envelope** over the cycle (`fields.cwl.maxele.nc`, with `time_of_zeta_max`)
- **Depth-averaged current velocity** — hourly fields (`fields.cwl.vel.nc`) and 6-minute station series (`points.cwl.vel.nc`)
- **Maximum current speed** and **maximum wind speed** envelopes (`fields.cwl.maxvel.nc`, `fields.cwl.maxwvel.nc`)
- **GFS forcing fields** on the native mesh (`icec.nc`, `pressfc.nc`, `uvgrd10m.nc`; S3 only)
- **Autovalidation subset** (`points.autoval.cwl.nc`; S3 only) — the 833 tide-gauge stations, i.e. `points.cwl.nc` without the 854 `UJ### SOUS00 SA###` points. These carry no site names here; STOFS-3D-Pacific uses the same ID pattern for points it labels "Satellite Altimetry Station", so they are most likely virtual altimetry points (TBD). Unlike the [STOFS-3D-Pacific](../../regional/usa/stofs-3d-pacific.md) subset, this one is complete: its `ncks -d station,1,833` slice ends exactly at the last gauge.

No inundation depth product is published separately; overland water level is carried on the floodplain nodes of the native mesh.

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS and anonymous S3)
- **License:** **U.S. Government work — public domain.** Distributed through NOAA Open Data Dissemination (NODD): open to the public and usable as desired. NOAA requests attribution for use or dissemination of unaltered data; it is not permissible to state or imply NOAA endorsement or affiliation, and modified data may not be presented as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** **Both.** Gridded (native unstructured mesh plus seven regridded regional GRIB2 domains) and station point time series at 1,687 sites (833 tide gauges plus 854 `UJ`/`SA###` points, most likely virtual altimetry points).
- **Data formats** (live-verified from file magic bytes, 2026-11-17 12Z):
  - **NetCDF-4 / HDF5** — the bias-corrected native-mesh files (`fields.cwl.nc`, `fields.cwl.maxele.nc`, `fields.swl.nc`) and the forcing files
  - **NetCDF-3 classic** — the uncorrected and other native-mesh files (`fields.cwl.noanomaly.nc`, `fields.cwl.maxele.noanomaly.nc`, `fields.htp.nc`, `fields.cwl.vel.nc`, `fields.cwl.maxvel.nc`) and all station point files. Classic format means no internal compression or chunking, which is why the uncorrected `fields.cwl.noanomaly.nc` (19.6 GB) is nearly twice the size of the corrected `fields.cwl.nc` (10.1 GB).
  - **GRIB2** — regional structured grids
  - **SHEF** — station bulletins (MLLW, feet)
- **Station list:** Embedded in the `station_name` variable of the point NetCDF files as fixed-width records combining NWS Handbook-5 ID, WMO header, NOS station ID, state, and site name — e.g. `PSBM1 SOUS41 8410140 ME Eastport`. Station coordinates and IDs are also in `staticfiles/v3.1/`.
- **DOI:** https://doi.org/10.25923/ng4h-4b85

### GRIB2 regional grids (live-verified from the 2026-11-17 12Z f012 files; unchanged by v3.1)

| Region | Projection | Grid (Ni × Nj) | Spacing | `generatingProcessIdentifier` |
|---|---|---|---|---|
| `conus.east` | Lambert conformal | 2145 × 1377 | 2539.703 m | 14 |
| `conus.west` | Lambert conformal | 2145 × 1377 | 2539.703 m | 17 |
| `alaska` | Polar stereographic | 825 × 553 | 5953.125 m | 17 |
| `hawaii` | Mercator | 321 × 225 | 2500 m | 17 |
| `guam` | Mercator | 193 × 193 | 2500 m | 20 |
| `puertori` | Mercator | 339 × 225 | 1250 m | 14 |
| `northpacific` | Mercator | 1473 × 1073 | 10000 m | 20 |

Lambert parameters: LaD 25.0°N, LoV 265.0°E, Latin1 = Latin2 = 25.0°. Alaska polar stereographic: LaD 60.0°N, orientation 210.0°. North Pacific spans 25°S–60.6°N, 110°E–250.9°E.

### Official download locations

**NOMADS (operational, 24/7 supported):**
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/
ftp://ftp.ncep.noaa.gov/data/nccf/com/stofs/prod/
```
Note the path is `stofs/prod`, **not** `estofs/prod` — the directory was renamed with the ESTOFS→STOFS transition. All three components share this directory (`stofs_2d_glo.YYYYMMDD/`, `stofs_3d_atl.YYYYMMDD/` and `stofs_3d_pac.YYYYMMDD/`), and retention is approximately **two days**. NOMADS carries the GRIB2, the SHEF bulletins, the `cwl`/`htp`/`swl` field and point files, both maxele files, `fields.cwl.maxvel.nc` and the two `noanomaly` field/point files. It does **not** carry the velocity files (`fields.cwl.vel.nc`, `points.cwl.vel.nc`), `fields.cwl.maxwvel.nc`, `points.autoval.cwl.nc` or the forcing files; those are S3 only.

**AWS Open Data (NODD) — the long-term archive:**
```
s3://noaa-gestofs-pds/stofs_2d_glo.YYYYMMDD/
https://noaa-gestofs-pds.s3.amazonaws.com/index.html
aws s3 ls --no-sign-request s3://noaa-gestofs-pds/
```
- AWS region: `us-east-1`
- SNS new-object notifications: `arn:aws:sns:us-east-1:123901341784:NewGESTOFSObject`
- **Archive depth (live-verified 2026-11-18):** 1,411 daily directories under `stofs_2d_glo.` from **2023-01-08** to present, with no gaps, plus 744 legacy `estofs.YYYYMMDD/` directories covering **2020-12-30 to 2023-01-12**.
- Each cycle also has a `CC/rerun/` folder of initial-condition and forcing files, which the README describes as stored for NOAA internal reference. These bring a day directory to about 1 TB.

> **Live-verified — the v2.1 → v3.1 boundary in the archive is not where the README puts it.** The bucket README says v2.1 ran through the 06Z cycle of 17 August 2026 and v3.1 from 12Z. But the archived files for **every cycle from 16 August 00Z to 17 August 06Z are v3.1 output** (`title = STOFS_2D_GLOBAL.V3.1.0`, `noanomaly` files present). All were written in one batch between 19:19 and 21:40 UTC on 17 August, and each has a `rerun/` folder. The last v2.1 cycle in the archive is **15 August 18Z**, and normal write times resume with 17 August 18Z. Whatever ran live on 16 August, the archive now holds v3.1 reruns for those six cycles. Split time series at **16 August 00Z**, not at the documented 17 August 12Z.

### Parallel (pre-implementation) feeds — closed
The NOMADS parallel directory `stofs/para/` that carried v3.1 before implementation now returns **HTTP 403**. On S3, the development prefixes have stopped: `_para/` (2024-01-01 to 2026-07-31), `_para2/` (2024-01-01 to 2026-07-30) and `_para4/` (2026-01-01 to 2026-08-28, the one that mirrored the full public product suite). `_para3/` and `_polar_para/` hold no daily directories. The README describes all of them as stored for NOAA internal reference. Expect the next upgrade's parallel run to appear under one of these prefixes.

---

## Notes

> **Live-verified caution — the GRIB2 parameter names are actively wrong, and filtering by `shortName` will give you the wrong field.** Each hourly GRIB2 file contains exactly three messages. Decoded with ecCodes 2.50.0 (unchanged from 2.48.0 and from v2.1) they appear as:
>
> | Message | Discipline/Category/Number | ecCodes `shortName` | ecCodes `name` | **What it actually is** |
> |---|---|---|---|---|
> | 1 | 10 / 3 / **250** | `unknown` | `unknown` | **Combined water level (`cwl`)** — the total, bias-corrected |
> | 2 | 10 / 3 / **194** | `elevhtml` | Ocean Surface Elevation Relative to Geoid | **Harmonic tidal prediction (`htp`)** — tide only |
> | 3 | 10 / 3 / **193** | `etsrg` | Extra Tropical Storm Surge | **Sub-tidal water level (`swl`)** — the surge |
>
> The mapping was established by decoding the single-product whole-cycle files, each of which contains 181 messages of exactly one parameter: `guam.cwl.grib2` → 250, `guam.htp.grib2` → 194, `guam.swl.grib2` → 193. It is independently confirmed by the arithmetic: parameter 250 equals 194 + 193 to within 2 × 10⁻⁵ m.
>
> The practical trap: a user filtering for total water level would reasonably reach for `elevhtml` ("Ocean Surface Elevation Relative to Geoid") and receive **the astronomical tide with no surge in it at all**. The field they want is the one ecCodes cannot name. Parameter 10/3/250 falls in the NCEP local-use range (192–254) and is absent from the ecCodes tables — apply the standard fallback of reading `discipline`, `parameterCategory` and `parameterNumber` directly and selecting on `parameterNumber == 250`, or simply use the per-product `cwl`/`htp`/`swl` files whose filenames are unambiguous.
>
> **Parameter 250 is also used by both 3-D components, on identical grid definitions.** [STOFS-3D-Atlantic](../../regional/usa/stofs-3d-atlantic.md) publishes it on `conus.east` and `puertori`, and [STOFS-3D-Pacific](../../regional/usa/stofs-3d-pacific.md) on `conus.west`, `alaska`, `hawaii`, `guam` and `northpacific`. The only key that differs is `generatingProcessIdentifier`: 14/17/20 here (by grid), 21 for STOFS-3D-Atlantic, 22/23 for STOFS-3D-Pacific. Track provenance by filename. The three systems are also on different vertical references (GMSL here, xGEOID20B for 3-D Atlantic gridded output, LMSL for 3-D Pacific).

- **No bitmap; land is the literal value 9999.0.** GRIB2 messages carry `bitmapPresent = 0` and `missingValue = 9999`. Land and dry points are encoded as an in-band 9999.0, not as a masked or NaN value. Readers that do not explicitly filter will silently average 9999 into their statistics. Live-verified valid fractions on the 2026-07-23 00Z f012 files: 21.3% of the CONUS East grid, 96.2% of Hawaii, 92.2% of Puerto Rico.
- **`conus.east` and `conus.west` are the same grid.** Both are the 2145 × 1377 / 2.5 km CONUS Lambert grid with identical projection parameters and identical first-grid-point coordinates. They differ only in which portion of the domain carries valid data. They are not two separate regional domains.
- **Station NetCDF fill values are inconsistent and automatic masking fails.** `zeta` declares `_FillValue = -99999.0`, but the file contains more than one sentinel. Under v2.1 there were four (−99999.0, −99998.9, −99998.8, −99998.7). On the 2026-11-17 12Z v3.1 run there are two: 4,173 elements at −99999.0 and **231 at −99998.7**, which `netCDF4`'s automatic masking leaves in the array as ordinary numbers near −99999. Mask on `zeta < -1000`, not on equality with `_FillValue`. This is the same class of defect documented for [RESPS](../../regional/canada/resps.md).
- **Time units carry a trailing Fortran comment.** The `time` variable's units string is `"seconds since 2026-05-26 12:00:00        ! NCDASE - BASE_DAT"` — a truncated Fortran namelist comment appended to an otherwise valid udunits string. `cftime` tolerates it and parses correctly; stricter udunits parsers may not. The epoch is a **fixed cold-start date, not the run start**. It does not change between cycles, but it **did change at v3.1** (from 2024-04-04 12:00 to 2026-05-26 12:00), so code that hard-codes the epoch will mis-date every post-upgrade file.
- **Conflicting convention attributes.** Files carry both `Conventions = UGRID-0.9.0` and a lowercase `convention = "CF-1.0                     ! NCCONV - CONVENTIONS"`. Most of the global attribute block is raw ADCIRC `fort.15` text with embedded Fortran comments and fixed-width padding; parse it defensively.
- **Version strings are unhelpful.** v3.1 files carry `title = STOFS_2D_GLOBAL.V3.1.0`, `version = unknown`, `git_hash = unknown` and an unchanged `runid = STOFS 2D GLOBAL v5.6.5`. Under v2.1 `version` read `noaa.stofs.2d.glo.v2.1.0r1.v55.12`. Use `title` to tell the releases apart, and record the SCN version externally.
- **`generatingProcessIdentifier` is inconsistent across grids of the same run.** Live-verified again on the 2026-11-17 12Z cycle: `conus.east` and `puertori` report 14, `conus.west`, `alaska` and `hawaii` report 17, and `guam` and `northpacific` report 20 — three different values from one model in one cycle. Do not use this key to identify the model on its own.
- **The v3.1 station list does not fully match the SCN.** The SCN's additions are all present: Bogue Sound at Emerald Isle NC (`EMDN7`), White Oak River at Swansboro NC (`SWBN7`), Kings Bay MSF Pier GA (`KBMG1`) and TEMCO Kalama Terminal WA (`KLMW1`). Naples FL is now NOS ID 8725114 and New London CT is `NLHC3`. South Amboy NJ is gone. But **Duck NC (`DUKN7`), Fort Point NH (`FTPN3`), Money Point VA (`MNPV2`) and Springmaid Pier SC (`MROS1`) are still present**, although the SCN lists them as removed and the pre-implementation parallel feed had dropped them. The University of Hawai'i Sea Level Center sites (`UH***`) keep the trailing short code first seen in the parallel feed (e.g. `UH002 SOUS00 GL113 Tarawa, Bairiki Kiribati tara`), so name matching across the upgrade fails. Match on NOS station ID where possible.
- **Volume warning.** A single cycle's products total about **155 GB** under v3.1, against about 63 GB under v2.1. The largest files are `fields.cwl.vel.nc` (38.7 GB), the forcing files (39.4 GB together), `fields.cwl.noanomaly.nc` and `fields.htp.nc` (19.6 GB each), `fields.swl.nc` (19.2 GB) and `fields.cwl.nc` (10.1 GB). GRIB2 is 4.4 GB per cycle and the station files about 170 MB. Most users should stay on the regional GRIB2 or the point files.
- **One station is a persistent outlier.** On the 2026-11-17 12Z run, station `UJ816 SOUS00 SA816` (35.44°E, 46.22°N, inland near the Sea of Azov) peaks at 126.5 m, against a median peak across all stations of 0.68 m. It is the only station over 10 m, as it was under v2.1 (137.9 m on 2026-07-23). Station `FRDP4 SOUS41 9753216 PR Fajardo` still returns sentinel values for the entire forecast. Apply sanity bounds before using the station set wholesale.
- **Relationship to other systems:**
  - Driving NWP: [GFS](../../../nwp_models/global/usa/gfs.md) supplies 10 m wind, surface pressure and sea ice.
  - Sibling component: [STOFS-3D-Atlantic](../../regional/usa/stofs-3d-atlantic.md) — same STOFS umbrella and version numbering, but a different model core (SCHISM), a regional domain, 3-D baroclinic physics, and NWM inland hydrology coupling. It shares this system's `conus.east` and `puertori` GRIB2 grids.
  - Sibling component: [STOFS-3D-Pacific](../../regional/usa/stofs-3d-pacific.md) — SCHISM-based 3-D Pacific basin component, operational since 17 August 2026, run once daily to +48 h. It publishes on this system's five Pacific GRIB2 grids under the same region names and covers the same U.S. Pacific coasts, so the two are alternative guidance for the same locations. The 3-D components share an S3 bucket (`noaa-nos-stofs3d-pds`) separate from this one.
  - Downstream: the [National Water Model](../../../hydrological_models/regional/usa/nwm.md) coastal total water level configurations take their ocean boundary from STOFS. Since NWM v3.1 (18 August 2026) that is the bias-corrected STOFS-2D-Global.
  - Comparable global deterministic surge system: [GDSPS](../canada/gdsps.md) (ECCC). GDSPS is a light-baroclinic NEMO configuration on a structured 1/12° grid with no overland domain; STOFS-2D-Global is a true 2D barotropic ADCIRC model on an unstructured mesh with an active floodplain. They are not equivalent products despite both being global.
  - Predecessor: ESTOFS, whose three regional domains (Atlantic, Pacific, Micronesia) were replaced by this single global system in late 2020. The `etsrg` GRIB2 parameter label is a survival from that era.

---

## Recent version history

| Version | Date | Notes |
|---|---|---|
| STOFS v3.1 | 17 Aug 2026, 12Z (archive: from 16 Aug 00Z) | SCN 26-51 (Updated); first announced for 7 July 2026, then 11 August. Gridded bias correction by graph diffusion (GRIB2 and `fields.cwl.nc` now corrected; uncorrected `noanomaly` NetCDF added). Station list revised. Cold restart (new time epoch 2026-05-26). Barrier arrays in the native-mesh output. GFS forcing files and an autovalidation station subset published on S3. Mesh, GRIB2 grids and encoding unchanged. |
| STOFS v2.1 (v2.1.4) | 2024 | SCN 23-124. In-file component stamp `STOFS_2D_GLOBAL.V2.1.0`, `runid` v5.6.5. Last v2.1 cycle in the S3 archive: 15 Aug 2026, 18Z. |
| STOFS v2.0.0 | Dec 2023 / early 2024 | Station bias correction against NOS/CO-OPS observations introduced; GFS forcing improved to hourly through +120 h, 3-hourly to +180 h. (Exact implementation date TBD.) |
| STOFS v1.1.1 | 10 Jan 2023 | PNS 23-01. STOFS-3D-Atlantic S3 bucket established; `stofs_2d_glo.YYYYMMDD` prefix begins 2023-01-08 on S3. |
| STOFS v1.0.1 | Dec 2022 | ESTOFS renamed to STOFS; STOFS-3D-Atlantic component added; improved resolution for U.S. East Coast, U.S. West Coast, Alaska and Iceland. |
| ESTOFS (global) | late 2020 | Global 2-D system replaced the three regional ESTOFS domains (Atlantic, Pacific, Micronesia). S3 `estofs.YYYYMMDD` prefix begins 2020-12-30. |

Earlier upgrades occurred in July 2021 and January 2023. (Full pre-2023 history TBD.)

---

## Official documentation
- AWS Open Data Registry entry: https://registry.opendata.aws/noaa-gestofs/
- Bucket README (file inventory; does not yet list the `noanomaly` or forcing files): https://noaa-gestofs-pds.s3.amazonaws.com/README.html
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/
- NOS Office of Coast Survey storm surge modeling overview: https://nauticalcharts.noaa.gov/learn/storm-surge-modeling.html
- ESTOFS/STOFS product pages: https://polar.ncep.noaa.gov/estofs/ and https://polar.ncep.noaa.gov/stofs/
- SCN 26-51 (Updated) — STOFS v3.1: https://www.weather.gov/media/notification/pdf_2026/SCN26-51_STOFS.v3.1_Updated.aaa.pdf
- SCN 23-124 (aaf) — STOFS v2.1.4: https://www.weather.gov/media/notification/pdf_2023_24/scn23-124_stofs_upgrade_v2.1.4_aaf.pdf
- PNS 23-43 — STOFS v2.0.0 proposal: https://www.weather.gov/media/notification/pdf_2023_24/pns23-43_stofs_2.0.0_upgrade.pdf
- PNS 23-01 — STOFS v1.1.1 known issues: https://www.weather.gov/media/notification/pdf_2023_24/pns23-01_stofs_v1.1.1.pdf
- PNS 22-37 — ESTOFS→STOFS transition: https://www.weather.gov/media/notification/pdf2/pns22-37_stofs_dec_2022_upgrade_aaa.pdf
- NOAA InPort — STOFS: https://www.fisheries.noaa.gov/inport/item/79430
- ADCIRC model documentation: http://www.adcirc.org
- Citation DOI: https://doi.org/10.25923/ng4h-4b85
