# TJ-NWP (TianJi Global–Regional Integrated NWP System)

## What this model is
TJ-NWP is a next-generation numerical weather prediction system developed by Tianji Weather, built on the **SD3 (Super Dynamics on Cube)** framework — a cubed-sphere, non-hydrostatic dynamical core running on heterogeneous (GPU) computing. Its "Global Free-Zoom" technology provides a seamless transition from a ~12 km global mesh to ~5 km refinement over Asia without traditional nesting. The underlying global system advertises forecasts to ~45 days (useful skill ~10 days).

The publicly distributed data on AWS is a subset of the full system: two **regional 0.1° (~11 km) deterministic domains** (Southeast Asia and Africa) and a **Western North Pacific tropical-cyclone track** product. The ~12 km global and ~5 km Asia products are **not** on the public bucket.

> **Not an AI model.** Despite "next-generation" framing, SD3 is a physics-based dynamical core; AI/ML is listed only as a downstream application. This entry should **not** be added to [`AI_MODELS.md`](../../AI_MODELS.md).

---

## Who runs it
- **Organization:** Tianji Weather Science and Technology Company (天机气象) — https://www.tjweather.com/
- **Country / region:** China (private company)

---

## What area it covers
The public bucket contains three sub-datasets:

- **Southeast Asia (regional NWP):** 90°E–140°E, 10°S–30°N
- **Africa (regional NWP):** 17.5°W–51.5°E, 35°S–37.5°N
- **Typhoon track (specialty):** Western North Pacific — point/track data, not gridded

---

## Basic details
- **Model type:** Deterministic regional NWP (two domains) + an ensemble-derived tropical-cyclone track product
- **Model system / core:** SD3 (Super Dynamics on Cube) — cubed-sphere grid, "Global Free-Zoom" seamless global-to-regional refinement
- **Dynamical formulation:** Non-hydrostatic
- **Convection-allowing:** No — the public regional domains are 0.1° (~11 km)
- **Horizontal resolution:**
  - Public regional domains (SEA, Africa): **0.1° (~11 km)**
  - Underlying global system: ~12 km global / ~5 km Asia zoom (not on the public bucket)
- **Vertical levels:** TBD (not documented). Isobaric outputs are provided on 13 pressure levels: 1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa
- **Forecast length:**
  - Regional domains: **240 h (10 days)**, hourly steps (`f001`–`f240`)
  - Typhoon track: out to 240 h at 6-hourly steps
- **Update frequency / cycles:** **2× daily (00 and 12 UTC)** — verified from the live `00z/` and `12z/` directories (the AWS registry's coarse "Daily" field understates this)
- **Temporal output resolution:**
  - Regional domains: hourly
  - Typhoon track: 6-hourly

---

## Data assimilation
- **Data assimilation:** TBD (not documented)

---

## Initial and boundary conditions
- TBD — not documented for the public regional domains.

---

## What it provides

**Regional domains (Southeast Asia — 149 variables; Africa — 153 variables):**
- Surface / near-surface: 2 m temperature, dew point, relative & specific humidity; 10 m / 50 m / 100 m winds; MSLP and corrected surface pressure; total-column precipitable water
- Precipitation: total rate plus phase-partitioned accumulations (rain, snow, graupel, ice)
- Radiation & fluxes: down/up shortwave & longwave at surface and TOA; sensible, latent and ground heat flux; momentum fluxes
- Clouds & stability: high/medium/low/total cloud cover, CAPE, Richardson number, PBL height
- Land surface: 4-layer soil temperature and moisture, snow depth / SWE, surface roughness
- Derived reflectivity: Stoelinga base and composite (max) reflectivity
- Water paths: liquid, ice, total
- Isobaric fields on 13 levels: geopotential height, temperature, dew point, RH, specific humidity, u/v wind, omega
- **Africa only:** dust fields — dust optical depth, near-surface dust concentration, dry deposition, and emission

**Typhoon track (Western North Pacific — 10 variables per storm):**
- Center latitude/longitude, minimum sea-level pressure, maximum 10 m wind
- Storm motion direction and translation speed, radius of maximum wind
- 34/50/64-kt wind radii by quadrant (NE/SE/SW/NW)
- Ensemble member count (`nmember`) — indicating the track is an ensemble-derived product

For the full per-variable inventory (short names, units, descriptions), see the provider documentation linked below.

---

## Data availability
- **Is the data free?** Yes
- **License:** [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) (attribution required)
- **Is the data downloadable?** Yes — public S3 bucket, no AWS account required (`--no-sign-request`)
- **Data formats:** NetCDF4
- **Official access point:**
  - Registry: https://registry.opendata.aws/dynamical-tj-nwp/
  - Bucket: `s3://tj-nwp/` (AWS region `us-west-2`)
  - `aws s3 ls --no-sign-request s3://tj-nwp/`

**Path structure** (verified live — note the `0p1/` resolution level, which the provider docs' diagram omits):

    # Regional domains (SEA / Africa)
    s3://tj-nwp/{southeast-asia|africa}/0p1/YYYY/MM/YYYYMMDD/{00z,12z}/
        tj-nwp-{sea|afr}.{YYYYMMDD}{HH}.t{HH}z.f{HHH}.nc     # HHH = 001..240

    # Typhoon track (no resolution level; one file per active WNP storm per cycle)
    s3://tj-nwp/typhoon-track/YYYY/MM/YYYYMMDD/{00z,12z}/
        tc_WNP_{STORMNAME}_track.nc

Update notifications are also published to an SNS topic: `arn:aws:sns:us-west-2:051370880159:tj-nwp-object_created`.

---

## Notes
- **Single product, three sub-datasets.** Tianji distributes SEA, Africa, and the WNP typhoon track as one *TJ-NWP* dataset (one bucket, one registry entry). SEA and Africa share the same SD3 configuration and variable set — Africa is the SEA variable set plus four dust fields — so they are documented here as one model with two domains rather than duplicated.
- **Typhoon-track placement (open decision):** the track product is ensemble-derived point data, not gridded NWP, and could alternatively be surfaced under `models/tropical_cyclone/` with a stub cross-linking back here. Kept inline for now since it's the same system/bucket/provider.
- **Public data is a subset.** The ~12 km global and ~5 km Asia-zoom products are not on the public bucket; only the two 0.1° regional domains and the WNP track are open.
- **Historical depth (verify before citing a start date):** the provider docs claim coverage from 2025-10-01 (regional) / 2025-01-01 (track), but the live bucket currently holds only `2026/` for all three sub-datasets. Treat the archive as ~2026-present until an earlier year appears.
- **Update cadence** was verified from live `00z`/`12z` directories (2× daily), not from the registry's coarse "Daily" label.

---

## Official documentation
- AWS registry: https://registry.opendata.aws/dynamical-tj-nwp/
- Provider dataset documentation: https://github.com/Tianji-Weather/aws-opendata-docs/blob/main/tj-nwp-descriptive-documentation.md
- "Get to know a dataset" notebook: https://github.com/Tianji-Weather/aws-opendata-docs/blob/main/get-to-know-a-dataset-tj-nwp.ipynb
- Operator: https://www.tjweather.com/
