# NorKyst v3 (ROMS NorKyst v3 Coastal Ocean Forecasting System)

## What this model is
**NorKyst v3** is MET Norway's operational high-resolution coastal ocean forecasting system for the Norwegian coast, built on **ROMS** (Regional Ocean Modeling System). It runs as an **ensemble** on an 800 m parent grid, with two **two-way nested 160 m child domains** covering Sulafjord and Oslofjord.

The system provides temperature, salinity, three-dimensional currents, vertical velocity, sea surface height, and vertical salt diffusivity on fixed depth levels, hourly. MET Norway describes its intended applications as oil-spill response, man-overboard drift, harmful algal bloom spread, and salmon lice dispersal — the diffusivity and vertical velocity fields are published specifically to support offline particle tracking.

NorKyst v3 also has a role inside the repository's Norwegian marine chain: **it supplies the surface currents that force [MyWaveWAM800m](../../../wave_models/regional/norway/mywavewam800m.md)**, MET Norway's coastal wave system.

MET Norway calls this "the Norkyst ROMS-EPS model," but **only four members are published** (see *Ensemble configuration*). The full ensemble is larger than the distributed subset.

*Live-verified against the operational THREDDS distribution on 2026-07-27 (00 UTC cycle), plus archive sampling back to 2024-01-01.*

---

## Who runs it
- **Production Unit:** Norwegian Meteorological Institute (MET Norway), Division for Ocean and Ice (`creator_email = fou-hi@met.no`)
- **Country:** Norway
- **Programme:** `project = "Ocean and Ice - Research to Operation (HI-R2O)"`
- **Grid provenance:** the ROMS grid file `norkyst_grd_v31.nc` carries `Institution = "Institute of Marine Research"` (Havforskningsinstituttet, IMR), `gridname = "norkyst_800m_v3"`, dated 2022-10-12. The grid definition originates with IMR; MET Norway runs the operational forecast.
- **Named contributors (file metadata):** Magne Simonsen (technical contact), Mateusz Matuszak (metadata author)
- **Operational status:** `operational_status = "Operational"`; `dataset_production_status = "In Work"` (the two disagree — see *Notes*)
- **Role in larger system:** supplies surface currents to [MyWaveWAM800m](../../../wave_models/regional/norway/mywavewam800m.md)

---

## What area it covers

### 800 m parent domain
- **Coverage:** the entire Norwegian coast, Norwegian Sea, North Sea, and southern Barents Sea
- **Domain bounds:** 54.295°N – 75.726°N, 4.615°W – 37.555°E
- **Grid dimensions:** 2747 (`X`) × 1148 (`Y`)
- **Grid extent:** ~2197.6 km × ~918.4 km

### 160 m child domains (two-way nested)
| Member | Domain | Grid (X × Y) | Longitude | Latitude |
|---|---|---|---|---|
| `m70` | **Sulafjord** | 552 × 777 | 4.715° – 7.629°E | 61.573° – 62.786°N |
| `m71` | **Oslofjord** | 417 × 377 | 9.878° – 11.395°E | 59.245° – 60.033°N |

### Projection — shared across all grids
- **Grid mapping:** polar stereographic (`projection_stere`)
- `straight_vertical_longitude_from_pole = 70.0`, `latitude_of_projection_origin = 90.0`, `standard_parallel = 60.0`
- `false_easting = 3369600.0`, `false_northing = 1844800.0`
- **WGS84 ellipsoid:** `semi_major_axis = 6378137.0`, `semi_minor_axis = 6356752.3142`
- proj4: `+proj=stere +lat_0=90 +lat_ts=60 +lon_0=70 +x_0=3369600 +y_0=1844800 +a=6378137 +b=6356752.3142 +units=m +no_defs +type=crs`

**The 160 m children share the parent's CRS and origin.** Parent `X` starts at 0 m with 800 m spacing; the Sulafjord child `X` starts at 673120 m with 160 m spacing. Child coordinates are absolute within the same projection, so parent and child grids overlay directly without reprojection — unlike MET Norway's wave models, where every domain has its own rotated pole.

