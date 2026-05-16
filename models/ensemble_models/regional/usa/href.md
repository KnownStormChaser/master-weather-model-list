# HREF (High-Resolution Ensemble Forecast)

> ⚠️ **Scheduled for retirement August 31, 2026 at 12 UTC.** HREF is scheduled for retirement and replacement by the [REFS](./refs.md) (RRFS Ensemble Forecast System) under [NWS Service Change Notice 26-48](https://www.weather.gov/media/notification/pdf_2026/scn26-48_RRFS_and_REFS_Implementation.pdf) (May 12, 2026), the same cycle that brings RRFS and REFS into operations. All four operational HREF domains (CONUS, Alaska, Hawaii, Puerto Rico) are included. Originally proposed under [NWS PNS 25-41](https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf) (June 26, 2025); subject to the standard CWD/ECE postponement contingency.

## What this model is
The High-Resolution Ensemble Forecast (HREF) is NOAA's operational convection-allowing regional ensemble system, providing probabilistic guidance for severe weather, heavy precipitation, winter storms, and aviation hazards across CONUS, Alaska, Hawaii, and Puerto Rico.

Structurally, **HREF is unusual among operational ensembles**: rather than running its own ensemble forecast members, it is a **post-processing ensemble of already-existing model outputs**. HREF members are drawn from current-cycle and time-lagged forecasts of the HiresW deterministic system (ARW + FV3 cores), the [NAM Nest](../../../nwp_models/regional/usa/nam-nest.md), and the [HRRR](../../../nwp_models/regional/usa/hrrr.md). HREF performs no data assimilation of its own and runs no model integration of its own — it is a probabilistic aggregator that combines the diverse forecasts produced by its constituent systems.

This design is operationally efficient and exploits **member diversity from different dynamical cores** (FV3, NMMB B-grid, WRF-ARW, NMM-B), different physics suites, different DA systems, and different initialization times to produce a probabilistic spread that approximates a "true" ensemble. HREF originated from the **SPC Storm Scale Ensemble of Opportunity (SSEO)**, an experimental system at NSSL/SPC's Hazardous Weather Testbed that demonstrated skill for six years before becoming operational HREF v1 at NCEP in 2017.

The current operational version is **HREFv3** (operational since May 2021), which is the **final version** of HREF. No further upgrades are planned. HREFv3 will be replaced by the [REFS](./refs.md) RRFS-based regional ensemble on August 31, 2026, as part of the broader UFS transition (SCN 26-48).

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP)
- **Country / region:** United States
- **Operating center:** NCEP Central Operations (NCO); developed at the Environmental Modeling Center (EMC)
- **Heritage:** Developed from the SPC Storm Scale Ensemble of Opportunity (SSEO) by NSSL and SPC

---

## What area it covers
HREF runs over **four separate operational domains**, each populated with members from convection-allowing regional models for that domain:
- **CONUS** — 10 members
- **Alaska** — 8 members
- **Hawaii** — 6 members
- **Puerto Rico** — 6 members

Each domain is a separate ensemble — HREF does not produce a unified North American ensemble.

---

## Basic details
- **Model type:** Multi-model regional ensemble (post-processing ensemble of existing model outputs)
- **Aggregator system:** HREF post-processing software (community-maintained at NCEP/EMC)
- **Member dynamical formulations:** Mix of non-hydrostatic cores
  - HiresW ARW: WRF-ARW non-hydrostatic, C-grid Arakawa
  - HiresW FV3 (replaced HiresW NMMB in HREFv3): FV3 non-hydrostatic finite-volume cubed-sphere
  - NAM Nest: NMMB non-hydrostatic, B-grid Arakawa
  - HRRR: WRF-ARW non-hydrostatic, C-grid Arakawa
- **Convection-allowing:** Yes — all member models run at 3 km resolution and explicitly resolve deep convection
- **Ensemble size:** Domain-dependent (10 / 8 / 6 / 6 — see above)
- **Native horizontal resolution:** 3 km (all members)
- **Output grid:** 3 km HREF grid (standardized common grid for all members and ensemble products)
- **Forecast length:** 48 hours
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly

---

## Membership composition

HREF combines **current and time-lagged forecasts** from its constituent models. Time-lagging includes 6-hour and 12-hour earlier cycles (model-dependent), effectively doubling the ensemble size without doubling computational cost. The general structure is:

### CONUS (10 members)
- HiresW ARW (current and 6-h time-lagged)
- HiresW FV3 (current and 6-h time-lagged) — replaced HiresW NMMB in HREFv3
- NAM Nest (current and 6-h or 12-h time-lagged)
- HRRR (current and 6-h time-lagged) — added in HREFv3

### Alaska (8 members)
- HiresW ARW (current and 6-h time-lagged)
- HiresW FV3 (current and 6-h time-lagged)
- NAM Nest (current and 6-h or 12-h time-lagged)
- HRRR (current and 6-h time-lagged) — added in HREFv3

### Hawaii (6 members)
- HiresW ARW (current and 6-h time-lagged)
- HiresW FV3 (current and 6-h time-lagged)
- NAM Nest (current and 6-h time-lagged)

### Puerto Rico (6 members)
- HiresW ARW (current and 6-h time-lagged)
- HiresW FV3 (current and 6-h time-lagged)
- NAM Nest (current and 6-h time-lagged)

---

## Initial conditions and ensemble design
HREF inherits the **initial conditions and lateral boundary conditions of its member models** — there is no HREF-specific data assimilation or perturbation strategy.

The ensemble's spread comes from three sources of diversity:
- **Multi-dynamical-core diversity:** Members use four different dynamical cores (FV3, NMMB, two flavors of WRF-ARW), which produce structurally different forecasts even from identical initial conditions
- **Multi-physics diversity:** Different physics suites across HiresW ARW, HiresW FV3, NAM Nest, and HRRR contribute representational diversity
- **Time-lagging:** Combining current and 6-/12-hour-old cycles provides additional ensemble spread that captures forecast-state uncertainty across initialization times

**Note:** This design has practical advantages (cheap to run, leverages existing model investments, exploits dynamical-core diversity) but lacks the principled initial-condition perturbation framework of true ensembles like [GEFS](../../../ensemble_models/global/usa/gefs.md) or [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md). HREF spread does not necessarily reflect formal forecast uncertainty in the traditional sense.

---

## What it provides

### Standard ensemble products
- Individual member forecasts (8–10 per domain)
- Ensemble mean and ensemble spread
- Probability of exceedance for many fields (precipitation, snowfall, instability, wind, etc.)
- Probability of categorical events (severe weather thresholds)

### Distinctive HREF products
HREF is particularly known for several specialized products that have become heavily used in operational severe weather forecasting:
- **Probability-matched mean (PMM):** A variation of the ensemble mean that restores the original ensemble amplitude — helpful for displaying intense events that traditional ensemble means smooth out (Ebert 2001, *MWR*).
- **Localized probability-matched mean (LPMM):** Restricts the PMM calculation to a defined radius of influence around each grid point, preventing geographically distant precipitation features from influencing local values (Clark 2017, *WAF*). Added in HREFv3.
- **Neighborhood probabilities (NP):** Probabilities computed over a defined radius around each grid point, useful for capturing convective storm timing and location uncertainty.
- **Ensemble Agreement Scale (EAS) probabilities:** Added in HREFv3 for precipitation and snow.
- **Probability of UH (updraft helicity)** at thresholds of 75, 100, and 200 m²/s² — a critical product for severe weather operations.
- **Probability of exceedance of flash flood guidance values and average recurrence intervals** for the CONUS domain (added in HREFv3).

### Operational use
HREF products are heavily used by:
- **NOAA Storm Prediction Center (SPC)** — for severe weather outlooks and convective hazard forecasting
- **NOAA Weather Prediction Center (WPC)** — for excessive rainfall outlooks and quantitative precipitation forecasts
- **NWS Weather Forecast Offices** — for short-range severe weather, winter storm, and aviation forecasting

---

## Relationship to other models

