# Aurora (AIWP reforecast archive)

## What this model is
Aurora is a deterministic, data-driven (AI) foundation model for the Earth system, run here in its 0.25° medium-range weather configuration to emulate global forecasts. This entry documents the **NOAA/CIRA AI Weather Prediction (AIWP) reforecast archive** of Aurora — a near-real-time and retrospective run initialized from operational analyses and distributed as open gridded output on AWS.

The archive runs the **upstream research weights unmodified**. It is a research archive rather than an operational forecast product, and is distinct from Aurora in its original research form, which has no public forecast data of its own.

---

## Who runs it
- **Organization:** CIRA (Colorado State University) and NOAA Global Systems Laboratory (NOAA-GSL), distributed via NOAA Open Data Dissemination (NODD). Underlying model developed by Microsoft Research AI for Science (Bodnar et al., 2025).
- **Country / region:** United States (distributor); model origin United States (Microsoft)

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Regular 0.25° latitude–longitude grid, 1440 × 721 points (90°N→90°S, 0°→359.75°E)

---

## Basic details
- **Model type:** Deterministic NWP (AI / machine-learning foundation model)
- **Model system / core:** Aurora — 3D Swin Transformer U-Net with a Perceiver-style encoder/decoder, pretrained on multiple geophysical datasets and fine-tuned for 0.25° weather forecasting. NetCDF `model_name` attribute: `aurora`.
- **Dynamical formulation:** Not applicable (data-driven ML model; no dynamical core)
- **Convection-allowing:** No (~0.25°, ≈28 km)
- **Horizontal resolution:** 0.25° (~28 km)
- **Grid dimensions:** 1440 × 721
- **Vertical levels:** 13 pressure levels (output): 1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa — read directly from the NetCDF `level` coordinate
- **Model top:** Highest output level 50 hPa (AI model; no physical model top)
- **Forecast length:** 240 h (10 days)
- **Update frequency / cycles:** 2× daily (00, 12 UTC) — **but see the cadence change and GFS-stream reliability notes below**
- **Temporal output resolution:** 6 h (forecast hours 000–240, 41 steps per cycle)
- **Archive processing version:** `3_2025-02-20` (NetCDF global attribute `version`). **This is an archive-wide processing version, not an Aurora model version** — the identical string appears in the Pangu-Weather, GraphCast, and FourCastNet files. It carries no information about which Aurora release produced the forecasts.

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
- **Surface / near-surface (4 variables):** 10-m u/v wind (`u10`, `v10`), 2-m temperature (`t2`), mean sea level pressure (`msl`)

All fields are `float32`, chunked one horizontal slice at a time (`(1, 1, 721, 1440)` for pressure-level fields), gzip level 4.

> Aurora and [Pangu-Weather](./pangu-aiwp.md) carry **identical field sets** — the same five pressure-level variables and the same four surface variables — which makes them the cleanest pair in the archive for like-for-like architecture comparison. Neither carries vertical velocity or precipitation; only [GraphCast](./graphcast-aiwp.md) provides those. [FourCastNet](./fourcastnet-aiwp.md) carries relative rather than specific humidity, so humidity comparisons against it require a conversion.
>
> Note that this is a **narrow slice of what Aurora can produce**. The published Aurora foundation model covers air quality, ocean waves, and tropical cyclone tracks in addition to atmospheric forecasting; this archive contains only the 0.25° deterministic weather configuration's core fields.

---

