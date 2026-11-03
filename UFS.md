# Unified Forecast System (UFS)

This page indexes all NOAA models in this repository that are part of, are being consolidated into, or are being retired by the **Unified Forecast System (UFS)** — NOAA's community-based, coupled Earth-system modeling framework that is gradually replacing the patchwork of independently-developed operational systems NCEP has run for decades.

The goal of this index is to make a coherent programme visible. Right now UFS shows up across the repository as separate line items — RRFSv1 here, GFSv17 there, RTOFS v3.0 elsewhere, NAM retirement somewhere else — but they are all parts of the same consolidation effort, scheduled and sequenced together. This page exists to surface that coherence.

Last updated: November 2026.

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

1. **2023 — HAFS replaces HWRF and HMON as the primary hurricane system.** The first major UFS-based operational system, superseding both legacy hurricane models at once. HWRF and HMON were frozen rather than retired and still run for active storms; see *Already integrated* below. HAFS uses FV3 atmosphere with online CMEPS coupling to ocean and waves.
2. **2024 — AQM v7 migrates to UFS-based architecture.** The atmospheric composition component of NAQFC moved from offline-coupled GFS-CMAQ to UFS-based online-coupled atmosphere-chemistry.
3. **2025 — RTOFS v2.5 deployed alongside HAFSv2.1.** RTOFS itself stays on HYCOM in v2.5; the UFS-relevant connection is that HAFSv2.1 is initialized from RTOFS v2.5, tightening the operational dependency between these systems.
4. **October 13, 2026 — HAFS v2.2 (implemented).** A version upgrade rather than a consolidation step, but it resyncs the operational hurricane system to the February 12, 2026 UFS revision and refreshes the MOM6 and WAVEWATCH III submodules, so it is where the UFS component stack lands in operations for the tropical-cyclone application. Scheduled by NWS SCN 26-76 (September 9, 2026) and **implemented on schedule with the 12 UTC cycle**.
5. **November 3, 2026 — RRFSv1 + REFS replace NAM, NAM Nest, HiresW (except Guam), HREF, and SREF (implemented).** This was the largest wave of retirements, and the first time UFS replaced several regional systems at once. It went in on schedule with the 12 UTC cycle, under NWS SCN 26-48 (implementation; last updated to AAF on October 15, 2026) and SCN 26-47 (termination; AAC, October 2, 2026). November 3 was the fifth target date and the first that held; see the slippage note below. The downstream consequences took effect on the same cycle under the companion SCN 26-89:
   - NBM inputs switched from NAM/HiresW to RRFS/REFS.
   - The NAM ObsProc dumps ended.
   - The MAG website moved to RRFS/REFS.

   SREF was folded into this wave rather than held for RRFSv2, because NBM v5.0 had already removed it as an input earlier in 2026. See *Already integrated* below.
6. **November 3, 2026 — HYSPLIT 9.1 couples RRFS and drops NAM and HREF (implemented, apparently a day early).** This is the dispersion-side consequence of the same-day NAM/HREF retirement, under NWS SCN 26-78 (AAA update of October 2, 2026). The RRFS-derived HYSPLIT input files are present on NOMADS from the 12 UTC cycle on **November 2**. See *Models that consume UFS outputs* below.
7. **Late 2027 (proposed; moved from October 2026) — GFSv17 + GDASv17.** GFS transitions from atmosphere-centric to a fully coupled Earth-system model, with MOM6 ocean, CICE6 sea ice and WAVEWATCH III waves coupled via CMEPS. GDAS introduces JEDI-based DA for ocean, sea ice and snow. The updated PNS 26-29 (AAA, October 28, 2026) ties the implementation to the move of NWS high-performance computing operations to the commercial cloud, anticipated in late 2027.
8. **Proposed (March 2026 PNS) — RTOFS v3.0.** Replaces HYCOM with MOM6 and CICE4 with CICE6, aligning RTOFS with the rest of the UFS component stack.
9. **Future — RRFSv2 (MPAS-based) replaces HRRR and RAP.** No formal retirement notification yet for these systems; they are expected to follow once RRFSv2 is operational.