**Resolution is exact, not nominal.** `X`/`Y` spacing is precisely 800 m and 160 m. This is a welcome contrast to the sibling wave products, where "4 km" means 4.45 km and "800 m" means 890 m.

- **Georeferencing:** 2-D `lat` / `lon` auxiliary variables are provided in every file
- **Bathymetry field:** `h` (`sea_floor_depth_below_sea_level`) in every file; the full grid file is published separately (see *Static fields*)

---

## Basic details
- **Model type:** Regional coastal ocean physics **ensemble**, with two-way nested child domains
- **Core ocean model:** **ROMS** (Regional Ocean Modeling System). Specific version not stated in file metadata (**TBD**); the summary points to `http://myroms.org`.
- **Sea ice model:** none published — see *Notes*
- **System name:** `Norkyst_v3-800m` / `Norkyst_v3-160m`; grid `norkyst_800m_v3`, grid file revision `v31`
- **Horizontal resolution:** 800 m (parent), 160 m (Sulafjord and Oslofjord children)
- **Vertical levels (distributed):** **15 fixed depth (z) levels** — 0, 1, 2, 3, 5, 7, 10, 15, 25, 50, 65, 75, 100, 200, 300 m
- **Vertical coordinate:** ROMS runs on **terrain-following sigma** natively; the distributed `zdepth` product is interpolated to the 15 fixed z-levels above. The native sigma output is not published.
- **Forecast length:** **member-dependent** — see the table below
- **Update frequency:** once daily
- **Production cycle:** 00 UTC
- **Temporal output resolution:** hourly, instantaneous snapshots (`cell_methods = "ocean_time: point"`)
- **Time encoding:** `seconds since 1970-01-01 00:00:00`
- **Conventions:** CF-1.8, ACDD-1.3
- **Archive availability:** analysis files from **2024-01-01**; aggregations from the same date
- **Bathymetry source:** not stated in the grid or output metadata (**TBD**)

### Forecast length by member — verified

| Member | Description | Forecast length | Total hourly steps |
|---|---|---|---|
| `m00` | 800 m ensemble control | **120 h** | 121 |
| `m60` | 800 m ensemble variant | **120 h** | 121 |
| `m70` | 800 m + 160 m Sulafjord, two-way nested | **24 h** | 25 |
| `m71` | 800 m + 160 m Oslofjord, two-way nested | **60 h** | 61 |

Verified from file titles (`"... 120 hours ocean forecast"`, `"160m 24 hours ocean forecast, Sulafjord"`, `"160m 60 hours ocean forecast, Oslofjord"`) and confirmed by counting time steps across the AN and FC file sets. **Do not assume a common forecast horizon across members.**

---

## Forcing
- **Atmospheric forcing:** the output carries `Uwind_eastward` / `Vwind_northward` (surface wind components) as pass-through fields, so the driving winds are inspectable directly. The source model is not named in the metadata (**TBD**) — MEPS is the natural candidate given MET Norway's other coastal systems, but this is not stated.
- **Tidal forcing:** a static `norkyst_tide.nc` file is published alongside the grid, indicating **explicit tidal forcing**. Constituents and source not documented (**TBD**).
- **River runoff:** not documented (**TBD**)
- **Lateral boundary conditions:** not documented (**TBD**). The catalog notes that sub-70 members "could differ either by other input (forcing), boundary conditions, mixing scheme, parameters, computational choices or other options," which implies a parent system exists but does not name it.
- **Initial conditions:** not documented (**TBD**)

---

## Coupling
- **Two-way nesting (internal):** members ≥ 70 run an 800 m parent grid two-way coupled to a 160 m child domain. Both parent and child output are published for those members, so the nesting is inspectable.
- **One-way to waves (external):** NorKyst v3 surface currents force [MyWaveWAM800m](../../../wave_models/regional/norway/mywavewam800m.md). The wave model publishes them as `Current` / `Currentdir` with the long name *"surface current speed from ocean model"*. Waves do not feed back into NorKyst.
- **Atmosphere:** one-way forcing only; no coupled atmosphere.

