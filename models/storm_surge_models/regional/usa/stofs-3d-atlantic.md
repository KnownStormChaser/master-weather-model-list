# STOFS-3D-Atlantic (Surge and Tide Operational Forecast System, 3-D Atlantic component)

## What this model is
**STOFS-3D-Atlantic** is NOAA's operational three-dimensional coastal ocean and total water level forecast system for the U.S. East Coast, Gulf of America and Puerto Rico. It runs once daily at 12Z, producing a 24-hour nowcast followed by a 96-hour forecast.

Unlike the depth-averaged [STOFS-2D-Global](../../global/usa/stofs-2d-global.md), STOFS-3D-Atlantic resolves the vertical structure of the water column and publishes temperature, salinity and three-dimensional currents alongside water level. The hydrodynamic core is **SCHISM** (Semi-implicit Cross-scale Hydroscience Integrated System Model), run on an unstructured mixed triangular/quadrilateral mesh of 2,926,236 nodes with 49 vertical layers.

Its defining capability is **one-way coupling to the National Water Model (NWM)**, which lets it simulate rainfall-driven overland flow, river flow and storm surge simultaneously — the compound-flooding problem that a coastal-only surge model cannot address.

STOFS-3D-Atlantic was introduced in December 2022 as the first 3-D component of STOFS, at the same time the system was renamed from ESTOFS. A second 3-D component, **STOFS-3D-Pacific**, is currently pre-operational and scheduled to become operational with STOFS v3.1 on 11 August 2026; it is not covered by this entry.

---

## Who runs it
- **Organization:** NOAA / National Ocean Service (NOS) / Office of Coast Survey / Coast Survey Development Laboratory (CSDL), with NOAA/NWS/NCEP Central Operations (NCO) running the operational suite
- **Model core developer:** Virginia Institute of Marine Science (VIMS) — SCHISM
- **Country / region:** United States (U.S. East Coast, Gulf of America, Puerto Rico)

---

## What area it covers
- **Coverage:** U.S. East Coast, Gulf of America and Puerto Rico
- **Mesh resolution:** 1.5–2 km near the shoreline, 600 m on the floodplain, 8 m for watershed rivers, 2–10 m for levees
- **Inundation coverage:** **Yes.** Along the U.S. coasts the land boundary follows the 10 m contour above xGEOID20B, covering the coastal transitional zone most exposed to combined coastal and inland flooding. Wetting and drying is active.
- **Inland extent:** The mesh runs well up into watershed rivers, which is why a large fraction of nodes carry water surface elevations far above any plausible coastal surge — see the caution under *What it provides*.

---

## Basic details
- **Model type:** Deterministic 3-D baroclinic coastal ocean / total water level model
- **Core hydrodynamic model:** SCHISM (`source = SCHISM model output version v10`, live-verified)
- **Dimensionality:** **3D baroclinic**, terrain-following vertical coordinate, **49 layers**
- **Forecast length:** 24-hour nowcast plus **96-hour forecast** (120-hour total span). Live-verified on the 2026-07-22 12Z run: station series run 2026-07-21 12:06 UTC through 2026-07-26 12:00 UTC (1,200 six-minute records).
- **Update frequency / cycles:** **1× daily, 12Z only.** Live-verified — no other cycle appears in any day directory. This is a major difference from [STOFS-2D-Global](../../global/usa/stofs-2d-global.md), which runs four cycles daily.
- **Temporal output resolution:**
  - GRIB2 water level: **hourly** (`f000`–`f096`)
  - Native-mesh field NetCDF: **hourly**, bundled in 12-hour chunks
  - Station NetCDF: **6-minute**
  - Station vertical profiles: **hourly**
  - SHEF bulletins: **30-minute**
- **Nowcast/forecast file convention:** `n###` files cover nowcast hours (`n001_012`, `n013_024`), `f###` cover forecast hours (`f001_012` through `f085_096`).
- **Software package version:** `stofs.v2.1.18` (live-verified from processing paths embedded in the output metadata)

