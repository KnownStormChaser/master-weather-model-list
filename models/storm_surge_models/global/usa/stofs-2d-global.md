# STOFS-2D-Global (Global Surge and Tide Operational Forecast System, 2-D component)

## What this model is
**STOFS-2D-Global** is NOAA's operational global water level forecast system, run four times daily by NCEP Central Operations on behalf of the NOS Office of Coast Survey. It produces a 6-hour nowcast followed by a 180-hour (7.5-day) forecast of coastal water levels for the entire globe.

The hydrodynamic core is **ADCIRC**, a 2D depth-averaged finite element model, run on a single global unstructured triangular mesh of **12,785,004 nodes and 24,875,336 elements** (live-verified in the file headers). Tides are integrated explicitly inside the model rather than added afterward.

Three water level products are distributed, and this three-way split is the defining feature of the system:
- **`cwl`** — combined water level (tide + surge), the total
- **`htp`** — harmonic tidal prediction (astronomical tide only)
- **`swl`** — sub-tidal water level (the isolated storm surge)

The system was renamed from **ESTOFS** (Extratropical Surge and Tide Operational Forecast System) in the STOFS v1.0.1 upgrade of December 2022, when a separate 3-D Atlantic component was added — see [STOFS-3D-Atlantic](../../regional/usa/stofs-3d-atlantic.md). Despite the "surge and tide" naming and the ETSRG parameter label inherited from ESTOFS, STOFS-2D-Global is a general water level model, not an extratropical-only one.

