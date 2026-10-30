# NWP-WRFDA (TMD Thailand)

## What this model is
NWP-WRFDA is the operational regional deterministic NWP system of the Thai Meteorological Department (TMD). It is a two-domain WRF-ARW configuration with its own 3D-Var data assimilation (WRFDA). It is used for short- to medium-range forecasting over Indochina and Thailand, with an emphasis on severe weather, tropical cyclones, heavy rain, flash floods and landslides.

---

## Who runs it
- **Organization:** Thai Meteorological Department (TMD / กรมอุตุนิยมวิทยา), Numerical Weather Prediction Sub-Division, Weather Forecast Division, Ministry of Digital Economy and Society
- **Country / region:** Thailand

---

## What area it covers
- **Coverage:** Two nested domains:
  - **Domain 1 (d01), "Indochina 9 km":** 92.71°E–112.75°E, 3.76°N–22.22°N; 242 × 230 grid points (verified from live files, run 2026102918)
  - **Domain 2 (d02), "Thailand 3 km":** 96.84°E–106.37°E, 5.22°N–20.78°N; 345 × 579 grid points (verified from live files, run 2026102918)
- **Map projection:** Mercator. Files carry 1-D `x`/`y` (or `lon`/`lat`) coordinates in degrees. Longitude spacing is constant (d01 ≈ 0.0831°, d02 ≈ 0.0277°). Latitude spacing varies with latitude (d01 0.0770°–0.0830°, d02 0.0259°–0.0276°), as expected for Mercator. The data variables declare `grid_mapping: mercator`, but no `mercator` grid-mapping variable exists in the files.
- **Domain details:** The ventilation-rate (VR-AVG) files use **smaller grids** than the precipitation/runoff files: d01 117 × 223 (96.03°E–105.68°E, 4.01°N–21.91°N) and d02 322 × 578. These are the same native spacing, cropped around Thailand.

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** WRF-ARW with WRFDA (version not stated on the TMD site or in the file attributes; TBD)
- **Dynamical formulation:** Non-hydrostatic (`non_hydrostatic = .true.` per TMD)
- **Convection-allowing:** d02 is 3 km. However, TMD lists `cu_physics = 3` (Grell 3D ensemble) without saying which domains use it, so whether d02 runs with convection parameterization off is TBD.
- **Horizontal resolution:** d01 ~9 km; d02 ~3 km
- **Vertical levels:** TBD
- **Model top:** TBD
- **Forecast length:** d01 240 h (10 days); d02 72 h. Verified for the 06, 12 and 18 UTC cycles of 2026-10-29. All checked cycles have the same length, including those tagged "ECMWF-IFS Included".
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC). Every cycle from 2026-10-01 00 UTC to 2026-10-29 18 UTC is present (116/116).
- **Temporal output resolution:** d01 3-hourly; d02 hourly (precipitation/runoff). Ventilation rate: one value per day at 07 UTC.

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** 3D-Var (WRFDA), assimilating Doppler radar, Himawari-9 satellite radiances, surface stations and radiosondes (per TMD). The cycling cadence is TBD.

---

## Initial and boundary conditions
- **Initial conditions:** WRFDA 3D-Var analysis (background source TBD)
- **Boundary conditions:** From a global model through a 5-grid-point specified lateral boundary zone (`spec_bdy_width = 5`). TMD does not name the global driver. The download page tags the 00 and 12 UTC cycles "[ECMWF-IFS Included]", and the 06/18 UTC cycles carry no tag. This suggests IFS forcing at 00/12 UTC and some other driver (GFS?) at 06/18 UTC. That reading is unconfirmed (TBD). The tagged cycles have the same file set and forecast length as the untagged ones.

---

## What it provides
Distribution is split between two formats. **Only a few products are gridded NetCDF**, and the rest are CSV.

**NetCDF (6 files per cycle):**
| File | Domain | Steps | Variable | Notes |
|---|---|---|---|---|
| `{RUN}.prec1hr.d02.nc` | d02 | 73 × hourly, 0–72 h | `Total_precipitation` (labelled mm/hr) | |
| `{RUN}.prec3hr.d01.nc` | d01 | 81 × 3-hourly, 0–240 h | `Total_precipitation` (labelled mm/hr) | Values reach ~268, which suggests 3-h totals labelled mm/hr (TBD) |
| `{RUN}.sfroff1hr.d02.nc` | d02 | 73 × hourly | `surface_runoff` (mm) | **Identical to prec1hr; see Notes** |
| `{RUN}.sfroff3hr.d01.nc` | d01 | 81 × 3-hourly | `surface_runoff` (mm) | **Identical to prec3hr; see Notes** |
| `{RUN}.VR-AVG.d01.nc` | d01 (cropped) | 10 daily | `VRAVG` (m² s⁻¹) | Ventilation rate, mean of 06 and 09 UTC, stamped 07 UTC |
| `{RUN}.VR-AVG.d02.nc` | d02 (cropped) | 3 daily | `VRAVG` (m² s⁻¹) | as above |

