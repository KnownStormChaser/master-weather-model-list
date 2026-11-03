# NBM (National Blend of Models)

## What this model is
The National Blend of Models (NBM) is a statistically post-processed forecast system that blends and calibrates output from many different numerical weather prediction models into a single, nationally consistent set of deterministic and probabilistic forecast guidance.

Rather than running its own atmospheric simulation, the NBM applies bias correction, weighting, and blending techniques to both NWS and non-NWS model guidance to produce a highly skillful starting point for official National Weather Service forecasts and the National Digital Forecast Database (NDFD).

The current operational version is **NBM v5.1**, implemented with the 12 UTC run on **November 3, 2026**. It brings in the newly operational [RRFS](./rrfs.md) and [REFS](../../../ensemble_models/regional/usa/refs.md) as inputs and drops the retired NAM, HiResW (except Guam) and HREF. See [Version history](#version-history).

---

## Who runs it
- **Organization:** NOAA / National Weather Service — Meteorological Development Laboratory (MDL), now part of NWS's new **Office of Modeling and Development**
- **Country / region:** United States

---

## What area it covers
- **Coverage:** United States and surrounding regions
- **Domain details:**  
  Multiple operational domains, including CONUS, Alaska, Hawaii, Puerto Rico, Guam, Oceanic regions, and a coarse global domain

---

## Basic details
- **Model type:** Statistical multi-model blend (post-processed guidance)
- **Horizontal resolution:**  
  - CONUS: ~2.5 km  
  - Alaska: ~3.0 km  
  - Hawaii: ~2.5 km  
  - Puerto Rico: ~1.25 km  
  - Oceanic: ~10 km  
  - Global: ~50 km
- **Forecast length:**  
  Up to ~264 hours (11 days), depending on variable
- **Update frequency / cycles:** Hourly (24× daily)
- **Temporal output resolution (since NBM v5.0):**  
  - Hourly out to 48 h for most parameters  
  - 3-hourly through ~192 h  
  - 6-hourly thereafter  
  - Some aviation and severe-weather fields keep the pre-v5.0 hourly-to-36 h cadence: ceiling, visibility, cloud base, cloud layers, low-level wind shear, turbulence, Ellrod Index, solar radiation, thunderstorm coverage, thunderstorm probability, and the Craven-Wiedenfeld Aggregate Severe Parameter

---

## What it provides
Statistically calibrated deterministic and probabilistic forecast guidance for:
- Near-surface temperature, dewpoint, apparent temperature, wind, and pressure
- Precipitation amount, type, and probability-matched mean (PMM) QPF (24h, CONUS/AK)
- Snow and ice guidance, including PMM snow/ice, calibrated snow exceedance (24h/48h/72h, CONUS), and deterministic snow depth (CONUS/AK)
- Sky cover, ceilings, and visibility
- Wind gusts and hazardous weather parameters
- Fire weather joint probabilities (wind speed × relative humidity combinations)
- Deterministic and probabilistic precipitable water
- Probabilistic surface-based CAPE (10th/50th/90th percentiles, CONUS)
- Wet bulb globe temperature in NBM text products (NBH/NBS/NBE/NBX)
- Probabilistic guidance (percentiles, thresholds, and risk-based products)

NBM guidance is the primary foundation for official NWS gridded forecasts in the [National Digital Forecast Database](./ndfd.md).

---

## Input models
NBM ingests a large, evolving set of deterministic, ensemble, statistical and wave/marine inputs from five operational centres: **NOAA/NWS**, the **Canadian Meteorological Centre (CMC / ECCC)**, **ECMWF**, the **U.S. Navy (FNMOC)**, and the **Australian Bureau of Meteorology (BoM)**. The table reproduces the official [NBM Model Inputs](https://vlab.noaa.gov/web/mdl/nbm-model-inputs) page as of 2026-11-03, which reflects the v5.1 input set. Resolutions are the input resolution NBM ingests, not necessarily the source model's native grid. "Updated" is the NBM cycle (UTC) at which a new run of that input is first used.

The mix of inputs differs from element to element and regionally. For which inputs feed a given output, see the [NBM Weather Elements table](https://vlab.noaa.gov/web/mdl/nbm-weather-elements). For a live view of which models were available for the latest 24 hours of runs, see the [NBM dashboard](https://blend.mdl.nws.noaa.gov/nbm-dashboard).

| Group | Model | Agency | Input grid | Cycles/day | Members | Updated (UTC) |
|---|---|---|---|---|---|---|
| Global deterministic | GFS | NCEP | 13 km | 4× | 1 | 05, 11, 17, 23 |
| | ECMWFD (IFS) | ECMWF | 0.10° | 2× | 1 | 08, 20 |
| | GDPS | CMC | 15 km | 2× | 1 | 06, 18 |
| | NAVGEMD | FNMOC | 50 km | 4× | 1 | 00, 06, 12, 18 |
| | ACCESS-G | BoM | 12 km | 2× | 1 | 07, 19 |
| | AIGFS (AI) | NCEP | 0.25° | 4× | 1 | 05, 11, 17, 23 |
| | ECAIFS (AI) | ECMWF | 0.25° | 2× | 1 | 08, 20 |
| Regional deterministic | RAP | NCEP | 13 km | 24× | 1 | every hour |
| | RAP-EXT | NCEP | 13 km | 4× | 1 | 00, 06, 12, 18 |
| | RDPS | CMC | 10 km | 4× | 1 | 04, 10, 16, 22 |
| Convection-allowing | **RRFS** (new in v5.1) | NCEP | 3 km | 24× | 1 | every hour |
| | HRRR | NCEP | 3 km | 24× | 1 | every hour |
| | HRRR-EXT | NCEP | 3 km | 4× | 1 | 03, 09, 15, 21 |
| Tropical cyclone | HAFS-A | AOML | 2 km | 4× | 1 | 00, 06, 12, 18 |
| | HAFS-B | AOML | 2 km | 4× | 1 | 00, 06, 12, 18 |
| Ensembles | **REFS** (new in v5.1) | NCEP | 3 km | 4× | 5 | 00, 06, 12, 18 |
| | GEFS | NCEP | 25 km | 4× | 30 | 01, 07, 13, 19 |
| | ECMWFE | ECMWF | 0.20° | 2× | 50 | 09, 21 |
| | GEPS | CMC | 50 km | 2× | 20 | 07, 19 |
| | REPS | CMC | 10 km | 2× | 20 | 07, 19 |
| | NAVGEME | FNMOC | 50 km | 2× | 20 | 06, 18 |
| | ACCESS-GE | BoM | 35 km | 4× | 18 | 02, 08, 14, 20 |
| Statistical / analysis | GFS MOS STN | NCEP | stations | 4× | 1 | 00, 06, 12, 18 |
| | GMOS | NCEP | 2.5 km | 2× | 1 | 07, 19 |
| | EC MOSD | ECMWF | stations | 2× | 1 | 08, 20 |
| | EC MOSE | ECMWF | stations | 2× | 50 | 09, 21 |
| | LAMPMOS | NCEP | stations | 24× | 1 | every hour |
| | GLMP (gridded LAMP) | NCEP | 2.5 km | 24× | 1 | every hour |
| | MELD | NCEP | 2.5 km | 24× | 1 | every hour |
| | MMEBC (WPC QPF) | NCEP/WPC | 5 km | 4× | 1 | 00, 06, 12, 18 |
| | QMD (NBM quantile-mapped stream) | MDL | 2.5 km | 4× | 1 | 01, 07, 13, 19 |
| | RTMA | NCEP | 2.5 km | 24× | 1 | every hour |
| Waves | GFS WW3D | NCEP | 25 km | 4× | 1 | 05, 11, 17, 23 |
| | GEFS WW3E | NCEP | 25 km | 4× | 30 | 01, 07, 13, 19 |
| | WW3D HIRES | NCEP | 9–17 km | 4× | 1 | 05, 11, 17, 23 |
| | GLWU (Great Lakes) | NCEP | 2.5 km | 4× | 1 | 02, 08, 14, 20 |
| | GEPS Waves | CMC | 25 km | 2× | 20 | 07, 19 |
| | REWPS | CMC | 2.5 km | 2× | 20 | 05, 17 |
| | NAVGEMD Waves | FNMOC | 100 km | 2× | 1 | 07, 19 |
| | NAVGEME Waves | FNMOC | 50 km | 2× | 20 | 07, 19 |

**Reading the table:**
- **The "REFS" input is the five RRFS ensemble members, not the REFS combined products.** Per MDL's v5.1 briefing, NBM v5.1 uses **no REFS time-lagged members**. HRRR, which is technically part of REFS, is used exactly as before. The members are the ones published under `rrfsens.YYYYMMDD/` (see the [RRFS entry](./rrfs.md#ensemble-member-output)).
- **Gone from the table at v5.1:** NAM (12 km parent and 3 km nest), the three HiResW members (ARW, ARW MEM2, FV3), HREF and NAM MOS, all of which retired on November 3, 2026.
- **The Guam exception is not shown.** MDL's v5.1 briefing says that, since RRFS/REFS do not cover Guam, the NBM **continues to use the two HiResW Guam members** there (ARW and FV3; see [HiresW Guam](./hiresw-guam.md)). The inputs table omits HiResW entirely.
- **HAFS-A and HAFS-B have replaced HWRF and HMON** as the tropical-cyclone inputs. The v5.0-era table listed HWRF and HMON at 1.5 km. When the switch happened is not recorded (TBD). The table credits HAFS to AOML. HAFS is run operationally by NCEP and was co-developed with AOML, so read "agency" here as the development partner. See [HAFS](../../../tropical_cyclone_models/hafs.md).
- **The resolution discrepancies flagged at v5.0 are resolved.** The table now shows the SCN 26-24 values for ECMWFD (0.10°), ECMWFE (0.20°), GDPS (15 km) and REPS (10 km), and AIGFS at 0.25°.
- **Not in the table:**
  - the ten Canadian members (a GDPS/GEPS/RDPS/REPS mix) that v5.0 added to the winter suite;
  - the RTOFS/RIOPS ocean/ice inputs and the NHC wTCM wind module that earlier revisions of this entry listed. Whether NBM still uses them is unconfirmed (TBD).
- **The page footer still reads "model inputs and sources for the NBM v5.0".** The table itself has been updated for v5.1: RRFS and REFS are present, and NAM, HiResW and HREF are absent.

### How v5.1 uses RRFS and REFS
From MDL's briefing *NBM v5.1 — Changes related to RRFS/REFS usage* (linked from the NBM Versions page):

**Elements that use RRFS/REFS data**

| Quantile-mapping system (QMD) | Core blend | Winter suite |
|---|---|---|
| Temperature, dew point, wind speed/gust, precipitation, relative humidity, apparent temperature | Sky cover, 1-h PoP, 1-h precipitation amount, surface CAPE, CWASP, transport wind, solar radiation, mixing height, low-level turbulence, low-level wind shear, sea surface temperature, maximum hourly reflectivity (RRFS only), vertically integrated liquid (RRFS only), precipitable water | Precipitation type, snow-liquid ratio, snow accumulation, ice accumulation, snow level, snow depth |

**When the data enter the blend**
- **QMD** runs 6.5 hours after its nominal cycle time, using RRFS and REFS from the same initialization (e.g. the 00z QMD uses 00z RRFS to 84 h and 00z REFS to 60 h, and runs at 0630Z). The most recent QMD output is copied into the core blend starting 7 hours after the QMD cycle and persists until the next QMD run. For example, 00z QMD output appears in the 07z–12z core blend.
- **Core blend and winter suite:**
  - Hourly RRFS (to 18 h) is used 3 hours old, so the 00z NBM uses the previous day's 21z RRFS.
  - The 84 h extended RRFS and the 60 h REFS refresh every 6 hours and are ingested 4 hours later, so the 00z runs first appear in the 04z NBM.
- **The winter suite does not ingest QMD precipitation from RRFS/REFS.** It uses direct model output precipitation instead.

**Weights**

The general principle was to take the weight from the removed systems and spread it evenly across RRFS and the REFS members.

| Weighting | Elements |
|---|---|
| Expert-weighted heavily toward RRFS/REFS | 1-h precipitation amount and PoP, precipitation type, snow-liquid ratio, snowfall, ice accumulation, snow level, snow depth, surface CAPE, sea surface temperature, low-level wind shear |
| Resolution-based (RRFS/REFS weighted heavily for their high resolution) | 6/12/24/48/72-h precipitation amount and PoP |
| Equal to other inputs | Temperature, dew point, wind speed and gust, relative humidity, apparent temperature, transport wind, mixing height, maximum hourly reflectivity, vertically integrated liquid |
| Dynamic (fluctuate daily) | Sky cover, precipitable water |

**Fewer domain seams.** Under v5.0 the NBM showed visible seams at the edges of the separate CAM domains. RRFS/REFS run on a single large North American domain, which removes most of them. HRRR still uses smaller domains, so faint seams can remain before about f036.

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, Cloud-Optimized GeoTIFF (COG), and ASCII/text bulletins (NBH/NBS/NBE/NBX/NBP)
- **Official download locations:**
  - **AWS — operational GRIB2:** `s3://noaa-nbm-grib2-pds/` — real-time plus an archive back to 2020-05-18. Organized by date (`blend.YYYYMMDD/`), with each cycle holding `core/`, `qmd/` and `text/`. Registry: https://registry.opendata.aws/noaa-nbm/
  - **AWS — operational COG:** `s3://noaa-nbm-pds/` — Cloud-Optimized GeoTIFF, one file per element and valid time. Organized by version, then domain, then date (`blendv3.2/` … `blendv5.0/`). Registry: https://registry.opendata.aws/noaa-nbm/
  - **AWS — parallel (pre-operational test stream):** `s3://noaa-nbm-para-pds/` — GRIB2 only (`core/` + `qmd/`, no text bulletins). It keeps a rolling window of about 5 days, with no archive, and carries the next-version candidate ahead of operational implementation. Registry: https://registry.opendata.aws/noaa-nbm-parallel/
  - **NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/blend/prod/ — real-time GRIB2 and text; about 2 days' retention, with no archive or parallel stream. Directory listings need `curl --http1.1` (see the [RRFS entry](./rrfs.md#data-availability)).

> ⚠️ **The COG stream has stopped at the v5.1 cutover (checked 2026-11-03, ~22 UTC).**
> - Every domain under `blendv5.0/` ends with the **11 UTC cycle of November 3**; the global domain ends with its 00 UTC cycle.
> - No `blendv5.1/` prefix has any content yet.
> - The GRIB2 bucket and NOMADS are unaffected and current.
>
> Previous version changes opened a new `blendvX.Y/` prefix, so a `blendv5.1/` tree is the likely successor. Whether it appears, and whether the gap is backfilled, is not yet known (TBD). Until then, use GRIB2.

### Text bulletin products
| Label | Product | Time step | Forecast hours |
|---|---|---|---|
| NBH | Hourly | Hourly | 1–25 h |
| NBS | Short | 3-hourly | 6–72 h |
| NBE | Extended | 12-hourly | 24–192 h |
| NBX | Super-Extended | 12-hourly | 204–264 h |
| NBP | Probabilistic (Extended) | 12-hourly | 24–264 h |

---

## Notes
- **Operational role:**
  - Days 0–3: the NBM is the starting point for the NWS gridded forecast, with WFOs making local edits.
  - Days 4–7 (CONUS): the NBM now serves directly as the gridded forecast, with only minor edits from the Weather Prediction Center in consultation with WFOs. The same Days 4–7 role is being extended to Alaska.
  - The NBM also supplies key tools for NWS Impact-Based Decision Support Services (IDSS).
- Bias correction methods include decaying averages, quantile mapping (QM), and dynamically weighted model contributions. NBM v5.0 extended QM to instantaneous temperature, dew point, apparent temperature, relative humidity, 12-hour max/min RH and significant wave height, replacing decaying-average logic for those fields.
- NBM v5.0 introduced a "percentile picking" method for deterministic wind speed and gust. It moves the forecast away from the mean QM value, depending on where the mean sits relative to the model distribution and climatology.
- NBM produces both deterministic and probabilistic guidance, and v5.0 increased consistency between them by applying shared input weighting and derivation methods.
- The Haines Index has not been produced since NBM v5.0 (CONUS, AK, HI, PR domains).

---

## Planned future versions

> **Status:** Unofficial. No NWS Service Change Notice or PNS exists for either item below as of November 3, 2026. The details come from NOAA/NWS presentation decks and should be treated as a planning signal, not a commitment.

### Winter-guidance and Southwest Pacific package (unnumbered)
In the February and April 2026 decks this package was described as "NBM v5.1". **That number has since gone to the RRFS/REFS input release of November 3, 2026**, so this package now has no version label. Its planned scope:
- **12-hour winter products** added to the winter suite
- Continued work on **remaining winter-weather guidance issues** left after v5.0
- Fixes for **"blocky" percentile fields** and **precipitation-type (ptype) fields**
- A **new NBM domain in the Southwest Pacific**, a coverage expansion rather than a methodology change

### PRO-GUIDES (long-term successor program)
The NBM user webinar (April 15, 2026) describes the **Next-Generation Probabilistic Forecast Guidance Suite (PRO-GUIDES)** as the longer-term direction beyond the v5.x line. A blueprint has been drafted, and a technical feasibility team has been tasked with assessing current versus needed resources. The stated intent is to use modern machine-learning / AI techniques for smarter blending and bias correction, and to better predict high-impact events. It has no version number, schedule or SCN.

**Sources (presentation decks, not formal notices):**
- NBM user webinar slides, April 15, 2026: https://www.weather.gov/media/wrn/calendar/NBMUserWebinar4-15-26.pdf
- WPC/HMT "NBMv5 Winter Overview" slides, February 10, 2026: https://www.wpc.ncep.noaa.gov/hmt/hmt_webpages/seminars/wwe/2026/slides_20260210.pdf

---

## Version history

### NBM v5.1 (operational November 3, 2026)
Implemented with the 12 UTC run on November 3, 2026, on the same cycle as the RRFS/REFS implementation and the NAM/HiResW/HREF/SREF terminations. The formal notice is [NWS SCN 26-89](https://www.weather.gov/media/notification/pdf_2026/scn26-89_RRFS_Downstream.pdf) (October 2, 2026), which gives the change no version number. The **v5.1** label comes from MDL's NBM Versions page. The planning decks had expected an interim "v5.0.1" around mid-July 2026, tracking an RRFS date that later slipped four times.
- **Inputs added:** [RRFS](./rrfs.md) (hourly to 18 h, extended to 84 h every 6 hours) and the five RRFS ensemble members (labelled REFS, to 60 h).
- **Inputs removed:** NAM (parent and nest) and the three HiResW members (two ARW, one FV3), on the CONUS, Alaska, Hawaii and Puerto Rico domains. The two HiResW Guam members remain in use. HREF and NAM MOS are also gone from the inputs table, which settles the two points SCN 26-89 left open.
- **Weights:** The expert weights held by NAM and HiResW were reassigned to RRFS/REFS, in both the core and QMD systems (see [How v5.1 uses RRFS and REFS](#how-v51-uses-rrfs-and-refs)).
- **Products:**
  - The CONUS core dry thunderstorm probability record (`DRYTPROB`) was discontinued. It was confirmed absent from the 12 and 18 UTC cycles of November 3, after appearing at the same cycles the day before.
  - The `YYA*** KWEI` SBN header was discontinued.
- **Visible effect:** Fewer domain seams near the edges of the old CAM domains.

### NBM v5.0 (operational May 5, 2026)
Implemented with the 13 UTC cycle on May 5, 2026, under NWS SCN 26-24. The original SCN (March 12, 2026) targeted April 15, 2026, and later updates moved it to April 23, then April 30 (AAC revision, April 28, 2026). The April 30 attempt fell within a Critical Weather Day / Enhanced Caution Event window, which triggered the SCN's contingency provision and pushed the cutover to May 5.

Key changes:
- Hourly guidance extended from 36 h to 48 h for most parameters.
- **New products:**
  - 24h PMM QPF and snow/ice (CO/AK)
  - joint fire weather probabilities
  - quantile-mapped 10 m wind and gust for AK/HI/PR/GU/OC
  - precipitable water guidance
  - calibrated snow exceedance (CO)
  - deterministic snow depth (CO/AK)
  - apparent temperature
  - CAPE weighted percentiles (CO)
- **Removed:** Haines Index.
- **Methodology:** QM used more widely, a new percentile-picking wind/gust method, and shared input weighting for deterministic and probabilistic outputs.
- **Hawaii ceiling and visibility** guidance replaced with the gridded LAMP "Meld" approach. It draws on the gridded observational analysis, RAP, GFS and ECMWF, with a random-forest "pseudo-observation" technique for Hawaiian island points.
- **Significant wave height (SWH) bias correction** now uses ~120 ensemble member inputs, up from 13 ensemble means.
- **SBN/NOAAPORT changes:**
  - removed the binary scaling factor for several wind and temperature elements;
  - added new cycles, projections and elements;
  - the **NBM global product is no longer sent over SBN/NOAAPORT**, though it is still produced and available via NOMADS.
- **Text product changes:** WBGT added to NBH/NBS/NBE/NBX; SWH available for all cycles in NBX; HID (Haines Index) removed from all text products.

Input changes in v5.0:
- Higher-resolution ECMWF (0.10°) and ECMWF Ensemble (0.20°), replacing 0.25° and 0.50°.
- Higher-resolution GDPS (15 km) and REPS (10 km), replacing 25 km and 15 km.
- ECAIFS (ECMWF AI/IFS) and AIGFS added as inputs for temperature, wind speed and QPF, on all domains except no QPF over Guam.
- Higher-resolution GEFS surface guidance (0.25° through 240 h; the vertical profile remains at 0.5°).
- WPC MMEBC QPF added as an input (CO).
- Ten Canadian ensemble members added to the winter suite.
- New wave input: REWPS (CMC, 2.5 km).
- **SREF dropped as an input.** That removed SREF's last major downstream consumer and let SREF's retirement be folded into the November 2026 RRFSv1 cutover.

Patch releases on v5.0:
- **v5.0.14** (July 28, 2026; SCN 26-70) — improved temperature guidance during seasonal transition periods, including anomalous temperature and dewpoint values.
- **v5.0.15** (August 21, 2026; SCN 26-74) — fixed the NBM's use of the NHC gridded tropical cyclone forecast, effective immediately.

### NBM v4.3 (operational May 27, 2025)
A smaller package to fill a gap identified by the NWS Tropical Service Program, plus minor fixes for the Public, Winter, Fire Weather and Aviation services. Its main change was to the NBM Tropical Cyclone Feature-Matched wind speed product. That work was originally planned for v5.0 but brought forward at the National Hurricane Center's request for the 2025 tropical season. The Storm Prediction Center's probabilistic severe products (tornado, hail, wind) were extended to Day 2 and recalibrated from a GEFS + HREF blend, replacing SREF. Announced under SCN 25-34 (April 15, 2025), after comments under PNS 24-75.

### NBM v4.2 (operational May 15, 2024)
Proposed under PNS 23-45 (comments through August 31, 2023). MDL's versions page gives no change summary; the details are in MDL's *National Blend of Models (NBM): Version 4.2 Upgrades and Improvements* document, linked from that page.

### NBM v4.1 (operational January 17, 2023)
Filled product gaps requested by the Aviation, Water Resources, and Marine and Tropical service programs. Highlights by service area:
- **Public:**
  - QMD probabilistic MaxT/MinT percentiles and exceedances (AK)
  - 24h quantile-mapped peak wind and gust percentiles (CO)
  - QMD PQPF 10th/50th/90th percentiles for 48h and 72h (CO, AK, HI, PR)
  - Wet Bulb Globe Temperature (CO, HI, PR, GU)
  - a 100-member, 50 km quasi-global upper-air blend (85°N–85°S)
- **Winter:** Improved calibration of snow, ice, freezing rain and unconditional precipitation type, using 100 ensemble members of direct-model-output ptype; snow level percentiles added.
- **Aviation:**
  - AWC 3-layer cloud product, high cirrus base and coverage, and icing (CO)
  - SPC calibrated Day-1 (4h and 24h) hail, wind and tornado probabilities (CO)
  - Alaska deterministic aviation guidance to 84 h
  - thunderstorm coverage (CO, AK, HI, PR, OC)
- **Fire weather:** Mixing heights calibrated against the RAP analysis.
- **Tropical:** A tropical cyclone feature-matching technique to preserve NHC's gridded wind field (wTCM) (CO, AK, HI, PR, OC).
- **Marine:** 10th/50th/90th percentile significant wave height on all domains; sea ice concentration and thickness for Alaska.

### NBM v4.0 (operational September 29, 2020)
- Calibrated PQPF extended to the Alaska, Puerto Rico and Oceanic domains, with further PQPF science improvements.
- Probabilistic MaxT/MinT percentiles and exceedances for CONUS.
- Standard deviations for temperature, dew point, sky cover, CAPE and significant wave height (CO, AK, HI, PR).
- Improved deterministic aviation guidance to 84 h for TAF planning.
- 6-h probability of thunder (CO, AK, HI, PR, OC).
- New Guam elements.
- Text products expanded with new elements and more than 2,000 new stations, about 9,000 in total.

### NBM v3.2 (operational February 19, 2020)
- PQPF improvements, especially in the mountainous West, with calibrated PQPF added for Alaska and Puerto Rico.
- Snowfall and ice percentiles and exceedances (CO, AK).
- Probabilistic MaxT/MinT percentiles (CO).
- Standard deviations for MaxT, MinT, 10 m wind and gust (CO, AK, HI, PR).
- Visibility and ceiling extended to 84 h for TAFs.
- New text elements and a new probabilistic text product.

### NBM v3.1 (operational October 2018, AWIPS 18.1.1)
- Added ECMWF deterministic and ensemble, and HRRR-Extended (1–36 h), as inputs across the CONUS, OCONUS and Oceanic domains.
- Introduced the **NBM station text bulletins**: MOS-style nearest-grid-point forecasts, four products generated every hour. Before v3.1, NBM was distributed as GRIB2 grids only.

### NBM v3.0 (operational July 27, 2017, AWIPS 17.3.1)
- More global and mesoscale inputs for the CONUS, OCONUS and Oceanic domains.
- **Hourly runs** with 1–36 h hourly forecasts.
- New elements, including calibrated 6-h PoP and QPF, ceiling and visibility, weather type, and snow and ice amounts (NWS SCN 17-59).

### NBM v2.0 (operational November 15, 2016, AWIPS 16.2.2)
Added the OCONUS domains: Alaska, Hawaii and Puerto Rico.

### NBM v1.0 (operational January 6, 2016, AWIPS 16.1.1)
The first operational NBM, developed after Congress mandated a blended-model approach following Hurricane Sandy (2012). It used three models (GFS, GEFS mean, CMC ensemble mean) for temperature, wind and sky cover over CONUS, twice daily.

---

## Official documentation
- NBM home (MDL): https://vlab.noaa.gov/web/mdl/nbm
- NBM Model Inputs: https://vlab.noaa.gov/web/mdl/nbm-model-inputs
- NBM Versions (includes the v5.1 "NBM changes for RRFS/REFS usage" briefing and the v4.2 upgrades document): https://vlab.noaa.gov/web/mdl/nbm-versions
- NBM Weather Elements: https://vlab.noaa.gov/web/mdl/nbm-weather-elements
- NWS SCN 26-89 (October 2, 2026; NBM inputs switch from NAM/HiResW to RRFS/REFS; DRYTPROB and `YYA*** KWEI` discontinued; effective November 3, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-89_RRFS_Downstream.pdf
- NWS SCN 26-74 (NBM v5.0.15, August 21, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-74_NBMv5.0.15.pdf
- NWS SCN 26-70 (NBM v5.0.14, July 28, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-70_nbmv5.0.14.pdf
- NWS SCN 26-24 (NBM v5.0 implementation, AAC revision dated April 28, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-24_Updated_NBM_V5.0_aac.pdf
- NWS SCN 25-34 (NBM v4.3 implementation): https://www.weather.gov/media/notification/pdf_2025/scn25-34NBM_V4.3.pdf