The current operational release is **STOFS v2.1** (SCN 23-124 references v2.1.4). **STOFS v3.1 is scheduled for 11 August 2026** per SCN 26-51 (Updated) — see [Upcoming changes](#upcoming-changes).

---

## Who runs it
- **Organization:** NOAA / National Ocean Service (NOS) / Office of Coast Survey / Coast Survey Development Laboratory (CSDL), with NOAA/NWS/NCEP Central Operations (NCO) running the operational suite
- **Development partners:** University of Notre Dame, University of North Carolina, The Water Institute of the Gulf
- **Country / region:** United States (global coverage)

---

## What area it covers
- **Coverage:** Global ocean and coastal floodplain
- **Domain details:** Single global unstructured mesh, no open boundaries. Live-verified station coverage on the 2026-07-23 00Z run spans 69.0°S to 82.5°N and the full longitude range.
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
- **Forecast length:** 180 hours, preceded by a **6-hour nowcast** (186-hour total span per cycle). Live-verified on the 2026-07-23 00Z run: station time series run 2026-07-22 18:06 UTC through 2026-07-30 12:00 UTC.
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** varies by product —
  - GRIB2 regional grids: **hourly** (f000–f180, 181 records)
  - Native-mesh field NetCDF: **hourly**
  - Station NetCDF: **6-minute** (1,860 records, live-verified)
  - SHEF station bulletins: **30-minute**
- **Computational timestep:** 6 s (implicit)
- **Operational footprint:** ~3,968 cores on WCOSS2 (`source` attribute live-verified as `Dogwood/Cactus`)

---

## Grid and bathymetry
- **Grid type:** Unstructured triangular mesh (`grid_type = Triangular`, `Conventions = UGRID-0.9.0`)
- **Horizontal resolution:** see *What area it covers* above
- **Mesh size:** 12,785,004 nodes / 24,875,336 triangular elements (live-verified from `stofs_2d_glo.t00z.fields.cwl.maxele.nc`, matching the official README exactly)
- **Mesh generator:** **OceanMesh2D** (live-verified `agrid` global attribute)
- **Bathymetry source:** Not documented. The `depth` variable is present on the native mesh with `long_name = "distance below geoid"`, but no source dataset is named. (TBD)
- **Wetting and drying:** **Yes.** Live-verified from the embedded ADCIRC `fort.15` attributes: `nolifa = 2` (wetting/drying with finite amplitude terms) and `h0 = 0.1` m minimum water depth.
- **Other configuration** (live-verified from `fort.15` attributes carried in every output file): `nolibf = 1` (quadratic bottom friction), `cf = 0.0005` (bottom friction coefficient), `ncor = 1` (spatially variable Coriolis), `nolica = nolicat = 1` (advective terms active), `eslm = -0.2` (Smagorinsky-type horizontal eddy viscosity), `nwp = 7` (seven nodal attributes).

---

## Vertical datum and reference level (important)
- **Vertical datum:** Differs by product —
  - **Gridded output (GRIB2 and native NetCDF):** global mean sea level (**GMSL**)
  - **Station NetCDF:** local mean sea level (**LMSL**)
  - **SHEF station bulletins:** **MLLW**, and in **feet**, not metres
- **What the water level field is measured relative to:**
  - `cwl` = total water level (tide + surge) above the stated datum
  - `htp` = astronomical tidal prediction above the stated datum
  - `swl` = the isolated surge residual
- **Datum conversion offsets provided?** No. No GMSL↔LMSL↔MLLW conversion field or per-station table is distributed. Users needing a hydrographic datum must supply their own.
- **Mean sea level trend / SLR handling:** Not documented. (TBD)

> **Live-verified caution — misleading geoid metadata, again.** The official documentation states unambiguously that output is referenced to **MSL** (the bucket README labels every gridded product "(m, MSL)"). But the file metadata says geoid throughout: the station NetCDF `zeta` variable carries `standard_name = sea_surface_height_above_geoid` and `long_name = "water surface elevation above geoid"`, the native-mesh `zeta_max` carries `standard_name = maximum_sea_surface_height_above_geoid`, and the GRIB2 tidal field is literally named *"Ocean Surface Elevation Relative to Geoid"*. This is the same defect documented for [GDSPS](../canada/gdsps.md) and [RESPS](../../regional/canada/resps.md) — and here, unlike GDSPS, **no attribute anywhere in the file corrects the record.** Trust the documentation, not the CF `standard_name`.

---

## Tide handling
- **Are tides included?** Yes — tides are integrated explicitly inside the ADCIRC run, and `cwl` is a genuine total water level.
- **Tidal forcing source:** Astronomical tidal potential applied directly in the interior. Live-verified from `fort.15`: `ntif = 8` (eight tidal constituents in the tidal potential), `ntip = 2` (tidal potential **plus** self-attraction and loading), `nbfr = 0` (**no** boundary tidal forcing — the global domain has no open boundaries). The system also includes **internal tides**.
- **Separation of components:** All three components are distributed directly as separate products (`cwl`, `htp`, `swl`) — a more complete separation than most operational surge systems, which typically publish only two of the three.
- **Tide–surge interaction:** Modelled nonlinearly within the run.

> **Live-verified — the three products are exactly additive, so only two are independent.** On the 2026-07-23 00Z f012 GRIB2 files, `cwl − (htp + swl)` has a maximum absolute residual of **2 × 10⁻⁵ m** across the CONUS East, Hawaii and Puerto Rico grids — i.e. zero to the GRIB2 packing precision. Downloading all three triples your transfer volume for no additional information. Fetch `cwl` and `htp` and subtract, or `swl` and `htp` and add.

---

## Forcing and coupling
- **Meteorological forcing — wind:** 10 m winds from [GFS](../../../nwp_models/global/usa/gfs.md) (`nws = 10`, gridded meteorological file forcing)
- **Meteorological forcing — pressure:** GFS mean sea level pressure
- **Forcing frequency:** Hourly GFS forcing through +120 h, then 3-hourly from +120 h to +180 h (introduced in STOFS v2.0.0; TBD whether unchanged at v2.1)
- **Ice forcing:** GFS **sea ice**, used to modify wind drag transfer. This is unusual for a surge model and matters at high latitudes.
- **Wave contribution:** None documented. STOFS-2D-Global is not coupled to a wave model and no wave setup term is published. (TBD)
- **River discharge / freshwater forcing:** None. Inland hydrology coupling to the National Water Model is a feature of [STOFS-3D-Atlantic](../../regional/usa/stofs-3d-atlantic.md), not of the global 2-D component.
- **Ocean forcing / boundary conditions:** None — the domain is global and has no open ocean boundaries.
- **Run continuity:** Hot-started continuously (`ihot = 568`); the live-verified `rnday = 839.5` days is the cumulative run length, not a per-cycle value.

---

## Data assimilation
- **Assimilates water level observations:** **No.** There is no data assimilation cycle.
- **Station bias correction:** Yes, applied in post-processing since STOFS v2.0.0. At stations where NOS/CO-OPS observations exist, the forecast is corrected by the bias between observed water levels and the most recent five days of nowcast water levels. This affects **station output only** — the gridded fields are uncorrected.

> **Live-verified — the correction is small, sparse, and time-varying.** Comparing `points.cwl.nc` against the (undocumented) `points.cwl.noanomaly.nc` on the 2026-07-23 00Z run: **236 of 1,688 stations (14.0%)** carry a non-zero correction. Among those, the median mean offset is **+0.165 m**, spanning **−1.147 m to +0.543 m**. The offset is **not** a fixed datum shift at any of the 236 stations — median temporal standard deviation is 0.015 m, reaching 1.85 m at one site. Use `points.cwl.nc` for guidance and `points.cwl.noanomaly.nc` for raw model verification.

---

## What it provides
- **Combined water level** (`cwl`) — total, tide + surge
- **Harmonic tidal prediction** (`htp`) — astronomical tide only
- **Sub-tidal water level** (`swl`) — isolated storm surge
- **Maximum water level envelope** over the cycle (`fields.cwl.maxele.nc`)
- **Depth-averaged current velocity** — hourly fields (`fields.cwl.vel.nc`) and 6-minute station series (`points.cwl.vel.nc`)
- **Maximum current speed** and **maximum wind speed** envelopes (`fields.cwl.maxvel.nc`, `fields.cwl.maxwvel.nc`)

No inundation depth product is published separately; overland water level is carried on the floodplain nodes of the native mesh.

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS and anonymous S3)
- **License:** **U.S. Government work — public domain.** Distributed through NOAA Open Data Dissemination (NODD): open to the public and usable as desired. NOAA requests attribution for use or dissemination of unaltered data; it is not permissible to state or imply NOAA endorsement or affiliation, and modified data may not be presented as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** **Both.** Gridded (native unstructured mesh plus seven regridded regional GRIB2 domains) and station point time series at 1,688 verification sites.
- **Data formats:**
  - **NetCDF-4 / HDF5** — native-mesh field files (live-verified HDF5 magic bytes)
  - **NetCDF-3 classic** — station point files (live-verified `CDF\001` magic bytes; note this means no internal compression or chunking)
  - **GRIB2** — regional structured grids
  - **SHEF** — station bulletins (MLLW, feet)
