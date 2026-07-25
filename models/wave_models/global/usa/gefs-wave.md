# GEFS-Wave (WAVEWATCH III ensemble / GWES) – NOAA/NCEP

## What this model is
GEFS-Wave is the operational global ocean wave ensemble of NOAA/NCEP, the ensemble counterpart to the deterministic [GFS-Wave](./ww3-noaa.md). It is built on the third-generation spectral wave model **WAVEWATCH III (WW3)** and produces probabilistic forecasts of significant wave height, wave periods, directions, and partitioned wind-sea and swell components.

Formerly the standalone Global Wave Ensemble System (GWES / `multi_1` ensemble), it was unified into the [Global Ensemble Forecast System (GEFS)](../../../ensemble_models/global/usa/gefs.md) at GEFS v12 (23 September 2020). Each wave member is one-way coupled to its corresponding GEFS atmospheric member; unification raised the wind-forcing frequency from 3 hours to 1 hour and extended the wave forecast from 10 to 16 days.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** Global oceans
- **Domain details:** Single global regular lat-lon grid at 0.25°, `global.0p25` (unlike the multi-grid deterministic [GFS-Wave](./ww3-noaa.md), the ensemble is distributed only on the single global 0.25° grid)

---

## Basic details
- **Model type:** Ensemble wave model
- **Grid system:** Single regular lat-lon grid (0.25°, global)
- **Core wave model:** WAVEWATCH III (WW3)
- **Horizontal resolution:** 0.25°
- **Forecast length:** 384 h (16 days)
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 3-hourly from f000–f240, then 6-hourly from f240–f384 (105 steps; live-verified)

---

## Forcing and nesting
- **Wind forcing:** GEFS atmosphere 10 m winds, coupled at **1-hour** frequency (one-way, atmosphere → wave; each wave member forced by its matching GEFS member)
- **Ice forcing:** Sea-ice concentration is included as a field in the member output (`ci`)
- **Current forcing:** Not documented for the ensemble
- **Initialization:** Each member's wave state is warm-started from the previous cycle's 6-hour forecast of the corresponding member
- **Nested inside / parent for:** Wave component of the [GEFS](../../../ensemble_models/global/usa/gefs.md) system; deterministic sibling is [GFS-Wave](./ww3-noaa.md)

---

## Ensemble configuration
- **Ensemble size:** 31 members — 1 control (`c00`) + 30 perturbed (`p01`–`p30`). Live-verified from the member tokens in the 12 UTC run. (NOAA's product description says "30 members," counting the perturbed members; the control is distributed alongside them.)
- **Source of perturbations:** Inherited from the driving [GEFS](../../../ensemble_models/global/usa/gefs.md) atmospheric ensemble (perturbed 10 m winds per member). The wave model itself is not separately perturbed.
- **Resolution / output differences vs deterministic sibling:** The ensemble is coarser and less frequent than [GFS-Wave](./ww3-noaa.md) — a single 0.25° global grid (vs seven native/post-processed grids), 3-/6-hourly output (vs hourly to f120), and a leaner field set on the derived products (see below).
- **Member packaging (live-verified 2026-07-25 12Z):** one GRIB2 file per member per step, member token in the filename — `gefs.wave.tCCz.{c00|pNN}.global.0p25.f###.grib2`. Raw member files carry a `.idx` sidecar; the derived mean/spread/prob products do **not** (verified: member `.idx` → HTTP 200, mean/spread/prob `.idx` → HTTP 404).
- **Derived products distributed as raw GRIB2:**
  - `mean` — ensemble mean
  - `spread` — ensemble spread (standard deviation)
  - `prob` — probability of exceedance (GRIB2 Product Definition Template 5, `probabilityType=1`), eight thresholds per variable

---

## Data assimilation
- **Assimilates wave observations:** No — GEFS-Wave is a forced wave ensemble with no wave data assimilation. Spread derives from the driving GEFS atmospheric ensemble.

---

## What it provides
All fields on the global 0.25° grid, GRIB2. Live-verified inventories (12 UTC run, f012; eccodes parameter names):

**Per member (`c00`, `p01`–`p30`) — 23 fields:**
- Surface wind: wind speed (`ws`), direction (`wdir`), u/v components (`u`, `v`)
- Sea ice: area fraction (`ci`)
- Combined sea state: significant height (`swh`)
- Wave periods: mean period from first moment (`mp1`), mean wave period (`mwp`), primary wave mean period (`perpw`)
- Wave directions: mean wave direction (`mwd`), primary wave direction (`dirpw`)
- Wind-wave partition: significant height (`shww`), mean period (`mpww`), direction (`wvdir`)
- Swell partitions 1–3: significant height (`shts`), mean period (`mpts`), direction (`swdir`)

**Ensemble mean and spread — 10 fields (leaner):** `swh`, `perpw`, `dirpw`, `shww`, `mpww`, `wvdir`, `ws`, `wdir`, and `shts`/`mpts` for **two** swell partitions. (No sea ice, wind components, `mp1`/`mwp`/`mwd`, or swell direction.)

**Probability (`prob`) — exceedance probabilities** for `swh`, `perpw`, `shww`, `mpww`, `ws`, plus `shts`/`mpts` for two swell partitions; eight thresholds per variable, no direction fields.

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent). Distributed via NOAA Open Data Dissemination (NODD); NOAA requests attribution and prohibits implying NOAA endorsement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (raw members carry a `.idx` sidecar; derived products do not)
- **File naming:** `gefs.YYYYMMDD/CC/wave/gridded/gefs.wave.tCCz.{token}.global.0p25.f###.grib2`, where `{token}` ∈ {`c00`, `p01`–`p30`, `mean`, `spread`, `prob`}
- **Official download locations:**
  - **NOMADS** (real-time, ~4-day rolling window for GEFS):
    https://nomads.ncep.noaa.gov/pub/data/nccf/com/gens/prod/
  - **NCEP production FTP** (ftpprd):
    https://ftpprd.ncep.noaa.gov/data/nccf/com/gens/prod/
  - **AWS Open Data** — bucket `noaa-gefs-pds` (live-verified; wave subtree archived from 2017-01-01 to present):
    https://registry.opendata.aws/noaa-gefs/
    e.g. `https://noaa-gefs-pds.s3.amazonaws.com/gefs.YYYYMMDD/CC/wave/gridded/`
  - **Microsoft Azure** — blob container `noaagefs` (live-verified; public, no SAS token needed):
    `https://noaagefs.blob.core.windows.net/gefs/gefs.YYYYMMDD/CC/wave/gridded/`
  - **Google Cloud Storage** — NODD bucket `gfs-ensemble-forecast-system` (live-verified):
    `https://storage.googleapis.com/gfs-ensemble-forecast-system/gefs.YYYYMMDD/CC/wave/gridded/`

