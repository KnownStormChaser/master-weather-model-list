# HRRR-Cast

## What this model is
HRRR-Cast is NOAA's first regional experimental AI forecast system: a data-driven deep-learning model trained on years of [HRRR](./hrrr.md) output that emulates the behaviour of the operational HRRR at a fraction of the computational cost. It produces short-range forecasts of reflectivity, temperature, wind, humidity, precipitation, and convective diagnostics over the contiguous United States.

Rather than solving the equations of motion, HRRR-Cast "learns" atmospheric evolution from historical HRRR data and rolls the forecast forward autoregressively. It is reported to run **100 to 1000 times more efficiently** than the physics-based HRRR — lightweight enough to run on a single laptop rather than a supercomputer — and is a key component of NOAA's broader **Project EAGLE** (Experimental Artificial intelligence Global and Limited-area Ensemble).

HRRR-Cast is **ensemble-only on the public feed** and its ensemble is **generative rather than perturbed**: members are stochastic samples drawn from a diffusion model, not integrations from perturbed initial states. See *Ensemble methodology*.

HRRR-Cast is **experimental**: it is run for testing and evaluation, not as an operational forecast product, and is not intended for operational decision-making. GSL states plainly that the system is subject to missing cycles arising from technical issues.

---

## Who runs it
- **Organization:** NOAA / Office of Oceanic and Atmospheric Research (OAR)
- **Country / region:** United States
- **Development:** NOAA Global Systems Laboratory (GSL), with contributions from CIRES (CU Boulder), CIRA (Colorado State University), NSSL, PSL, WPO, EPIC, and the NOAA OCIO. Run experimentally at NWS/NCEP Environmental Modeling Center (EMC) for evaluation.

---

## What area it covers
- **Coverage:** Contiguous United States (CONUS)
- **Domain details:** Output is on the **operational HRRR CONUS grid** — a 1799 × 1059 Lambert conformal grid at 3 km spacing, 1,905,141 points. Decoded with ecCodes 2.48.0 from the 2026-08-06 12 UTC cycle: Dx = Dy = 3000 m; Latin1 = Latin2 = 38.5° (tangent); LoV = 262.5°E; first grid point 21.138°N / 237.280°E. Identical to the [HRRR](./hrrr.md) CONUS grid. No Alaska, Hawaii, or Puerto Rico domains are published.

---

## Basic details
- **Model type:** Regional AI ensemble forecast system; emulator of the convection-allowing HRRR
- **Model system / core:** Data-driven deep-learning emulator built in TensorFlow/Keras. The live pipeline distributes a **diffusion (probabilistic) network**; a deterministic network exists in the codebase but is not the distributed configuration. The specific network architecture is not described in the public documentation reviewed here (TBD).
- **Dynamical formulation:** Not applicable — HRRR-Cast does not solve dynamical equations; it is a trained neural network that predicts atmospheric evolution from learned patterns in HRRR data.
- **Convection-allowing:** Output is produced on the 3 km HRRR grid and reproduces explicit storm structure (composite reflectivity, storm-scale features), but convection is **not dynamically resolved** — convective behaviour is learned/implicit, as with other AI weather models.
- **Horizontal resolution:** 3 km (native HRRR CONUS grid; verified). **New in V3** — V1 and V2 sampled every other HRRR grid point, giving an effective 6 km grid; V3 is the first version to run at the native 3 km spacing.
- **Vertical levels:** 20 pressure levels (verified from live files), up from 12 in V1: 200, 300, 350, 400, 450, 500, 550, 600, 650, 700, 750, 800, 825, 850, 875, 900, 925, 950, 975, 1000 mb. Note the irregular spacing — 50 mb below 800 mb, coarsening to 100 mb aloft. Additional surface, 2 m, 10 m, and 80 m fields.
- **Forecast length (live-verified):**
  - **48 hours** (49 steps, f00–f48) at 00, 06, 12, 18 UTC
  - **18 hours** (19 steps, f00–f18) at the other 20 cycles
