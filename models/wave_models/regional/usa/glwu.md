# GLWU (Great Lakes Wave Unstructured) – NOAA/NCEP

## What this model is
GLWU is NOAA/NCEP's operational deterministic wave forecast system for the Great Lakes and Lake Champlain, providing wave guidance to National Weather Service Great Lakes Weather Forecast Offices and the public. It is built on the third-generation spectral wave model **WAVEWATCH III (WW3)**, run on an **unstructured triangular mesh** whose resolution ranges from ~2.5 km in deep water to ~250 m near the coast, resolving the lakes and their connecting waters.

It is the U.S. counterpart to the Great Lakes domains of Canada's [RDWPS](../canada/rdwps-canada.md), but differs in three ways: it uses an unstructured mesh (rather than RDWPS's structured ~1 km per-lake grids), it cycles hourly (24× daily rather than 4×), and it also covers Lake Champlain.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** The Great Lakes (including connecting channels) and Lake Champlain
- **Domain details (live-verified 2026-07-25, grid headers decoded):**
  - **Great Lakes** — distributed on two regridded GRIB2 grids: a Lambert-conformal 2.5 km grid (`grlc_2p5km`, 581×361, from ~92.6°W/41.5°N) and a regular lat-lon 500 m grid (`grlr_500m`, 3436×2338, 92.7°W–75.5°W, 41.2°N–49.3°N, 0.005°×0.0035°)
  - **Lake Champlain** — the `_lc` products, on the same two grid types at much smaller extent (2.5 km Lambert 31×91 near 74°W/43.6°N; 500 m regular lat-lon 92×571, ~73.5°W, 43.5°N–45.5°N)
  - Native model output is on the underlying unstructured mesh (distributed as NetCDF)

---

## Basic details
- **Model type:** Deterministic wave model
- **Grid system:** Unstructured triangular mesh (native); distributed as regridded structured grids (2.5 km Lambert conformal, 500 m regular lat-lon) plus the native mesh in NetCDF
- **Core wave model:** WAVEWATCH III (WW3)
- **Horizontal resolution:** ~2.5 km (deep water) to ~250 m (nearshore) on the native mesh; distributed at 2.5 km and 500 m
- **Forecast length:** 48 h (short-range cycles) and 149 h (long-range cycles at 01, 07, 13, 19 UTC)
- **Update frequency / cycles:** 24× daily (hourly runs) — 20 short-range (48 h) and 4 long-range (01/07/13/19 UTC, 149 h)
- **Temporal output resolution:** Hourly (live-verified: 48 steps short-range, 149 steps long-range)

---

## Forcing and nesting
- **Wind forcing:** 10 m winds (the driving atmospheric source is not stated in the NOMADS product description; the forcing winds `WIND`/`WDIR`/`UGRD`/`VGRD` are embedded in the output). **TBD** — driving atmospheric model not verified.
- **Ice forcing:** TBD (not documented in the provided description)
- **Current forcing:** None documented
- **Nested inside / parent for:** The Great Lakes and Lake Champlain are closed basins, so no external wave boundary conditions are needed.

---

## Data assimilation
- **Assimilates wave observations:** TBD — no wave data assimilation is documented; GLWU is understood to be a forced wave system, but this is not verified here.

---

## What it provides
Wave forecasts on the GRIB2 grids, 19 fields per step (live-verified, `grlc_2p5km_sr` 12 UTC run):
- Surface wind: wind speed (`WIND`), direction (`WDIR`), u/v components (`UGRD`, `VGRD`)
- Combined sea state: significant height (`HTSGW`)
- Primary wave system: mean period (`PERPW`), direction (`DIRPW`)
- Wind-wave partition: significant height (`WVHGT`), mean period (`WVPER`), direction (`WVDIR`)
- Swell partitions 1–3: significant height (`SWELL`), mean period (`SWPER`), direction (`SWDIR`)

The native NetCDF output (`glwu.glwu`, `glwu.glwu_lc`) carries the corresponding cycle's forecast on the unstructured mesh.

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent). Distributed via NOAA/NCEP; NOAA requests attribution and prohibits implying NOAA endorsement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (regridded 2.5 km / 500 m products, each with a `.idx` sidecar) and NetCDF-4/HDF5 (native unstructured mesh)
- **File naming:** `glwu.YYYYMMDD/glwu.{product}.tCCz.{grib2|nc}`, where `{product}` ∈ {`glwu`, `glwu_lc` (NetCDF); `grlc_2p5km`, `grlc_2p5km_sr`, `grlr_500m`, `grlc_2p5km_lc`, `grlc_2p5km_lc_sr`, `grlr_500m_lc` (GRIB2)}. The `_sr` suffix marks the 48 h short-range product; no suffix on the 2.5 km grid marks the 149 h long-range product (present only at 01/07/13/19 UTC); `_lc` marks the Lake Champlain domain.
- **Official download location:**
  - **NOMADS** (real-time; ~2-day rolling window):
    https://nomads.ncep.noaa.gov/pub/data/nccf/com/glwu/prod/
  - NCEP production FTP (ftpprd) mirrors the same production tree.
- **Not on cloud mirrors:** GLWU is **not** carried by the NODD AWS/Azure/GCP mirrors (live-verified: AWS/Azure paths 404, no GLWU S3 bucket). This is a genuine NOMADS-only system, unlike [GFS-Wave](../global/usa/ww3-noaa.md) and [GEFS-Wave](../global/usa/gefs-wave.md).

---

## Notes
- **`_lc` = Lake Champlain, not a Great Lakes variant.** The `_lc` products cover Lake Champlain (~44°N, 73.5°W), a separate small domain distributed on the same two grid types as the Great Lakes. Confirmed from decoded grid bounds.
- **Two regridded distributions + native mesh.** The GRIB2 products are structured regriddings of the underlying unstructured mesh: `grlc_2p5km` (Lambert conformal, 2.5 km) and `grlr_500m` (regular lat-lon, 500 m). The native mesh is distributed only in NetCDF (`glwu.glwu`, `glwu.glwu_lc`). Working with the native output requires unstructured-grid NetCDF handling (mesh node/element variables rather than i/j indices), as with [WW3-MEDITA](../italy/ww3-medita.md).
- **500 m grid is short-range only.** The 500 m products (`grlr_500m`, `grlr_500m_lc`) are produced to 48 h at every cycle; the 149 h long-range forecast is distributed only on the 2.5 km grid, at the four long-range cycles (01/07/13/19 UTC).
- **Short retention.** NOMADS carries only ~2 days of GLWU cycles, and there is no cloud archive — users needing history must harvest cycles as they are produced.
- **Relationship to other systems:**
  - Canadian counterpart: [RDWPS](../canada/rdwps-canada.md) (Great Lakes domains) and its ensemble sibling [REWPS](../canada/rewps-canada.md).
  - Same WW3 lineage as the U.S. global wave systems [GFS-Wave](../global/usa/ww3-noaa.md) and [GEFS-Wave](../global/usa/gefs-wave.md), but regional and unstructured rather than global.

---

## Official documentation
- GLWU / NCEP marine modeling: https://polar.ncep.noaa.gov/waves/
- NOMADS product access: https://nomads.ncep.noaa.gov/