HREF is **fundamentally dependent** on its constituent models. Any disruption to those models propagates directly to HREF.

- **[HRRR](../../../nwp_models/regional/usa/hrrr.md):** Provides 4 of the 10 CONUS HREF members (current + time-lagged), and 4 of the 8 Alaska HREF members. HREFv3 added HRRR to the membership for the first time.
- **[NAM Nest](../../../nwp_models/regional/usa/nam-nest.md):** Provides 2 members per domain (current + time-lagged). The 12-hour time-lagged NAM Nest is used for some HREF cycles.
- **HiresW (ARW, FV3):** Provides the foundational 4 members per domain. The HiresW system is itself slated for retirement under PNS 25-41 except for the Guam domain (see [HiresW Guam](../../../nwp_models/regional/usa/hiresw-guam.md)).
- **[REFS](./refs.md):** Replacement, operational August 31, 2026 at 12 UTC. REFS is built on the [RRFS](../../../nwp_models/regional/usa/rrfs.md) UFS infrastructure — combining current and 6 h time-lagged RRFS deterministic and ensemble cycles, plus current and 6 h time-lagged HRRR members for CONUS and Alaska — rather than the post-processing approach HREF uses. REFS runs to 60 hours (longer than HREF's 48 hours) at 4 cycles daily for all domains (HREF runs 4× daily only for CONUS; AK, HI, PR are 2× daily).
- **[NBM](../../../nwp_models/regional/usa/nbm.md):** Uses HREF probabilistic fields as inputs for blended guidance products.

### Heritage: SPC Storm Scale Ensemble of Opportunity (SSEO)
HREF originated as the SSEO at NSSL and SPC's Hazardous Weather Testbed in the early 2010s. SSEO was an experimental SPC research ensemble that demonstrated skill for six years before EMC adopted the design and made HREF operational at NCEP in 2017. The "ensemble of opportunity" concept — combining whatever convection-allowing model output happens to be operationally available, rather than running purposefully-perturbed ensemble members — is a defining HREF design choice that distinguishes it from true ensemble systems.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data formats:** GRIB2
- **Official download locations:**
  - **NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/href/prod/ (real-time, ~10-day rolling)
  - **AWS Open Data:** https://registry.opendata.aws/noaa-href/
  - **SPC HREF viewer:** https://www.spc.noaa.gov/exper/href/ (interactive product viewer; 00 and 12 UTC cycles)

HREF distributes ensemble products (mean, spread, probability fields, neighborhood probabilities, etc.) as separate GRIB2 files from individual member output. Individual members are available through NOMADS for users wanting raw model output.

---

## Notes
- HREF is a **post-processing ensemble**, not a true ensemble. Its spread reflects multi-model and time-lagging diversity rather than principled initial-condition perturbations. Users should interpret HREF probabilities accordingly — they reflect dispersion across structurally diverse forecasts, not formal forecast uncertainty.
- HREF's reliance on multiple constituent models means it inherits all of those models' biases, errors, and operational dependencies. If a constituent model is missing or delayed, HREF runs with reduced membership.
- The HREF probabilistic products — particularly PMM, LPMM, neighborhood probabilities, and UH probabilities — have become foundational guidance for severe weather forecasting at SPC and NWS Weather Forecast Offices, even as the underlying ensemble design is recognized as a "first-generation" approach.
- HREF is one of the relatively few operational ensemble systems with **explicit multi-dynamical-core diversity**. Other major ensembles like ECMWF ENS and GEFS use a single dynamical core across all members.
- HREFv3's addition of HRRR was particularly impactful — HRRR's hourly DA, dense radar assimilation, and convection-allowing skill brought significantly improved member quality to the CONUS HREF.
- HREF has been **frozen since HREFv3 (May 2021)**. The original HREFv3 announcement noted it would be "the final major upgrade of the HREF system," with REFS as the planned successor.

---

## Status and retirement timeline
- **Scheduled for full retirement August 31, 2026 at 12 UTC** under NWS Service Change Notice 26-48 (May 12, 2026); detailed product list in companion SCN 26-47.
- Retirement covers all four HREF domains (CONUS, Alaska, Hawaii, Puerto Rico).
- **Replacement** is the [REFS](./refs.md) RRFS-based regional ensemble, running to 60 hours (vs HREF's 48) at 4 cycles daily for all domains.
- Originally proposed for early 2026 under PNS 25-41; the date slipped through pre-operational evaluation of RRFSv1/REFS before being scheduled in SCN 26-48. Per SCN 26-48, if August 31, 2026 is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, retirement moves to 12 UTC on the next eligible weekday.
- 2025 NOAA Hazardous Weather Testbed Spring Forecasting Experiment evaluations indicated REFS performed competitively with HREF for Day 1 and Day 2 forecasts, and slightly better for some objective metrics including deep convection (>40 dBZ) prediction. This supported the decision to proceed with HREF→REFS replacement.

---

## Recent version history

### HREFv3 — operational May 2021 (current; final version)
The last HREF upgrade. Headline changes:
- **HiresW NMMB replaced by HiresW FV3** for all domains — first time an FV3-based regional model was included in HREF
- **HRRR added as a new member** for CONUS and Alaska domains
- CONUS membership increased from 8 to 10; Alaska from 6 to 8; Hawaii and Puerto Rico unchanged at 6 each
- Forecast length extended to 48 hours (from 36 h in HREFv2)
- Added local probability-matched mean (LPMM) as a new product
- Added Ensemble Agreement Scale (EAS) probabilities for precipitation and snow
- Added flash flood guidance and average recurrence interval probabilities for CONUS
- The HREFv3 announcement explicitly noted it would be the final major upgrade of HREF, with [REFS](./refs.md) planned as the successor

### HREFv2 — operational 2018
- Earlier operational version with HiresW NMMB and HiresW ARW members plus NAM Nest
- 36-hour forecast length
- 8 CONUS members, 6 Alaska / Hawaii / Puerto Rico members

### HREFv1 — operational 2017
First operational implementation of the HREF design at NCEP, based on the experimental SPC SSEO. Initially CONUS-focused; subsequent HREFv2 added other domains.

### Heritage: SPC SSEO (2010s)
The Storm Scale Ensemble of Opportunity ran as an experimental product at SPC and NSSL's Hazardous Weather Testbed beginning in the early 2010s, demonstrating skill before transition to NCEP operations as HREFv1 in 2017.

---

## Official documentation
- HREF/HiresW at EMC: https://www.emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/href-hiresw.php
- HREF Vlab page: https://vlab.noaa.gov/web/emc/href-hiresw
- HREF v3.0 release notes: https://www.nco.ncep.noaa.gov/pmb/codes/nwprod/href.v3.1.7/release_notes_href_v3.0.0.pdf
- HREFv3 announcement: https://www.weather.gov/news/211205-href-model-upgrade
- HREFv3 graphics (EMC): https://www.emc.ncep.noaa.gov/mmb/mpyle/hrefv3/
- SPC HREF Ensemble Viewer: https://www.spc.noaa.gov/exper/href/
- NWS SCN 26-48 (RRFS and REFS implementation, HREF retirement effective August 31, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-48_RRFS_and_REFS_Implementation.pdf
- AWS Open Data: https://registry.opendata.aws/noaa-href/

### Key references
- Ebert, E. E. (2001). *Ability of a poor man's ensemble to predict the probability and distribution of precipitation.* Mon. Wea. Rev., 129, 2461–2480. (Foundation for the probability-matched mean technique)
- Clark, A. J. (2017). *Generation of ensemble mean precipitation forecasts from convection-allowing ensembles.* Wea. Forecasting, 32, 1569–1583. https://doi.org/10.1175/WAF-D-16-0199.1 (Foundation for LPMM)
- Roberts, B., et al. (2020). *IMPACTS of skillful convection-allowing ensemble forecasts on operational severe weather forecasting at NOAA's Storm Prediction Center.* Wea. Forecasting, 35, 1923–1940. https://doi.org/10.1175/WAF-D-19-0199.1 (Operational impact assessment of HREF)
