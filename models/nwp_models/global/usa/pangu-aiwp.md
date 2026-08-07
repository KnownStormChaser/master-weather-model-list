# Pangu-Weather (AIWP reforecast archive)

## What this model is
Pangu-Weather is a deterministic, data-driven (AI) global weather model that emulates medium-range forecasts using a 3D transformer trained on ERA5, rather than solving physical equations. This entry documents the **NOAA/CIRA AI Weather Prediction (AIWP) reforecast archive** of Pangu-Weather — a near-real-time and retrospective run of the model initialized from operational analyses and distributed as open gridded output on AWS.

The archive runs the **upstream research weights unmodified**. It is a research archive rather than an operational forecast product, and is distinct from Pangu-Weather in its original research form, which has no public forecast data of its own.

---

## Who runs it
- **Organization:** Cooperative Institute for Research in the Atmosphere (CIRA, Colorado State University) and NOAA Global Systems Laboratory (NOAA-GSL), distributed via NOAA Open Data Dissemination (NODD). Underlying model developed by Huawei Cloud (Bi et al., 2023).
- **Country / region:** United States (distributor); model origin China (Huawei)

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Regular 0.25° latitude–longitude grid, 1440 × 721 points (90°N→90°S, 0°→359.75°E)

---

## Basic details
- **Model type:** Deterministic NWP (AI / machine-learning emulator)
- **Model system / core:** Pangu-Weather — 3D Earth-Specific Transformer (3DEST), trained on ERA5 0.25° reanalysis. NetCDF `model_name` attribute: `panguweather`.
- **Dynamical formulation:** Not applicable (data-driven ML model; no dynamical core)
- **Convection-allowing:** No (~0.25°, ≈28 km)
- **Horizontal resolution:** 0.25° (~28 km)
- **Grid dimensions:** 1440 × 721
- **Vertical levels:** 13 pressure levels (output): 1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa — read directly from the NetCDF `level` coordinate
- **Model top:** Highest output level 50 hPa (AI model; no physical model top)
- **Forecast length:** 240 h (10 days)
- **Update frequency / cycles:** 2× daily (00, 12 UTC) — **but see the cadence change and GFS-stream reliability notes below**
- **Temporal output resolution:** 6 h (forecast hours 000–240, 41 steps per cycle)
- **Archive processing version:** `3_2025-02-20` (NetCDF global attribute `version`; an archive-wide processing version shared identically by all four AIWP models, not a Pangu-Weather model version)

---

## Data assimilation
- **Data assimilation:** No — the model does not run its own assimilation; it ingests an external operational analysis as the initial state.

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** Two parallel streams — **NOAA GFS analysis** (directories with no suffix) and **ECMWF IFS analysis** (directories ending in `_IFS`)
- **Boundary conditions:** None (global model)

---

## What it provides

Deterministic forecasts, **69 fields per step** (verified from NetCDF headers, 2026-08-05 12 UTC, both streams):

- **Pressure-level fields on all 13 levels (5 variables):** geopotential (`z`), specific humidity (`q`), temperature (`t`), u wind (`u`), v wind (`v`)
- **Surface / near-surface (4 variables):** mean sea level pressure (`msl`), 2-m temperature (`t2`), 10-m u/v wind (`u10`, `v10`)

All fields are `float32`, chunked one horizontal slice at a time (`(1, 1, 721, 1440)` for pressure-level fields), gzip level 4.

> Pangu-Weather and [Aurora](./aurora-aiwp.md) carry **identical field sets** — the same five pressure-level variables and the same four surface variables — which makes them the cleanest pair in the archive for like-for-like architecture comparison. Neither carries vertical velocity or precipitation; only [GraphCast](./graphcast-aiwp.md) provides those. [FourCastNet](./fourcastnet-aiwp.md) carries relative rather than specific humidity, so humidity comparisons against it require a conversion.

---

## Data availability
- **Is the data free?** Yes
- **License:** Open — NODD states "no restrictions on the use of this data." Effectively open/public-domain distribution. (Note: the *output data* is unrestricted; the underlying Pangu-Weather **code/weights** carry their own upstream license, which does not restrict reuse of this derived output.)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.8), one `.nc` file per cycle spanning f000–f240. **~4.60 GB per cycle** on the GFS stream and **~2.78 GB** on the IFS stream (see the size-asymmetry note below).
- **Official download location:**
  `s3://noaa-oar-mlwp-data/PANG_v100_GFS/` and `s3://noaa-oar-mlwp-data/PANG_v100_IFS/` (anonymous S3, us-east-1)
  Browse: https://noaa-oar-mlwp-data.s3.amazonaws.com/index.html
  Registry: https://registry.opendata.aws/aiwp/