- **Update frequency / cycles:** Hourly — 24 cycles per day, 00–23 UTC (verified)
- **Temporal output resolution:** Hourly
- **Ensemble size:** **18 members** (`m00`–`m17`), plus ensemble mean and ensemble spread. Live-verified across all 24 cycles of 2026-08-06 and the full retained window on 2026-08-07. **No NOAA source reviewed states a member count for V3** — the figure is verified from the feed only, and the count is a runtime parameter rather than a fixed property of the system (see *Ensemble methodology*).
- **Observed publication latency:** first file of a cycle written ≈ T+4 h 40 m; a 48-hour cycle finishes publishing ≈ T+6 h 40 m, an 18-hour cycle ≈ T+5 h 20 m. Verified across the 00, 12, 15, and 23 UTC cycles of 2026-08-06. **This feed is not usable for real-time short-range application** — a 48 h forecast completes publication almost seven hours after its own initialization time, by which point roughly seven forecast hours have already elapsed.

---

## Data assimilation
- **Data assimilation:** None of its own. HRRR-Cast performs no data assimilation.
- **Initial conditions:** HRRR analyses (pressure-level and surface GRIB, plus the previous hour's 1 h surface forecast used in precipitation handling), normalized per variable and per level.
- **Boundary conditions:** **GFS** supplies lateral boundary conditions, interpolated onto the HRRR grid. HRRR-Cast is therefore not a purely HRRR-derived system — global-scale information enters through the GFS boundary stream.
- **Static inputs:** land–sea mask (`LAND`) and orography (`OROG`) are ingested as static constant channels. Neither is distributed as an output field.

---

## Ensemble methodology

HRRR-Cast's ensemble is **generative, not perturbation-based**. There are no perturbed initial conditions, no stochastic physics, and no time-lagging. Members are independent stochastic samples drawn from a diffusion model conditioned on a single set of HRRR initial conditions and GFS boundary conditions; the deterministic network is a separate model selected by disabling diffusion.

Three practical consequences for cataloguing and downstream use:

- **The member count is a runtime argument, not a system property.** The forecast driver takes an explicit member count / member-ID range. This explains the undocumented drift in ensemble size observed on the live feed (9 → 14 → 18 members between mid-2025 and mid-2026) with no accompanying announcement, and means the count may change again without notice. Re-verify against the live directory rather than relying on this entry's figure.
- **There is no control member.** All 18 members are equivalent stochastic draws. `m00` carries no special status in the naming scheme, and treating it as a control run would be a mistake. (The distributed configuration appears to be diffusion-only; whether any deterministic run is published alongside is unconfirmed — no such token appears on the feed.)
- **Spread is a property of the generative sampler, not of analysis uncertainty.** HRRR-Cast spread does not represent observation or analysis error in the way an EnKF- or ETKF-seeded ensemble does. Ensemble calibration was a stated V3 development focus, so spread reliability should be treated as an open question.

A probability-matched mean (PMM) is computed by the pipeline in ensemble mode but is **not published** on the public feed.

---

## What it provides
Forecasts of the following, confirmed from the live GRIB2 inventory (172 records per file, identical across members, mean, and spread):

- **Reflectivity:** composite reflectivity (REFC)
- **Temperature:** 2 m temperature; temperature on 20 pressure levels
- **Moisture:** 2 m dewpoint; 2 m relative humidity; specific humidity on 20 pressure levels; precipitable water
- **Wind:** 10 m and 80 m wind components; wind on 20 pressure levels; wind gusts; 10 m wind speed; storm motion (USTM/VSTM); vertical wind shear components (VUCSH/VVCSH)
- **Precipitation:** accumulated precipitation (APCP); categorical rain / freezing rain (CRAIN/CFRZR)
- **Convective diagnostics:** surface CAPE and CIN; storm-relative helicity (0–1 km, 0–3 km); relative vorticity (0–1 km, 0–2 km); **updraft helicity, maximum and minimum, over 0–2 km, 0–3 km, and 2–5 km (MXUPHL/MNUPHL)**; **maximum updraft and downdraft vertical velocity over the 100–1000 mb layer (MAXUVV/MAXDVV)**
- **Cloud / ceiling / visibility:** total, low, middle, and high cloud cover; cloud ceiling height; surface visibility
- **Mass / geopotential:** MSLP; surface pressure; geopotential height on 20 pressure levels, plus 0 °C isotherm and cloud-ceiling heights
- **Other:** potential temperature (2 m); vertical velocity on pressure levels

Most fields beyond the directly predicted state variables are **computed diagnostics**, derived in post-processing from the network's predicted fields rather than predicted by the network itself. The low/mid/high cloud-cover fields are an exception — V3 added these as direct neural-network predictions. GSL notes that the new severe-convective fields in particular require further investigation before being relied upon.

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent). Distributed under the NOAA Open Data Dissemination (**NODD**) programme. Note the experimental, non-operational status — the data carries a research/evaluation-only caveat, though this is a use disclaimer rather than a licence restriction.
- **Is the data downloadable?** Yes — anonymous public access confirmed (no account, no credentials, no approval gate).
- **Data formats:** GRIB2 (`.pgrb2`), each file paired with a `.idx` index for byte-range subsetting. NCEP-style filenames, e.g. `hrrrcast.m00.t12z.pgrb2.f06` (member 00, 12 UTC cycle, forecast hour 06), `hrrrcast.avg.t12z.pgrb2.f06` (ensemble mean), `hrrrcast.spr.t12z.pgrb2.f06` (ensemble spread).
- **Retention:** short rolling window — roughly 2.5 days were present when checked on 2026-08-07 (oldest retained cycle 2026-08-04 18 UTC). There is no long-term archive on this bucket.
- **Official download location:**
  - S3 browsable index: https://noaa-gsl-experimental-pds.s3.amazonaws.com/index.html#HRRRCast/
  - Bucket / path: `s3://noaa-gsl-experimental-pds/HRRRCast/<YYYYMMDD>/<HH>/`