---

## Data assimilation
- **DA scheme: TBD, and probably not formal DA.** Nothing in the output metadata documents an assimilation scheme. However, the static file directory contains **`norkyst_nud.nc`** and **`norkyst_ana_nud-0.5.nc`**, whose names point to **nudging (relaxation) toward an analysis field** rather than a variational or ensemble assimilation scheme. The `-0.5` suffix plausibly encodes a nudging coefficient or timescale.

  **This is inference from filenames, not documentation.** Treat NorKyst v3 as having no confirmed formal DA, with nudging as the likely mechanism, until MET Norway confirms. This is worth asking about — the distinction matters for anyone using the analysis files as a reanalysis-like product.

- **Assimilated observations:** not documented (**TBD**)

---

## What it provides

### 3D ocean fields (on the 15 z-levels)
| Variable | Long name | Units |
|---|---|---|
| `temperature` | Sea water potential temperature | Celsius |
| `salinity` | Sea water salinity | 1e-3 |
| `u_eastward` | Sea water eastward velocity | m s⁻¹ |
| `v_northward` | Sea water northward velocity | m s⁻¹ |
| `w` | Sea water z velocity | m s⁻¹ |
| `AKs` | Ocean vertical salt diffusivity | m² s⁻¹ |

**Currents are already rotated to geographic east/north.** ROMS natively outputs velocities on grid-relative `xi`/`eta` axes; this product delivers `u_eastward`/`v_northward`, so no grid rotation is needed. Convenient, and easy to miss if you're used to raw ROMS history files.

**Vertical velocity and vertical diffusivity are both published**, which is unusual for an operational ocean product. Together with hourly output they make the dataset directly usable for offline Lagrangian particle tracking — the salmon-lice and oil-spill applications the summary cites.

### Surface fields
| Variable | Long name | Units |
|---|---|---|
| `zeta` | **Sea surface height above geoid** | m |
| `Uwind_eastward` | Surface u-wind component | m s⁻¹ |
| `Vwind_northward` | Surface v-wind component | m s⁻¹ |

Note the sea-level reference frame: `zeta` is **above geoid**, not above mean sea surface. Depth level 0 m serves as the surface layer for the 3-D fields.

### Sea ice fields
**None.** The GCMD keyword list includes `SEA ICE CONCENTRATION`, but no sea ice variable is present in any distributed file. The keyword is boilerplate; do not build against it.

### Static fields
Published under `norkystv3_his_files/static_nc_files/`:
- `norkyst_grd_v31.nc` — ROMS grid file (Arakawa C-grid: `xi_rho` 2747 × `eta_rho` 1148, plus `psi`/`u`/`v` staggered grids), bathymetry, land–sea mask, metric terms
- `fine160m_m70.nc`, `fine160m_m71.nc` — child grid definitions for Sulafjord and Oslofjord
- `norkyst_tide.nc` — tidal forcing
- `norkyst_nud.nc`, `norkyst_ana_nud-0.5.nc` — nudging fields

A `scripts/` directory also exists in the catalog but contained no datasets when checked.

---

## Ensemble configuration
The catalog describes NorKyst v3 as a **ROMS-EPS**, and documents the member numbering scheme:

- **Members < 70** — different simulations on the 800 m grid, differing by forcing, boundary conditions, mixing scheme, parameters, computational choices, or other options
- **Members ≥ 70** — two-way nested runs with an 800 m parent and a higher-resolution child
- `m00` — control member of the 800 m ensemble
- `m70` — 800 m + 160 m Sulafjord
- `m71` — 800 m + 160 m Oslofjord