---

## Notes
- **Period of record (verified live 2026-08-06):** GFS-initialized **2020-09-30 → present**; IFS-initialized **2022-01-01 → present**. The registry gives the GFS start as "10/2020"; the first directory is in fact `2020/0930`, carrying both the 00 and 12 UTC initializations of that day. Start dates are chosen to avoid overlap with Pangu-Weather's training and fine-tuning period.
- **File / path convention:** `PANG_v100_III/YYYY/mmdd/PANG_v100_III_YYYYmmddhh_f000_f240_06.nc`, where `III` is `GFS` or `IFS`. Only a `v100` version exists — there is no second Pangu version stream in the bucket.

> ⚠️ **The GFS-initialized stream is currently unreliable; the IFS stream is not.** Over 2026-07-30 → 2026-08-06, the IFS stream carried all 16 expected cycles while the GFS stream carried 9, with **2026-08-01 missing entirely** and most days producing a single cycle instead of two. The identical gap pattern appears across *every* model in the bucket — [Aurora](./aurora-aiwp.md), [GraphCast](./graphcast-aiwp.md), and [FourCastNet](./fourcastnet-aiwp.md) are missing exactly the same cycles — so this is an upstream GFS initial-condition ingest problem, not a Pangu-specific one. The registry warns that "data may be missing and is not guaranteed to be available at any given time," but does not indicate that the two streams differ in reliability. **Anyone needing complete coverage should prefer the IFS stream or verify per-cycle presence rather than assuming a regular 2×/day series.**

> ⚠️ **Cadence changed from 4× to 2× daily around the end of 2023.** Verified: 2023-12-01 carries 00/06/12/18 UTC initializations, 2024-01-01 carries only 00/12. The change appears archive-wide and permanent. The registry's path specification still documents `hh` as "00, 06, 12, or 18", which is correct for the historical record but misleading for anything after 2023. Retrospective studies spanning the boundary will see the sample rate halve mid-record.

- **GFS-stream files are ~1.65× larger than IFS-stream files for identical content.** Both streams carry the same variables, shapes, chunking, and gzip level, so the uncompressed sizes are identical (~4.60 GB vs ~2.78 GB compressed). The two are produced in different environments — the GFS files report `netcdf=4.9.4-development, hdf5=1.14.2`, the IFS files `netcdf=4.9.3, hdf5=1.14.6` — and creation timestamps cluster several hours apart. The most likely explanation is a difference in low-order float entropy (e.g. bit-rounding applied on one path but not the other) rather than a content difference, but this has not been confirmed (**TBD**). Storage estimates should be taken per stream.
- **Part of the AIWP archive** alongside [Aurora](./aurora-aiwp.md), [FourCastNet](./fourcastnet-aiwp.md) (v1/v2), and [GraphCast](./graphcast-aiwp.md), all sharing this bucket, format, cadence, and license. The archive is described in Radford et al. (2025, BAMS).
- This is an **AI/ML model** — recorded in [`AI_MODELS.md`](../../../../AI_MODELS.md).
- Real-time visualizations: https://aiweather.cira.colostate.edu
- Contact / data manager: Dr. Jacob Radford (jacob.radford@noaa.gov); NODD: nodd@noaa.gov

---

## Relationship to other models

### Within the AIWP archive
Sibling entries: [Aurora](./aurora-aiwp.md), [GraphCast](./graphcast-aiwp.md), [FourCastNet](./fourcastnet-aiwp.md). All four share the bucket, the NetCDF-4 layout, the 0.25°/13-level grid, the 240 h / 6-hourly output structure, the dual GFS/IFS initialization, and the open licence — they differ in architecture, period of record, and distributed field set. Pangu-Weather and Aurora are the only pair with identical field sets.

### Distinct from operational AI systems
Unlike NOAA's [AIGFS](./aigfs.md) or ECMWF's [AIFS Single](../eu/aifs-single.md), this archive is **not an operational forecast product**. It runs unmodified research weights, is not fine-tuned on the initializing centre's own analyses, carries no service-change notices or operational support commitments, and offers no availability guarantee. It should be treated as a research dataset.

---

## Official documentation
- https://registry.opendata.aws/aiwp/
- https://noaa-oar-mlwp-data.s3.amazonaws.com/README.txt
- BAMS paper: Radford et al., 2025, *Bull. Amer. Meteor. Soc.*, 106, E68–E76, https://doi.org/10.1175/BAMS-D-24-0057.1
- Model reference: Bi et al., 2023, *Nature* 619, 533–538, https://www.nature.com/articles/s41586-023-06185-3 — code: https://github.com/198808xc/Pangu-Weather
