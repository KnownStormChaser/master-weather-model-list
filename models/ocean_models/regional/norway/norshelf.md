# Norshelf 2.4 km (NORKYST_DA)

## What this model is
**Norshelf** — formally **NORKYST_DA** — is MET Norway's operational **data-assimilating** coastal ocean forecasting system for the Norwegian shelf. It runs **ROMS 4.3** at 2.4 km with **42 terrain-following vertical levels** and a **physical-space 4D-variational (4D-Var)** assimilation scheme.

It is deliberately a *coarser* configuration of the same domain as [NorKyst v3](./norkyst-v3.md). MET Norway states the reasoning directly: 2.4 km was chosen to suit the scale of the available observations, and to balance resolving eddy dynamics against confining the nonlinearities that limit 4D-Var. Norshelf is the assimilation engine; NorKyst v3 is the high-resolution forecast.

Norshelf assimilates **satellite sea surface temperature, in-situ observations from ARGOS drifters, CTD sections and ferry boxes, and HF-radar surface currents**.

Two things make this entry unusual in the catalog. First, MET Norway publishes the **4D-Var observation-space diagnostics** — cost function values, conjugate-gradient convergence, and per-variable innovation statistics — as open NetCDF alongside the forecast fields. Very few operational centres do this. Second, the system produces an **underwater sound-velocity product** for acoustic propagation, which is not a standard ocean-model output.

*Live-verified against the operational THREDDS distribution on 2026-07-27/28 (00 UTC cycle), plus archive sampling back to 2017-12.*

---

## Who runs it
- **Production Unit:** Norwegian Meteorological Institute (MET Norway), Division for Ocean and Ice
- **Country:** Norway
- **Programme:** MET Norway operational marine services
- **Role in larger system:** the data-assimilating counterpart to [NorKyst v3](./norkyst-v3.md) on the same domain — see *Relationship to NorKyst v3*
- **Operational status:** no `operational_status` attribute is carried (the files are raw ROMS output without an ACDD metadata block). The system has run continuously since December 2017.

---

## What area it covers
- **Coverage:** Norwegian shelf, Norwegian Sea, North Sea, and southern Barents Sea — very nearly the same footprint as [NorKyst v3](./norkyst-v3.md)
- **Grid dimensions:** 901 (`xi_rho`) × 351 (`eta_rho`), on a ROMS Arakawa C-grid (`xi_u` 900, `eta_v` 350)
- **Approximate corner coordinates (verified from `lat`/`lon` arrays):**

| Grid corner | Latitude | Longitude |
|---|---|---|
| (0, 0) | 54.959°N | 7.949°E |
| (0, 900) | 69.902°N | 36.538°E |
| (350, 0) | 57.720°N | −4.463°E |
| (350, 900) | 75.787°N | 18.410°E |

Envelope roughly 55°N – 76°N, 4.5°W – 36.5°E. (The grid is projected, so corners are not axis-aligned min/max.)

- **Projected extent:** `xi_rho` −3,326,400 m → −1,166,400 m; `eta_rho` −1,764,800 m → −924,800 m
- **Projection:** polar stereographic
  - `grid_mapping_name = polar_stereographic`, `straight_vertical_longitude_from_pole = 70.0`, `latitude_of_projection_origin = 90.0`, `standard_parallel = 60.0`
  - `false_easting = 0.0`, `false_northing = 0.0`
  - WGS84 ellipsoid (`semi_major_axis = 6378137.0`, `inverse_flattening = 298.257223563`)
  - proj4: `+proj=stere +lat_0=90 +lat_ts=60 +lon_0=70 +x_0=0 +y_0=0 +ellps=WGS84 +units=m +no_defs +type=crs`
- **Resolution:** `dx = 2400.0` m, exact (declared in the grid-mapping variable and confirmed by coordinate spacing)
- **Georeferencing:** 2-D `lat_rho`/`lon_rho` (plus `lat_u`/`lon_u`, `lat_v`/`lon_v`) in the native files; `lat`/`lon` in the z-depth files
- **Land-sea masks:** `mask_rho`, `mask_u`, `mask_v` included
- **Bathymetry:** `h` (bathymetry at RHO-points) in every file; grid file `norshelf_grd.nc` referenced but not published in the open catalog