---

## Grid and bathymetry
- **Grid type:** Unstructured **mixed triangular and quadrilateral** mesh (`nMaxSCHISM_hgrid_face_nodes = 4`), UGRID-style topology with explicit node, face and edge arrays
- **Mesh size (live-verified):** **2,926,236 nodes**, **5,654,157 elements**, **8,580,540 edges**
- **Vertical coordinate:** terrain-following, 49 layers. `zCoordinates` is published as a full time-varying 3-D field of shape `(time, 2926236, 49)`, so layer depths must be read from the data rather than reconstructed from a static sigma table.
- **Bathymetry source:** Not documented (TBD)
- **Wetting and drying:** **Yes** — `dryFlagNode`, `dryFlagElement` and `dryFlagSide` are published per timestep, alongside a `minimum_depth` variable
- **Land masking:** Applied in post-processing from a static `idmask` fix file; masked nodes are set to −99999.0

> **Live-verified — the element table differs between products on the same node set.** `fields.out2d_*` publishes `SCHISM_hgrid_face_nodes` with shape `(5654157, 4)` — the full mixed triangular/quadrilateral mesh. `field2d_*` publishes the same array with shape `(5548650, 3)` — triangles only, and 105,507 fewer faces. Node count is identical at 2,926,236 in both. Do not assume a single connectivity array applies across products; read it from the file you are actually using. (Mechanism TBD.)

---

## Vertical datum and reference level

**Three different datums appear across four products of the same model.** This is the single most consequential thing to get right when using STOFS-3D-Atlantic.

| Product | Vertical datum | Units |
|---|---|---|
| Native-mesh field NetCDF (`fields.out2d_*`, `field2d_*`) | **xGEOID20B** | m |
| Maximum elevation (`fields.cwl.maxele.nc`) | **xGEOID20B** | m |
| Station NetCDF (`points.cwl.nc`) | **NAVD88** | m |
| GRIB2 regional grids | xGEOID20B (inherited; **not stated in the GRIB header**) | m |
| SHEF bulletins | **MLLW** | **feet** |

Live-verified `long_name` strings on the water level variables:
- Gridded: `water surface elevation above xgeoid20b`
- Station NetCDF: `water surface elevation above navd88`, with `standard_name = sea_surface_height_above_navd88`

- **Datum conversion offsets provided?** No. No conversion grid or per-station offset table is published.
- **Mean sea level trend / SLR handling:** Not documented (TBD)

> **Caution — the gridded and station products are on different datums.** This is easy to miss because both are labelled "water surface elevation" in metres and both come from the same model cycle. Comparing a station time series against the gridded field at the same location without applying a NAVD88↔xGEOID20B conversion will produce a systematic offset that varies geographically.

> **Caution — the CF `standard_name` is invented and will not validate.** `sea_surface_height_above_navd88` does not exist in the CF standard name table. Tools that resolve datum from `standard_name` against the CF vocabulary will fail or silently fall through. Read the `long_name`, or hard-code the datum per product from the table above.

> **Caution — the GRIB2 files carry no datum information at all.** The regional GRIB2 grids are the most convenient product for most users and the easiest to misuse: nothing in the message identifies the reference surface. See the *Notes* section for the related problem that the GRIB2 parameter is shared with the other STOFS components.

---

## Tide handling
- **Are tides included?** Yes — tides are forced at the open ocean boundaries and integrated within SCHISM. The water level fields represent combined tidal and subtidal elevation.
- **Separation of components:** **No.** [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) publishes combined water level, harmonic tidal prediction and sub-tidal residual as three separate products; STOFS-3D-Atlantic publishes **only** the combined water level. There is no `htp` or `swl` product and no way to isolate the surge from the distributed output. Users needing a surge residual must supply their own tidal prediction.
- **Tidal constituents / boundary forcing source:** TBD

