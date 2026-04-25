# Unified Forecast System (UFS)

This page indexes all NOAA models in this repository that are part of, are being consolidated into, or are being retired by the **Unified Forecast System (UFS)** — NOAA's community-based, coupled Earth-system modeling framework that is gradually replacing the patchwork of independently-developed operational systems NCEP has run for decades.

The goal of this index is to make a coherent programme visible. Right now UFS shows up across the repository as separate line items — RRFSv1 here, GFSv17 there, RTOFS v3.0 elsewhere, NAM retirement somewhere else — but they are all parts of the same consolidation effort, scheduled and sequenced together. This page exists to surface that coherence.

Last updated: April 2026.

---

## What UFS is

UFS is a **coupled Earth-system modeling framework** developed jointly by NOAA, NCAR, the U.S. Navy, NASA, the broader research community, and external operational and academic partners. Its operational purpose is to consolidate NOAA's operational forecast suite — historically a collection of independently-developed systems with overlapping responsibilities and divergent infrastructure — into a single, modular framework where atmosphere, ocean, sea ice, waves, land, and atmospheric composition share a common code base, common couplers, and a common community development model.

NOAA's stated goal is to reduce the **NCEP Production Suite from 21 operational systems to 8** under UFS. The 21 → 8 figure is NOAA's own framing and refers specifically to operational NCEP systems; it is not a claim about every model in this repository. What follows is the subset of those changes that affect publicly downloadable systems documented here.

The core architectural pieces of UFS are:

- **Atmosphere:** FV3 (Finite-Volume Cubed-Sphere) in current and near-term configurations; MPAS (Model for Prediction Across Scales) is the planned successor for the convection-allowing regional component (RRFSv2)
- **Ocean:** MOM6 (Modular Ocean Model 6)
- **Sea ice:** CICE6 (Community Ice CodE)
- **Waves:** WAVEWATCH III
- **Land:** Noah-MP
- **Atmospheric composition:** CMAQ (Community Multiscale Air Quality) embedded within UFS, replacing the legacy offline-coupled GFS-CMAQ chain
- **Coupler:** CMEPS (Community Mediator for Earth Prediction Systems), built on the NUOPC/ESMF infrastructure
- **Data assimilation:** JEDI (Joint Effort for Data assimilation Integration) — the new community DA framework being introduced in stages, replacing legacy GSI for several variables in GFSv17

For external technical documentation of UFS itself, see the UFS Community site at https://ufscommunity.org/. This page documents UFS from the operational catalog perspective: what's running, what's being retired, and what users need to switch to.

---

## How UFS is rolling out

UFS is not arriving as a single cutover. Components are being absorbed into UFS one at a time, on a multi-year schedule, with each operational replacement triggering specific retirements. The rough sequencing is:

1. **2023 — HAFS replaces HWRF and HMON.** The first major UFS-based operational system, replacing both legacy hurricane models simultaneously. HAFS uses FV3 atmosphere with online CMEPS coupling to ocean and waves.
2. **2024 — AQM v7 migrates to UFS-based architecture.** The atmospheric composition component of NAQFC moved from offline-coupled GFS-CMAQ to UFS-based online-coupled atmosphere-chemistry.
3. **2025 — RTOFS v2.5 deployed alongside HAFSv2.1.** RTOFS itself stays on HYCOM in v2.5; the UFS-relevant connection is that HAFSv2.1 is initialized from RTOFS v2.5, tightening the operational dependency between these systems.
4. **Targeted early-to-mid 2026 — RRFSv1 + REFS replace NAM, NAM Nest, HiresW (except Guam), HREF, and NARRE.** This is the largest wave of retirements and is the first time UFS replaces multiple regional systems simultaneously.
5. **Targeted October 2026 (proposed) — GFSv17 + GDASv17.** GFS transitions from atmosphere-centric to a fully coupled Earth-system model with MOM6 ocean, CICE6 sea ice, and WAVEWATCH III waves all coupled via CMEPS. GDAS introduces JEDI-based DA for ocean, sea ice, and snow.
6. **Proposed (March 2026 PNS) — RTOFS v3.0.** Replaces HYCOM with MOM6 and CICE4 with CICE6, aligning RTOFS with the rest of the UFS component stack.
7. **Future — RRFSv2 (MPAS-based) replaces HRRR, RAP, NAM 12 km, and SREF.** No formal retirement notification yet for these systems; they are expected to follow once RRFSv2 is operational.