**Only four members are published: `m00`, `m60`, `m70`, `m71`.** The numbering (a jump from 00 to 60) implies a larger internal ensemble of which this is a curated subset. How many members run operationally is not documented (**TBD**).

**The catalog's advice for identifying members does not work.** It states: *"See the summary in netcdf file for information about a specific member."* Verified — `m60`'s `summary` attribute is **byte-identical** to `m00`'s generic system description. There is nothing in the distributed files distinguishing the perturbed member from the control. **What makes `m60` different from `m00` cannot be determined from the data**, which substantially limits its usefulness as an ensemble member. Worth raising with MET Norway.

Because only a control plus one unlabelled variant are published on the 800 m grid, this product is **not usable as a probabilistic ensemble** in practice. Treat `m00` as the deterministic product and `m60` as an unexplained second realization.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, no approval gate
- **License:** **CC BY 4.0** — `license = "https://spdx.org/licenses/CC-BY-4.0 (CC-BY-4.0)"`, declared in all output files. Attribution to MET Norway required; no share-alike obligation.
- **Is the data downloadable?** Yes — direct HTTP and OPeNDAP
- **Data formats:** NetCDF-4 (CF-1.8 / ACDD-1.3)
- **Access methods:** OPeNDAP (`dodsC`), HTTP file server (`fileServer`), WMS, WCS
- **Top-level catalog:** https://thredds.met.no/thredds/fou-hi/norkystv3.html

### 1. Daily files (analysis + forecast)
- **Catalog:** `https://thredds.met.no/thredds/catalog/fou-hi/norkystv3_his_files/YYYY/MM/DD/catalog.html`
- **Naming:**
  - Analysis: `norkyst{800,160}_his_zdepth_<YYYYMMDD>T00Z_<mID>_AN.nc`
  - Forecast: `norkyst{800,160}_his_zdepth_latest_<mID>_FC_<NNNN>.nc`

**The AN/FC split, decoded:**
- `_AN` — **24 hourly steps**, the cycle day itself (00:00 → 23:00 UTC). One file per member per grid.
- `_FC_0001` … `_FC_000N` — the forecast beyond the analysis day, **chunked one file per forecast day**. For a 120 h member: `_0001` = day +1 (24 steps), `_0002` = day +2, `_0003` = day +3, `_0004` = day +4, `_0005` = a **single step** at +120 h. For the 60 h Oslofjord member: `_0001` (24 steps) + `_0002` (13 steps). For the 24 h Sulafjord member: `_0001` alone (1 step).

**Verified example** (`m00`, 2026-07-27 cycle): `_AN` spans 07-27T00:00 → 07-27T23:00; `_FC_0001` spans 07-28T00:00 → 07-28T23:00; `_FC_0005` is 08-01T00:00 only. Total 121 steps = +0 to +120 h.

**The `latest` in FC filenames is misleading** — the files sit inside a dated day directory, so they are not overwritten across days. But they are **deleted once superseded**: archive day folders retain only `_AN` files. Verified — 2026/07/20 and 2025/07/27 contain analysis files exclusively.

**File sizes (800 m, per 24-step file):** ~4.1–4.4 GB. The 160 m children are far smaller (~70–390 MB). A single full 120 h member is roughly 21 GB.

### 2. Archive (analysis only)
- **Structure:** `YYYY/MM/DD/`
- **Live-measured start: 2024-01-01**, with `norkyst800_..._m00_AN.nc` alone on that date. A `2023/` directory exists in the catalog but is **empty** — the same empty-year-directory artifact seen in the [WW3 4 km](../../../wave_models/regional/norway/met-norway-ww3.md) archive.
- **Members entered the archive progressively** (sampled):

| Date | Members present |
|---|---|
| 2024-01-01 | `norkyst800:m00` |
| 2024-06-01 | `norkyst800:m00`, `norkyst160:m70`, `norkyst160:m71` |
| 2024-12-01 | above **+ `norkyst800:m70`, `norkyst800:m71`** |
| 2026-01-01 | above **+ `norkyst800:m60`** |