---

## Basic details
- **Model type:** Regional coastal ocean physics, deterministic, **with 4D-Var data assimilation**
- **Core ocean model:** **ROMS 4.3** — `code_dir = ".../roms-4.3_DA"`, `git_url = "https://github.com/myroms/roms.git"`, `git_rev = 862ed34b10ef5b97f2489dd48d924c1c9a75cfb3`
- **Sea ice model:** none
- **System name:** `NORKYST_DA`; product name "Norshelf"; file title `MET NorShelf (2.4km)`; ROMS header `norshelf.h`, CPP macro `NORSHELF`
- **Horizontal resolution:** 2.4 km (exact)
- **Vertical levels (native):** **42** terrain-following levels (`s_rho = 42`, `s_w = 43`)
- **Vertical coordinate:** **terrain-following sigma**, published natively — unlike [NorKyst v3](./norkyst-v3.md), the raw sigma output is distributed, with `Vtransform`, `hc`, `Cs_r`, and `s_rho` available for reconstructing true depths
- **Vertical levels (z-interpolated product):** 17 fixed depths — 0, 1, 3, 5, 10, 15, 20, 25, 30, 40, 50, 75, 100, 150, 200, 250, 300 m
- **Forecast length:** **96 hours** (analysis day + 72 h forecast)
- **Update frequency:** once daily
- **Production cycle:** 00 UTC
- **Target delivery:** ~10:13 UTC (**+10 h 13 m**) — see *Publication latency*
- **Temporal output resolution:** hourly (`qck`), daily means (`avg`), 6-hourly (sound velocity)
- **Conventions:** CF-1.4, SGRID-0.3
- **Time encoding:** `ocean_time`, `seconds since 1970-01-01 00:00:00`, proleptic Gregorian
- **Archive availability:** **2017-12-01 onward**

### ROMS configuration (from file global attributes)
- **Turbulence closure:** GLS mixing with Canuto-A stability functions (`GLS_MIXING`, `CANUTO_A`, `CRAIG_BANNER`, `CHARNOK`)
- **Surface fluxes:** bulk formulae (`BULK_FLUXES`), with cool-skin correction (`COOL_SKIN`), atmospheric pressure loading (`ATM_PRESS`), longwave-out, solar penetration (`SOLAR_SOURCE`), E−P freshwater flux (`EMINUSP`)
- **Equation of state:** nonlinear (`NONLIN_EOS`)
- **Tides:** `SSH_TIDES`, `UV_TIDES`
- **Wave–current interaction:** `WIND_MINUS_CURRENT` (stress computed relative to surface current)
- **Tracer advection:** temperature — Upstream3 horizontal / Centered4 vertical; salinity — **HSIMT** (both directions)
- **Momentum advection:** `UV_U3HADVECTION`, `UV_C4VADVECTION`
- **Nudging:** `IMPLICIT_NUDGING`, with `norshelf_nud_2.4.nc`
- **Lateral boundary conditions:**

| Variable | Scheme |
|---|---|
| `zeta` | Chapman |
| `ubar`, `vbar` | Flather |
| `u`, `v`, `temp`, `salt` | Radiation + nudging (`RadNud`) |
| `tke` | Gradient |

- **Parallelization:** MPI, 12×12 tiling, Intel `ifort`, PIO/PnetCDF

---

## Forcing
- **Atmospheric forcing:** daily file `norshelf_atm_<YYYYMMDD>T00Z.nc`, applied through bulk flux formulae. **Source model not named in metadata (TBD)** — MEPS is the natural candidate but is not stated.
- **Tidal forcing:** **TPXO9** — `tide_file = "norshelf_tide_tpxo9.nc"`. Explicit tides in both sea level and currents.
- **River runoff:** yes — `river_file = "norshelf_river.nc"`. Static file, so climatological or fixed-discharge rather than dynamically coupled (**TBD**).
- **Lateral boundary conditions:** daily `norshelf_bry_<YYYYMMDD>T00Z.nc` plus a climatology file `norshelf_clm_<YYYYMMDD>T00Z.nc`. **Parent system not named (TBD).**
- **Initial conditions:** daily `norshelf_ini_<YYYYMMDD>T00Z.nc`; restart file `norshelf_rst_<YYYYMMDD>T00Z.nc`
- **Ice forcing:** none