- **Station list:** Embedded in the `station_name` variable of the point NetCDF files as fixed-width records combining NWS Handbook-5 ID, WMO header, NOS station ID, state, and site name — e.g. `PSBM1 SOUS41 8410140 ME Eastport`. No separate station metadata file is published.
- **DOI:** https://doi.org/10.25923/ng4h-4b85

### GRIB2 regional grids (all live-verified from the 2026-07-23 00Z f012 files)

| Region | Projection | Grid (Ni × Nj) | Spacing |
|---|---|---|---|
| `conus.east` | Lambert conformal | 2145 × 1377 | 2539.703 m |
| `conus.west` | Lambert conformal | 2145 × 1377 | 2539.703 m |
| `alaska` | Polar stereographic | 825 × 553 | 5953.125 m |
| `hawaii` | Mercator | 321 × 225 | 2500 m |
| `guam` | Mercator | 193 × 193 | 2500 m |
| `puertori` | Mercator | 339 × 225 | 1250 m |
| `northpacific` | Mercator | 1473 × 1073 | 10000 m |

Lambert parameters: LaD 25.0°N, LoV 265.0°E, Latin1 = Latin2 = 25.0°. Alaska polar stereographic: LaD 60.0°N, orientation 210.0°. North Pacific spans 25°S–60.6°N, 110°E–250.9°E.

