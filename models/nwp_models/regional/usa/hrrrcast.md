# HRRR-Cast

## What this model is
HRRR-Cast is NOAA's first regional experimental AI forecast system: a data-driven deep-learning model trained on years of [HRRR](./hrrr.md) output that emulates the behaviour of the operational HRRR at a fraction of the computational cost. It produces short-range (0–48 h) forecasts of reflectivity, temperature, wind, humidity, precipitation, and convective diagnostics over the contiguous United States.

Rather than solving the equations of motion, HRRR-Cast "learns" atmospheric evolution from historical HRRR data and rolls the forecast forward autoregressively. It is reported to run **100 to 1000 times more efficiently** than the physics-based HRRR — lightweight enough to run on a single laptop rather than a supercomputer — and is a key component of NOAA's broader **Project EAGLE** (Experimental Artificial intelligence Global and Limited-area Ensemble).

HRRR-Cast is **experimental**: it is run for testing and evaluation, not as an operational forecast product, and is not intended for operational decision-making. Since Version 2 it is an **ensemble** system (members plus an ensemble mean).

---

## Who runs it
- **Organization:** NOAA / Office of Oceanic and Atmospheric Research (OAR)
- **Country / region:** United States
- **Development:** NOAA Global Systems Laboratory (GSL), with contributions from CIRES (CU Boulder), CIRA (Colorado State University), NSSL, PSL, WPO, EPIC, and the NOAA OCIO. Run experimentally at NWS/NCEP Environmental Modeling Center (EMC) for evaluation.

---

## What area it covers
- **Coverage:** Contiguous United States (CONUS)
- **Domain details:** Output is on the **operational HRRR CONUS grid** — a 1799 × 1059 Lambert conformal grid at 3 km spacing (verified from the live GRIB2 headers: Dx = Dy = 3000 m; LaD 38.5°N; LoV 262.5°E; SW corner ≈ 21.14°N, 122.72°W). No Alaska, Hawaii, or Puerto Rico domains are published.

---

## Basic details
- **Model type:** Regional AI forecast system (ensemble since V2); emulator of the convection-allowing HRRR
- **Model system / core:** Data-driven deep-learning emulator. The specific network architecture is not stated in the public documentation reviewed here (TBD).
- **Dynamical formulation:** Not applicable — HRRR-Cast does not solve dynamical equations; it is a trained neural network that predicts atmospheric evolution from learned patterns in HRRR data.
- **Convection-allowing:** Output is produced on the 3 km HRRR grid and reproduces explicit storm structure (composite reflectivity, storm-scale features), but convection is **not dynamically resolved** — convective behaviour is learned/implicit, as with other AI weather models.
- **Horizontal resolution:** 3 km (native HRRR CONUS grid; verified). Note: V3 documentation emphasises training "at the native 3 km resolution," implying earlier versions may have trained/run at coarser resolution before regridding — see Notes.
- **Vertical levels:** 20 pressure levels (200–1000 mb; verified from live files), up from 12 levels in V1. Additional surface, 2 m, 10 m, and 80 m fields.
- **Forecast length:** 48 hours (verified: f00–f48)
- **Update frequency / cycles:** Hourly — 24 cycles per day, 00–23 UTC (verified)
- **Temporal output resolution:** Hourly (f00–f48; verified)
- **Ensemble:** Yes (since V2). See Notes for the current member count, which is in transition on the live feed.

---

## Data assimilation
- **Data assimilation:** None of its own. HRRR-Cast performs no data assimilation; as an AI emulator it is initialized from HRRR analyses and integrates forward autoregressively.
- **Initial conditions:** Derived from HRRR analyses. The exact analysis source and any perturbation strategy used to seed the ensemble members are not specified in the public documentation reviewed here (TBD).

---

## What it provides
Forecasts (deterministic members plus ensemble mean) of the following, confirmed from the live GRIB2 inventory:

- **Reflectivity:** composite reflectivity (REFC)
- **Temperature:** 2 m temperature; temperature on 20 pressure levels
- **Moisture:** 2 m dewpoint; 2 m relative humidity; specific humidity on 20 pressure levels; precipitable water
- **Wind:** 10 m and 80 m wind components; wind on 20 pressure levels; wind gusts; storm-motion (U/V-STM); vertical wind shear
- **Precipitation:** accumulated precipitation (APCP); categorical rain / freezing rain (CRAIN/CFRZR)
- **Convective diagnostics:** surface CAPE and CIN; storm-relative helicity (0–1 km, 0–3 km); relative vorticity
- **Cloud / ceiling / visibility:** total, low, middle, and high cloud cover; cloud ceiling height; surface visibility
- **Mass / geopotential:** MSLP; surface pressure; geopotential height on 20 pressure levels, plus 0 °C isotherm and cloud-ceiling heights
- **Other:** land–sea mask; potential temperature (2 m); vertical velocity on pressure levels

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent). Note the experimental, non-operational status — the data carries a research/evaluation-only caveat, though this is a use disclaimer rather than a licence restriction.
- **Is the data downloadable?** Yes — anonymous public access confirmed (no account, no credentials, no approval gate).
- **Data formats:** GRIB2 (`.pgrb2`), each file paired with a `.idx` index for byte-range subsetting. NCEP-style filenames, e.g. `hrrrcast.m00.t12z.pgrb2.f06` (member 00, 12 UTC cycle, forecast hour 06) and `hrrrcast.avg.t12z.pgrb2.f06` (ensemble mean).
- **Official download location:**
  - S3 browsable index: https://noaa-gsl-experimental-pds.s3.amazonaws.com/index.html#HRRRCast/
  - Bucket / path: `s3://noaa-gsl-experimental-pds/HRRRCast/<YYYYMMDD>/<HH>/`