---

## Coupling
- **Standalone ocean physics.** One-way atmospheric forcing through bulk formulae; no online coupling to waves, ice, or atmosphere.
- The `WIND_MINUS_CURRENT` option means surface stress is computed from the wind *relative to* the modelled current — a wave-free approximation to current feedback on stress, not wave coupling.

---

## Data assimilation
This is the defining feature of the system.

- **DA scheme:** **physical-space 4D-Var (4D-PSAS)**, per the catalog description
- **Configuration (verified from observation files):** **1 outer loop, 12 inner loops** (`Nouter = 1`, `Ninner = 12`)
- **Update cycle:** daily, 00 UTC
- **Assimilation window:** 24 hours (implied by the daily cycle and the 24-step analysis file; not documented explicitly — **TBD**)
- **Increment application:** not documented (**TBD**)

### Assimilated observations (per MET Norway's catalog description)
- **Sea surface temperature:** satellite (source missions not specified)
- **In-situ temperature and salinity:** ARGOS drifters, CTD sections, ferry boxes
- **Surface currents:** **HF radar**

HF-radar current assimilation is uncommon in operational ocean systems and is a genuine distinguishing feature.

### Observation-space diagnostics are published openly
The `sea_norshelf_obs_files` catalog contains daily ROMS **MOD** files — `norshelf_mod_<YYYYMMDD>T00Z.nc` — carrying the full 4D-Var diagnostic bundle. For the 2026-06-30 cycle:

- **`datum = 755926`** — individual observations assimilated that day
- **`survey = 193`** — observation time groupings
- **`state_var = 9`** — assimilated state variables
- Per-variable statistics: `obs_mean`, `obs_std`, `model_mean`, `model_std`, `model_bias`, `SDE`, `CC` (correlation), `MSE`, `Nused_obs`
- Cost-function terms: `NL_iDataPenalty`, `NL_fDataPenalty`, `Jf`, `Jdata` across 10 cost variables
- Conjugate-gradient convergence: `cg_beta`, `cg_delta`, `cg_Gnorm_v/y`, `cg_Greduc_v/y`, `cg_Ritz`, `cg_RitzErr`, `Ritz`, `nConvRitz`

This makes it possible to audit assimilation performance independently — daily innovation statistics and convergence behaviour, not just the analysis product. MET Norway also maintains a public observation and performance tracking site (see *Official documentation*).

> **The observation feed has stalled.** Live-checked 2026-07-28: the most recent MOD file is `norshelf_mod_20260630T00Z.nc` (modified 2026-06-30T10:47Z). There is no `2026/07/` directory in the obs catalog — **almost four weeks with no new observation files**, while the forecast files continued publishing normally throughout July. Whether this is a temporary outage or a discontinued stream is **TBD**; worth confirming before depending on it. Note also a stray `2025.tmp` directory in the obs catalog, suggesting the tree is not closely maintained.

---

## What it provides

### Native sigma-level fields (`qck` — hourly)
| Variable | Description |
|---|---|
| `temp`, `salt` | Potential temperature (°C), salinity |
| `u`, `v` | Velocity components on the **grid-relative** (xi/eta) staggered C-grid |
| `u_eastward`, `v_northward` | Velocity rotated to **geographic** east/north on rho-points |
| `ubar`, `vbar` | Barotropic (depth-averaged) velocity |
| `w` | Vertical velocity |
| `zeta` | Free surface |
| `AKs` | Vertical salt diffusivity |
| `tke`, `gls` | Turbulent kinetic energy and generic length-scale |
| `angle` | Grid rotation angle |
| `h`, `mask_rho`, `mask_u`, `mask_v`, `lat_*`, `lon_*` | Static grid fields |

**Both raw and rotated currents are published.** Use `u`/`v` with `angle` if you need the native C-grid; use `u_eastward`/`v_northward` for anything geographic. This is more complete than NorKyst v3, which publishes only the rotated form.