### Official download locations

**NOMADS (operational, 24/7 supported):**
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/
ftp://ftp.ncep.noaa.gov/data/nccf/com/stofs/prod/
```
Note the path is `stofs/prod`, **not** `estofs/prod` — the directory was renamed with the ESTOFS→STOFS transition. Both components share this directory (`stofs_2d_glo.YYYYMMDD/` and `stofs_3d_atl.YYYYMMDD/`), and retention is approximately **two days**.

**AWS Open Data (NODD) — the long-term archive:**
```
s3://noaa-gestofs-pds/stofs_2d_glo.YYYYMMDD/
https://noaa-gestofs-pds.s3.amazonaws.com/index.html
aws s3 ls --no-sign-request s3://noaa-gestofs-pds/
```
- AWS region: `us-east-1`
- SNS new-object notifications: `arn:aws:sns:us-east-1:123901341784:NewGESTOFSObject`
- **Archive depth (live-verified 2026-07-23):** 1,293 daily directories under `stofs_2d_glo.` from **2023-01-08** to present, plus 744 legacy `estofs.YYYYMMDD/` directories covering **2020-12-30 to 2023-01-12**.

---

## Notes

> **Live-verified caution — the GRIB2 parameter names are actively wrong, and filtering by `shortName` will give you the wrong field.** Each hourly GRIB2 file contains exactly three messages. Decoded with eccodes 2.48.0 they appear as:
>
> | Message | Discipline/Category/Number | eccodes `shortName` | eccodes `name` | **What it actually is** |
> |---|---|---|---|---|
> | 1 | 10 / 3 / **250** | `unknown` | `unknown` | **Combined water level (`cwl`)** — the total |
> | 2 | 10 / 3 / **194** | `elevhtml` | Ocean Surface Elevation Relative to Geoid | **Harmonic tidal prediction (`htp`)** — tide only |
> | 3 | 10 / 3 / **193** | `etsrg` | Extra Tropical Storm Surge | **Sub-tidal water level (`swl`)** — the surge |
>
> The mapping was established by decoding the single-product whole-cycle files, each of which contains 181 messages of exactly one parameter: `guam.cwl.grib2` → 250, `guam.htp.grib2` → 194, `guam.swl.grib2` → 193. It is independently confirmed by the arithmetic: parameter 250 equals 194 + 193 to within 2 × 10⁻⁵ m.
>
> The practical trap: a user filtering for total water level would reasonably reach for `elevhtml` ("Ocean Surface Elevation Relative to Geoid") and receive **the astronomical tide with no surge in it at all**. The field they want is the one eccodes cannot name. Parameter 10/3/250 falls in the NCEP local-use range (192–254) and is absent from the eccodes tables at this version — apply the standard fallback of reading `discipline`, `parameterCategory` and `parameterNumber` directly and selecting on `parameterNumber == 250`, or simply use the per-product `cwl`/`htp`/`swl` files whose filenames are unambiguous.

- **No bitmap; land is the literal value 9999.0.** GRIB2 messages carry `bitmapPresent = 0` and `missingValue = 9999`. Land and dry points are encoded as an in-band 9999.0, not as a masked or NaN value. Readers that do not explicitly filter will silently average 9999 into their statistics. Live-verified valid fractions on the 00Z f012 files: 21.3% of the CONUS East grid, 96.2% of Hawaii, 92.2% of Puerto Rico.
- **`conus.east` and `conus.west` are the same grid.** Both are the 2145 × 1377 / 2.5 km CONUS Lambert grid with identical projection parameters and identical first-grid-point coordinates. They differ only in which portion of the domain carries valid data (file sizes 2.7 MB vs 1.3 MB for the same forecast hour). They are not two separate regional domains.
- **Station NetCDF fill values are inconsistent and automatic masking fails.** `zeta` declares `_FillValue = -99999.0` and the global attribute `dry_Value = -99999.0`, but the file actually contains **four distinct sentinel values**: −99999.0, −99998.9, −99998.8 and −99998.7. On the 2026-07-23 00Z run, 498 of 3,626 sentinel elements do not equal the declared `_FillValue`, so `netCDF4`'s automatic masking leaves them in the array as ordinary numbers near −99999. Mask on `zeta < -1000`, not on equality with `_FillValue`. This is the same class of defect documented for [RESPS](../../regional/canada/resps.md).
- **Time units carry a trailing Fortran comment.** The `time` variable's units string is `"seconds since 2024-04-04 12:00:00        ! NCDASE - BASE_DAT"` — a truncated Fortran namelist comment appended to an otherwise valid udunits string. `cftime` tolerates it and parses correctly; stricter udunits parsers may not. Note also that the epoch is a **fixed cold-start date, not the run start** — it does not change from cycle to cycle.
- **Conflicting convention attributes.** Files carry both `Conventions = UGRID-0.9.0` and a lowercase `convention = "CF-1.0                     ! NCCONV - CONVENTIONS"`. Most of the global attribute block is raw ADCIRC `fort.15` text with embedded Fortran comments and fixed-width padding; parse it defensively.
- **Three different version strings in one file.** `version = noaa.stofs.2d.glo.v2.1.0r1.v55.12`, `title = STOFS_2D_GLOBAL.V2.1.0`, `runid = STOFS 2D GLOBAL v5.6.5`, while the governing Service Change Notice is for STOFS **v2.1.4**. The umbrella STOFS release number, the 2-D component number, and the internal run identifier are all distinct. Record the SCN version externally rather than relying on the in-file stamp.
- **`generatingProcessIdentifier` is inconsistent across grids of the same run.** Live-verified on the 00Z cycle: `conus.east` and `puertori` report 14, `conus.west`, `alaska` and `hawaii` report 17, and `guam` and `northpacific` report 20 — three different values from one model in one cycle. Do not use this key to identify the model.
- **`points.cwl.noanomaly.nc` is undocumented.** It is present in every cycle but absent from the official bucket README file table. See *Data assimilation* above for what it contains.
- **Volume warning.** A single cycle is approximately **63 GB**, of which 59 GB is native-mesh NetCDF (`fields.cwl.vel.nc` alone is 24.5 GB, and each of `fields.{cwl,htp,swl}.nc` is ~11 GB) and 4 GB is GRIB2. The station files total ~142 MB. Most users should stay on the regional GRIB2 or the point files.
- **One station is a persistent outlier.** On the 2026-07-23 00Z run, station `UJ816 SOUS00 SA816` (35.44°E, 46.22°N, inland near the Sea of Azov) peaks at 137.9 m, against a median peak across all stations of 0.60 m and only that one station exceeding 10 m. Station `FRDP4 SOUS41 9753216 PR Fajardo` returns sentinel values for the entire forecast. Apply sanity bounds before using the station set wholesale.
- **Relationship to other systems:**
  - Driving NWP: [GFS](../../../nwp_models/global/usa/gfs.md) supplies 10 m wind, MSLP and sea ice.
  - Sibling component: [STOFS-3D-Atlantic](../../regional/usa/stofs-3d-atlantic.md) — same STOFS umbrella and version numbering, but a different model core (SCHISM), a regional domain, 3-D baroclinic physics, and NWM inland hydrology coupling. The two share the NOMADS `stofs/prod` directory but use separate S3 buckets.
  - Comparable global deterministic surge system: [GDSPS](../canada/gdsps.md) (ECCC). GDSPS is a light-baroclinic NEMO configuration on a structured 1/12° grid with no overland domain; STOFS-2D-Global is a true 2D barotropic ADCIRC model on an unstructured mesh with an active floodplain. They are not equivalent products despite both being global.
  - Predecessor: ESTOFS, whose three regional domains (Atlantic, Pacific, Micronesia) were replaced by this single global system in late 2020. The `etsrg` GRIB2 parameter label is a survival from that era.

---

## Upcoming changes

### STOFS v3.1 — effective 11 August 2026
**SCN 26-51 (Updated)** announces an upgrade of the Surge and Tide Operational Forecast System to version 3.1. The notice was first issued 3 June 2026 with an effective date of 7 July 2026 and subsequently revised to **11 August 2026**.

As of the 2026-07-23 00Z cycle the upgrade had **not** taken effect — output files still stamp `STOFS_2D_GLOBAL.V2.1.0`. Everything in this entry describes the v2.1 configuration.

The v3.1 upgrade is expected to cover the STOFS-2D-Global and STOFS-3D-Atlantic components together and to introduce a third component, **STOFS-3D-Pacific**, which is currently running daily in a parallel path (`s3://noaa-nos-stofs3d-pds/STOFS-3D-Pac/para1_pro/`). Specific changes to STOFS-2D-Global under v3.1 are TBD pending review of the SCN.