All three NODD cloud mirrors carry the wave subtree — including the derived `mean`/`spread`/`prob` products — typically with no rate limits and a deeper archive than the NOMADS rolling window.

---

## Notes
- **All three clouds carry GEFS-Wave.** For GEFS the Google link points to the raw Cloud Storage bucket (`gfs-ensemble-forecast-system`), which does contain the wave data — in contrast to the deterministic [GFS-Wave](./ww3-noaa.md) case, where the Google *Earth Engine* `NOAA/GFS0P25` asset is atmosphere-only.
- The `wave/` directory also contains a `station/` subdirectory with spectral bulletins and point spectra. These are **station/point time-series products and fall outside this repository's gridded-data scope**; only `gridded/` is cataloged here.
- **Derived products are a reduced field set** relative to the raw members: mean/spread drop sea ice, wind components, the extra period/direction parameters, and one swell partition; the probability product carries only scalar magnitudes and periods (no directions). Users needing the full field set or all three swell partitions must work from the raw members.
- **Coupling and family relationships:** one-way coupled to the [GEFS](../../../ensemble_models/global/usa/gefs.md) atmosphere; deterministic sibling is [GFS-Wave](./ww3-noaa.md). Peer global wave ensembles in this repository include the Navy's [FNMOC wave ensemble](./fnmoc-wave-ensemble.md) and Canada's [GEWPS](../canada/gewps-canada.md).
- The atmospheric GEFS extends to 35 days at the 00 UTC cycle, but the **wave ensemble is capped at 16 days (384 h)** across all cycles.

---

## Recent version history
### GEFS v12 — 23 September 2020 (wave unification)
- Retired the standalone Global Wave Ensemble System (GWES); wave ensemble unified into GEFS as a one-way-coupled WW3 component under `gefs.YYYYMMDD/CC/wave/`
- Wind-forcing frequency increased from 3 h to 1 h
- Wave forecast length extended from 10 to 16 days
- Distributed on a single 0.25° global grid with 31 members plus mean/spread/probability products

### Upcoming — GEFS v13
- GEFS v13 is under development for joint deployment with GFSv17 (proposed for October 2026); see the [GEFS entry](../../../ensemble_models/global/usa/gefs.md) for status. Wave paths/products may change; re-verify against NOMADS if that upgrade goes operational.

---

## Official documentation
- GEFS documentation (EMC): https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/gefs.php
- WAVEWATCH III at NCEP: https://polar.ncep.noaa.gov/waves/
- AWS NODD registry: https://registry.opendata.aws/noaa-gefs/
- Azure GEFS (AIforEarthDataSets): https://microsoft.github.io/AIforEarthDataSets/data/noaa-gefs.html