Long retrospective studies should check member availability for their period rather than assuming a stable file set.

### 3. Best-estimate aggregations (three, one per published grid/member)
| Aggregation | OPeNDAP path | Span | Steps | Missing |
|---|---|---|---|---|
| 800 m control | `fou-hi/norkystv3_800m_m00_be` | 2024-01-01T00Z → 2026-08-01T00Z | 22,511 | ~122 h (0.5%) |
| 160 m Sulafjord | `fou-hi/norkystv3_160m_m70_be` | 2024-04-18T00Z → 2026-07-28T00Z | 19,885 | ~60 h (0.3%) |
| 160 m Oslofjord | `fou-hi/norkystv3_160m_m71_be` | 2024-04-17T00Z → 2026-07-29T12:00Z | 19,957 | ~48 h (0.2%) |

Base URL: `https://thredds.met.no/thredds/dodsC/<path>`

Each aggregation runs from the archive start through the end of that member's current forecast, which is why the end dates differ (+120 h, +24 h, +60 h respectively). **None is gap-free** — 0.2–0.5% of hours are missing. Read the time axis rather than assuming a regular hourly index.

**No aggregation is published for `m60`** and none for the 800 m output of `m70`/`m71`.

**These aggregations are the practical route for time-series and climatology work** — 2.5+ years of hourly coastal ocean state through one endpoint, without touching 4 GB daily files.

### Publication latency (live-measured, 2026-07-27 00 UTC cycle)
- `_AN` files: `date_created` ≈ 04:20 UTC (**+4 h 20 m**)
- `_FC` chunks: created 04:50 → 06:22 UTC, sequentially by lead day (**+4 h 50 m to +6 h 22 m**)
- **Analysis files are rewritten in place later the same day.** The `_AN` files carry an `ncks` recompression step in their `history` timestamped 18:42–19:03 UTC, and their catalog modification times match. Content-based cache validation will see these files change ~14 hours after first publication.

### Terms of service
MET Norway asks users **not to spawn parallel OPeNDAP sessions or file downloads**, reserving the right to block IPs causing traffic overload. WMS is not recommended beyond simple demonstration. Status: https://status.met.no

---

## Version history

Formal version history is not published. The following is reconstructed from live archive inspection and file metadata, and should be treated as observational rather than authoritative.

### 2022-10-12 — v3 grid created
`norkyst_grd_v31.nc` carries `date = "2022-10-12"` and `gridname = "norkyst_800m_v3"`, created by the Institute of Marine Research using `gridmap`. The `v31` filename suffix indicates a grid revision 3.1.

### 2024-01-01 — Archive and aggregation start
First archived analysis file (`m00`, 800 m) and first timestamp in the 800 m aggregation. No earlier data is retrievable; the `2023/` catalog directory is empty.

### ~April 2024 — 160 m child domains enter the archive
Sulafjord (`m70`) and Oslofjord (`m71`) 160 m output first appears; the aggregations for both begin 2024-04-17 / 2024-04-18.

### Between mid-2024 and December 2024 — 800 m output added for nested members
The 800 m parent grids of `m70` and `m71` begin being archived alongside their 160 m children.

### Between March 2025 and January 2026 — member `m60` added
`m60` is absent on 2025-07-27 and present on 2026-01-01. Its distinguishing configuration is not documented.

---

## Notes