---

## Recent version history

| Version | Date | Notes |
|---|---|---|
| STOFS v2.1 (v2.1.4) | 2024 | Current operational release (SCN 23-124). In-file component stamp `STOFS_2D_GLOBAL.V2.1.0`, `runid` v5.6.5. |
| STOFS v2.0.0 | Dec 2023 / early 2024 | Station bias correction against NOS/CO-OPS observations introduced; GFS forcing improved to hourly through +120 h, 3-hourly to +180 h. (Exact implementation date TBD.) |
| STOFS v1.1.1 | 10 Jan 2023 | PNS 23-01. STOFS-3D-Atlantic S3 bucket established; `stofs_2d_glo.YYYYMMDD` prefix begins 2023-01-08 on S3. |
| STOFS v1.0.1 | Dec 2022 | ESTOFS renamed to STOFS; STOFS-3D-Atlantic component added; improved resolution for U.S. East Coast, U.S. West Coast, Alaska and Iceland. |
| ESTOFS (global) | late 2020 | Global 2-D system replaced the three regional ESTOFS domains (Atlantic, Pacific, Micronesia). S3 `estofs.YYYYMMDD` prefix begins 2020-12-30. |

Earlier upgrades occurred in July 2021 and January 2023. (Full pre-2023 history TBD.)

