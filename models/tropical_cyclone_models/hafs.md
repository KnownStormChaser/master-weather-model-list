# HAFS (Hurricane Analysis and Forecast System)

>  **Upgrade to v2.2 scheduled October 13, 2026.** [NWS Service Change Notice 26-76](https://www.weather.gov/media/notification/pdf_2026/scn26-76_HAFSv2.2.pdf) (September 9, 2026) schedules HAFSv2.2, taking the PNS 26-15 proposal to operations. Headline changes: model code resynced to the **February 12, 2026 UFS revision**, a scale-aware 3D-TKE EDMF PBL scheme, Noah-MP land surface, **RRTMG → RRTMGP radiation**, refreshed MOM6 and WW3 submodules, VI/DA refinements, two new observation types (commercial GPS radio occultation and sUAS), and an updated GFDL vortex tracker. One product change: a **10 m wind gust** variable added to the GRIB2 output. **This entry documents v2.1 as currently operational**; the v2.2 details are marked as scheduled throughout and should be promoted to current once the upgrade lands. See [Version history](#version-history).

## What this model is
The Hurricane Analysis and Forecast System (HAFS) is a specialized, coupled tropical cyclone numerical weather prediction system developed within NOAA's **Unified Forecast System (UFS)** framework. It is NOAA's operational hurricane forecast model, having replaced the legacy HWRF and HMON systems in 2023.

HAFS is designed specifically to forecast tropical cyclones and their associated hazards, using storm-centered, moving high-resolution nests to resolve the inner-core structure, intensity change, and air–sea interactions. The system includes specialized vortex initialization and inner-core data assimilation tailored to hurricane prediction, and is coupled to ocean and (in one configuration) wave models to capture air–sea feedbacks that drive intensity change and rapid intensification.

HAFS runs in two parallel operational configurations — **HFSA** (global) and **HFSB** (NHC basins) — both upgraded together at each version release. Each forecast cycle is tied to a specific active tropical cyclone rather than running on a fixed geographic domain.

The current operational version is **HAFSv2.1**, implemented on July 29, 2025. **HAFSv2.2 is scheduled for October 13, 2026** under SCN 26-76 (September 9, 2026), which supersedes the PNS 26-15 proposal — see *Version history* below.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country:** United States
- **Developed by:** NOAA Environmental Modeling Center (EMC) and the UFS Hurricane Application Team, with AOML/HRD
- **Operational implementation:** NCEP Central Operations (NCO)

---

## When this model runs
HAFS **runs only when an active tropical cyclone (or invest) exists**. Each forecast is triggered by official storm messages issued by the National Hurricane Center, Central Pacific Hurricane Center, or Joint Typhoon Warning Center, and is tied to a specific storm and cycle rather than a fixed geographic domain. The triggering TC-vitals / storm-message files are staged on the public server under `inphfsa/` and `inphfsb/` (files named `message1`, `message2`, …).

Cycles are run at **00, 06, 12, and 18 UTC** for each active storm.

---

## What area it covers
- **Coverage:** Storm-centered moving domains following tropical cyclones. Coverage differs by configuration:
  - **HFSA — global:** all TC basins — North Atlantic (L), Eastern & Central Pacific (E/C), Western Pacific (W), North Indian Ocean (A/B), and Southern Hemisphere (S/P). *Live-verified July 2026: HFSA cycles simultaneously carried Atlantic (`02l`), Eastern Pacific (`06e`, `07e`, `98e`) and Western Pacific (`11w`, `92w`) storms.*
  - **HFSB — NHC basins only:** North Atlantic, Eastern Pacific, Central Pacific. *Live-verified July 2026: HFSB cycles carried only Atlantic and Eastern Pacific storms; no Western Pacific storm ever appeared in HFSB.*
- **Storm selection:** Driven by official warning/invest messages from NHC / CPHC / JTWC. The moving nest follows each storm center, and the system runs multiple simultaneous storms across basins.

---

## Configurations: HFSA and HFSB

HAFS runs in two operational configurations, upgraded together at each release but differing in coverage, ocean model, and wave coupling. Both are publicly distributed under separate `hfsa.*` / `hfsb.*` directory and file-naming patterns.

| Aspect | **HFSA (HAFS-A)** | **HFSB (HAFS-B)** |
|---|---|---|
| Role | Primary / global configuration | Secondary / basin-scale configuration |
| Basin coverage | Global (all TC basins) | NHC basins only (NATL, EPAC, CPAC) |
| Ocean model (v2.1) | **MOM6** (since v2.0), 55 levels, **KPP** mixed-layer scheme; initialized from RTOFS v2.5 | **MOM6** (switched from HYCOM in v2.1), **ePBL** mixing-layer scheme |
| Wave model output | **WAVEWATCH III distributed** (`ww3.grb2`, bulletins, spectra) | No wave products distributed |

The two configurations are designed to give forecasters parallel guidance from different model setups — a dual-deterministic approach in the spirit of the historical HWRF/HMON pairing.

---

## Basic details
- **Model type:** Tropical cyclone–specific NWP system, coupled atmosphere–ocean(–wave)
- **Framework:** Unified Forecast System (UFS)
- **Atmospheric core:** FV3 (Finite-Volume Cubed-Sphere)
- **Coupling framework:** CMEPS (Community Mediator for Earth Prediction Systems)
- **Horizontal resolution — distributed output (live-verified 2026-07-25, both HFSA and HFSB):**
  - **Parent domain:** regular lat-lon, **0.06°** (~6–7 km), 1681 × 1361 grid, storm-centered (~101° lon × 82° lat)
  - **Storm-following nest:** regular lat-lon, **0.02°** (~2 km), 1001 × 801 grid (20° × 16° centered on the storm)
- **Horizontal resolution — native model grid:** **5.4 km parent, 1.8 km moving nest**, per the EMC implementation record: the v2.0 FY2024 implementation moved to "higher resolution moving-nesting (5.4-1.8 km, instead of 6-2 km) configuration with slightly adjusted parent domain coverage". Neither v2.1 nor the scheduled v2.2 changes these. Note the distributed output grids (0.06° parent, 0.02° nest) are interpolated products and coarser than the native nest.
- **Vertical levels:** distributed output on **45 pressure levels** (1000–2 hPa). Native model levels: 81 (documented HAFS L81 configuration; not independently verified here).
- **Forecast length:** **126 hours (5.25 days)**, output every 3 hours from f000 to f126 (43 steps; live-verified).
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC) per active storm.
- **Temporal output resolution:** 3-hourly.