Each of these has slipped at least once relative to its original target date, and further slippage should be expected. The pattern of slippage is itself worth knowing about — UFS is a major undertaking and conservative scheduling is the norm rather than the exception.

---

## Already integrated

These systems already run on UFS infrastructure as of April 2026.

### [HAFS — Hurricane Analysis and Forecast System](./models/tropical_cyclone_models/hafs.md)
- **Operational since:** June 2023 (v1.0); current version v2.1 (July 2025)
- **UFS components used:** FV3 atmosphere, HYCOM ocean (transitioning), WAVEWATCH III waves, CMEPS coupler
- **Replaced:** [HWRF](./models/tropical_cyclone_models/hwrf.md) and [HMON](./models/tropical_cyclone_models/hmon.md) (both retired 2023)
- **Notes:** First major UFS-based operational system. HAFS runs only when active tropical cyclones exist; supports basins worldwide (NATL, EPAC, CPAC, WPAC, NIO, SH).

### [NAQFC AQM v7 — National Air Quality Forecast Capability](./models/air_quality_models/regional/usa/naqfc.md)
- **Operational since:** May 2024 (v7.0.7 — UFS-based architecture introduced)
- **UFS components used:** UFS-based online-coupled atmosphere-chemistry embedding CMAQ v5.2.1
- **Replaced:** Offline-coupled GFS-CMAQ chain used in AQMv5 and AQMv6
- **Notes:** Transition from three separate domains (CONUS, AK, HI at ~12 km) to a single unified North American domain at 13 km.

---

## Active replacements (in progress or imminent)

These are the most operationally significant UFS transitions that are either imminent or in late-stage pre-operational status as of April 2026.

### [RRFS — Rapid Refresh Forecast System](./models/nwp_models/regional/usa/rrfs.md) and [REFS — RRFS Ensemble Forecast System](./models/ensemble_models/regional/usa/refs.md)
- **Status:** Late-stage pre-operational (April 2026); originally targeted "early 2026," delayed
- **UFS components used:** FV3 dynamical core (v1); MPAS planned for v2
- **Replaces:**
  - [NAM](./models/nwp_models/regional/usa/nam.md) (12 km parent and all 3 km nests including fire weather)
  - [NAM Nest](./models/nwp_models/regional/usa/nam-nest.md)
  - [HREF](./models/ensemble_models/regional/usa/href.md) (replaced by REFS)
  - HiresW (CONUS, Alaska, Hawaii, Puerto Rico domains — see [HiresW Guam](./models/nwp_models/regional/usa/hiresw-guam.md) for the surviving exception)
  - NARRE (not in this repository)
- **Authority:** [NWS PNS 25-41](https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf) (June 26, 2025)
- **Domains:** CONUS, Alaska (3 km), Hawaii, Puerto Rico (2.5 km), plus a separate 1.5 km fire-weather domain
- **Forecast extension:** REFS extends to 60 hours (HREF was 48 hours); 4× daily for all domains (HREF was 2× daily for non-CONUS)