### Z-level fields (`qck_ZDEPTHS` — hourly, 17 depths)
`temperature`, `salinity`, `u_eastward`, `v_northward`, `zeta`, `h`, `mask_rho`, `lon`, `lat`

The convenient product for most users — no sigma-to-z conversion needed.

### Daily averages (`avg`)
Native sigma fields (`temp`, `salt`, `u`, `v`, `ubar`, `vbar`, `w`, `zeta`, `AKs`) as daily means. Note `avg` files carry **no** `u_eastward`/`v_northward` — rotation must be done manually using `angle`.

### Sound velocity (`lyd` — 6-hourly)
`norshelf_lyd_fc_<YYYYMMDD>T00Z.nc` — an underwater acoustics product. "Lyd" is Norwegian for *sound*; the file's `history` shows it derives from `soundvelNS.nc`.

| Variable | Shape | Description |
|---|---|---|
| `sound_vel` | (17, 42, 351, 901) | **Sound velocity** on sigma levels |
| `sonic_depth` | (17, 351, 901) | Sonic layer depth |
| `profile_category` | (17, 351, 901) | Integer classification of the sound-speed profile |
| `temp`, `salt`, `zeta` | | Supporting fields |
| `Vtransform`, `hc`, `Cs_r`, `s_rho` | | Sigma-coordinate parameters |

17 time steps at **6-hourly** spacing, spanning the full 96 h run (00Z through +96 h). Roughly 5.56 GB per cycle.

> **This file type cannot be read over OPeNDAP.** Both `.dds` and `.das` return `Error { code = 403; message = "NcDDS Variable data type = long" }` — the file contains an `int64` variable (`Vtransform`) that the DAP2 protocol has no type for, and the THREDDS DAP handler rejects the whole dataset rather than skipping the variable. **Use the HTTP file server instead**; the server supports byte ranges (`accept-ranges: bytes`), so a NetCDF-4/HDF5 reader can pull metadata and slices without downloading all 5.56 GB.

> The `lyd` file also carries `title = "ROMS 3.7 - NorShelf-2.4km"` while the `qck` files report ROMS 4.3. The sound-velocity post-processing chain appears not to have been updated alongside the model.

---

## Data availability
- **Is the data free?** Yes — no registration, no API key, no approval gate
- **License:** **No `license` attribute is declared in any Norshelf file.** Verified across `qck_an`, `qck_fc`, `qck_ZDEPTHS_fc`, and `avg_fc` — these are raw ROMS output with a CF-1.4 / SGRID-0.3 header and no ACDD metadata block. This is a real difference from MET Norway's other marine products ([NorKyst v3](./norkyst-v3.md), [MyWaveWAM800m](../../../wave_models/regional/norway/mywavewam800m.md), [WW3 4 km](../../../wave_models/regional/norway/met-norway-ww3.md)), all of which declare CC BY 4.0 in-file.

  MET Norway's **server-wide free-data terms** apply — NLOD and CC BY 4.0, attribution required — and are linked from every THREDDS catalog page. Treat the data as CC BY 4.0 by institutional policy, but note that unlike its siblings this product carries **no per-file licence declaration**. Worth confirming if you need a machine-verifiable licence chain.
- **Is the data downloadable?** Yes — direct HTTP and OPeNDAP (except `lyd`; see above)
- **Data formats:** NetCDF-4/HDF5
- **Access methods:** OPeNDAP (`dodsC`), HTTP file server (`fileServer`), WMS, WCS
- **Top-level catalog:** https://thredds.met.no/thredds/fou-hi/norshelf.html

### 1. Daily files
- **Catalog:** `https://thredds.met.no/thredds/catalog/sea_norshelf_files/YYYY/MM/catalog.html`
- **Naming:** `norshelf_<type>_<an|fc>_<YYYYMMDD>T00Z.nc`

**The AN/FC split.** The `history` attribute shows the forecast file is cut from the full run with `ncks -O -d ocean_time,24,` — the split is at hour 24:

| File | Steps | Coverage (2026-07-27 cycle) |
|---|---|---|
| `_an` | 24 hourly | 07-27T00:00 → 07-27T23:00 (hours 0–23) |
| `_fc` | 73 hourly | 07-28T00:00 → 07-31T00:00 (hours 24–96) |

Together: 97 steps, 0 to +96 h.

**Seven file types are published for the current cycle, but only five are retained:**

| File type | Cadence | Size (per cycle) | Retained in archive? |
|---|---|---|---|
| `qck_an` | hourly | ~3.6 GB | **Yes** |
| `qck_fc` | hourly | ~10.8 GB | **No** — rolling ~41 days |
| `qck_ZDEPTHS_an` | hourly, 17 z | ~320 MB | **Yes** |
| `qck_ZDEPTHS_fc` | hourly, 17 z | ~959 MB | **Yes** |
| `avg_an` | daily mean | ~110 MB | **Yes** |
| `avg_fc` | daily mean | ~304 MB | **Yes** |
| `lyd_fc` | 6-hourly | ~5.56 GB | **No** — rolling ~41 days |

**Live-measured retention for `qck_fc` and `lyd_fc`:** earliest available 2026-06-17, latest 2026-07-27 — a **41-day rolling window**. Verified absent from 2026/05 and all earlier months. The other five types persist indefinitely.

**Fixed-name latest files** (overwritten each cycle, at the catalog root):
`norshelf_qck_fc_latest.nc`, `norshelf_qck_ZDEPTHS_fc_latest.nc`, `norshelf_avg_fc_latest.nc`

### 2. Archive
- **Structure:** `YYYY/MM/` — MET Norway notes the directory layout **changed to YYYY/MM on 2024-04-30**; earlier years have been reorganized into the same scheme, so traversal is uniform.
- **Live-verified start: 2017-12** (first month present; the December 2017 folder holds 28 days)
- **Documented caveat:** *"access to older files in the archive is reduced during maintenances"* — plan for intermittent unavailability of historical files.

**Product mix has changed over time** (sampled):

| Period | File types present |
|---|---|
| 2017-12 → 2019 | `avg_an`, **`his_an`**, `qck_ZDEPTHS_an`, `qck_an` — analysis only |
| by 2020-06 | `avg_an`, `avg_fc`, `qck_ZDEPTHS_an`, `qck_an` — forecast output begins; `his_an` discontinued |
| by 2023-06 | above **+ `qck_ZDEPTHS_fc`** |
| 2024-05 → present | unchanged five-type archive set |

Retrospective studies spanning 2017–2020 should expect a different and thinner file set.

### 3. Aggregations (four)
| Aggregation | OPeNDAP path | Content |
|---|---|---|
| Daily averages | `sea_norshelf_avg_agg` | Sigma-level daily means |
| 3-hourly history | `sea_norshelf_3hr_agg` | Sigma-level, 3-hourly |
| Hourly history | `sea_norshelf_his_agg` | Sigma-level, hourly |
| Hourly history z-depths | `sea_norshelf_his_ZDEPTHS_agg` | 17 z-levels, hourly |

Base URL: `https://thredds.met.no/thredds/dodsC/<path>`

**Live-measured spans:**

| Aggregation | Span | Steps | Missing |
|---|---|---|---|
| `sea_norshelf_his_ZDEPTHS_agg` | 2017-12-01T00:00Z → 2026-07-31T00:00Z | 74,137 hourly | ~1,800 h (2.4%) |
| `sea_norshelf_avg_agg` | 2017-12-01T12:00Z → 2026-07-30T12:00Z | 3,086 daily | ~78 d (2.5%) |

**Nearly nine years of hourly, assimilated coastal ocean state through a single endpoint.** This is the longest continuous record of any MET Norway marine product documented in this repository, and it is analysis-quality rather than pure forecast. For Norwegian shelf hindcast and climatology work it is the strongest candidate in the `fou-hi` tree.

Gaps are larger than NorKyst v3's (2.4% vs 0.5%) — read the time axis, don't assume a regular index.

*The `3hr` and `his` aggregation dimensions were not retrieved within timeout; both are very large. Expect slow first responses and subset aggressively.*