- **Lossy compression is applied, and it has corrupted `forecast_reference_time`.** The `history` attribute shows `ncks -O -7 -L 1 --ppc default=5`, i.e. NetCDF-4 with deflate level 1 and **precision-preserving compression quantized to 5 significant digits**. For geophysical fields this is a reasonable trade. But `forecast_reference_time` is a scalar non-coordinate variable and is quantized along with everything else: for the 2026-07-27 00 UTC cycle it reads **1785106432 = 2026-07-26T22:53:52Z**, which is **66 minutes before** the actual cycle time of 2026-07-27T00:00:00Z. Verified identical across `m00`, `m60`, and `m70`, so it is systematic rather than a per-member setting.

  **Use `time[0]` or the filename timestamp to determine the cycle. Do not use `forecast_reference_time`.** The `time` coordinate itself is clean (NCO excludes dimension coordinates from default `--ppc`) — consecutive values are exactly 3600 s apart.

  The 5-significant-digit quantization also applies to `temperature`, `salinity`, and the velocity fields. At typical coastal values that is roughly ±0.001 °C and ±0.0001 m s⁻¹ — negligible for most uses, but worth knowing before computing small differences or gradients.

- **`m60` is undocumented.** See *Ensemble configuration*. The catalog tells you to read the file summary; the file summary is generic boilerplate identical to the control member's.

- **Only analysis files survive in the archive.** Forecast chunks are deleted once superseded. If you need forecast-lead-time-resolved output (for skill scoring, say), you must harvest daily — the archive and aggregations give you the best-estimate analysis stream only.

- **Forecast length varies by member (120 / 60 / 24 h).** Code that iterates members with a fixed lead-time loop will fail on `m70` and `m71`.

- **No sea ice despite GCMD keywords.** See *What it provides*.

- **`dataset_production_status = "In Work"`** alongside `operational_status = "Operational"` — the same contradictory attribute pair carried by [MyWaveWAM800m](../../../wave_models/regional/norway/mywavewam800m.md). Given 2.5+ years of stable archive and consistent latency, the operational designation is the credible one.

- **Repository placement.** This entry uses `ocean-model.template.md` and sits under `models/ocean_models/`, despite the system being described as a ROMS-EPS. That follows the repository's phenomenon-over-ensemble-status convention (the same reasoning that keeps wave and surge ensembles with their deterministic siblings). The practical case is stronger still here: with only a control and one unlabelled variant published, the distributed product does not function as an ensemble.

- **Relationship to other MET Norway marine systems.** NorKyst v3 is the current-forcing source for [MyWaveWAM800m](../../../wave_models/regional/norway/mywavewam800m.md). MET Norway's `fou-hi` catalog also carries **Norshelf 2.4 km**, **Topaz5**, **Barents-2.5 km (ROMS EPS)**, and **AICE** — none yet documented in this repository. Norshelf is the most likely candidate for NorKyst's lateral boundaries, but this is unverified.

- **Naming.** MET Norway uses `Norkyst_v3-800m` / `Norkyst_v3-160m` in file titles, `norkystv3` in catalog paths, `norkyst800`/`norkyst160` in filenames, and "NorKyst"/"Norkyst" inconsistently. This entry uses **NorKyst v3**. "NorKyst" is Norwegian for "Norwegian coast."

---

## Official documentation
- NorKyst v3 catalog and model description: https://thredds.met.no/thredds/fou-hi/norkystv3.html
- Daily file catalog: https://thredds.met.no/thredds/catalog/fou-hi/norkystv3_his_files/catalog.html
- Static grid and forcing files: https://thredds.met.no/thredds/catalog/fou-hi/norkystv3_his_files/static_nc_files/catalog.html
- MET Norway ocean and sea ice THREDDS root: https://thredds.met.no/thredds/fou-hi/fou-hi.html
- MET Norway ocean model overview: https://ocean.met.no/models
- MET Norway licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- ROMS project: https://www.myroms.org
- Institute of Marine Research (grid provenance): https://www.hi.no
- CC BY 4.0 licence text: https://creativecommons.org/licenses/by/4.0/

> **Documentation gap:** MET Norway publishes no PUM, validation report, or configuration document for NorKyst v3. The catalog page carries seven bullet points. ROMS version, atmospheric forcing source, lateral boundary source, river runoff treatment, tidal constituents, bathymetry source, assimilation scheme, and full ensemble size are all undocumented and recorded as TBD above. This is the least-documented entry in the MET Norway marine set and would benefit most from direct contact.