### Volume

Live-measured from the 2026-08-06 cycles. Every token (18 members, mean, spread) carries an identical 172-record inventory, so volume scales linearly with member count.

| | 48 h cycles (00/06/12/18) | 18 h cycles (other 20) |
|---|---|---|
| Objects per cycle (incl. `.idx`) | 1,960 | 760 |
| Steps per token | 49 | 19 |
| Mean GRIB2 file size | ~504 MB | ~504 MB |
| Volume per token | ~24.7 GB | ~9.6 GB |
| **Volume per cycle** | **~485 GB** | **~173 GB** |

Daily total is roughly **5.4 TB**. Byte-range subsetting via the `.idx` sidecars is effectively mandatory for any practical use; whole-file retrieval of even a single member-cycle is a ~25 GB download.

### GRIB2 encoding caveats

Two encoding decisions will silently mislead standard tooling:

- **Everything is Product Definition Template 0.** Members, ensemble mean, and ensemble spread all use PDT 0 (deterministic forecast). `numberOfForecastsInEnsemble`, `perturbationNumber`, `typeOfEnsembleForecast`, and `derivedForecast` are **absent from every message**. Member identity, and the distinction between a member, the mean, and the spread, exist **only in the filename**. A tool that groups by GRIB header alone will treat the spread field as an eighteenth-plus member and silently corrupt any statistic computed from it. Contrast [HGEFS](../../../ensemble_models/global/usa/hgefs.md), which declares its ensemble size correctly in-header.
- **APCP is encoded as instantaneous.** The precipitation record carries `stepType = instant` with no `lengthOfTimeRange` and no `typeOfStatisticalProcessing`, so the accumulation interval cannot be recovered from the headers. Empirically it is a **per-step hourly** accumulation: domain-mean values across f01/f03/f06/f12/f24/f48 of the 2026-08-06 12 UTC cycle were 0.0295, 0.0229, 0.0126, 0.0252, 0.0271, and 0.0234 mm — non-monotonic, and therefore not a run-total. Consumers must supply the accumulation semantics themselves.

Spread was confirmed to be genuine ensemble standard deviation rather than a mislabelled member: 2 m temperature at f06 spanned 0.07–2.54 K (mean 0.535 K) against member fields spanning 278–320 K.

---