> **Note on the "seven days" design goal:** The AWS registry and NOAA documentation describe HAFS's design objective of forecasts "out to seven days." As of HAFSv2.1 the operational forecast length remains 126 h (5.25 days); the 7-day target is not yet operationally implemented.

---

## Initialization and data assimilation

HAFS uses a tropical cyclone–specific vortex initialization and inner-core data assimilation system designed to improve intensity, rapid-intensification, and storm-structure forecasts.

**Vortex initialization:**
- Storm vortex relocation and initialization
- Improved VI for storm intensity representation (Pmin adjustment / intensity enhancement) (v2.1)
- Wavenumber filtering applied to DA increments (v2.1)
- Storm-following Three-Dimensional Incremental Analysis Update (3DIAU) in inner-core DA (v2.1)

**Assimilated observations:**
- Aircraft reconnaissance: Tail Doppler Radar (TDR), High-Density Observations (HDOB), dropsondes
- Satellite: Atmospheric Motion Vectors (AMVs), GPS Radio Occultation, NOAA-21 ATMS and CrIS (both added in v2.1, via CRTM 2.4.0.1)

**Removed in v2.1:**
- P-3 and C-130 aircraft Stepped Frequency Microwave Radiometer (SFMR) surface wind speed retrievals

The SFMR removal is operationally significant — earlier HAFS versions assimilated these aircraft-borne surface wind retrievals, but they were turned off in v2.1.