---

## Forcing and coupling
- **Meteorological forcing:** NWP surface fields. `windSpeedX`, `windSpeedY`, `windStressX`, `windStressY`, `precipitationRate` and `evaporationRate` are carried in the 2-D output. Specific driving model(s) and forcing frequency TBD.
- **River discharge / freshwater forcing:** **Yes — one-way coupling to the National Water Model (NWM).** This lets the system represent rainfall-induced overland flow, river flow and storm surge in a single simulation, and is the principal reason to use STOFS-3D-Atlantic over the 2-D global system for compound coastal-inland flooding.
- **Ocean boundary conditions:** Open lateral boundaries with tidal and subtidal forcing; source TBD
- **Wave contribution:** None documented (TBD)
- **Ice forcing:** Not documented (TBD)

---

## Data assimilation
- **Assimilates water level observations:** Not documented (TBD)
- **Station bias correction:** No `noanomaly` variant is published, unlike [STOFS-2D-Global](../../global/usa/stofs-2d-global.md), where the presence of that variant reveals a station-level bias correction. Whether any correction is applied here is TBD.

---

## What it provides

**Water level**
- Combined water level (tide + surge) — hourly on the native mesh (`fields.out2d_*` `elevation`, `field2d_*` `elev`) and on two regional GRIB2 grids
- Maximum water level envelope (`fields.cwl.maxele.nc`)
- Water level "disturbance" as vector GeoPackage polygons

**Ocean state, full 3-D fields** (S3 only — see *Data availability*)
- `fields.temperature_*`, `fields.salinity_*`
- `fields.horizontalVelX_*`, `fields.horizontalVelY_*`
- `fields.zCoordinates_*` — time-varying vertical layer positions

**Ocean state, 2-D slices** (`field2d_*`, on NOMADS and S3)
- `temp_surface`, `temp_bottom`, `salt_surface`, `salt_bottom`
- `uvel_surface`, `uvel_bottom`, `uvel4.5` and `vvel_surface`, `vvel_bottom`, `vvel4.5` — velocity at the surface, at the bottom, and at 4.5 m below the free surface

**2-D diagnostics** (`fields.out2d_*`)
- `depthAverageVelX`, `depthAverageVelY`
- `windSpeedX/Y`, `windStressX/Y`, `precipitationRate`, `evaporationRate`
- `dryFlagNode`, `dryFlagElement`, `dryFlagSide`, `bottom_index_node`, `minimum_depth`, `idmask`

**Station products**
- 6-minute surface time series at 108 sites: water level, u, v, salinity, temperature
- Hourly vertical profiles at the same 108 sites: temperature, salinity, u, v, `zCoordinates` through the water column, plus 10 m wind (`uwind_speed`, `vwind_speed`)
- 30-minute SHEF water level bulletins at 105 sites

> **Live-verified — `fields.cwl.maxele.nc` covers the forecast only, and its `time` dimension does not index the data.** `zeta_max` is one-dimensional, shape `(2926236,)`, while `time` has length 2 with values `[90000, 432000]` seconds since 2026-07-21 12:00 — that is, 2026-07-22 13:00 to 2026-07-26 12:00 UTC. The pair encodes the **window over which the maximum was taken** (forecast hours f001–f096), not two data slices. The 24-hour nowcast is excluded from the envelope.

> **Live-verified — sanity-bound the maximum-elevation field before use.** On the 2026-07-22 12Z run, `zeta_max` spans **−113.45 m to +1025.50 m** across 2,728,959 valid nodes (93.26% of the mesh). Most of that range is legitimate: 85.6% of valid nodes fall in −3 to +5 m, and the 14.4% in the 5–20 m band largely reflects inland river nodes whose surface genuinely sits well above the geoid. But **420 nodes exceed 20 m and 84 fall below −10 m**, with the extremes clustered in tight spatial pairs along the Maine and Massachusetts coasts (for example 1025.50 m and 834.21 m at adjacent nodes near 70.56°W, 43.32°N) — a signature of isolated numerical artifacts rather than physical signal. Median is 0.738 m, 99th percentile 10.71 m.

