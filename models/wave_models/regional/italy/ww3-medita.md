# WW3-MEDITA (Mediterranean WAVEWATCH III)

## What this model is
WW3-MEDITA is an operational regional wave forecasting system covering the entire Mediterranean basin, operated by Agenzia ItaliaMeteo and Arpae Emilia-Romagna.

It is based on the third-generation spectral wave model **WAVEWATCH III (WW3)** and uses an **unstructured calculation grid with variable resolution** — approximately 18 km in the open sea, refining down to **150–200 m along the Italian coast**. The unstructured grid allows a gradual, continuous transition between low- and high-resolution regions without the discontinuities that nested structured grids introduce at domain boundaries.

WW3-MEDITA became operational in October 2025, replacing the previous SWAN-MEDITARE system.

---

## Who runs it
- **Organization:** Agenzia ItaliaMeteo and Arpae Emilia-Romagna (operational forecasting chain), with implementation in collaboration with the Italian Department of Civil Protection
- **Country / region:** Italy

---

## What area it covers
- **Coverage:** Entire Mediterranean basin, with a focus on Italian coastal waters
- **Grid type:** Unstructured (variable-resolution triangular mesh)
- **Advertised domain (MeteoHub dataset card):** 30°N–46°N, 7°W–36°E
- **Live-verified mesh extent:** 30.2645°N–45.8030°N, 7.1159°W–36.2171°E (from the distributed `latitude` / `longitude` node arrays, cycle 2026072800)

---

## Basic details
- **Model type:** Regional deterministic wave model (unstructured mesh)
- **Grid system:** Single unstructured triangular mesh — **220,888 nodes**, **403,822 elements** (live-verified)
- **Core wave model:** WAVEWATCH III (WW3)
- **Version number:** **TBD** — no `WAVEWATCH_III_version_number` attribute is carried in the distributed files, unlike MET Norway's WW3 output
- **Horizontal resolution:** Variable. Documented as ~18 km (open sea) to ~150–200 m (Italian coast). Live-verified mesh edge lengths:

  | Statistic | Edge length |
  |---|---|
  | Minimum | 0.091 km |
  | 1st percentile | 0.221 km |
  | 25th percentile | 0.355 km |
  | Median | 0.985 km |
  | 75th percentile | 2.654 km |
  | 95th percentile | 9.011 km |
  | Maximum | 20.30 km |

  About 35% of edges are shorter than 500 m and about 6% are shorter than 250 m, consistent with the documented coastal refinement. The coarse end reaches slightly beyond the advertised 18 km.

- **Forecast length:** 72 hours
- **Records per file:** **96 hourly steps spanning T−23 h to T+72 h** relative to the cycle reference time — see *Time axis* below
- **Update frequency:** 1× daily
- **Production cycle:** 00 UTC
- **Temporal output resolution:** 1-hourly
- **Spectral discretization:** frequency range **0.0500–0.7932 Hz** (from the `comment` attribute on `vuss`). This is consistent with **30 frequency bins at the standard WW3 1.1 geometric ratio** starting at 0.05 Hz (0.05 × 1.1²⁹ = 0.7932), but the bin count and the directional resolution are **TBD** — neither is stated in the files or in published documentation.

---

## Time axis (live-verified)

Each parameter file contains **96 hourly records**, not 72. The time axis runs from **23 hours before** the cycle reference time to **72 hours after** it. Verified identical across the 2026-07-26, 2026-07-27 and 2026-07-28 cycles:

| Cycle | `forecast_reference_time` | `time[0]` | `time[-1]` |
|---|---|---|---|
| 2026072600 | 2026-07-26 00:00Z | 2026-07-25 01:00Z (−23 h) | 2026-07-29 00:00Z (+72 h) |
| 2026072700 | 2026-07-27 00:00Z | 2026-07-26 01:00Z (−23 h) | 2026-07-30 00:00Z (+72 h) |
| 2026072800 | 2026-07-28 00:00Z | 2026-07-27 01:00Z (−23 h) | 2026-07-31 00:00Z (+72 h) |

**Code that assumes `time[0]` equals the cycle hour will be off by 23 hours.** The leading segment is presumably a spin-up / hindcast leg driven by the previous day's atmospheric analysis, but this is **not documented by the operator** and the interpretation is **TBD**.

- `time` units: `seconds since 1970-01-01`, calendar `proleptic_gregorian`
- `forecast_reference_time` is stored as a bare `int64` epoch-second value **with no `units` attribute** (only `long_name` and `standard_name`). It must be interpreted as seconds since 1970-01-01 by convention, not by declaration.
- All field variables carry `cell_methods = "time: mean"`, i.e. the records are **hourly means, not instantaneous values**. This applies to the directional and frequency fields (`dir`, `fp`) as well, where time-averaging is a questionable operation — treat those two with care.