Most of these have slipped at least once relative to their original target dates, and further slippage should be expected. UFS is a major undertaking, and conservative scheduling is the norm rather than the exception.
- **RRFSv1/REFS** slipped four times before going in. It was originally targeted for "early 2026" under PNS 25-41, then set to August 31, 2026 under SCN 26-48, then October 6 (July 6 update), then October 14 (September 9 update). It was finally implemented on November 3 (set by the October 2 update).
- **GFSv17** has now slipped too, from October 2026 to late 2027, and is tied to the cloud transition rather than to a model-readiness date.
- **HAFS v2.2** is the exception: it went in on the date its SCN set.

The three implementations that had clustered on October 13 and 14 — HAFS v2.2, RRFS/REFS with its retirement wave, and HYSPLIT 9.1 — decoupled, as this page had anticipated. HAFS v2.2 went live on October 13 as scheduled (see *Already integrated* below). The October 2, 2026 updates moved RRFS/REFS and HYSPLIT 9.1 together to November 3. That date then carried four notices: RRFS/REFS, the legacy retirements, HYSPLIT 9.1 and the SCN 26-89 downstream changes. **All four took effect.** RRFS/REFS, the retirements and the checkable SCN 26-89 items changed on the 12 UTC cycle. The HYSPLIT RRFS inputs appear from 12 UTC on November 2, a day before their notice's effective date.

---

## Already integrated

These systems already run on UFS infrastructure as of November 2026.

