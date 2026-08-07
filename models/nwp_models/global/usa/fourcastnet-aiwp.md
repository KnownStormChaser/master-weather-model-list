# FourCastNet (AIWP reforecast archive)

## What this model is
FourCastNet is a deterministic, data-driven (AI) global weather model that emulates medium-range forecasts using Fourier neural operators trained on ERA5, rather than solving physical equations. This entry documents the **NOAA/CIRA AI Weather Prediction (AIWP) reforecast archive** of FourCastNet — a near-real-time and retrospective run of the model initialized from operational analyses and distributed as open gridded output on AWS.

The archive carries **two model versions in separate prefixes**: the original FourCastNet (v1, `FOUR_v100_*`) and FourCastNet v2-small (`FOUR_v200_*`). Only v2-small is still updating; see [Version streams](#version-streams).

> **This is not FourCastNetGFS.** NOAA/NCEP separately ran [FourCastNetGFS](#relationship-to-other-models), an experimental GDAS-initialized productionization distributed as GRIB2 from its own bucket. That system stopped producing forecasts on **2026-03-01**. The AIWP archive runs the upstream research weights and continues to update.

---

## Who runs it
- **Organization:** Cooperative Institute for Research in the Atmosphere (CIRA, Colorado State University) and NOAA Global Systems Laboratory (NOAA-GSL), distributed via NOAA Open Data Dissemination (NODD). Underlying model developed by NVIDIA (Pathak et al., 2022; Bonev et al., 2023).
- **Country / region:** United States (distributor); model origin United States (NVIDIA)

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Regular 0.25° latitude–longitude grid, 1440 × 721 points (90°N→90°S, 0°→359.75°E)

---

## Basic details
- **Model type:** Deterministic NWP (AI / machine-learning emulator)
- **Model system / core:** FourCastNet v2-small — Spherical Fourier Neural Operator (SFNO) with a vision-transformer-style architecture and Fourier-transform-based token mixing, trained on ERA5 0.25° reanalysis. NetCDF `model_name` attribute: `fourcastnetv2-small`.
- **Dynamical formulation:** Not applicable (data-driven ML model; no dynamical core)
- **Convection-allowing:** No (~0.25°, ≈28 km)
- **Horizontal resolution:** 0.25° (~28 km)
- **Grid dimensions:** 1440 × 721
- **Vertical levels:** 13 pressure levels (output): 1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa
- **Model top:** Highest output level 50 hPa (AI model; no physical model top)
- **Forecast length:** 240 h (10 days)
- **Update frequency / cycles:** 2× daily (00, 12 UTC) — **but see the cadence change and GFS-stream reliability notes below**
- **Temporal output resolution:** 6 h (forecast hours 000–240, 41 steps per cycle)
- **Archive processing version:** `3_2025-02-20` (NetCDF global attribute `version`; an archive-wide processing version shared with the other AIWP models, not a FourCastNet model version)

---

## Data assimilation
- **Data assimilation:** No — the model does not run its own assimilation; it ingests an external operational analysis as the initial state.

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** Two parallel streams — **NOAA GFS analysis** (directories with no suffix) and **ECMWF IFS analysis** (directories ending in `_IFS`)
- **Boundary conditions:** None (global model)

---

## What it provides

Deterministic forecasts, **73 fields per step** (verified from NetCDF headers, 2026-08-05 12 UTC, both streams):

- **Pressure-level fields on all 13 levels (5 variables):** u wind (`u`), v wind (`v`), geopotential (`z`), temperature (`t`), **relative humidity (`r`)**
- **Surface / near-surface (8 variables):** 10-m u/v wind (`u10`, `v10`), **100-m u/v wind (`u100`, `v100`)**, 2-m temperature (`t2`), **surface pressure (`sp`)**, mean sea level pressure (`msl`), **total column water vapour (`tcwv`)**

All fields are `float32`, chunked one horizontal slice at a time (`(1, 1, 721, 1440)` for pressure-level fields), gzip level 4.

> FourCastNet's field set differs from its AIWP siblings in three ways that matter for cross-model comparison. It is the **only AIWP model carrying 100-m winds, surface pressure, and total column water vapour** — useful for wind-energy and moisture-transport work. It carries **relative humidity** on pressure levels where [Pangu](./pangu-aiwp.md), [Aurora](./aurora-aiwp.md), and [GraphCast](./graphcast-aiwp.md) carry specific humidity, so humidity comparisons across models require a conversion. And it carries **no vertical velocity and no precipitation** — only [GraphCast](./graphcast-aiwp.md) provides those.

---

## Version streams

| Prefix | Model | Period of record | Status |
|---|---|---|---|
| `FOUR_v100_GFS` | FourCastNet v1 | 2020-09-30 → 2023-10-31 | **Frozen** — no longer updating |
| `FOUR_v200_GFS` | FourCastNet v2-small | 2020-09-30 → present | Active (with gaps, see below) |
| `FOUR_v200_IFS` | FourCastNet v2-small | 2022-01-01 → present | Active |

Two consequences worth noting. First, **v1 has no IFS-initialized counterpart** — it exists only as a GFS-initialized stream. Second, **v2 was backfilled across v1's entire period**, both starting 2020-09-30, so `FOUR_v100_GFS` is fully superseded for any date it covers. There is no date range for which v1 is the only option. Use v1 only if you specifically need the original architecture's output for a version-comparison study; otherwise prefer `FOUR_v200_*`.

The registry page documents FourCastNetv2-small as "available from 10/2020 to present" and does not mention the v1 prefix at all. The `FOUR_v100_GFS` objects were last written 2025-01-10, consistent with a bulk re-upload rather than ongoing production.

---

## Data availability
- **Is the data free?** Yes
- **License:** Open — NODD states "no restrictions on the use of this data." Effectively open/public-domain distribution. (Note: the *output data* is unrestricted; FourCastNet's upstream **code/weights** carry their own license — Apache-2.0 for the ECMWF `ai-models-fourcastnetv2` plugin — which does not restrict reuse of this derived output.)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.8), one `.nc` file per cycle spanning f000–f240. **~4.5 GB per cycle** on the GFS stream and **~2.3 GB** on the IFS stream (see the size-asymmetry note below).
- **Official download location:**
  `s3://noaa-oar-mlwp-data/FOUR_v200_GFS/` and `s3://noaa-oar-mlwp-data/FOUR_v200_IFS/` (anonymous S3, us-east-1); legacy v1 at `s3://noaa-oar-mlwp-data/FOUR_v100_GFS/`
  Browse: https://noaa-oar-mlwp-data.s3.amazonaws.com/index.html
  Registry: https://registry.opendata.aws/aiwp/

---

## Notes
- **File / path convention:** `FOUR_vNNN_III/YYYY/mmdd/FOUR_vNNN_III_YYYYmmddhh_f000_f240_06.nc`, where `NNN` is `100` or `200` and `III` is `GFS` or `IFS`.

> ⚠️ **The GFS-initialized stream is currently unreliable; the IFS stream is not.** Over 2026-07-30 → 2026-08-06, the IFS stream carried all 16 expected cycles while the GFS stream carried 9, with **2026-08-01 missing entirely** and most days producing a single cycle instead of two. The identical gap pattern appears across *every* model in the bucket — [Pangu](./pangu-aiwp.md), [Aurora](./aurora-aiwp.md), and [GraphCast](./graphcast-aiwp.md) are missing exactly the same cycles — so this is an upstream GFS initial-condition ingest problem, not a FourCastNet-specific one. The registry warns that "data may be missing and is not guaranteed to be available at any given time," but does not indicate that the two streams differ in reliability. **Anyone needing complete coverage should prefer the IFS stream or verify per-cycle presence rather than assuming a regular 2×/day series.**

> ⚠️ **Cadence changed from 4× to 2× daily around the end of 2023.** Verified: 2023-12-01 carries 00/06/12/18 UTC initializations, 2024-01-01 carries only 00/12. The change appears archive-wide and permanent. The registry's path specification still documents `hh` as "00, 06, 12, or 18", which is correct for the historical record but misleading for anything after 2023. Retrospective studies spanning the boundary will see the sample rate halve mid-record.

- **GFS-stream files are ~2× larger than IFS-stream files for identical content.** Both streams carry the same variables, shapes, chunking, and gzip level, so the uncompressed sizes are identical (~4.5 GB vs ~2.3 GB compressed). The two are produced in different environments — the GFS files report `netcdf=4.9.4-development, hdf5=1.14.2`, the IFS files `netcdf=4.9.3, hdf5=1.14.6` — and creation timestamps cluster several hours apart. The most likely explanation is a difference in low-order float entropy (e.g. bit-rounding applied on one path but not the other) rather than a content difference, but this has not been confirmed (**TBD**). It does mean storage estimates should be taken per stream.
- **Part of the AIWP archive** alongside [Pangu-Weather](./pangu-aiwp.md), [Aurora](./aurora-aiwp.md), and [GraphCast](./graphcast-aiwp.md), all sharing this bucket, format, cadence, and license. The archive is described in Radford et al. (2025, BAMS).
- This is an **AI/ML model** — recorded in [`AI_MODELS.md`](../AI_MODELS.md).
- Real-time visualizations: https://aiweather.cira.colostate.edu
- Contact / data manager: Dr. Jacob Radford (jacob.radford@noaa.gov); NODD: nodd@noaa.gov

---

## Relationship to other models

### Distinct from NOAA/NCEP's FourCastNetGFS
**FourCastNetGFS** (NOAA/NCEP, experimental) was a separate productionization: FourCastNet v2 initialized from NCEP 0.25° GDAS analyses, distributed as GRIB2 (`fcngfs.tCCz.pgrb2.0p25.fFFF`) from the `noaa-nws-fourcastnetgfs-pds` bucket, 4× daily to 240 h. **That stream stopped producing forecasts on 2026-03-01** (final day a partial: 00/06/12 UTC only, 249 objects, last write 19:32 UTC). Its AWS registry entry still describes it in the present tense. Unlike GraphCastGFS → [AIGFS](./aigfs.md), FourCastNetGFS has **no operational descendant** — NOAA's operational AI line went to GraphCast, not FourCastNet.

The AIWP FourCastNet archive is not a continuation of FourCastNetGFS: different operator (CIRA/GSL vs NCEP), different initialization streams (GFS *and* IFS vs GDAS only), different format (NetCDF vs GRIB2), different bucket, and it is still running.

### Within the AIWP archive
Sibling entries: [Pangu-Weather](./pangu-aiwp.md), [Aurora](./aurora-aiwp.md), [GraphCast](./graphcast-aiwp.md). All four share the bucket, the NetCDF-4 layout, the 0.25°/13-level grid, the 240 h / 6-hourly output structure, the dual GFS/IFS initialization, and the open licence — they differ in architecture, period of record, and distributed field set.

---

## Official documentation
- https://registry.opendata.aws/aiwp/
- https://noaa-oar-mlwp-data.s3.amazonaws.com/README.txt
- BAMS paper: Radford et al., 2025, *Bull. Amer. Meteor. Soc.*, 106, E68–E76, https://doi.org/10.1175/BAMS-D-24-0057.1
- Model references: Pathak et al., 2022, *FourCastNet: A Global Data-driven High-resolution Weather Model using Adaptive Fourier Neural Operators*, arXiv:2202.11214; Bonev et al., 2023, *Spherical Fourier Neural Operators*, arXiv:2306.03838
- Implementation used: ECMWF `ai-models-fourcastnetv2` plugin, https://github.com/ecmwf-lab/ai-models-fourcastnetv2