**CSV (26 files per cycle):** 2 m temperature, 2 m RH, surface pressure, MSLP, 10 m U/V wind, 10 m wind speed and direction, downward long- and shortwave radiation, OLR (each for d01 and d02), plus 1-h precipitation (d02), 3-h precipitation (d01) and 24-h precipitation (d01, d02). The CSVs hold the full grid in wide layout: one row per grid point (`lat,lon`), one column per valid time (e.g. `2026-10-29_18:00Z`). d02 files are roughly 60–120 MB each.

---

## Data availability
- **Is the data free?** Yes (no registration or key required)
- **License:** **None stated.** The site footer reads "© 2026 Numerical Weather Prediction – Thai Meteorological Department (TMD) | All Right Reserved", and no terms of use are published. **Open access, not open licensing**; reuse terms are unresolved (TBD).
- **Is the data downloadable?** Yes, over HTTPS with no authentication, plus an unauthenticated JSON listing API
- **Data formats:** NetCDF and CSV. **TMD labels the NetCDF files "NetCDF-4", but every file checked is actually NetCDF-3 classic** (CDL format 1, uncompressed, unchunked). The global attribute reads `Convention: CF-1.X` (a placeholder, not a real CF version).
- **Archive / retention:** Every cycle from 2026-10-01 00 UTC onward is available. Runs before that return HTTP 404 and an empty API listing, even though the run selector still offers them back to 2026-09-01. Whether this is a fixed start or a rolling ~30-day window is TBD; re-check after 2026-11-01.
- **Official download location:**
  https://hpc.tmd.go.th/download

URL patterns (verified):
- NetCDF: `https://hpc.tmd.go.th/static/netcdf/{YYYYMMDDHH}/{YYYYMMDDHH}.{product}.{dNN}.nc`
- CSV: `https://hpc.tmd.go.th/static/csv/{YYYYMMDDHH}/{var}.{dNN}.{YYYYMMDDHH}.csv`
- File listing API: `https://hpc.tmd.go.th/api/download-files?init_time={YYYYMMDDHH}` returns JSON (`status`, `initial_time`, `count`, and `files[]` with `filename`, `description`, `domain`, `domain_code`, `format`, `size`, `url`). A run with no data returns `count: 0` and still reports `status: success`.

---

## Notes
- **Physics configuration** (as published on TMD's about page): WSM 3-class simple ice microphysics (`mp_physics = 3`); RRTM longwave (`ra_lw_physics = 1`); Dudhia shortwave (`ra_sw_physics = 1`, `radt = 60` min); Grell 3D ensemble cumulus (`cu_physics = 3`, `cudt = 60` min; which domains use it is TBD); YSU PBL and surface layer (`bl_pbl_physics = 1`, `sf_sfclay_physics = 1`); Unified Noah LSM with 4 soil layers and 21 land-use categories; positive-definite moisture and scalar advection (`moist_adv_opt = 1`, `scalar_adv_opt = 1`).
- **Surface runoff files are mis-produced.** For both domains and every cycle checked (2026102912, 2026102918), `sfroff1hr`/`sfroff3hr` are bit-for-bit identical to `prec1hr`/`prec3hr`. Their `History` attribute is copied from the precipitation script (`... -> Total_precipitation[unit:kg_deg-2]_0`). Treat them as precipitation duplicates, not runoff, until TMD fixes this.
- The download page's example URL uses run `2026081800`, which is outside the current archive and returns 404. The URL pattern is right, but the example itself no longer works.
- The page's example uses `http://`. HTTPS works and is preferred.
- The first time step (analysis time) of the precipitation files is all zeros, so the steps are period accumulations ending at the valid time.
- Time coordinate: `hours since 1998-01-01 00:00` (float32), with no `calendar` attribute on the precipitation files.
- Ventilation rate (VR-AVG) is an air-pollution dispersion product used in Thailand's haze and open-burning management. It does not fit neatly in NWP and is noted here rather than filed as an air-quality entry.
- Only precipitation and ventilation rate are distributed as gridded NetCDF. The core surface fields (T2m, wind, pressure, RH, radiation) are CSV-only. CSV is machine-readable full-grid data but is not a standard gridded format. Whether CSV-only variables count toward scope is a catalog decision.
- For another public Southeast Asia regional NWP domain in this repository, see [TJ-NWP](../../non_governmental/tj-nwp.md).

---

## Official documentation
- Download centre: https://hpc.tmd.go.th/download
- System description and physics settings (Thai): https://hpc.tmd.go.th/about
- Interactive map viewer: https://wxmap.tmd.go.th
- TMD main site: https://www.tmd.go.th