**Scheduled additions in v2.2 (October 13, 2026):** commercial **GPS radio occultation** and **sUAS** (small uncrewed aircraft system) observations enter the assimilation set, alongside **TDR superobbing** for the existing Tail Doppler Radar stream. The SCN does not reinstate SFMR. Other v2.2 DA changes are an adjusted warm-cycling threshold, storm perturbation smoothing in the vortex initialization, an adjusted IAU window, `hafs_datool` fixes for `uv_rotation` and MPI use, and a GSI submodule sync to February 4, 2026.

**Boundary / initial conditions:** Global-scale conditions and boundary information derive from the GFS/GDAS system.

---

## Earth-system components
HAFS is a coupled multi-component Earth-system prediction model. Components (as of v2.1) include:

- **Atmosphere:** FV3
- **Ocean:** **Both configurations run MOM6 as of v2.1**, but they reached it separately and are configured differently:
  - **HFSA:** MOM6 since **v2.0** (July 2024), which "established and transitioned to use MOM6 ocean coupling (HFSA only)" through CMEPS with inline-CDEPS. Uses **55 vertical levels and the KPP mixed-layer scheme**, and takes sea-surface currents into account when estimating air–sea fluxes. Initialized from RTOFS v2.5 (deployed simultaneously with HAFSv2.1); v2.1 brought upgraded ocean coupling and an improved mixed-layer scheme.
  - **HFSB:** MOM6 since **v2.1** (July 2025), switched from HYCOM, using the MOM6 **ePBL** mixing-layer scheme.

> **Correction (September 2026):** earlier revisions of this entry described HFSA's ocean as HYCOM. That is wrong. The EMC HFSA implementation page records the MOM6 transition under the v2.0 FY2024 implementation, explicitly scoped "(HFSA only)", and the v2.1 change log lists only the HFSB switch. SCN 25-48 names neither model — it says only that the ocean model is "initialized using the latest RTOFS version 2.5" — so the HYCOM attribution did not come from the implementation notice. The v2.1 and v2.2 sample run directories both carry `hfsa.mom6.f###.nc` ocean output and no HYCOM files, consistent with this. SCN 26-76's phrasing for v2.2 — a single "latest MOM6 submodule" line covering both configurations — is what it appears to be, not evidence of a new switch.
- **Waves:** WAVEWATCH III — coupled and its output **distributed for HFSA only**. HFSB does not distribute wave products.
- **Land:** Noah-MP
- **Sea ice:** CICE (when relevant for higher-latitude storms)

Coupling is online via CMEPS, exchanging fluxes and surface fields during the forecast.

---

## Atmospheric physics (v2.1)

The HAFSv2.1 upgrade brought physics improvements based on the July 3, 2024 UFS revision:
- **Convection:** Improved Scale-Aware Simplified Arakawa-Schubert (sa-SAS) with scale-adaptive convective cloud-water calculations and prognostic sigma closure for all TC basins
- **Boundary layer:** Improved TKE-based Eddy-Diffusivity Mass-Flux (EDMF) PBL scheme
- **Radiation:** Exponential-random cloud-overlap method enabled in RRTM-G

### Scheduled for v2.2 (October 13, 2026)
SCN 26-76 replaces most of the above. The physics changes are:
- **Boundary layer:** scale-aware **3D-TKE EDMF** scheme, replacing the v2.1 TKE-EDMF PBL, with mixing length varying as a function of wind speed and PBL height
- **Radiation:** **RRTMG → RRTMGP** — a change of radiation package, not a tuning change, and the most consequential physics item in the upgrade
- **Land surface:** Noah-MP (listed as a v2.2 science change in the SCN, though Noah-MP is already the documented HAFS land component; the SCN does not say what specifically changes)
- **Ocean and waves:** latest MOM6 submodule with updated parameter options; updated WW3 submodule

