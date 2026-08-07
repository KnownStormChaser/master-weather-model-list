# GraphCast (AIWP reforecast archive)

## What this model is
GraphCast is a deterministic, data-driven (AI) global weather model that emulates medium-range forecasts using a graph neural network trained on ERA5, rather than solving physical equations. This entry documents the **NOAA/CIRA AI Weather Prediction (AIWP) reforecast archive** of GraphCast — a near-real-time and retrospective run of the model initialized from operational analyses and distributed as open gridded output on AWS.

> **This is not AIGFS.** NOAA/NCEP separately operates [AIGFS](./aigfs.md), an operational GraphCast-lineage model fine-tuned on NOAA's own GDAS analyses and distributed as GRIB2 on NOMADS. The AIWP archive runs the *upstream research* GraphCast weights, unmodified, and is a research archive rather than an operational forecast product. The two are different datasets from different organizations with different formats, cadences, and purposes. See [Relationship to other models](#relationship-to-other-models).

---

## Who runs it
- **Organization:** Cooperative Institute for Research in the Atmosphere (CIRA, Colorado State University) and NOAA Global Systems Laboratory (NOAA-GSL), distributed via NOAA Open Data Dissemination (NODD). Underlying model developed by Google DeepMind (Lam et al., 2023).
- **Country / region:** United States (distributor); model origin United States / United Kingdom (Google DeepMind)

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Regular 0.25° latitude–longitude grid, 1440 × 721 points (90°N→90°S, 0°→359.75°E)

---

## Basic details
- **Model type:** Deterministic NWP (AI / machine-learning emulator)
- **Model system / core:** GraphCast — encode–process–decode graph neural network on a multi-mesh icosahedral representation, ~37M parameters, trained on ERA5 0.25° reanalysis. The archive runs the "GraphCast Operational" configuration.
- **Dynamical formulation:** Not applicable (data-driven ML model; no dynamical core)
- **Convection-allowing:** No (~0.25°, ≈28 km)
- **Horizontal resolution:** 0.25° (~28 km)
- **Grid dimensions:** 1440 × 721
- **Vertical levels:** 13 pressure levels (output): 1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa
- **Model top:** Highest output level 50 hPa (AI model; no physical model top)
- **Forecast length:** 240 h (10 days)
- **Update frequency / cycles:** 2× daily (00, 12 UTC) — **but see the cadence change and GFS-stream reliability notes below**
- **Temporal output resolution:** 6 h (forecast hours 000–240, 41 steps per cycle)
- **Archive processing version:** `3_2025-02-20` (NetCDF global attribute `version`; an archive-wide processing version shared with the other AIWP models, not a GraphCast model version)

---

## Data assimilation
- **Data assimilation:** No — the model does not run its own assimilation; it ingests an external operational analysis as the initial state.

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** Two parallel streams — **NOAA GFS analysis** (directories with no suffix) and **ECMWF IFS analysis** (directories ending in `_IFS`)
- **Boundary conditions:** None (global model)

---

## What it provides

Deterministic forecasts, **83 fields per step** (verified from NetCDF headers, 2026-08-05 12 UTC, both streams):

- **Pressure-level fields on all 13 levels (6 variables):** geopotential (`z`), specific humidity (`q`), temperature (`t`), u wind (`u`), v wind (`v`), **vertical velocity (`w`)**
- **Surface / near-surface (5 variables):** 10-m u/v wind (`u10`, `v10`), 2-m temperature (`t2`), mean sea level pressure (`msl`), **6-hourly accumulated precipitation (`apcp`)**

All fields are `float32`, chunked one horizontal slice at a time (`(1, 1, 721, 1440)` for pressure-level fields), gzip level 4.

> GraphCast is the **richest of the AIWP models** in field coverage. It is the only one in the archive carrying vertical velocity, and the only one carrying precipitation. Users needing `w` or `apcp` from this bucket have no alternative model to fall back on — [Pangu-Weather](./pangu-aiwp.md), [Aurora](./aurora-aiwp.md), and [FourCastNet v2](./fourcastnet-aiwp.md) all omit both.

---