### Publication latency (live-measured, 2026-07-27 00 UTC cycle)
All seven file types published between **10:12:39 and 10:13:42 UTC** — approximately **+10 h 13 m** after the cycle, with the whole set landing inside about a minute.

Substantially later than [NorKyst v3](./norkyst-v3.md) (+4 h 20 m), which is expected: 4D-Var with 12 inner loops over ~750,000 daily observations is far more expensive than a forward run.

### Terms of service
MET Norway asks users **not to spawn parallel OPeNDAP sessions or file downloads**, reserving the right to block IPs causing traffic overload. WMS is not recommended beyond simple demonstration. Status: https://status.met.no

---

## Relationship to NorKyst v3

The two systems share a domain, a projection, and a model core, and differ in resolution and purpose.

| | **Norshelf (this entry)** | **[NorKyst v3](./norkyst-v3.md)** |
|---|---|---|
| Purpose | Data assimilation | High-resolution forecast |
| Resolution | 2.4 km (exact) | 800 m (exact) |
| Grid | 901 × 351 | 2747 × 1148 |
| Vertical | 42 sigma levels (native published) | 15 fixed z-levels (sigma not published) |
| Model | ROMS 4.3 | ROMS, version not stated |
| DA | **4D-Var**, 1 outer / 12 inner | None confirmed; nudging files present |
| Forecast length | 96 h | 120 h (control) |
| Cycle | 00 UTC daily | 00 UTC daily |
| Latency | +10 h 13 m | +4 h 20 m |
| Archive start | **2017-12-01** | 2024-01-01 |
| Ensemble | No | Nominally (4 members published) |
| In-file licence | **None declared** | CC BY 4.0 |

**Same projection, different origin convention.** Both use `+proj=stere +lat_0=90 +lat_ts=60 +lon_0=70` on WGS84. Norshelf sets `x_0 = y_0 = 0`; NorKyst v3 sets `x_0 = 3369600`, `y_0 = 1844800`. To place Norshelf coordinates in NorKyst's frame, **add 3,369,600 m to `xi_rho` and 1,844,800 m to `eta_rho`** — doing so puts the Norshelf western edge at 43,200 m and its eastern edge at 2,203,200 m against NorKyst's 0 → 2,197,600 m, and its southern edge at 80,000 m against NorKyst's 0 → 918,400 m. The two footprints very nearly coincide, and the resolutions are in an exact **3:1** ratio. *(Offsets derived from published grid-mapping attributes and coordinate arrays; not stated by MET Norway.)*

**Does Norshelf feed NorKyst v3?** Not documented, and **TBD**. The circumstantial case: the catalog describes Norshelf as *"a setup of the Norkyst domain with reduced horizontal resolution used for data assimilation"*, and NorKyst v3 publishes a static file named `norkyst_ana_nud-0.5.nc` — an *analysis* nudging field. A daily 4D-Var analysis on a co-located 3:1 grid is the obvious candidate for what NorKyst nudges toward. But neither system's metadata names the other, and Norshelf's own boundary source is equally unnamed. **Do not assert the linkage without confirmation from MET Norway** — this is the single most useful question to ask about either entry.

---

## Notes

- **Native sigma output is published**, with `Vtransform`, `hc`, `Cs_r`, and `s_rho` available in the `lyd` files. This makes Norshelf usable for work requiring the true model vertical structure, unlike NorKyst v3 where only z-interpolated output is distributed. Note that the sigma parameters are most readily accessible from the `lyd` file, which is the one product OPeNDAP cannot serve — a slightly awkward combination.

- **Sea surface height reference frame is not stated.** `zeta` carries `long_name = "free-surface"` with no reference specification. NorKyst v3's equivalent explicitly says "above geoid"; Norshelf's does not. Given explicit TPXO9 tidal forcing, `zeta` includes the tidal signal (**inferred, not documented**).

- **Currents include tides.** `SSH_TIDES` and `UV_TIDES` are compiled in and TPXO9 forcing is applied, so `u`/`v`/`ubar`/`vbar` are total (tidal + subtidal) Eulerian currents. No Stokes drift — the system is not wave-coupled.

