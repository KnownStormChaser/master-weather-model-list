# Aurora (AIWP reforecast archive)

## What this model is
Aurora is a deterministic, data-driven (AI) foundation model for the Earth system, run here in its 0.25° medium-range weather configuration to emulate global forecasts. This entry documents the **NOAA/CIRA AI Weather Prediction (AIWP) reforecast archive** of Aurora — a near-real-time and retrospective run initialized from operational analyses and distributed as open gridded output on AWS.

---

## Who runs it
- **Organization:** CIRA (Colorado State University) and NOAA-GSL, distributed via NOAA Open Data Dissemination (NODD). Underlying model developed by Microsoft Research AI for Science (Bodnar et al., 2025).
- **Country / region:** United States (distributor); model origin United States (Microsoft)

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Regular 0.25° latitude–longitude grid, 1440 × 721 points (90°N→90°S, 0°→359.75°E)

---

## Basic details
- **Model type:** Deterministic NWP (AI / machine-learning foundation model)
- **Model system / core:** Aurora — 3D Swin Transformer U-Net with a Perceiver-style encoder/decoder, pretrained on multiple geophysical datasets and fine-tuned for 0.25° weather forecasting. Archive internal version string: `3_2025-02-20`.
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
- **License:** Open — NODD states "no restrictions on the use of this data." Effectively open/public-domain distribution. (Note: the *output data* is unrestricted; Aurora's upstream **code/weights** carry their own license, which does not restrict reuse of this derived output.)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.8), one `.nc` file per cycle spanning f000–f240. Files are large — **~4.6 GB per cycle** at full 0.25° resolution.
- **Official download location:**  
  `s3://noaa-oar-mlwp-data/AURO_v100_GFS/` and `s3://noaa-oar-mlwp-data/AURO_v100_IFS/` (anonymous S3, us-east-1)  
  Browse: https://noaa-oar-mlwp-data.s3.amazonaws.com/index.html  
  Registry: https://registry.opendata.aws/aiwp/

---

## Notes
- **Undocumented as of this writing:** Aurora is present and updating in the bucket but is not listed in the AIWP registry page or the bucket `README.txt` (last modified 2025-01-21). Details below are from live bucket inspection and NetCDF headers, not vendor documentation.
- **Period of record (verified live):** GFS-initialized from **2025-01-22**; IFS-initialized from **2025-01-23**; both → present. Shorter record than the other AIWP models. Data may be missing for some cycles.
- **File / path convention:** `AURO_v100_III/YYYY/mmdd/AURO_v100_III_YYYYmmddhh_f000_f240_06.nc`, where `III` is `GFS` or `IFS`.
- This archive is the **deterministic base Aurora 0.25° weather model**. Microsoft's newer **Aurora 1.5** (probabilistic ensemble, hourly output, additional variables) is a separate release and is *not* what this archive contains.
- **Part of the AIWP archive** alongside FourCastNet and GraphCast, sharing this bucket, format, cadence, and license (Radford et al., 2025, BAMS).
- This is an **AI/ML model** — recorded in [`AI_MODELS.md`](../AI_MODELS.md).
- Real-time visualizations: https://aiweather.cira.colostate.edu
- Contact / data manager: Dr. Jacob Radford (jacob.radford@noaa.gov); NODD: nodd@noaa.gov

---

## Official documentation
- https://registry.opendata.aws/aiwp/
- https://noaa-oar-mlwp-data.s3.amazonaws.com/README.txt
- BAMS paper: Radford et al., 2025, *Bull. Amer. Meteor. Soc.*, 106, E68–E76, https://doi.org/10.1175/BAMS-D-24-0057.1
- Model reference: Bodnar et al., 2025, "A Foundation Model for the Earth System," *Nature*, https://doi.org/10.1038/s41586-025-09005-y — code: https://github.com/microsoft/aurora