---

## Data availability
- **Is the data free?** Yes (no registration, no API key, direct HTTPS and anonymous S3)
- **License:** **U.S. Government work — public domain.** Distributed through NOAA Open Data Dissemination (NODD). NOAA requests attribution for use or dissemination of unaltered data; it is not permissible to state or imply NOAA endorsement, and modified data may not be presented as original unaltered NOAA data.
- **Is the data downloadable?** Yes
- **Output geometry:** **Both.** Gridded (native unstructured mesh plus two regridded regional GRIB2 domains) and station point time series and vertical profiles.
- **Data formats:** **NetCDF-4 / HDF5** (fields, stations, profiles), **GRIB2** (regional grids), **SHEF** (station bulletins), **GeoPackage** (`.gpkg` disturbance polygons)
- **Station list:** Embedded in the `station_name` variable of the point NetCDF files as fixed-width 50-character records combining NWS Handbook-5 ID, WMO header, NOS station ID, state and site name — e.g. `PSBM1 SOUS41 8410140 ME Eastport`. Records are **null-padded**, not space-padded; strip `\x00` when parsing. No separate station metadata file is published.

### GRIB2 regional grids (live-verified from the 2026-07-22 12Z f012 files)

| Region | Projection | Grid (Ni × Nj) | Spacing |
|---|---|---|---|
| `conus.east` | Lambert conformal | 2145 × 1377 | 2539.703 m |
| `puertori` | Mercator | 339 × 225 | 1250 m |

Lambert parameters: LaD 25.0°N, LoV 265.0°E, Latin1 = Latin2 = 25.0°. These are the same NDFD-family grid definitions used by [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) — identical projection parameters and dimensions.

Each hourly file contains **exactly one message**. Per-cycle whole-run files are also published as `conus.east.cwl.grib2` (115 MB) and `puertori.cwl.grib2` (9.3 MB).

### Official download locations