## Notes
- **AI approach.** HRRR-Cast is a standalone data-driven AI emulator of the physics-based HRRR — NOAA's first *regional* experimental AI forecast system.
- **Relationship to HRRR.** It was conceived as an emulator of [HRRR](./hrrr.md), sharing HRRR's 3 km CONUS grid, and inherits that grid exactly. Early evaluations reported it performing at least as well as the operational HRRR for reflectivity out to ~7 hours, with comparable performance to 48 hours and comparable humidity, temperature, and wind skill. As of V3, GSL reports reflectivity and precipitation skill comparable to the operational HRRR at all forecast times, and ensemble performance comparable to HRRR and to experimental [RRFS](./rrfs.md) configurations. It complements rather than replaces HRRR.
- **Cycle length matches HRRR's pattern.** The 48 h / 18 h split by synoptic cycle mirrors the operational HRRR CONUS schedule exactly — a consequence of the emulation target, and convenient for like-for-like verification.
- **Verification & tooling.** Verified with the "WxVx" tool (built on the Model Evaluation Tools, MET) and compared against the operational HRRR in real time via GSL's Model Analysis Tool Suite (MATS). Ensemble output — including individual members in a "postage stamp" layout — is viewable in GSL's DESI visualization tool.
- **Efficiency.** Reported at 100–1000× the computational efficiency of the operational HRRR; runnable on a single laptop. GSL characterises the deterministic configuration as ~1000× faster and the probabilistic configuration as ~100× faster.
- **Code and weights are public.** The live pipeline is published at https://github.com/NOAA-GSL/HRRRCast-live (default branch `master`), which documents the ensemble driver, diagnostic computation, and GRIB2 export path. This is unusual among operational-track NWP systems and makes the ensemble construction independently auditable.
- **Ongoing development.** Stated focus areas are continued ensemble performance improvements, downscaling for fire-weather applications, and incorporating AI data assimilation. Formal evaluation at the NOAA Hazardous Weather Testbed was expected during 2026.

---

## Recent version history

### Version 1 — released July 2025
NOAA's first regional experimental AI forecast system, released by GSL. Trained on ~3 years of HRRR data. Produced forecasts at multiple lead times (1-, 3-, and 6-hour) for reflectivity, temperature, humidity, and winds across CONUS, with 12 vertical levels. Ran on a 6 km effective grid (every other HRRR grid point). Early results showed promise for probabilistic forecasting.

### Version 2 — experimental at EMC since September 2025 (announced November 24, 2025)
- Vertical resolution increased from **12 to 20 levels**
- New variables: 10 m and 80 m winds, 2 m dewpoint, 2 m relative humidity, total cloud cover, visibility, and cloud ceiling height
- Precipitation extended to **hourly accumulations**; convective diagnostics (CAPE, CIN) added
- Introduced the **HRRR-Cast ensemble** (9 members, hourly, to 48 h)
- Trained on **4 years** of HRRR data
- Still on the 6 km effective grid

### Version 3 — released March 2026
- **Horizontal resolution increased to native 3 km**, matching the operational HRRR — the first version to do so
- Trained on **nine years** of HRRR data
- **~30 new diagnostic variables**, including wind shear, helicity, updraft helicity, and vertical velocity extrema. GSL notes the new severe-convective fields require further investigation
- Low, mid, and high cloud cover added as **direct neural-network predictions**
- Reflectivity and precipitation skill now comparable to the operational HRRR at all forecast times
- Prepared for integration into Project EAGLE; code and data released publicly via NODD
- Ensemble size on the live feed rose to 18 members at some point between the V3 release and August 2026; the exact date is not recoverable from the feed given its ~2.5-day retention, and no NOAA source reviewed documents the count

---

## Official documentation
- HRRR-Cast home page (NOAA GSL): https://gsl.noaa.gov/hrrrcast/
- "HRRRCast Version 3: New release of NOAA's experimental regional AI forecast model" (NOAA GSL, Mar 25, 2026): https://gsl.noaa.gov/news/hrrrcast-version-3-new-release-of-noaas-experimental-regional-ai-forecast-model/
- "HRRRCast: Hourly, High-resolution Ensemble Forecasts Join Project EAGLE" (NOAA EPIC, May 19, 2026): https://epic.noaa.gov/hrrrcast-ensemble-forecasts-join-project-eagle/
- Project EAGLE ensemble configuration (NOAA EPIC): https://epic.noaa.gov/ai/eagle-ensemble-configuration/
- "New upgrades to HRRR-Cast" (NOAA GSL, Nov 24, 2025): https://gsl.noaa.gov/news/new-upgrades-to-hrrr-cast-noaas-experimental-ai-powered-regional-model/
- "NOAA Research develops an AI-powered sibling to its flagship weather model" (NOAA Research, Jul 22, 2025): https://research.noaa.gov/noaa-research-develops-an-ai-powered-sibling-to-its-flagship-weather-model/
- Live pipeline source code (NOAA-GSL): https://github.com/NOAA-GSL/HRRRCast-live
- AWS data (experimental PDS, S3): https://noaa-gsl-experimental-pds.s3.amazonaws.com/index.html#HRRRCast/