### [GFS](./models/nwp_models/global/usa/gfs.md) and [GEFS](./models/ensemble_models/global/usa/gefs.md) — GFSv17 and GDASv17
- **Status:** Proposed for October 2026; public comment period open through May 2026
- **UFS components used:** FV3 atmosphere at C1152 (~9 km, up from C768/~13 km), MOM6 ocean, CICE6 sea ice, WAVEWATCH III waves, CMEPS coupler, JEDI DA for ocean/sea ice/snow
- **Replaces:** Current atmosphere-centric GFS v16 and GDAS v16
- **Authority:** [NWS PNS 26-29](https://www.weather.gov/media/notification/) (April 15, 2026), with companion product-removal notice PNS 26-30 (April 16, 2026)
- **Notes:** Largest single UFS transition planned. Pre- and post-v17 forecast comparisons should be treated as separate model eras for validation, backtesting, and climatology.

### [RTOFS Global](./models/ocean_models/global/us/rtofs-global.md) — v3.0
- **Status:** Proposed (PNS 26-17, March 2026); not yet operationally scheduled
- **UFS components used:** MOM6 ocean (replacing HYCOM), CICE6 sea ice (replacing CICE4)
- **Replaces:** Current RTOFS v2.5 HYCOM/CICE4 system
- **Notes:** RTOFS v3.0 represents alignment with the UFS ocean and ice model stack used elsewhere (MOM6 + CICE6 are also used in GFSv17). HAFS (which initializes from RTOFS) will inherit MOM6/CICE6 ocean state in due course.

### [NBM v5.0](./models/nwp_models/regional/usa/nbm.md)
- **Operational since:** April 15, 2026
- **UFS relationship:** NBM is not itself a UFS model — it's a statistical post-processing system. But v5.0 added UFS-relevant inputs:
  - Higher-resolution GDPS (15 km) and REPS (10 km) inputs (these are Canadian, not UFS-related)
  - **AIGFS** (NOAA's experimental AI global model) added as input for temperature, wind speed, and QPF
  - **ECAIFS** (ECMWF's AI/IFS hybrid) similarly added
  - **SREF eliminated** as an input — this is operationally significant because it removes a major downstream consumer of SREF, accelerating the case for SREF retirement under RRFSv2
- **Notes:** NBM's evolving input set tracks the UFS rollout indirectly. As GFSv17, RRFSv1, and REFS go operational, expect future NBM upgrades to incorporate them and remove their predecessors.

---

## Scheduled retirements (first wave — with RRFSv1)

These systems are covered by NWS PNS 25-41 (June 26, 2025) and are scheduled for retirement once RRFSv1 is fully operational. Originally targeted for early 2026, slipped due to RRFSv1 delays.

| Retiring system | Replacement | Notes |
|---|---|---|
| [NAM](./models/nwp_models/regional/usa/nam.md) | RRFSv1 (CONUS 3 km, post-processed grids) | Full retirement: 12 km parent, all 3 km nests, fire-weather nest |
| [NAM Nest](./models/nwp_models/regional/usa/nam-nest.md) | RRFSv1 | All convection-allowing nests retired |
| [HREF](./models/ensemble_models/regional/usa/href.md) | [REFS](./models/ensemble_models/regional/usa/refs.md) | Full replacement; REFS extends to 60h, runs 4× daily for all regions |
| HiresW CONUS, AK, HI, PR | RRFSv1 (3 km CONUS/AK; 2.5 km HI/PR) | Guam domain explicitly preserved — see [HiresW Guam](./models/nwp_models/regional/usa/hiresw-guam.md) |
| NARRE (not in repo) | REFS | NARRE's hourly 12-h ensemble guidance replaced by REFS's 60-h forecasts |

---

## Expected future retirements (second wave — with RRFSv2)

These systems are publicly signaled (through NOAA roadmap documents and NBM v5.0's elimination of SREF as an input) but have **no formal retirement notification** as of April 2026. They are expected to be retired when RRFSv2 (MPAS-based) is operational, which has no announced date.

| Retiring system | Replacement | Status |
|---|---|---|
| [HRRR](./models/nwp_models/regional/usa/hrrr.md) | RRFSv2 (MPAS) | Expected; no formal SCN |
| [RAP](./models/nwp_models/regional/usa/rap.md) | RRFSv2 (MPAS) | Expected; no formal SCN |
| NAM 12 km (parent domain only) | RRFSv2 large-domain configuration | Expected |
| SREF (not in repo) | REFS / RRFSv2 ensemble | Already lost largest consumer (NBM v5.0); expected retirement |

These represent the second major retirement wave under UFS. Users with operational dependencies on HRRR or RAP should plan for migration to RRFS but should not expect this to happen in 2026.

---

## The 21 → 8 mapping

NOAA's stated goal of reducing the NCEP Production Suite from 21 to 8 reflects multiple consolidations happening simultaneously. The table below shows which legacy systems collapse into which UFS components, restricted to systems documented in this repository.

| UFS component (target) | Legacy systems consolidated |
|---|---|
| **GFSv17** (global atmosphere + ocean + ice + wave) | GFS (v16); GDAS (v16); historically separate ocean and wave initialization paths |
| **RRFSv1** (regional convection-allowing deterministic) | NAM, NAM Nest, HiresW (except Guam) |
| **REFS** (regional convection-allowing ensemble) | HREF, NARRE |
| **HAFS** (tropical cyclone) | HWRF, HMON (already retired 2023) |
| **AQM v7 within NAQFC** (air quality) | Pre-v7 offline-coupled GFS-CMAQ chain |
| **RTOFS v3.0** (global ocean) | RTOFS v2.5 (HYCOM); historically separate Navy/NOAA ocean initialization paths |
| **GEFS** + **REFS** + **RRFSv2 ensemble** (future) | GEFS (continues), HREF, NARRE, SREF |
| **RRFSv2 (MPAS, future)** | HRRR, RAP, NAM 12 km, plus SREF |

The remaining UFS components in the 8-system target include atmospheric ensemble systems and specialized non-NWP services that are outside this repository's scope.

---

## Models that consume UFS outputs

Some operational models do not themselves run on UFS but incorporate UFS-derived inputs:

### [NBM (National Blend of Models)](./models/nwp_models/regional/usa/nbm.md)
NBM is a statistical post-processor, not a UFS component. But as UFS systems become operational, NBM ingests their output and discontinues legacy inputs. NBM v5.0 (April 2026) eliminated SREF as an input and added AIGFS — a pattern that will continue as RRFSv1, REFS, and GFSv17 go operational.

### Downstream coupled systems
[HAFS](./models/tropical_cyclone_models/hafs.md) is initialized from [RTOFS](./models/ocean_models/global/us/rtofs-global.md), so RTOFS upgrades flow into HAFS in subsequent cycles. When RTOFS migrates to MOM6/CICE6 in v3.0, HAFS's ocean initialization will follow.

---

## What this index does not cover

- **Internal NCEP infrastructure systems** that are not publicly distributed (workflow management, post-processing chains)
- **Non-NCEP UFS users** (research configurations, university-run UFS instances)
- **Detailed UFS architecture** — for that, see https://ufscommunity.org/
- **Atmospheric ensemble systems unrelated to UFS** — GEFS continues to run, but its UFS-relevant changes are limited to coupling with GFSv17's analysis system
- **AI components within UFS** — these may emerge in future versions but as of April 2026 are not part of operational UFS components. See [AI_MODELS.md](./AI_MODELS.md) for the AI-specific index.

---

## Tracking changes

For lifecycle events (implementation dates, version numbers, slippage), see [STATUS.md](./STATUS.md). UFS-related items in STATUS.md are kept brief, with detailed UFS context maintained here.

For the broader Copernicus and ECMWF programmes (which are UFS's European peers but operate independently), see [COPERNICUS.md](./COPERNICUS.md) and the various ECMWF entries in `models/`.

---

## Contributing

If a UFS-related change is announced — a new SCN, a slippage, a new component integration, or a new retirement notification — please update both this file and STATUS.md with consistent dates and link to the authoritative source. The UFS rollout is multi-year and active; this page will need ongoing maintenance.