### [RRFS — Rapid Refresh Forecast System](./models/nwp_models/regional/usa/rrfs.md) and [REFS — RRFS Ensemble Forecast System](./models/ensemble_models/regional/usa/refs.md)
- **Operational since:** **November 3, 2026, 12 UTC** (RRFS v1.0 / REFS v1.0)
- **UFS components used:** FV3 dynamical core (v1); MPAS planned for v2
- **Replaced (all retired on the same cycle):**
  - NAM (12 km parent and all 3 km nests, including fire weather); last cycle 06 UTC, November 3
  - NAM Nest
  - HREF, replaced by REFS; last cycle 06 UTC, November 3
  - HiresW CONUS, Alaska, Hawaii and Puerto Rico domains. The Guam domain survives as [HiresW Guam](./models/nwp_models/regional/usa/hiresw-guam.md), because RRFS does not reach Guam.
  - SREF, replaced by REFS; last cycle 09 UTC, November 3
  - NAM MOS, retired alongside NAM
  - NARRE, nominally replaced by REFS, but **already decommissioned on or about January 7, 2026** under SCN 25-87, so not part of this cutover

  None of the retired systems has a catalogue entry any longer; see [STATUS.md](./STATUS.md#recent-retirements) for the retirement record.
- **Authority:**
  - [NWS SCN 26-48 AAF](https://www.weather.gov/media/notification/pdf_2026/scn26-048_Updated_RRFS_and_REFS_Implementation.aaf.pdf) (implementation): issued May 12, 2026, and updated July 6, August 24, September 9, October 2 and October 15, 2026. The October 15 update changed precipitation-type post-processing and three REFS products without moving the date.
  - [SCN 26-47 AAC](https://www.weather.gov/media/notification/pdf_2026/scn26-47_Updated_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS_aac.pdf) (termination; October 2, 2026).
  - [SCN 26-89](https://www.weather.gov/media/notification/pdf_2026/scn26-89_RRFS_Downstream.pdf) (downstream NBM/ObsProc/MAG changes; October 2, 2026).
  - Originally proposed in [NWS PNS 25-41](https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf) (June 26, 2025).
- **Implementation confirmed:**
  - Every RRFS cycle on November 3 is complete under `/rrfs/prod/`, and the REFS 12 UTC cycle is complete under `/refs/prod/`.
  - None of the retired systems produced a 12 UTC cycle.
  - Record inventories and GRIB2 identification keys are identical before and after the cutover, so **model output is unchanged**.
  - See the [STATUS.md](./STATUS.md) November 3 items for the checks.
- **Distribution:**
  - **NOMADS:** `/nccf/com/rrfs/prod/` and `/nccf/com/refs/prod/`, two days' retention. The versioned `/rrfs/v1.0/` and `/refs/v1.0/` paths are identical mirrors.
  - **NODD:** `s3://noaa-rrfs-ops-pds/` (CC0-1.0) is the archive back to August 13, 2026.
  - The `para/` paths closed about ten hours before the cutover. The NODD bucket paused ingestion from about 20:20 UTC on November 2 until 15:28 UTC on November 3, then backfilled completely.
  - The `s3://noaa-rrfs-pds` prototype bucket has been frozen since August 12, 2026, and uses an incompatible internal layout.
- **Published product set:**
  - **RRFS runs hourly, but the product set varies by cycle.** The 00/06/12/18 UTC cycles run to 84 h, with the full hourly `prslev`/`2dfld` set on the CONUS, Alaska, Hawaii and Puerto Rico grids, plus the 13 km North America grid and the AWIPS subset. That is 947 GRIB2 files per cycle. The 03/09/15/21 UTC cycles carry the hourly set to f018 only. The remaining sixteen cycles carry only the 15-minute subhourly 2D fields. See the [RRFS output organization](./models/nwp_models/regional/usa/rrfs.md#output-organization).
  - **Five ensemble members** are published at the synoptic cycles under `rrfsens.YYYYMMDD/CC/m00#/`, f000–f060 on four grids, with a reduced parameter set and no BUFR. They have been public since September 9, 2026.
  - **Fire-weather output** comes from a separate, relocatable 1.5 km domain.
- **Ensemble forecast cadence:** REFS runs to 60 hours, 4× daily (00/06/12/18 UTC), on all four domains. It combines the five RRFS members with the 6-hour-old RRFS deterministic and ensemble cycles. For the CONUS and Alaska domains, HRRR contributes two more members (current and 6 h old), which makes HRRR an explicit input to a UFS-era operational system even though HRRR itself was not retired in this wave.
- **Compared with HREF:** REFS extends forecasts from 48 to 60 hours and runs 4 cycles daily on every domain, where HREF ran only twice daily for Alaska, Hawaii and Puerto Rico.

### [HAFS — Hurricane Analysis and Forecast System](./models/tropical_cyclone_models/hafs.md)
- **Operational since:** June 2023 (v1.0); **current version v2.2, operational from the 12 UTC cycle on October 13, 2026**; previously v2.1 (July 29, 2025)
- **UFS components used:** FV3 atmosphere, MOM6 ocean (both configurations), WAVEWATCH III waves (output distributed for HFSA only), Noah-MP land, CMEPS coupler
- **Replaced as primary system:** [HWRF](./models/tropical_cyclone_models/hwrf.md) and [HMON](./models/tropical_cyclone_models/hmon.md), in 2023. **Neither has been retired.** Both are frozen at their final pre-HAFS configurations and still run operationally for active storms, as guidance alongside HAFS. Their source is still in NCO's operational code listing (`hwrf.v13.2.11`, `hmon.v3.2.12`, as of October 2026), and no retirement notice has been issued. Earlier revisions of this page said both were retired in 2023, which was wrong.
- **v2.2 (SCN 26-76, September 9, 2026; implemented October 13, 2026):** Model code resynced to the **February 12, 2026 UFS revision** — the first resync since the July 2024 revision used in v2.1, and the main reason this upgrade matters for the UFS index. Physics moves to a scale-aware 3D-TKE EDMF PBL scheme, Noah-MP land surface, and **RRTMGP radiation replacing RRTMG**, with PBL mixing length varying with wind speed and PBL height. The MOM6 and WW3 submodules are both updated. Vortex initialization and DA are refined (warm-cycling threshold, storm perturbation smoothing, GSI submodule synced to February 4, 2026, adjusted IAU window, `hafs_datool` fixes, TDR superobbing), and two new observation types are assimilated: commercial GPS radio occultation and sUAS data. Post-processing gets an updated GFDL hurricane tracker. One product change: a **10 m wind gust** variable added to the GRIB2 output.
- **Implementation confirmed:** NOAA published no completion notice, and NCO's Model Changes page has no HAFS v2.2 row. The date was confirmed from the live data on October 21, 2026. Every HFSA and HFSB `atm` file from the 13 October 12 UTC cycle onward has the v2.2 inventory (750 messages, with `GUST:10 m above ground`), while files uploaded on schedule before that cycle have the v2.1 inventory (749). NCO's operational code listing shows `hafs.v2.2.8`, and the `/hafs/para/` parallel feed has been closed since the cutover. A few cycles from October 12–13 on the NODD bucket contain v2.2 files re-uploaded during the switchover; see the [HAFS entry](./models/tropical_cyclone_models/hafs.md#cutover-on-the-nodd-bucket).
- **Notes:** First major UFS-based operational system. HAFS runs only when active tropical cyclones exist; supports basins worldwide (NATL, EPAC, CPAC, WPAC, NIO, SH).
- **Ocean component (resolved):** an earlier revision of this page left open whether HFSA was still on HYCOM. It is not. HFSA has run MOM6 since v2.0 (July 2024) and HFSB since v2.1 (July 2025), and the HAFS entry was corrected in September 2026. v2.2 only refreshes the MOM6 submodule for both configurations, so HAFS is fully on the UFS ocean component.

### NAQFC AQM v7 — National Air Quality Forecast Capability
- **Operational since:** May 2024 (v7.0.7 — UFS-based architecture introduced)
- **UFS components used:** UFS-based online-coupled atmosphere-chemistry embedding CMAQ v5.2.1
- **Replaced:** Offline-coupled GFS-CMAQ chain used in AQMv5 and AQMv6
- **Notes:** Transition from three separate domains (CONUS, AK, HI at ~12 km) to a single unified North American domain at 13 km.

---

## Active replacements (in progress or imminent)

These are the most operationally significant UFS transitions that are either imminent or in late-stage pre-operational status as of November 2026.

### [GFS](./models/nwp_models/global/usa/gfs.md) and [GEFS](./models/ensemble_models/global/usa/gefs.md) — GFSv17 and GDASv17
- **Status:** Proposed; **now planned for late 2027** (previously October 2026). The updated PNS 26-29 (AAA, October 28, 2026) ties the implementation to the move of NWS high-performance computing operations to the commercial cloud. There is no SCN yet.
- **UFS components used:** FV3 atmosphere at C1152 (~9 km, up from C768/~13 km), MOM6 ocean, CICE6 sea ice, WAVEWATCH III waves, CMEPS coupler, JEDI DA for ocean/sea ice/snow
- **Replaces:** Current atmosphere-centric GFS v16 and GDAS v16
- **Authority:** [NWS PNS 26-29 AAA](https://www.weather.gov/media/notification/pdf_2026/pns26-29_Updated_Science_for_GFSv17_aaa.pdf) (October 28, 2026; new timeline). The original [PNS 26-29](https://www.weather.gov/media/notification/pdf_2026/pns26-29_Science_for_GFSv17.pdf) is dated April 15, 2026, with companion product-removal notice PNS 26-30 (April 16, 2026).
- **Notes:** Largest single UFS transition planned. Pre- and post-v17 forecast comparisons should be treated as separate model eras for validation, backtesting, and climatology.

### [RTOFS Global](./models/ocean_models/global/us/rtofs-global.md) — v3.0
- **Status:** Proposed (PNS 26-17, March 2026); not yet operationally scheduled
- **UFS components used:** MOM6 ocean (replacing HYCOM), CICE6 sea ice (replacing CICE4)
- **Replaces:** Current RTOFS v2.5 HYCOM/CICE4 system
- **Notes:** RTOFS v3.0 represents alignment with the UFS ocean and ice model stack used elsewhere (MOM6 + CICE6 are also used in GFSv17). HAFS initializes its ocean from RTOFS, and both HAFS configurations already run MOM6, so v3.0 would make that a MOM6-to-MOM6 initialization path.

### [NBM v5.0](./models/nwp_models/regional/usa/nbm.md)
- **Operational since:** May 5, 2026 (originally targeted April 15; slipped to April 23, then April 30, with the April 30 attempt deferred under the SCN's CWD/ECE contingency)
- **UFS relationship:** NBM is not itself a UFS model — it's a statistical post-processing system. But v5.0 added UFS-relevant inputs:
  - Higher-resolution GDPS (15 km) and REPS (10 km) inputs (these are Canadian, not UFS-related)
  - **AIGFS** (NOAA's operational AI global model) added as input for temperature, wind speed, and QPF
  - **ECAIFS** (ECMWF's AI/IFS hybrid) similarly added
  - **SREF eliminated** as an input — this is operationally significant because it removed the last major downstream consumer of SREF, and SCN 26-47 folded SREF retirement into the first wave alongside RRFSv1/REFS rather than holding it for RRFSv2
- **Notes:** NBM's evolving input set tracks the UFS rollout indirectly. For RRFSv1 and REFS the switch took effect on November 3, 2026 under [SCN 26-89](https://www.weather.gov/media/notification/pdf_2026/scn26-89_RRFS_Downstream.pdf) (October 2, 2026), which replaces the NBM's NAM and HiresW inputs with RRFS and REFS. The one checkable product consequence, removal of the CONUS core `DRYTPROB` record, is confirmed from the 12 UTC cycle. GFSv17 has no equivalent notice yet.

---

## Completed retirements (first wave — with RRFSv1, November 3, 2026)

These systems were retired on **November 3, 2026 at 12 UTC**, the cycle that brought RRFSv1 and REFS into operations. The set was originally proposed in NWS PNS 25-41 (June 26, 2025). The retirements were made under NWS SCN 26-47 (termination; AAC, October 2, 2026), and the RRFS/REFS implementation under companion SCN 26-48. The retired systems' catalogue entries have been removed; [STATUS.md](./STATUS.md#recent-retirements) keeps the retirement record. Last cycles are as verified on NOMADS on 2026-11-03.

| Retired system | Replacement | Notes |
|---|---|---|
| NAM | [RRFS](./models/nwp_models/regional/usa/rrfs.md) | Full retirement: 12 km parent, all 3 km nests, fire-weather nest. Last cycle 06 UTC, November 3 |
| NAM Nest | [RRFS](./models/nwp_models/regional/usa/rrfs.md) | All convection-allowing nests retired with NAM |
| HREF | [REFS](./models/ensemble_models/regional/usa/refs.md) | REFS extends to 60 h and runs 4× daily for all regions. Last cycle 06 UTC, November 3 |
| HiresW CONUS, AK, HI, PR | [RRFS](./models/nwp_models/regional/usa/rrfs.md) (3 km CONUS/AK; 2.5 km HI/PR) | Last cycles 00 UTC (CONUS, HI) and 06 UTC (AK, PR), November 3. The Guam domain continues as [HiresW Guam](./models/nwp_models/regional/usa/hiresw-guam.md) |
| SREF | [REFS](./models/ensemble_models/regional/usa/refs.md) | Moved into the first wave from the expected second wave. Last cycle 09 UTC, November 3 |
| NARRE | [REFS](./models/ensemble_models/regional/usa/refs.md) | **Retired earlier**, on or about January 7, 2026, under [SCN 25-87](https://www.weather.gov/media/notification/pdf_2025/scn25-87_EMC_services_termination.pdf). Not named in SCN 26-47 and not part of this wave |
| NAM MOS | Post-processed RRFS-based statistical guidance | Retired alongside NAM |
| NAM and HREF coupling into HYSPLIT | RRFS coupling into HYSPLIT | Retired under a separate notice, SCN 26-78 (HYSPLIT 9.1). No `hysplit.tCCz.nam*` or `rens.*` files remain on NOMADS, and the RRFS inputs are present from 12 UTC on November 2 |
| NAM and HiresW inputs to the NBM; NAM ObsProc dumps; NAM, SREF and HRW pages on the MAG website | RRFS and REFS (NBM, MAG); none (ObsProc) | Retired under a separate notice, SCN 26-89, on the same cycle. The last NAM ObsProc dump was 12 UTC, November 3. HREF and NAM MOS are not addressed as NBM inputs |

---

## Expected future retirements (second wave — with RRFSv2)

These systems are publicly signaled (through NOAA roadmap documents) but have **no formal retirement notification** as of early November 2026 (NWS notification index re-checked 2026-11-03). They are expected to be retired when RRFSv2 (MPAS-based) is operational, which has no announced date.

| Retiring system | Replacement | Status |
|---|---|---|
| [HRRR](./models/nwp_models/regional/usa/hrrr.md) | RRFSv2 (MPAS) | Expected; no formal SCN |
| [RAP](./models/nwp_models/regional/usa/rap.md) | RRFSv2 (MPAS) | Expected; no formal SCN |

These represent the second major retirement wave under UFS. Users with operational dependencies on HRRR or RAP should plan for migration to RRFS but should not expect this to happen in 2026.

Note that SREF, which was previously listed in this second-wave table, was moved to the first wave and retired on November 3, 2026 under SCN 26-47.

---

## The 21 → 8 mapping

NOAA's stated goal of reducing the NCEP Production Suite from 21 to 8 reflects multiple consolidations happening simultaneously. The table below shows which legacy systems collapse into which UFS components, restricted to systems documented in this repository.

| UFS component (target) | Legacy systems consolidated |
|---|---|
| **GFSv17** (global atmosphere + ocean + ice + wave) | GFS (v16); GDAS (v16); historically separate ocean and wave initialization paths |
| **RRFSv1** (regional convection-allowing deterministic) | NAM, NAM Nest, HiresW (except Guam) — all retired November 3, 2026 |
| **REFS** (regional convection-allowing ensemble) | HREF, SREF (retired November 3, 2026), NARRE (retired January 2026) |
| **HAFS** (tropical cyclone) | HWRF, HMON (superseded as primary in 2023, but still running as frozen legacy guidance; no retirement notice yet) |
| **AQM v7 within NAQFC** (air quality) | Pre-v7 offline-coupled GFS-CMAQ chain |
| **RTOFS v3.0** (global ocean) | RTOFS v2.5 (HYCOM); historically separate Navy/NOAA ocean initialization paths |
| **GEFS** + **REFS** + **RRFSv2 ensemble** (future) | GEFS (continues), HREF, NARRE, SREF |
| **RRFSv2 (MPAS, future)** | HRRR, RAP |

The remaining UFS components in the 8-system target include atmospheric ensemble systems and specialized non-NWP services that are outside this repository's scope.

---

## Models that consume UFS outputs

Some operational models do not themselves run on UFS but incorporate UFS-derived inputs:

### [NBM (National Blend of Models)](./models/nwp_models/regional/usa/nbm.md)
NBM is a statistical post-processor, not a UFS component. But as UFS systems become operational, NBM ingests their output and discontinues legacy inputs. NBM v5.0 (May 2026) eliminated SREF as an input and added AIGFS. The next step took effect on November 3, 2026. Under [SCN 26-89](https://www.weather.gov/media/notification/pdf_2026/scn26-89_RRFS_Downstream.pdf) (October 2, 2026), the NBM replaced its NAM (12 km and 3 km) and HiresW inputs with RRFS and all RRFS-based REFS configurations, in both the Core and QMD systems, with the expert weights reassigned. The input switch cannot be seen in the distributed files. Its one checkable product consequence can: the CONUS core `DRYTPROB` record is absent from the 12 UTC cycle onward. The notice does not address HREF or NAM MOS as inputs, though both retired the same day. GFSv17 has no comparable notice yet.

### [HYSPLIT](./models/air_quality_models/regional/usa/hysplit-dust.md) — RRFS coupling live (inputs present from November 2, 2026, 12 UTC)
HYSPLIT is an offline-coupled Lagrangian dispersion model: it ingests pre-computed meteorological fields rather than computing meteorology itself, so its driving NWP source is a configuration choice that follows the operational suite. **HYSPLIT 9.1 (SCN 26-78, AAA update of October 2, 2026, effective November 3, 2026) couples RRFS in and retires the NAM and HREF couplings.** On NOMADS the RRFS input files appear from the 12 UTC cycle on **November 2**, a day before the notice's date, and no NAM or `rens` inputs remain in the two retained days. New ARL-packed input files are `hysplit.tCCz.rrfs.${domain}`, `hysplit.tCCz.rrfsfHH.${domain}` (HH = 00–42 in 6 h steps) and archived `hysplit.tCCz.rrfsa.${domain}`, for the 00/06/12/18 UTC cycles; the `hysplit.tCCz.nam*` set and the `hysplit.tCCz.rens.{hiresw,hrrr,nam}.m0M` regional-ensemble inputs are removed.

This makes HYSPLIT the first NAQFC component to consume RRFS output, and it carries a **timing cost for catalogued data**: the higher-resolution input delays the dust products under `dustcs.YYYYMMDD/` by roughly 11–15 minutes at 06 UTC and 45–50 minutes at 12 UTC, with `canned_wfo` products 45–50 minutes late. Fixed-schedule pollers need wider windows. On November 3 the 06 and 12 UTC `dustcs.tCCz.sfc.1hr.grib2` files landed at 09:09 and 15:45 UTC. No pre-upgrade cycle is still on NOMADS to measure the delay against, so the SCN's figures are unverified.

The SCN's domain enumerations do not match its own prose. The intro lists `na, hi, ak, pr, firewx` for the RRFS forecast files, while the filename line lists `na, hi, pr, firewx` and puts `ak` on the `rrfsfHH` line together with `conus`. **The filename lines were right.** The live files (`/pub/data/nccf/com/hysplit/prod/`, checked 2026-11-03) are:
- `rrfs.{firewx,hi,na,pr}`
- `rrfsa.{ak,hi,na,pr}`
- `rrfsfHH.{ak,conus}` The notice also closes with a statement that Alaska and Hawaii "will continue using CMAQ v4" — CMAQ belongs to the [AQM](./models/air_quality_models/regional/usa/aqm.md) component rather than HYSPLIT, and no CMAQ v4 is documented in the current NAQFC chain, so this reads as carried-over boilerplate rather than a HYSPLIT statement. HYSPLIT-Dust is CONUS-only regardless. The AAA update changed only the date and left both wording problems in place.

### Downstream coupled systems
[HAFS](./models/tropical_cyclone_models/hafs.md) is initialized from [RTOFS](./models/ocean_models/global/us/rtofs-global.md), so RTOFS upgrades flow into HAFS in subsequent cycles. Both HAFS configurations already run MOM6 (HFSA since v2.0, HFSB since v2.1), and v2.2 (October 13, 2026) kept that. For now they are initialized from the HYCOM-based RTOFS v2.5. When RTOFS migrates to MOM6/CICE6 in v3.0, the ocean model on both sides of that handoff will be MOM6.

### REFS member contributions from non-UFS systems
[HRRR](./models/nwp_models/regional/usa/hrrr.md) contributes two members (current and 6 h old cycles) to the CONUS and Alaska REFS domains. HRRR is not itself a UFS system and was not retired in the first wave. That makes HRRR an explicit operational input to a UFS-era ensemble from the RRFSv1 implementation on November 3, 2026 until the eventual RRFSv2 transition that retires HRRR.

---

## What this index does not cover

- **Internal NCEP infrastructure systems** that are not publicly distributed (workflow management, post-processing chains)
- **Non-NCEP UFS users** (research configurations, university-run UFS instances)
- **Detailed UFS architecture** — for that, see https://ufscommunity.org/
- **Atmospheric ensemble systems unrelated to UFS** — GEFS continues to run, but its UFS-relevant changes are limited to coupling with GFSv17's analysis system
- **AI components within UFS** — these may emerge in future versions but as of May 2026 are not part of operational UFS components. See [AI_MODELS.md](./AI_MODELS.md) for the AI-specific index.

---

## Tracking changes

For lifecycle events (implementation dates, version numbers, slippage), see [STATUS.md](./STATUS.md). UFS-related items in STATUS.md are kept brief, with detailed UFS context maintained here.

For the broader Copernicus and ECMWF programmes (which are UFS's European peers but operate independently), see [COPERNICUS.md](./COPERNICUS.md) and the various ECMWF entries in `models/`.

---

## Contributing

If a UFS-related change is announced — a new SCN, a slippage, a new component integration, or a new retirement notification — please update both this file and STATUS.md with consistent dates and link to the authoritative source. The UFS rollout is multi-year and active; this page will need ongoing maintenance.