---

## What it provides

Per storm, per cycle, the following products are distributed (live-verified 2026-07-25). File naming: `{stormID}.{YYYYMMDDHH}.{hfsa|hfsb}.{product}` — e.g. `06e.2026072512.hfsa.parent.atm.f000.grb2`. Storm IDs are ATCF-style prefixes (e.g. `06e`, `11w`; `9x` numbers denote invests such as `98e`).

**Gridded GRIB2 forecast fields** (f000–f126, 3-hourly, each with a `.idx` sidecar):
- `parent.atm.f###.grb2` — parent-domain atmosphere (**749 messages** in v2.1: winds, temperature, RH, geopotential on 45 pressure levels; MSLP, CAPE/CIN, precipitation, PWAT, helicity, updraft helicity, surface/flux fields). **Becomes 750 in v2.2** — see below.
- `storm.atm.f###.grb2` — storm-following nest atmosphere (same variable suite, ~2 km); same 749 → 750 change
- `parent.sat.f###.grb2` / `storm.sat.f###.grb2` — synthetic (simulated) satellite brightness temperature, 8 channels (top-of-atmosphere)

**Storm-summary and track products** (produced at the end of each run):
- `parent.swath.grb2` — storm-total swath summary (one per storm)
- `trak.atcfunix` — **ATCF track and intensity guidance** (the standard operational format), including extended thermodynamic / shear / SST / RMW parameters (produced for named/numbered storms)
- `storm_info` — one-line storm name + ID (e.g. `fausto06e`)
- `grib.stats.short`, `stats.tpc` — diagnostic / verification statistics

### Scheduled product change for v2.2 (October 13, 2026)

SCN 26-76 states that one variable — **10 m wind gust** — is added to the GRIB2 output and that there are no other product changes. Comparing the published v2.2 sample files against the v2.1 samples message-by-message (cycle 2026-09-01 00 UTC vs 2024-09-24 06 UTC, f126) shows the addition is real but **not uniform across product files**:

| File | v2.1 | v2.2 | Change |
|---|---|---|---|
| `parent.atm` (HFSA and HFSB) | 749 messages | **750** | `GUST:10 m above ground` **added** |
| `storm.atm` (HFSA and HFSB) | 749 messages | **750** | `GUST:10 m above ground` **added** |
| `parent.nhc` AWIPS subset | 96 messages | **96** | `GUST:surface` **replaced by** `GUST:10 m above ground` |

**The NHC subset substitution is a breaking change and the SCN does not mention it.** Anything keying on a surface gust record in the `nhc` files will find it gone rather than joined by a new record. In the `atm` files the surface gust is retained and the 10 m gust is additional, so the two file families diverge in behaviour. Note that the `nhc` subset files are not present on the NODD S3 bucket — they are AWIPS/SBN-bound — so this affects AWIPS consumers rather than NODD users.

A second difference visible in the samples but absent from the SCN: the WAVEWATCH III point output changes from `out_pnt.ww3` to **`out_pnt.ww3.nc`**, i.e. binary to NetCDF. This is an internal run-directory file rather than a distributed product, so it should not affect NOMADS or S3 consumers, but it is worth checking if you pull from the sample or parallel feeds.

**Wave products (HFSA only):**
- `ww3.grb2` — WAVEWATCH III gridded output, regular lat-lon **0.1°**, 1031 × 401 grid (significant wave height, peak/mean periods, wave/swell direction, partitioned swell/wind-sea fields, plus 10 m wind)
- `ww3_bull.tar`, `ww3_cbull.tar`, `ww3_csbull.tar` — wave bulletins
- `ww3_spec.tar` — wave spectra

Forecast variables include: tropical cyclone track; maximum sustained winds and minimum central pressure; storm structure and size; rainfall; surface winds and hazards; and (HFSA) storm-generated waves and air–sea interaction.