---

## Notes
- **AI approach.** HRRR-Cast is a standalone data-driven AI emulator of the physics-based HRRR — NOAA's first *regional* experimental AI forecast system. Update the repository's [`AI_MODELS.md`](../../../../AI_MODELS.md) index when this entry is added (see the follow-up snippet suggested at review time; there is not yet a regional-AI section in that index).
- **Relationship to HRRR.** It was conceived as an emulator of [HRRR](./hrrr.md), sharing HRRR's 3 km CONUS grid. Early evaluations reported it performing at least as well as the operational HRRR for reflectivity out to ~7 hours, with comparable performance to 48 hours and comparable humidity, temperature, and wind skill. It complements rather than replaces HRRR.
- **Ensemble size is in transition on the live feed (flag).** Through 2026-07-10 the feed carried a stable set of members `m00`–`m08` (full f00–f48) plus a consistently partial `m09` (≈10 forecast hours only), matching the documented "9 members." As of the 2026-07-11 cycles the member set expanded to `m00`–`m13` (14 tokens). This change is undocumented in the sources reviewed and its timing coincides with the expected V3 rollout, but the version/configuration is **unconfirmed** — treat the member count as provisional and re-verify against the live directory. The purpose of the historically partial `m09` stream is also unconfirmed.
- **Which version is currently on the feed is unconfirmed (flag).** The live inventory (20 pressure levels; 10 m/80 m winds; 2 m dewpoint/RH; total cloud cover, visibility, ceiling; hourly APCP; CAPE/CIN) matches the documented **V2** field set, and the output is already on the native 3 km HRRR grid. The member-count expansion on 2026-07-11 is consistent with a **V3**-era configuration (V3's stated focus was ensemble calibration and more reliable spread, trained at native 3 km), but no source reviewed confirms V3 is the deployed feed. Verify before stating a version.
- **Verification & tooling.** As of September 2025, HRRR-Cast V2 runs experimentally at NWS/EMC, verified with the "WxVx" tool (built on the Model Evaluation Tools, MET) and compared against the operational HRRR in real time via GSL's Model Analysis Tool Suite (MATS). Ensemble output — including individual members in a "postage stamp" layout — is viewable in GSL's DESI visualization tool.
- **Efficiency.** Reported at 100–1000× the computational efficiency of the operational HRRR; runnable on a single laptop.
- **File-naming quirk.** Member files use `m00`–`mNN`; the ensemble mean uses the `avg` token. No separate spread product was present in the samples inspected.

---

## Recent version history

### Version 1 — released July 2025
NOAA's first regional experimental AI forecast system, released by GSL. Trained on ~3 years of HRRR data. Produced forecasts at multiple lead times (1-, 3-, and 6-hour) for reflectivity, temperature, humidity, and winds across CONUS, with 12 vertical levels. Early results showed promise for probabilistic (ensemble-like) forecasting.

### Version 2 — experimental at EMC since September 2025 (announced November 24, 2025)
- Vertical resolution increased from **12 to 20 levels**
- New variables: 10 m and 80 m winds, 2 m dewpoint, 2 m relative humidity, total cloud cover, visibility, and cloud ceiling height
- Precipitation extended to **hourly accumulations**; convective diagnostics (CAPE, CIN) added
- Introduced the **HRRR-Cast ensemble** (documented as **9 members**, hourly, to 48 h)
- Trained on **4 years** of HRRR data

### Version 3 — in development (expected late 2025 / early 2026 per Nov 2025 announcement)
- Planned training on ~9 years of HRRR data at native 3 km resolution
- Focus on ensemble calibration and more reliable spread
- Intended as the next step toward evaluating HRRR-Cast for a possible future transition to NWS operations
- **Status on the live feed is unconfirmed** — see Notes.

---

## Official documentation
- HRRR-Cast home page (NOAA GSL): https://gsl.noaa.gov/hrrrcast/
- "New upgrades to HRRR-Cast" (NOAA GSL, Nov 24, 2025): https://gsl.noaa.gov/news/new-upgrades-to-hrrr-cast-noaas-experimental-ai-powered-regional-model/
- "NOAA Research develops an AI-powered sibling to its flagship weather model" (NOAA Research, Jul 22, 2025): https://research.noaa.gov/noaa-research-develops-an-ai-powered-sibling-to-its-flagship-weather-model/
- AWS data (experimental PDS, S3): https://noaa-gsl-experimental-pds.s3.amazonaws.com/index.html#HRRRCast/