- **No ACDD metadata block.** No `title` beyond `"MET NorShelf (2.4km)"`, no `summary`, no `creator_*`, no `license`, no `geospatial_*` bounds, no `time_coverage_*`. Everything in this entry's coverage and timing sections was derived from coordinate arrays and file contents rather than read from attributes. Catalogue harvesters expecting discovery metadata will find none.

- **The `lyd` OPeNDAP failure is a protocol limitation, not an outage.** DAP2 has no 64-bit integer type; the `int64` `Vtransform` variable makes THREDDS reject the dataset entirely. Byte-range HTTP with an HDF5 reader works fine.

- **Two file types are not archived.** `qck_fc` (full-resolution hourly sigma forecast) and `lyd_fc` (sound velocity) roll off after ~41 days. If you need either historically, harvest continuously.

- **Relationship to other MET Norway marine systems.** MET Norway's `fou-hi` catalog also carries **Topaz5**, **Barents-2.5 km (ROMS EPS)**, and **AICE** on the ocean side — none yet documented here. On the wave side see [WW3 4 km](../../../wave_models/regional/norway/met-norway-ww3.md), [MyWaveWAM3km](../../../wave_models/regional/norway/mywavewam3km.md), [MyWaveWAM800m](../../../wave_models/regional/norway/mywavewam800m.md), and [ARCWAM](../../../wave_models/regional/norway/arcwam.md).

- **Naming.** MET Norway uses **NORKYST_DA** as the formal system name and **Norshelf** as the product name, with the catalog heading reading `NORKYST_DA (aka. "Norshelf")`. Filenames and paths use `norshelf`; file titles use `MET NorShelf (2.4km)`. This entry uses **Norshelf 2.4 km**. Note the collision risk: "NORKYST_DA" and "[NorKyst v3](./norkyst-v3.md)" are different systems.

---

## Version history

No formal version history is published. The following is reconstructed from archive inspection and file metadata.

### 2026-07 (current) — ROMS 4.3
`git_rev 862ed34b10ef5b97f2489dd48d924c1c9a75cfb3`, built against `roms-4.3_DA`. The sound-velocity product still reports ROMS 3.7 in its title, suggesting that chain lags the model upgrade.

### 2026-06-30 — Observation files stop
Last MOD file published. Forecast production continued uninterrupted. Cause unknown.

### 2024-04-30 — Directory restructure
MET Norway documents the file organization changing to a `YYYY/MM` structure on this date. Earlier years appear to have been migrated to the same layout.

### Between 2020-06 and 2023-06 — `qck_ZDEPTHS_fc` added
Z-level forecast output enters the archive.

### Between 2019-06 and 2020-06 — Forecast production begins; `his_an` discontinued
`avg_fc` first appears; the `his_an` file type present from 2017 disappears.

### 2017-12-01 — System start
Earliest archived files and earliest timestamp in both verified aggregations.

---

## Official documentation
- Norshelf catalog and model description: https://thredds.met.no/thredds/fou-hi/norshelf.html
- **Observation and performance tracking site:** https://projects.met.no/norshelf/index.html *(link verified live; this is the closest thing to a validation report MET Norway publishes for this system)*
- File catalog: https://thredds.met.no/thredds/catalog/sea_norshelf_files/catalog.html
- Observation files: https://thredds.met.no/thredds/catalog/sea_norshelf_obs_files/catalog.html
- MET Norway ocean model overview (NorKyst section): https://ocean.met.no/models#norkyst
- MET Norway ocean and sea ice THREDDS root: https://thredds.met.no/thredds/fou-hi/fou-hi.html
- MET Norway licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- ROMS project: https://www.myroms.org
- ROMS source: https://github.com/myroms/roms
- CC BY 4.0 licence text: https://creativecommons.org/licenses/by/4.0/

> **Documentation note:** unusually for the `fou-hi` tree, Norshelf has both a substantive catalog description (model core, resolution, vertical levels, DA scheme and rationale, assimilated observation types) and a dedicated public performance-tracking site. Atmospheric forcing source, lateral boundary source, river treatment, assimilation window, and increment application remain undocumented and are recorded as TBD above.