---

## Data availability

### Distribution channels

**1. NOMADS (NCEP operational distribution):**
- Production: https://nomads.ncep.noaa.gov/pub/data/nccf/com/hafs/prod/
- Parallel feed: https://nomads.ncep.noaa.gov/pub/data/nccf/com/hafs/para/
- FTP equivalent: ftp://ftp.ncep.noaa.gov/data/nccf/com/hafs/prod/
- Directory layout: `hfsa.YYYYMMDD/{00,06,12,18}/…` and `hfsb.YYYYMMDD/{00,06,12,18}/…`. Storm ID is a filename prefix within the cycle folder — **there are no per-storm subdirectories.** Input storm-message files are under `inphfsa/` and `inphfsb/`.

**2. AWS Open Data (NOAA Open Data Dissemination):**
- S3 bucket: `s3://noaa-nws-hafs-pds/` (region `us-east-1`)
- Browser access: https://noaa-nws-hafs-pds.s3.amazonaws.com/index.html
- AWS CLI (no account required): `aws s3 ls --no-sign-request s3://noaa-nws-hafs-pds/`
- SNS new-object notifications: `arn:aws:sns:us-east-1:709902155096:NewHAFSObject` (Lambda and SQS protocols only)
- **Path format differs from NOMADS:** prefixes are `hfsa/YYYYMMDD/{cycle}/…` and `hfsb/YYYYMMDD/{cycle}/…` (no dot before the date). Rolling retention on S3 is roughly one week of real-time cycles.
- Additional top-level prefixes: `hfsa_retro/`, `hfsb_retro/` (a few 2022-season retrospective cases — Fiona 07L, Nicole 17L — more available on request), `inphfsa/`, `inphfsb/` (input TC-vitals messages), plus `fix/`, `herc/`, `test/`.

**3. NCEP parallel / sample feeds (development testing):**
- HFSA samples: https://www.emc.ncep.noaa.gov/gc_wmb/vxt/zack/HAFS_Sample_Files/HFSA_sample/hafs/v2.1/ · **v2.2:** https://www.emc.ncep.noaa.gov/gc_wmb/vxt/zack/HAFS_Sample_Files/HFSA_sample/hafs/v2.2/
- HFSB samples: https://www.emc.ncep.noaa.gov/gc_wmb/vxt/zack/HAFS_Sample_Files/HFSB_sample/hafs/v2.1/ · **v2.2:** https://www.emc.ncep.noaa.gov/gc_wmb/vxt/zack/HAFS_Sample_Files/HFSB_sample/hafs/v2.2/
- The v2.2 sample directories carry a single cycle each (`hfsa.20260901/00`, `hfsb.20260901/00`). These are full internal run directories, not the distributed product set — they include ocean, wave, DA and configuration files that never reach NOMADS or S3. Useful for diffing message inventories ahead of the upgrade; not a model of what you will receive.
- **Event-driven v2.2 parallel feed** on NOMADS after **October 9, 2026**: https://nomads.ncep.noaa.gov/pub/data/nccf/com/hafs/para/ — four days of overlap before the October 13 implementation, and only when a storm is active, so plan any pre-cutover comparison around actual TC activity.

### Data details
- **Is the data free?** Yes
- **License:** Public domain (U.S. Government work). Distributed via NOAA Open Data Dissemination (NODD): data are open to the public and may be used as desired; NOAA requests attribution and prohibits implying NOAA endorsement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (gridded atmosphere, satellite, swath, waves; each with `.idx`), ATCF (`trak.atcfunix` track files), plain text (`storm_info`), and TAR bundles (wave bulletins/spectra)