---

## Forcing
- **Atmospheric forcing (10 m wind):** Spatial blending of two atmospheric models:
  - **ICON-2I** (~2.2 km resolution) over the Italian domain
  - **ECMWF-IFS** (~9 km resolution) over the rest of the Mediterranean

  The blending is designed so that the highest-resolution wind forcing coincides with the area where the wave model resolution itself is highest (Italian coastal waters). The forcing winds are **not** echoed into the distributed output, so the blend is not directly inspectable from the files (unlike MET Norway's WW3 product, which carries `uwnd`/`vwnd`).

- **Current forcing:** **TBD** — not documented, and no current fields are distributed.
- **Lateral boundary conditions:** None required — the Mediterranean basin is essentially closed at this domain extent, with the western edge near Gibraltar treated within the model grid.

---

## Data assimilation
- **Assimilates wave observations:** No documented assimilation. Treated as a free-running forced model.

---

## What it provides

The MeteoHub distribution publishes **16 items** — 12 forecast fields and 4 grid/metadata arrays — each in its own subdirectory. Live-verified variable inventory for cycle 2026072800:

### Forecast fields (dimensions `(time: 96, node: 220888)`, `float32`)

| Dir | Variable | `long_name` | Approx. file size |
|---|---|---|---|
| `HS/` | `hs` | significant height of wind and swell waves | 64.8 MB |
| `DIR/` | `dir` | wave mean direction | 58.5 MB |
| `T01/` | `t01` | mean period T01 | 60.4 MB |
| `FP/` | `fp` | wave peak frequency | 56.3 MB |
| `UUSS/` | `uuss` | eastward surface Stokes drift | ~60 MB |
| `VUSS/` | `vuss` | northward surface Stokes drift | ~60 MB |
| `UUBR/` | `uubr` | RMS of bottom velocity amplitude, zonal | ~60 MB |
| `VUBR/` | `vubr` | RMS of bottom velocity amplitude, meridional | ~60 MB |
| `SXX/` | `sxx` | radiation stress component Sxx | ~60 MB |
| `SXY/` | `sxy` | radiation stress component Sxy | ~60 MB |
| `SYY/` | `syy` | radiation stress component Syy | ~60 MB |
| `FBB/` | `fbb` | wave dissipation in bottom boundary layer | ~60 MB |

### Grid and metadata arrays

| Dir | Variable | Shape | Size |
|---|---|---|---|
| `LATITUDE/` | `latitude` | `(node: 220888)` | 485 KB |
| `LONGITUDE/` | `longitude` | `(node: 220888)` | 536 KB |
| `TRI/` | `tri` | `(element: 403822, noel: 3)` | 1.47 MB |
| `MAPSTA/` | `MAPSTA` | `(node: 220888)` | 11 KB |

**No units are declared.** Not one variable in the distribution carries a `units` attribute. Units must be inferred from CF `standard_name` and WW3 convention: `hs` in m, `dir` in degrees, `t01` in s, `fp` in Hz, Stokes drift and bottom velocity in m s⁻¹, radiation stress components in m³ s⁻², `fbb` in W m⁻². Confirm against the operator before using these in quantitative work.

WW3-MEDITA does **not** distribute partitioned swell fields (primary/secondary swell or wind-sea partitions), peak period, mean period T02, wave spectra, or maximum crest/trough variables — a notably leaner parameter set than basin-scale Copernicus Marine wave products such as MEDWAM. Note that `fp` is distributed as a **frequency**, not a period; peak period must be derived as 1/`fp`.

The MeteoHub map viewer (separate from the raw data download) displays significant wave height, wave propagation direction, mean period and peak period.

---

## Data availability
- **Is the data free?** Yes
- **License:** CC BY 4.0 (attribution required). The MeteoHub dataset card states `License: CCBY4.0` with `Attribution: ItaliaMeteo-ARPAE`. See https://meteohub.agenziaitaliameteo.it/app/license
- **Is the data downloadable?** Yes — anonymous HTTP, no registration, no API key
- **Data formats:** NetCDF-4 (HDF5 container), zlib level 5 with shuffle filter, chunked `[10, 50000]`
- **Direct download (MeteoHub, raw cycle archives):**
  https://meteohub.agenziaitaliameteo.it/nwp/ww3/
- **Parameter list:**
  https://meteohub.agenziaitaliameteo.it/nwp/ww3/WW3_shortname_parameter_index.txt
- **Dataset metadata API:**
  https://meteohub.agenziaitaliameteo.it/api/datasets/ww3 — returns `{"id": "ww3", "name": "WW3 MEDITA", "category": "SEA", "format": "netcdf", "source": "nwp", "is_public": true}`
- **Map viewer:**
  https://meteohub.agenziaitaliameteo.it/app/maps/marine
- **ItaliaMeteo product page:**
  https://www.agenziaitaliameteo.it/en/sea/forecasts/ww3-medita/

### File naming
Cycle directories follow `YYYYMMDDHH/` (always `HH = 00`). Each cycle directory contains one subdirectory per parameter, and each subdirectory contains **exactly one file, whose name is identical in every subdirectory**:

```
/nwp/ww3/{YYYYMMDDHH}/{PARAM}/ww3_{YYYYMMDDHH}_surface-0.nc
```

e.g. `https://meteohub.agenziaitaliameteo.it/nwp/ww3/2026072800/HS/ww3_2026072800_surface-0.nc`

The filename does **not** encode which parameter it holds — only the parent directory does. Scripts that flatten downloads into a single folder will silently overwrite all 16 files with one another.

### Retention — much shorter than the directory listing suggests
The root listing shows roughly 9 cycle directories, but **most of them are empty**. Checked 2026-07-28:

| Cycle directories listed | 9 (2026072000 – 2026072800) |
|---|---|
| Cycles actually containing data | **3** (2026072600, 2026072700, 2026072800) |

Older cycles retain their full 16-subdirectory skeleton with every subdirectory emptied; the directory mtimes record the deletion time rather than the production time. Effective retention is therefore **about 3 days**, not the ~9 days or ~1 month a directory listing implies. Users needing history must harvest cycles as they are produced, and should test for file presence rather than directory presence.

### ARCO / Zarr layer is credential-gated
The MeteoHub dataset card advertises "Analysis-Ready, Cloud Optimized (ARCO) data in Zarr format". This is served from `https://meteohub.agenziaitaliameteo.it/api/arco/ww3/` and returns **HTTP 401 `"Missing credentials"`** to anonymous requests, including for `.zmetadata`. Only the raw NetCDF tree under `/nwp/ww3/` is openly accessible. The catalog documents the open NetCDF distribution; the ARCO layer is noted here for completeness and is **out of scope** as an approval/credential-gated channel unless it is later opened.

### Not in the open data catalog
WW3-MEDITA is **absent from the ItaliaMeteo CKAN portal** (`dati.agenziaitaliameteo.it`) — a `package_search` for `ww3` returns only an incidental keyword match on the BOLAM/MOLOCH dataset. The CC BY 4.0 declaration therefore rests on the MeteoHub dataset card and platform licence page rather than on a formal open-data catalog record. This is the same pattern noted for MOLOCH_AIM.

---

## Metadata defects (live-verified)

Several attribute problems are worth knowing about before trusting the files' self-description:

- **No `units` attribute anywhere** in the distribution (see above).
- **`globwave_name` is wrong on three radiation-stress variables:**
  - `sxx` → `wave_from_direction` (copied from `dir`)
  - `syy` → `northward_sea_water_velocity`
  - `sxy` → empty string

  The `long_name` and `standard_name` attributes on these three are correct; only `globwave_name` is corrupted. Do not key variable identification off `globwave_name`.
- **`t01` carries `standard_name = sea_surface_wind_wave_mean_period_from_variance_spectral_density_first_frequency_moment`** — the `wind_wave` component of that name is misleading. WW3's `t01` is the mean period of the **total** spectrum, not the wind-sea partition.
- **The `comment` attributes are misplaced.** `uuss` carries `uss=sqrt(uuss**2+vuss**2)` while `vuss` carries the spectral frequency range `0.0500 to 0.7932 Hz` — the frequency-range comment evidently belongs to the pair, not to the meridional component alone.
- **`valid_min`/`valid_max` are scaled-integer bounds applied to float data.** E.g. `hs` has `valid_max = 32000` while the data are `float32` metres (observed max ≈ 3.1 m). These bounds are inherited from a packed-integer GLOBWAVE encoding that is not in use here, and will reject nothing if applied literally.
- **`MAPSTA` carries no information in this distribution** — all 220,888 values are `1` (active sea point). It is distributed for format completeness; there are no excluded, boundary or land-flagged nodes to mask.

---

## Working with the unstructured mesh

The mesh is described by three files that must be read alongside any field file:

- `latitude` / `longitude` — per-node coordinates, `(node,)`, float32
- `tri` — triangulation connectivities, `(element, 3)`, int32

**`tri` uses 1-based indexing** (verified: minimum 1, maximum 220888 = node count). Python and most plotting libraries expect 0-based indices, so subtract 1:

```python
import netCDF4, numpy as np
import matplotlib.tri as mtri

base = "https://meteohub.agenziaitaliameteo.it/nwp/ww3/2026072800"
lat = netCDF4.Dataset(f"{base}/LATITUDE/ww3_2026072800_surface-0.nc")["latitude"][:]
lon = netCDF4.Dataset(f"{base}/LONGITUDE/ww3_2026072800_surface-0.nc")["longitude"][:]
tri = netCDF4.Dataset(f"{base}/TRI/ww3_2026072800_surface-0.nc")["tri"][:] - 1   # 1-based -> 0-based

ds = netCDF4.Dataset(f"{base}/HS/ww3_2026072800_surface-0.nc")
hs = ds["hs"][:]            # (96, 220888) — index 23 is T+0, not index 0
mesh = mtri.Triangulation(lon, lat, tri)
```

Field variables are indexed by mesh node, not by `(i, j)`. Standard structured-grid tooling will not work on these files without first reconstructing the mesh. Because the files are NetCDF-4/HDF5 and the server supports range requests, `h5py` over `fsspec` can be used to read headers and time slices without downloading the full ~60 MB per parameter.

---

## Relationship to other Mediterranean wave products
The Mediterranean is covered by several operational wave models with different strengths. WW3-MEDITA is distinct from each:

- **[MEDWAM](../greece/medwam.md)** — Copernicus Marine product (HCMR, Greece; WAM Cycle 6 at 1/24° ~4.6 km, structured grid, multi-satellite altimetric data assimilation, two-way coupled to Mediterranean ocean physics, 10-day forecast, 2× daily). MEDWAM is higher-frequency, longer-range, data-assimilating, and ocean-coupled, but uses a uniform structured grid that cannot resolve sub-kilometre coastal features.
- **[WW3-MARO](../france/ww3-maro-france.md)** — Météo-France's WAVEWATCH III Mediterranean product distributed via data.gouv.fr (~0.10° structured grid, AROME-forced, ~72-hour forecast, 2× daily). Same model family as WW3-MEDITA but a structured grid at coarser resolution.
- **WW3-MEDITA (this entry)** — Italian WW3 product on an unstructured mesh designed specifically to refine down to 150–200 m along the Italian coast. No documented data assimilation, no swell partitioning, single daily cycle. Its niche is high-resolution coastal wave detail in Italian waters, not basin-wide consistency or assimilation-based accuracy.

Users needing data-assimilating, long-range, or partitioned wave products should look to MEDWAM. Users needing the highest-resolution coastal wave guidance for Italian waters specifically should look to WW3-MEDITA.

The model is also part of the same operational ecosystem as the [ICON-2I atmospheric model](../../../nwp_models/regional/italy/icon-2i.md), which provides the high-resolution component of its wind forcing.

For unstructured-mesh handling, the closest analogue elsewhere in this repository is NOAA's [GLWU](../usa/glwu.md), whose native NetCDF distribution is also node-indexed.

---

## Notes
- The use of an unstructured triangular mesh is the defining technical feature of WW3-MEDITA. Most operational regional wave models in this repository use structured (regular lat-lon or rotated) grids, with multi-resolution behaviour achieved through nesting (e.g., GWAM → EWAM → CWAM in DWD's chain) or refined-cell schemes (e.g., the SMC grid used by AMM15-WW3). WW3-MEDITA's continuous-resolution mesh avoids the artefacts that can arise at nest boundaries.
- The three headline gotchas for new users, in order of how much damage they cause: the **96-step T−23 h time axis**, the **~3-day real retention** behind a 9-directory listing, and the **identical filenames across all 16 parameter directories**.
- Operational since October 2025; the previous Mediterranean wave product in the same operational chain was SWAN-MEDITARE, based on the SWAN nearshore wave model.

---

## Official documentation
- ItaliaMeteo product page: https://www.agenziaitaliameteo.it/en/sea/forecasts/ww3-medita/
- ItaliaMeteo marine forecasts overview: https://www.agenziaitaliameteo.it/en/sea/forecasts/
- ItaliaMeteo announcement: https://www.agenziaitaliameteo.it/en/agenzia/news-e-gallery/news/ww3-medita-operativo-il-nuovo-modello-per-la-previsione-dello-stato-del-mare/
- Arpae marine forecasts: https://www.arpae.it/it/temi-ambientali/mare/previsioni-mare
- WAVEWATCH III: https://polar.ncep.noaa.gov/waves/wavewatch/

---

*Live-verified against cycles 2026072600 / 2026072700 / 2026072800 on 2026-07-28.*