---

## Official documentation
- AWS Open Data Registry entry: https://registry.opendata.aws/noaa-gestofs/
- Bucket README (authoritative file inventory): https://noaa-gestofs-pds.s3.amazonaws.com/README.html
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/
- NOS Office of Coast Survey storm surge modeling overview: https://nauticalcharts.noaa.gov/learn/storm-surge-modeling.html
- ESTOFS/STOFS product pages: https://polar.ncep.noaa.gov/estofs/ and https://polar.ncep.noaa.gov/stofs/
- SCN 26-51 (Updated) — STOFS v3.1, effective 11 August 2026: https://www.weather.gov/media/notification/pdf_2026/scn26-51_STOFS.v3.1.pdf
- SCN 23-124 (aaf) — STOFS v2.1.4: https://www.weather.gov/media/notification/pdf_2023_24/scn23-124_stofs_upgrade_v2.1.4_aaf.pdf
- PNS 23-43 — STOFS v2.0.0 proposal: https://www.weather.gov/media/notification/pdf_2023_24/pns23-43_stofs_2.0.0_upgrade.pdf
- PNS 23-01 — STOFS v1.1.1 known issues: https://www.weather.gov/media/notification/pdf_2023_24/pns23-01_stofs_v1.1.1.pdf
- PNS 22-37 — ESTOFS→STOFS transition: https://www.weather.gov/media/notification/pdf2/pns22-37_stofs_dec_2022_upgrade_aaa.pdf
- ADCIRC model documentation: http://www.adcirc.org
- Citation DOI: https://doi.org/10.25923/ng4h-4b85