## Data availability
- **Is the data free?** Yes
- **License:** Open — NODD states "no restrictions on the use of this data." Effectively open/public-domain distribution. (Note: the *output data* is unrestricted; GraphCast's upstream **code/weights** carry their own license, which does not restrict reuse of this derived output.)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.8), one `.nc` file per cycle spanning f000–f240. Files are large — **~5.7 GB per cycle** on the GFS stream and **~3.9 GB** on the IFS stream (see the size-asymmetry note below).
- **Official download location:**
  `s3://noaa-oar-mlwp-data/GRAP_v100_GFS/` and `s3://noaa-oar-mlwp-data/GRAP_v100_IFS/` (anonymous S3, us-east-1)
  Browse: https://noaa-oar-mlwp-data.s3.amazonaws.com/index.html
  Registry: https://registry.opendata.aws/aiwp/

---

## Notes
- **Period of record (verified live 2026-08-06):** GFS-initialized **2021-12-31 → present**; IFS-initialized **2022-01-01 → present**. The registry describes the GraphCast record as starting 01/2022; the GFS stream's first directory is in fact `2021/1231`, holding the 12 and 18 UTC initializations of that day. Start dates are chosen to avoid overlap with GraphCast's training and fine-tuning period.
- **File / path convention:** `GRAP_v100_III/YYYY/mmdd/GRAP_v100_III_YYYYmmddhh_f000_f240_06.nc`, where `III` is `GFS` or `IFS`.

> ⚠️ **The GFS-initialized stream is currently unreliable; the IFS stream is not.** Over 2026-07-30 → 2026-08-06, the IFS stream carried all 16 expected cycles while the GFS stream carried 9, with **2026-08-01 missing entirely** and most days producing a single cycle instead of two. The identical gap pattern appears across *every* model in the bucket — [Pangu](./pangu-aiwp.md), [Aurora](./aurora-aiwp.md), and [FourCastNet v2](./fourcastnet-aiwp.md) are missing exactly the same cycles — so this is an upstream GFS initial-condition ingest problem, not a GraphCast-specific one. The registry warns that "data may be missing and is not guaranteed to be available at any given time," but does not indicate that the two streams differ in reliability. **Anyone needing complete coverage should prefer the IFS stream or verify per-cycle presence rather than assuming a regular 2×/day series.**

> ⚠️ **Cadence changed from 4× to 2× daily around the end of 2023.** Verified: 2023-12-01 carries 00/06/12/18 UTC initializations, 2024-01-01 carries only 00/12. The change appears archive-wide and permanent. The registry's path specification still documents `hh` as "00, 06, 12, or 18", which is correct for the historical record but misleading for anything after 2023. Retrospective studies spanning the boundary will see the sample rate halve mid-record.

- **GFS-stream files are ~1.5× larger than IFS-stream files for identical content.** Both streams carry the same variables, shapes, chunking, and gzip level, so the uncompressed sizes are identical (~5.7 GB vs ~3.9 GB compressed). The two are produced in different environments — the GFS files report `netcdf=4.9.4-development, hdf5=1.14.2`, the IFS files `netcdf=4.9.3, hdf5=1.14.6` — and creation timestamps cluster several hours apart. The most likely explanation is a difference in low-order float entropy (e.g. bit-rounding applied on one path but not the other) rather than a content difference, but this has not been confirmed (**TBD**). It does mean storage estimates should be taken per stream.
- Historical files are substantially larger still: the earliest GFS cycles (2021-12-31) are ~9.3 GB each, against ~5.7 GB today. Whether this reflects a compression or precision change is unconfirmed (**TBD**).
- **Part of the AIWP archive** alongside [Pangu-Weather](./pangu-aiwp.md), [Aurora](./aurora-aiwp.md), and [FourCastNet](./fourcastnet-aiwp.md), all sharing this bucket, format, cadence, and license. The archive is described in Radford et al. (2025, BAMS).
- This is an **AI/ML model** — recorded in [`AI_MODELS.md`](../AI_MODELS.md).
- Real-time visualizations: https://aiweather.cira.colostate.edu
- Contact / data manager: Dr. Jacob Radford (jacob.radford@noaa.gov); NODD: nodd@noaa.gov

---

## Relationship to other models

### Distinct from NOAA/NCEP's GraphCast productionizations
This archive runs the **upstream research GraphCast weights unmodified**. It should not be confused with either of NCEP's own GraphCast-lineage systems:

- **[AIGFS](./aigfs.md)** (NOAA/NCEP, **operational**) — GraphCast fine-tuned on NOAA GDAS analyses, GRIB2 on NOMADS, 4× daily, 384 h. This is an operational forecast product; the AIWP archive is not.
- **GraphCastGFS** (NOAA/NCEP, experimental, **ended 2026-05-05**) — the experimental predecessor to AIGFS, distributed from the separate `noaa-nws-graphcastgfs-pds` bucket. That stream stopped producing forecasts on 2026-05-05 (final day was a partial, 00 UTC only). The AIWP GraphCast archive is **not** a continuation of it — different operator, different weights, different format, different bucket.
- **[GEML](../canada/gdps-geml.md)** (ECCC, experimental) — the Canadian GraphCast productionization, fine-tuned on ERA5 + ECMWF HRES.

### Within the AIWP archive
Sibling entries: [Pangu-Weather](./pangu-aiwp.md), [Aurora](./aurora-aiwp.md), [FourCastNet](./fourcastnet-aiwp.md). All four share the bucket, the NetCDF-4 layout, the 0.25°/13-level grid, the 240 h / 6-hourly output structure, the dual GFS/IFS initialization, and the open licence — they differ in architecture, period of record, and distributed field set.

---

## Official documentation
- https://registry.opendata.aws/aiwp/
- https://noaa-oar-mlwp-data.s3.amazonaws.com/README.txt
- BAMS paper: Radford et al., 2025, *Bull. Amer. Meteor. Soc.*, 106, E68–E76, https://doi.org/10.1175/BAMS-D-24-0057.1
- Model reference: Lam et al., 2023, *Science* 382(6677), 1416–1421, https://doi.org/10.1126/science.adi2336 — code: https://github.com/google-deepmind/graphcast