> **No ocean output is distributed.** Despite HAFS being a coupled atmosphere–ocean system, the MOM6 ocean fields are not part of the public product set. A full operational cycle on the NODD bucket (verified `hfsa/20260909/12/`, 1,062 objects, and `hfsb/20260909/12/`, 700 objects) contains only `parent.atm`, `storm.atm`, `parent.sat`, `storm.sat`, `parent.swath`, `trak.atcfunix`, `storm_info`, the statistics files, and — for HFSA — the WW3 products. The `mom6.f###.nc` files visible in the sample directories have no counterpart in either distribution channel. Ocean-state users need RTOFS, not HAFS.
>
> The same listing shows **no `parent.nhc` AWIPS subset files on S3** either, though they appear in the samples. Their presence on NOMADS was not verified.

---

## Version history

### Scheduled — HAFSv2.2, October 13, 2026 (SCN 26-76)
**Not yet operational.** Scheduled by [NWS SCN 26-76](https://www.weather.gov/media/notification/pdf_2026/scn26-76_HAFSv2.2.pdf), issued September 9, 2026, which supersedes the PNS 26-15 proposal (issued March 3, 2026; comments closed April 5, 2026).

- **Code sync:** model code updated to the latest UFS revision as of **February 12, 2026**. v2.1 was built on the July 3, 2024 revision, so this is a nineteen-month jump in the underlying UFS code base and the reason the upgrade matters beyond the TC application. Note the SCN gives **two different sync dates** for different components: February 12, 2026 for the UFS revision, February 4, 2026 for the GSI submodule. The earlier PNS 26-15 proposal cited only February 4, which is the GSI date.
- **Atmospheric physics:** scale-aware 3D-TKE EDMF PBL scheme; Noah-MP land surface; **RRTMG → RRTMGP** radiation; PBL mixing length varying with wind speed and PBL height.
- **Ocean and waves:** latest MOM6 submodule with updated parameter options; updated WW3 submodule.
- **Vortex initialization and DA:** adjusted warm-cycling threshold; storm perturbation smoothing in VI; GSI submodule synced to February 4, 2026; adjusted IAU window; `hafs_datool` fixes (`uv_rotation`, MPI use); TDR superobbing.
- **New observation types:** **commercial GPS radio occultation** and **sUAS (small uncrewed aircraft system)** data. These are additions to the assimilated observation set, not replacements — the v2.1 SFMR removal is not reversed.
- **Post-processing:** updated GFDL hurricane tracker.
- **Products:** one new variable, 10 m wind gust. See [What it provides](#what-it-provides) for the verified per-file behaviour, which differs from the SCN's description for the NHC subset files.
- **Timing and paths:** no change to product delivery times or to the NOMADS production path. Event-driven v2.2 parallel feed at `/hafs/para/` after October 9, 2026.
- **Skill:** NCEP reports v2.2.0 was tested against 2025 operational v2.1 and shows improved track and intensity guidance in all global oceanic basins.
- *When this lands, promote this section to current and revisit the radiation, PBL, and DA details above.* The ocean and wave entries need no rewrite — both configurations are already MOM6 (see [Earth-system components](#earth-system-components)); v2.2 only refreshes the submodule.

### July 29, 2025 — HAFSv2.1 (current operational)
- Model code synced to the July 3, 2024 UFS revision (SCN 25-48)
- Atmospheric physics: improved sa-SAS convection, TKE-EDMF PBL, exponential-random cloud overlap in RRTM-G
- **HFSB ocean model switched HYCOM → MOM6** (MOM6 ePBL mixing)
- HFSA ocean initialized from RTOFS v2.5 (deployed simultaneously); upgraded ocean coupling and mixed-layer scheme
- Improved vortex initialization; wavenumber filtering on DA increments; storm-following 3DIAU in inner-core DA
- NOAA-21 ATMS and CrIS added to assimilation (CRTM 2.4.0.1); P-3 and C-130 SFMR surface winds removed
- Updated NHC storm GRIB2 subset files for AWIPS support

### 2024 — HAFSv2.0
- Operational upgrade with HFSA and HFSB configurations; continued physics, DA, and coupling refinements

### June 27, 2023 — HAFSv1.0 (initial operational implementation)
- HAFS replaced HWRF and HMON as NOAA's operational hurricane forecast system on WCOSS2
- First operational moving-nest hurricane forecasting in the UFS framework; initial atmosphere–ocean–wave coupling via CMEPS

---

## Relationship to other models

### Predecessors
HAFS replaced **HWRF** and **HMON** as NOAA's *primary* operational hurricane forecast system in 2023. Both are now on legacy status — frozen at their final pre-HAFS configurations — but **continue to run operationally for active storms** as guidance alongside HAFS; no retirement date has been announced as of mid-2026.

### Companion NOAA operational models
- **GFS / GDAS:** large-scale environmental and boundary conditions, and DA background
- **GEFS:** ensemble track guidance complementing HAFS deterministic intensity guidance
- **RTOFS (v2.5):** ocean initial conditions for HFSA's coupled **MOM6** ocean component. When RTOFS itself migrates to MOM6/CICE6 under the proposed v3.0, this initialization path changes on the RTOFS side; HAFS is already on MOM6 and is unaffected in that respect.

### International peers
HAFS is one of several operational TC-focused NWP systems worldwide. Peers include the UK Met Office hurricane configuration of MetUM, the CMA tropical cyclone model, and the JMA typhoon model. These differ in nest design, coupling architecture, and DA configuration, and are typically used together as multi-model consensus guidance.

---

## Notes
- HAFS output availability depends on tropical cyclone activity. The system is **not intended for general weather forecasting** — during quiet periods in all basins, no HAFS data is produced.
- **The two configurations are not interchangeable.** HFSA is global and wave-coupled; HFSB covers only NHC basins and distributes no wave products. Both run MOM6, but with different mixed-layer schemes (HFSA: KPP, 55 levels; HFSB: ePBL), so they are not interchangeable on the ocean side either. Choose the configuration to match your basin and whether you need wave output.
- The 126 h (5.25 day) forecast length is shorter than global models because TC intensity skill degrades rapidly beyond this range and the high-resolution moving nest is computationally expensive.
- File organization is flat within each cycle: the storm ID is a filename prefix, not a subdirectory. Invest areas use `9x`-series IDs (e.g. `98e`).

---

## Official documentation
- WPO HAFS overview: https://wpo.noaa.gov/the-hurricane-analysis-and-forecast-system-hafs/
- AOML HAFS page: https://www.aoml.noaa.gov/hurricane-analysis-and-forecast-system/
- EMC HFSA / HFSB implementation pages: https://emc.ncep.noaa.gov/hurricane/HFSA/about.php?branch=impl · https://emc.ncep.noaa.gov/hurricane/HFSB/about.php?branch=impl
- HAFS GitHub: https://github.com/hafs-community/HAFS · https://github.com/NOAA-EMC/HAFS
- **HAFSv2.2 implementation notice (SCN 26-76, September 9, 2026; effective October 13, 2026):** https://www.weather.gov/media/notification/pdf_2026/scn26-76_HAFSv2.2.pdf
- HAFSv2.1 implementation notice (SCN 25-48, July 2025): https://www.weather.gov/media/notification/pdf_2025/scn25-48_updated_HAFS_v2.1_aaa.pdf
- HAFSv2.2 proposal (PNS 26-15, March 2026 — superseded by SCN 26-76): https://www.weather.gov/media/notification/pdf_2026/pns26-15_HAFSv2.2.pdf
- AWS Open Data registry: https://registry.opendata.aws/noaa-nws-hafs/
- NOAA Open Data Dissemination (NODD): https://www.noaa.gov/information-technology/open-data-dissemination

### Operational contacts
- Hurricane Modeling Project Lead: Dr. Zhan Zhang, NOAA/NCEP/EMC
- Development: NOAA EMC and AOML/HRD