## Data availability
- **Is the data free?** Yes
- **License:** Open — NODD states "no restrictions on the use of this data." Effectively open/public-domain distribution. (Note: the *output data* is unrestricted; Aurora's upstream **code/weights** carry their own license, which does not restrict reuse of this derived output.)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.8), one `.nc` file per cycle spanning f000–f240. Files are large — **~4.55 GB per cycle** on the GFS stream and **~2.72 GB** on the IFS stream (see the size-asymmetry note below).
- **Official download location:**
  `s3://noaa-oar-mlwp-data/AURO_v100_GFS/` and `s3://noaa-oar-mlwp-data/AURO_v100_IFS/` (anonymous S3, us-east-1)
  Browse: https://noaa-oar-mlwp-data.s3.amazonaws.com/index.html
  Registry: https://registry.opendata.aws/aiwp/

---

## Notes
- **Undocumented in the source documentation.** Aurora is present and updating in the bucket but is not listed on the AIWP registry page or in the bucket `README.txt`. The registry page was re-checked on **2026-08-06** and still names only FourCastNetv2-small, Pangu-Weather, and GraphCast. Everything in this entry comes from live bucket inspection and NetCDF headers, not vendor documentation.
- **Period of record (verified live 2026-08-06):** GFS-initialized **2025-01-22 → present**; IFS-initialized **2025-01-23 → present**. This is a substantially shorter record than the other AIWP models, which reach back to 2020–2022. The first GFS day carries only the 12 UTC initialization; the first IFS day carries both 00 and 12 UTC.
- **File / path convention:** `AURO_v100_III/YYYY/mmdd/AURO_v100_III_YYYYmmddhh_f000_f240_06.nc`, where `III` is `GFS` or `IFS`. Only a `v100` version exists.

> ⚠️ **The GFS-initialized stream is currently unreliable; the IFS stream is not.** Over 2026-07-30 → 2026-08-06, the IFS stream carried all 16 expected cycles while the GFS stream carried 9, with **2026-08-01 missing entirely** and most days producing a single cycle instead of two. The identical gap pattern appears across *every* model in the bucket — [Pangu](./pangu-aiwp.md), [GraphCast](./graphcast-aiwp.md), and [FourCastNet](./fourcastnet-aiwp.md) are missing exactly the same cycles — so this is an upstream GFS initial-condition ingest problem, not an Aurora-specific one. The registry warns that "data may be missing and is not guaranteed to be available at any given time," but does not indicate that the two streams differ in reliability. **Anyone needing complete coverage should prefer the IFS stream or verify per-cycle presence rather than assuming a regular 2×/day series.**

> ⚠️ **Cadence changed from 4× to 2× daily around the end of 2023.** Verified: 2023-12-01 carries 00/06/12/18 UTC initializations, 2024-01-01 carries only 00/12. The change appears archive-wide and permanent. Aurora's record begins in 2025 and so falls entirely within the 2×/day era — this note matters only when comparing Aurora against the longer-record AIWP models across the boundary, where their sample rate halves and Aurora's does not change.

- **GFS-stream files are ~1.67× larger than IFS-stream files for identical content.** Both streams carry the same variables, shapes, chunking, and gzip level, so the uncompressed sizes are identical (~4.55 GB vs ~2.72 GB compressed). The two are produced in different environments — the GFS files report `netcdf=4.9.4-development, hdf5=1.14.2`, the IFS files `netcdf=4.9.3, hdf5=1.14.6` — and creation timestamps cluster several hours apart. The most likely explanation is a difference in low-order float entropy (e.g. bit-rounding applied on one path but not the other) rather than a content difference, but this has not been confirmed (**TBD**). Storage estimates should be taken per stream.
- This archive is the **deterministic base Aurora 0.25° weather model**. Microsoft's newer **Aurora 1.5** (probabilistic ensemble, hourly output, additional variables) is a separate release and is *not* what this archive contains.
- **Part of the AIWP archive** alongside [Pangu-Weather](./pangu-aiwp.md), [FourCastNet](./fourcastnet-aiwp.md), and [GraphCast](./graphcast-aiwp.md), all sharing this bucket, format, cadence, and license. The archive is described in Radford et al. (2025, BAMS).
- This is an **AI/ML model** — recorded in [`AI_MODELS.md`](../../../../AI_MODELS.md).
- Real-time visualizations: https://aiweather.cira.colostate.edu
- Contact / data manager: Dr. Jacob Radford (jacob.radford@noaa.gov); NODD: nodd@noaa.gov

---

## Relationship to other models

### Within the AIWP archive
Sibling entries: [Pangu-Weather](./pangu-aiwp.md), [GraphCast](./graphcast-aiwp.md), [FourCastNet](./fourcastnet-aiwp.md). All four share the bucket, the NetCDF-4 layout, the 0.25°/13-level grid, the 240 h / 6-hourly output structure, the dual GFS/IFS initialization, and the open licence — they differ in architecture, period of record, and distributed field set. Aurora has by far the shortest record of the four.

### Distinct from operational AI systems
Unlike NOAA's [AIGFS](./aigfs.md) or ECMWF's [AIFS Single](../eu/aifs-single.md), this archive is **not an operational forecast product**. It runs unmodified research weights, is not fine-tuned on the initializing centre's own analyses, carries no service-change notices or operational support commitments, and offers no availability guarantee. Its undocumented status in the registry compounds this — treat it as a research dataset.

---

## Official documentation
- https://registry.opendata.aws/aiwp/ (does not mention Aurora as of 2026-08-06)
- https://noaa-oar-mlwp-data.s3.amazonaws.com/README.txt
- BAMS paper: Radford et al., 2025, *Bull. Amer. Meteor. Soc.*, 106, E68–E76, https://doi.org/10.1175/BAMS-D-24-0057.1
- Model reference: Bodnar et al., 2025, "A Foundation Model for the Earth System," *Nature*, https://doi.org/10.1038/s41586-025-09005-y — code: https://github.com/microsoft/aurora
