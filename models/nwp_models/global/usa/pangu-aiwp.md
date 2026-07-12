# Pangu-Weather (AIWP reforecast archive)

## What this model is
Pangu-Weather is a deterministic, data-driven (AI) global weather model that emulates medium-range forecasts using a 3D transformer trained on ERA5, rather than solving physical equations. This entry documents the **NOAA/CIRA AI Weather Prediction (AIWP) reforecast archive** of Pangu-Weather — a near-real-time and retrospective run of the model initialized from operational analyses and distributed as open gridded output on AWS.

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
- **Model system / core:** Pangu-Weather — 3D Earth-Specific Transformer (3DEST), trained on ERA5 0.25° reanalysis
- **Dynamical formulation:** Not applicable (data-driven ML model; no dynamical core)
- **Convection-allowing:** No (~0.25°, ≈28 km)
- **Horizontal resolution:** 0.25° (~28 km)
- **Grid dimensions:** 1440 × 721
- **Vertical levels:** 13 pressure levels (output): 1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa
- **Model top:** Highest output level 50 hPa (AI model; no physical model top)
- **Forecast length:** 240 h (10 days)
- **Update frequency / cycles:** 2× daily (00, 12 UTC)
- **Temporal output resolution:** 6 h (forecast hours 000–240, 41 steps per cycle)

---

## Data assimilation
- **Data assimilation:** No — the model does not run its own assimilation; it ingests an external operational analysis as the initial state.

---

## Initial and boundary conditions (for limited-area models)
- **Initial conditions:** Two parallel streams — **NOAA GFS analysis** (directories with no suffix) and **ECMWF IFS analysis** (directories ending in `_IFS`)
- **Boundary conditions:** None (global model)

---

## What it provides
Deterministic forecasts of:
- Surface / near-surface: mean sea level pressure (`msl`), 2-m temperature (`t2`), 10-m u/v wind (`u10`, `v10`)
- Pressure-level fields on all 13 levels: geopotential (`z`), temperature (`t`), u wind (`u`), v wind (`v`), specific humidity (`q`)

---

## Data availability
- **Is the data free?** Yes
- **License:** Open — NODD states "no restrictions on the use of this data." Effectively open/public-domain distribution. (Note: the *output data* is unrestricted; the underlying Pangu-Weather **code/weights** carry their own upstream license, which does not restrict reuse of this derived output.)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.8), one `.nc` file per cycle spanning f000–f240
- **Official download location:**  
  `s3://noaa-oar-mlwp-data/PANG_v100_GFS/` and `s3://noaa-oar-mlwp-data/PANG_v100_IFS/` (anonymous S3, us-east-1)  
  Browse: https://noaa-oar-mlwp-data.s3.amazonaws.com/index.html  
  Registry: https://registry.opendata.aws/aiwp/

---

## Notes
- **Period of record (verified live):** GFS-initialized ~10/2020 → present; IFS-initialized 01/2022 → present. Start dates are chosen to avoid overlap with Pangu's training/fine-tuning period. Data may be missing for some cycles.
- **File / path convention:** `PANG_v100_III/YYYY/mmdd/PANG_v100_III_YYYYmmddhh_f000_f240_06.nc`, where `III` is `GFS` or `IFS`.
- **Part of the AIWP archive** alongside FourCastNet (v1/v2) and GraphCast, all sharing this bucket, format, cadence, and license. The archive is described in Radford et al. (2025, BAMS).
- This is an **AI/ML model** — recorded in [`AI_MODELS.md`](../AI_MODELS.md).
- Real-time visualizations: https://aiweather.cira.colostate.edu
- Contact / data manager: Dr. Jacob Radford (jacob.radford@noaa.gov); NODD: nodd@noaa.gov

---

## Official documentation
- https://registry.opendata.aws/aiwp/
- https://noaa-oar-mlwp-data.s3.amazonaws.com/README.txt
- BAMS paper: Radford et al., 2025, *Bull. Amer. Meteor. Soc.*, 106, E68–E76, https://doi.org/10.1175/BAMS-D-24-0057.1
- Model reference: Bi et al., 2023, *Nature* 619, 533–538, https://www.nature.com/articles/s41586-023-06185-3 — code: https://github.com/198808xc/Pangu-Weather