**NOMADS (operational, 24/7 supported):**
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/stofs_3d_atl.YYYYMMDD/
ftp://ftp.ncep.noaa.gov/data/nccf/com/stofs/prod/stofs_3d_atl.YYYYMMDD/
```
The `stofs/prod` directory holds all operational STOFS components side by side (`stofs_2d_glo.YYYYMMDD/` and `stofs_3d_atl.YYYYMMDD/`), with roughly two days of retention.

**AWS Open Data (NODD) — the long-term archive:**
```
s3://noaa-nos-stofs3d-pds/STOFS-3D-Atl/stofs_3d_atl.YYYYMMDD/
aws s3 ls --no-sign-request s3://noaa-nos-stofs3d-pds/STOFS-3D-Atl/
```
- AWS region: `us-east-1`
- **Archive depth (live-verified 2026-07-23):** 1,280 daily directories from **2023-01-12** to present. Nine days are missing from the 1,289-day span — the archive is near-complete but not gap-free, so do not assume continuity when building time series.

> **S3 carries substantially more than NOMADS.** NOMADS publishes the GRIB2 grids, `field2d_*`, `fields.out2d_*`, `fields.cwl.maxele.nc`, the station and profile files, and the SHEF bulletins. The full 3-D state variables — `fields.temperature_*`, `fields.salinity_*`, `fields.horizontalVelX/Y_*` and `fields.zCoordinates_*` — appear **only on S3**, as do the `.gpkg` disturbance files and the raw `schout_adcirc_*.nc` output. If you need the 3-D ocean state, NOMADS is not sufficient.

---

## Notes

> **Live-verified caution — the GRIB2 `shortName` is `unknown`, and every STOFS component shares the same parameter.** Each hourly GRIB2 file contains exactly one message, encoded as discipline 10 / category 3 / **parameter 250**, which eccodes 2.48.0 cannot resolve (`shortName = unknown`, `name = unknown`). Parameter 10/3/250 falls in the NCEP local-use range 192–254. Apply the standard fallback: read `discipline`, `parameterCategory` and `parameterNumber` directly and select on `parameterNumber == 250`.
>
> The compounding problem is that **[STOFS-2D-Global](../../global/usa/stofs-2d-global.md) uses the identical parameter on the identical `conus.east` and `puertori` grid definitions.** Messages from the two systems are not distinguishable from content alone. `generatingProcessIdentifier` is 21 for STOFS-3D-Atlantic against 14/17/20 for STOFS-2D-Global, but that key is inconsistent even within a single STOFS-2D-Global cycle, so it is not a safe discriminator. **Track provenance by filename.** Note also that the two systems are on different datums (xGEOID20B here, GMSL for 2-D Global), so a mix-up is not merely a labelling error.

- **No bitmap; land is the literal value 9999.0.** GRIB2 messages carry `bitmapPresent = 0` and `missingValue = 9999`, with land and dry points encoded in-band. Filter explicitly or 9999 will enter your statistics. NetCDF products use −99999.0 instead, applied consistently — unlike the four-way inconsistent sentinel values documented for [STOFS-2D-Global](../../global/usa/stofs-2d-global.md).
- **`points.cwl.nc` and `points.cwl.temp.salt.vel.nc` are the same file under two names.** Live-verified on the 2026-07-22 12Z run: identical dimensions (1,200 × 108), identical variable lists, and all five data variables (`zeta`, `u`, `v`, `salinity`, `temperature`) bit-identical. Download one, not both. Note that NOMADS publishes only `points.cwl.temp.salt.vel.nc`, while S3 publishes both — so the same data reaches users under different names depending on the source.
- **`depth` units are inconsistent between products.** In `fields.out2d_*` and `fields.cwl.maxele.nc`, bathymetry carries `units = mm` with `standard_name = depth below geoid`. In `field2d_*`, the same quantity carries `units = m` with `long_name = bathymetry`. SCHISM depth is conventionally metres; treat the `mm` label as a post-processing error and verify against a known sounding before using bathymetry from the out2d family.
- **The `depth` long_name in the out2d family is a duplicated string.** `distance  below geoiddistance  below geoid` — a concatenation bug. Cosmetic, but a useful signal of how much metadata is applied after the fact.
- **Metadata is applied post-hoc by NCO, not written by the model.** The `history` attribute in every field file is a long chain of `ncks`, `ncatted`, `ncap2`, `ncra` and `ncbo` calls. Datum labels, fill values and even dimension definitions are attached in post-processing, applied independently per product. This is the mechanism behind the datum and units inconsistencies above, and it means a fix to one product does not propagate to the others.
- **Time epochs are cycle-relative, and differ from STOFS-2D-Global.** Files use `seconds since YYYY-MM-DD 12:00:00 UTC` anchored to **the day before the cycle date** — the start of the 24-hour nowcast. STOFS-2D-Global instead uses a fixed cold-start epoch that never changes. Do not assume a shared time convention across the STOFS family.
- **Disturbance products are vector GeoPackage, S3 only.** `stofs_3d_atl.t12z.disturbance.f###.gpkg` and `.n###.gpkg` are published hourly at roughly 53–58 MB each, totalling around 6.8 GB per cycle. These are derived polygon products rather than raw gridded output; the NetCDF and GRIB2 are the primary data.
- **Volume warning.** A full cycle runs to tens of gigabytes. Individual 3-D chunks are large: `field2d_*` 3.07 GB each (10 files), `fields.out2d_*` ~1.4 GB each (10 files), and `temperature`, `salinity`, `horizontalVelX/Y`, `zCoordinates` roughly 1.05–1.22 GB each across 10 chunks apiece. The archive also carries five rolling `schout_adcirc_YYYYMMDD.nc` files at 1.81 GB each. The regional GRIB2 (1.2 MB per hour for CONUS East) and the station files (5.3 MB) are the practical products for most users.
- **Station counts differ between products.** 108 sites in the NetCDF station and profile files, but only 105 in the SHEF bulletins. The SHEF set is a curated subset, not a complete rendering.
- **Relationship to other systems:**
  - Sibling component: [STOFS-2D-Global](../../global/usa/stofs-2d-global.md) — same STOFS umbrella and version numbering, but a different model core (ADCIRC), global coverage, 2-D barotropic physics, four cycles daily, and a three-way tide/surge product split that this system does not offer.
  - Sibling component (pre-operational): STOFS-3D-Pacific — same SCHISM core and the same S3 bucket, covering the U.S. West Coast, Alaska, Hawaii, Guam and the North Pacific. Scheduled to become operational with STOFS v3.1 on 11 August 2026; see [Upcoming changes](#upcoming-changes).
  - Driving hydrology: National Water Model (NOAA/NWS Office of Water Prediction) — not currently catalogued.
  - Comparable regional surge system: [RESPS](../canada/resps.md) (ECCC) — but RESPS is 2-D barotropic with no overland domain and no hydrological coupling, so the two are not equivalent products.
  - SCHISM model documentation: http://ccrm.vims.edu/schismweb/

---

## Upcoming changes

### STOFS v3.1 — effective 11 August 2026
**SCN 26-51 (Updated)** announces the upgrade of the Surge and Tide Operational Forecast System to version 3.1. The notice was first issued 3 June 2026 with an effective date of 7 July 2026 and subsequently revised to **11 August 2026**.

As of the 2026-07-22 12Z cycle the upgrade had **not** taken effect — production output is still built from the `stofs.v2.1.18` package. Everything in this entry describes that configuration.

Specific changes to STOFS-3D-Atlantic under v3.1 are TBD pending review of the SCN. A parallel pre-implementation feed is available for comparison at:
```
https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/para/stofs_3d_atl.YYYYMMDD/
```
The same upgrade brings STOFS-3D-Pacific to operational status and modifies STOFS-2D-Global — see that entry's *Upcoming changes* section for live-verified differences there.

---

## Recent version history

| Version | Date | Notes |
|---|---|---|
| STOFS v3.1 | 11 Aug 2026 (scheduled) | SCN 26-51 (Updated). Changes to this component TBD. |
| STOFS v2.1.18 | 2026 | Package version live-verified in the 2026-07-22 12Z output. |
| STOFS v1.1.1 | 10 Jan 2023 | PNS 23-01. S3 bucket established; archive begins 2023-01-12. |
| STOFS v1.0.1 | Dec 2022 | ESTOFS renamed to STOFS; STOFS-3D-Atlantic introduced as the first 3-D component. |

Full version history TBD.

---

## Official documentation
- AWS Open Data Registry: https://registry.opendata.aws/noaa-nos-stofs3d/
- NOMADS production directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/stofs/prod/
- NOS Office of Coast Survey storm surge modeling overview: https://nauticalcharts.noaa.gov/learn/storm-surge-modeling.html
- STOFS product page: https://polar.ncep.noaa.gov/stofs/
- SCN 26-51 (Updated) — STOFS v3.1, effective 11 August 2026: https://www.weather.gov/media/notification/pdf_2026/scn26-51_STOFS.v3.1.pdf
- PNS 23-01 — STOFS v1.1.1 known issues: https://www.weather.gov/media/notification/pdf_2023_24/pns23-01_stofs_v1.1.1.pdf
- PNS 22-37 — ESTOFS→STOFS transition: https://www.weather.gov/media/notification/pdf2/pns22-37_stofs_dec_2022_upgrade_aaa.pdf
- SCHISM model documentation: http://ccrm.vims.edu/schismweb/
