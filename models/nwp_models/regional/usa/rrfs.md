# RRFS (Rapid Refresh Forecast System)

## What this model is
The Rapid Refresh Forecast System (RRFS) is NOAA's next-generation convection-allowing, hourly-updating regional numerical weather prediction system for North America.

RRFS is built on the Unified Forecast System (UFS) framework and is designed to consolidate and replace several legacy NCEP regional modeling systems, including the NAM, HiresW (except the Guam domain), HREF, SREF, and NARRE. It provides both deterministic and ensemble guidance, with the ensemble component distributed as REFS (RRFS Ensemble Forecast System).

RRFS and REFS are scheduled to become operational on **October 6, 2026 at 12 UTC** under NWS Service Change Notice 26-48 (May 12, 2026; updated July 6, 2026), subject to the standard CWD/ECE postponement contingency. A pre-implementation real-time parallel feed is expected on NOMADS on or about **August 11, 2026**.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** North America
- **Domain details:**
  Full North America parent domain, with output distributed on five grids:

  | Output grid | Projection (decoded) | Ni × Nj | Spacing | Anchor / notes |
  |---|---|---|---|---|
  | North America | `rotated_ll`, south pole 35°S / 247°E, no rotation angle | 1127 × 683 | 0.1083° (≈ 12 km) | First point −36.9303° / 299.0° in rotated coordinates; 769,741 points. Filename token is `13km`. |
  | CONUS | `lambert`, LoV 262.5°E, Latin1 = Latin2 = LaD = 38.5°N | 1799 × 1059 | 3000 m | First point 21.138123°N, 237.280472°E |
  | Alaska | `polar_stereographic`, LaD 60°N | 1649 × 1105 | 2976 m | First point 40.53°N, 181.429°E; 1,822,145 points |
  | Hawaii | `mercator`, LaD 20°N | 321 × 225 | 2500 m | First point 18.072699°N, 198.474999°E |
  | Puerto Rico | `mercator` | 544 × 310 | 2500 m | — |
  | Fire weather | `lambert`, LoV 265°E, Latin1 = Latin2 = LaD = 25°N | varies per cycle | **1270 m** | Relocatable — see below |

  All grids use `shapeOfTheEarth = 6` (spherical, 6371229 m).

  **The fire-weather domain is relocatable and is not a fixed 5° × 5° box.** SCN 26-48
  describes it as a "relocatable 1.5 km RRFS fire weather" domain. Decoding two
  consecutive cycles shows both the grid size and the corner moving: the 2026-08-12
  06 UTC cycle was 561 × 355 anchored at 39.442°N / 234.191°E, and the 12 UTC cycle
  was 522 × 390 anchored at 36.454°N / 244.665°E. The actual grid increment is
  **1270 m**, not the 1500 m implied by the `1p5km` filename token — the token is a
  label, not a measurement.

  **The Alaska grid increment is 2976 m, not 3000 m**, despite the `3km` filename token.
  CONUS is a true 3000 m.

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing)
- **Framework:** Unified Forecast System (UFS)
- **Dynamical core (v1):** FV3 (Finite-Volume Cubed-Sphere), in its limited-area configuration (FV3-LAM)
- **Dynamical formulation:** Non-hydrostatic, finite-volume on the cubed-sphere limited-area grid
- **Convection-allowing:** Yes — deep convection is explicitly resolved at all RRFS resolutions (3 km, 2.5 km, 1.5 km); no cumulus parameterization is used
- **Horizontal resolution:**
  - ≈ 12 km (North America parent output grid, `13km` token)
  - 3 km (CONUS), 2976 m (Alaska)
  - 2.5 km (Hawaii, Puerto Rico)
  - 1270 m (relocatable fire-weather domain, `1p5km` token)
- **Vertical levels:** 65 (NOMADS model description)
- **Forecast length:**
  - **84 hours** at the 00, 06, 12, and 18 UTC synoptic cycles
  - **18 hours** at the other 20 hourly cycles
  - **36 hours** for the fire-weather domain
- **Update frequency / cycles:** Hourly (24× daily); fire weather 4× daily (00/06/12/18 UTC)
- **Temporal output resolution:** 15-minute subhourly output f001–f018 at every cycle;
  hourly output where the full product set is published — but note that **the full
  product set is not published at every cycle**. See "Output organization" below.

---

## Data assimilation
- **Data assimilation:** Yes
- **Method:** Hourly cycling **GSI-based hybrid 3DEnVar** (Gridpoint Statistical Interpolation), with flow-dependent background-error covariances drawn from an associated convective-scale ensemble combined with a static background-error covariance term. The configuration follows the convective-scale DA lineage developed for HRRR and RAP, adapted to the FV3-LAM dynamical core.
- **Cadence:** Hourly analysis updates, with the cycle continuously providing first-guess fields to the next hour.
- **Direct radar reflectivity assimilation:** RRFS directly assimilates radar reflectivity observations within the GSI EnVar framework — distinct from the indirect cloud-analysis approach used in some prior NCEP convection-allowing systems. A non-variational complex cloud analysis is also available as an optional post-variational step (as in HRRR/RAP).
- **Assimilated observations:** Conventional surface, aircraft, and radiosonde observations; satellite radiances; GPS radio occultation; mesonet observations; radar reflectivity; and additional convective-scale observations characteristic of the HRRR-lineage DA design.
- **Notes:** The specific operational configuration (e.g., static/ensemble weight, localization radii, ensemble source) is documented in the EMC RRFSv1 evaluation materials and ongoing peer-reviewed literature on the GSI-based EnVar system for FV3-LAM.

---

## What it provides
Deterministic short-range forecasts of:
- Near-surface temperature, humidity, wind, and pressure
- Convective and severe storm evolution
- Precipitation amount and type
- Low clouds, ceilings, and visibility
- Aviation-relevant fields
- Fire-weather–relevant fields (dedicated 1.5 km domain)
- Subhourly (15-minute) output in early forecast hours

RRFS is designed to provide a single, unified convection-allowing guidance source across North America, replacing the role previously filled by multiple separate systems.

---

## Ensemble member output (used by REFS)
In addition to the deterministic forecast, the RRFS run produces **five ensemble forecast members** at the 00, 06, 12, and 18 UTC cycles, running to 60 hours over the same North America domain. These members use different initial conditions, lateral boundary conditions, and model physics relative to the deterministic forecast. The members are consumed by [REFS](../../../ensemble_models/regional/usa/refs.md) (along with 6 h time-lagged copies of both the deterministic and ensemble RRFS, and HRRR members for CONUS/AK) to generate combined ensemble products.

---

## Output organization

Deterministic output lives under `rrfs.YYYYMMDD/CC/`; fire-weather output under a
separate `firewx.YYYYMMDD/CC/`. Lead time is a **three-digit** token (`f000`) —
[REFS](../../../ensemble_models/regional/usa/refs.md) uses two digits, which is a
frequent source of 404s when code is shared between the two.

| Filename pattern | Content |
|---|---|
| `rrfs.tCCz.prslev.13km.fFFF.na.grib2` | Pressure-level, North America parent grid |
| `rrfs.tCCz.2dfld.13km.fFFF.na.grib2` | 2D fields, North America parent grid |
| `rrfs.tCCz.prslev.3km.fFFF.{conus\|ak}.grib2` | Pressure-level, CONUS / Alaska |
| `rrfs.tCCz.prslev.2p5km.fFFF.{hi\|pr}.grib2` | Pressure-level, Hawaii / Puerto Rico |
| `rrfs.tCCz.2dfld.3km.fFFF.{conus\|ak}.grib2` | 2D fields, CONUS / Alaska |
| `rrfs.tCCz.2dfld.2p5km.fFFF.{hi\|pr}.grib2` | 2D fields, Hawaii / Puerto Rico |
| `rrfs.tCCz.2dfld.3km.subh.fFFF.{conus\|ak}.grib2` | 15-minute 2D fields, CONUS / Alaska |
| `rrfs.tCCz.2dfld.2p5km.subh.fFFF.{hi\|pr}.grib2` | 15-minute 2D fields, Hawaii / Puerto Rico |
| `rrfs.tCCz.prslev.1p5km.fFFF.firewx_lcc.grib2` | Pressure-level, fire weather (under `firewx.YYYYMMDD/CC/`) |
| `rrfs.tCCz.2dfld.1p5km.fFFF.firewx_lcc.grib2` | 2D fields, fire weather (under `firewx.YYYYMMDD/CC/`) |

### The 24 hourly cycles are not equivalent — three distinct tiers

This is the single most consequential thing to know before scripting against RRFS, and
it is not stated in the SCN. **Sixteen of the twenty-four cycles publish nothing but the
15-minute subhourly files.**

| Cycles | Hourly `prslev` + `2dfld` | 13 km NA output | 15-min `subh` | GRIB2 files/cycle |
|---|---|---|---|---|
| **00, 06, 12, 18 UTC** | f000–f084 on all four subset grids | f000–f084 hourly | f001–f018 | **922** |
| **03, 09, 15, 21 UTC** | f000–f018 on all four subset grids | No | f001–f018 | 224 |
| **All other 16 cycles** | **None** | No | f001–f018 | 72 |

Verified by enumeration across both distributions: NOMADS cycles 12–17 on 2026-08-12,
and AWS cycles 00/01/03/04/09/15/21 on 2026-08-11 plus 03/09 on 2026-08-12. The pattern
is identical on both, so it is a property of the model suite and not of the transition.
A 3-hourly forecast pulled from the "hourly" model will silently fall back to subhourly
2D fields for two cycles out of every three.

### 13 km North America output moved from 3-hourly to hourly

On the AWS prototype the North America grid was written **3-hourly**: f000–f084, 29 steps
per family, 58 GRIB2 files per synoptic cycle. On the NOMADS parallel feed it is
**hourly**, 85 steps per family. This is the only cadence change observed anywhere across
the transition.

Records per file, decoded and confirmed identical on both distributions:

| Family | Records |
|---|---|
| `prslev` (every grid, including 13 km NA and fire weather) | 675 |
| `2dfld` | 318 |
| `2dfld … subh` | 157 |

### Publication latency (NOMADS, measured)

Off-synoptic 15 UTC cycle on 2026-08-12: first file 16:13 UTC (**T+1h13m**), last file
16:48 UTC (**T+1h48m**). Subhourly files trail the hourly ones by about eight minutes.

The 12 UTC cycle on 2026-08-12 is **not** representative — its files all appeared at once
at 13:49 UTC, which is the feed being switched on rather than the run publishing
normally. Do not use day-one timings for scheduling.

A full description of RRFS products including variables and encoding is available at
https://www.nco.ncep.noaa.gov/pmb/products/rrfs.

---

## Relationship to other models
RRFS is intended to replace the following legacy NCEP regional systems on October 6, 2026:
- **NAM** (12 km parent domain and 3 km nests – CONUS, AK, HI, PR, fire weather)
- **NAM Nest**
- **HiresW** (all domains except Guam)
- **HREF** (replaced by REFS)
- **SREF** (replaced by REFS)
- **NARRE** (replaced by REFS)
- **NAM MOS** (retired alongside NAM)

HRRR, RAP, and the NAM 12 km parent domain are not retired with RRFSv1. These systems are expected to be retired later in conjunction with RRFSv2, which is planned to transition to the MPAS dynamical core. HRRR additionally contributes two members (current and 6 h old cycles) to the CONUS and Alaska REFS domains, making it an explicit operational input to REFS during the RRFSv1 era.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**
  - **NOMADS (pre-implementation parallel feed, live since 2026-08-12 12 UTC):**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/rrfs/para/
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/para/noaaport/rrfs/
  - **NOMADS (post-implementation, from October 6, 2026):**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/rrfs/prod/

> ⚠️ **NOMADS is currently the only channel. The AWS prototype bucket has stopped
> updating and no replacement cloud mirror exists.**
>
> `s3://noaa-rrfs-pds` last wrote deterministic output at the **11 UTC** cycle on
> 2026-08-12 (final object timestamp 12:58:58 UTC) and ensemble member output under
> `rrfs_a/` at the same cycle. The NOMADS parallel feed picked up at the **12 UTC**
> cycle. The handover is clean — no cycle is duplicated and none is missing — but it
> leaves a single point of access.
>
> Probed on 2026-08-12 and all absent: `noaa-nws-rrfs-pds`, `noaa-rrfs-para-pds`,
> `noaa-refs-pds`, `noaa-nws-refs-pds`, `noaa-rrfs-pds-para` (S3, 404) and
> `gs://rrfs` (GCS, 404). There is no NODD landing zone for the parallel feed.

> ⚠️ **The AWS registry description contradicts the bucket's observed behaviour (TBD).**
> The registry page states that on the start of the parallel phase the prototype feed
> will stop updating, but that users "will be able to access the pre-implementation and
> operational data through this bucket or through the feeds noted in the Service Change
> Notice," with bucket filenames and directory structures aligned to the SCN. As of
> 2026-08-12 the bucket carries no post-cutover data at all. Whether NODD intends to
> re-point the bucket at the parallel stream, or whether the sentence describes only the
> eventual operational feed, is unresolved. Recheck before relying on AWS for any RRFS
> data after 2026-08-12 11 UTC.

### What the move to NOMADS costs

Three capabilities present on the AWS prototype have no equivalent on the parallel feed.
All three were confirmed by direct request, not inferred.

1. **No `.idx` sidecars.** Every GRIB2 object on AWS carried a matching `.idx`. On
   NOMADS, `…grib2.idx` returns 404 for every family tested (`prslev.3km.conus`,
   `2dfld.13km.na`, `2dfld.2p5km.subh.hi`). **Byte-range subsetting is not available.**
   This matters more for RRFS than for most entries: a single CONUS `prslev` step is
   ~580 MB and a full 84-hour synoptic CONUS `prslev` series is roughly 48 GB. Users who
   previously pulled a handful of fields per step with `wgrib2 -i`, cfgrib, or kerchunk
   must now either download whole files or build their own index. NCEP's GRIB filter
   service is a possible substitute if and when it is extended to the `para` paths —
   it is not currently (**TBD**).

2. **No individual ensemble members.** AWS carried the five RRFS ensemble members under
   `rrfs_a/rrfsens.YYYYMMDD/CC/m001` … `m005`, each with `prslev` (24 steps) and `2dfld`
   (61 steps) on the CONUS, Alaska, Hawaii, Puerto Rico **and** North America grids,
   plus `.idx` sidecars and per-member BUFR. The NOMADS `refs/para/` tree contains only
   `ensprod/` — the combined products. **Raw member output now has no open channel.**
   See the [REFS entry](../../../ensemble_models/regional/usa/refs.md#data-availability).

3. **No BUFR soundings.** AWS synoptic cycles carried `rrfs.tCCz.bufrsnd.tar.gz`,
   `rrfs.tCCz.class1.bufr`, and an exploded `bufr.CC/` directory of ~1,900 per-station
   files. All return 404 on the NOMADS 12 UTC cycle. These are point soundings rather
   than gridded output and so are outside catalog scope, but the loss is worth naming
   because BUFRKIT-style workflows depended on them.

**Archive depth also shrinks.** AWS held roughly ten days of rolling history. The NOMADS
parallel tree holds **two days** — verified 2026-08-13, when 2026-08-12 and 2026-08-13
were present and 2026-08-11 had already rolled off. Combined with the loss of `.idx`
sidecars, this makes RRFS substantially harder to consume than it was on the prototype:
anyone needing more than 48 hours of history must now mirror the feed themselves, at
roughly 922 files and several hundred gigabytes per synoptic cycle.

### NOAAPORT parallel stream

`https://nomads.ncep.noaa.gov/pub/data/nccf/com/para/noaaport/rrfs/` is a **flat rolling
directory**, not a dated tree, and carries a different product subset:
`grib2.rrfs.tCCz.{3km|13km}.fFFF.na` — North America grid only, at the 00/06/12/18 UTC
cycles, roughly 25 steps per resolution per cycle, with a window of about 24 hours
(oldest file 2026-08-11 20:30 UTC when checked). This stream predates the main parallel
feed: it was already carrying 2026-08-11 cycles. It is the AWIPS/NOAAPORT distribution
subset and is not a substitute for the full `rrfs/para/` tree.

---

## Status
- **2026-08-12, 12 UTC — parallel feed live on NOMADS; AWS prototype frozen.** The
  pre-implementation real-time feed began at the 12 UTC cycle at
  `/pub/data/nccf/com/rrfs/para/`, one day later than the "on or about August 11" date
  in SCN 26-48. The `s3://noaa-rrfs-pds` prototype bucket stopped at the 11 UTC cycle
  the same day. NOMADS is now the sole distribution channel; see
  [Data availability](#data-availability) for what the move costs.
- Proposal for legacy model retirement published in NWS Public Information Statement 25-41 (June 26, 2025), with a public comment period through July 26, 2025.
- Originally targeted for operational implementation in early 2026; implementation slipped through pre-operational evaluation.
- **NWS Service Change Notice 26-48 (May 12, 2026; updated July 6, 2026)** scheduled RRFS and REFS operational implementation for October 6, 2026 at 12 UTC, with retirement of NAM, HREF, SREF, and HiresW (except Guam) on the same day (terminations under companion SCN 26-47). Per the SCN, if the implementation date is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, implementation moves to 12 UTC on the next eligible weekday. The July 6, 2026 update is the second slip, moving the date from August 31, 2026 to October 6, 2026.
- Pre-implementation parallel data feed expected on NOMADS on or about August 11, 2026.
- RRFSv2 (based on the MPAS dynamical core) is under development and will drive the next phase of legacy model retirements (HRRR, RAP, NAM 12 km parent).

---

## Notes
- RRFS is a convection-allowing model and does not use a cumulus parameterization.
- Not all legacy NAM and HiresW products are reproduced in RRFS; some products are generated via the Smartinit post-processing system applied to RRFS output.
- A new RRFS verification website will replace the legacy regional verification graphics at EMC once RRFS is officially implemented.
- The cycle structure (84 h at 00/06/12/18 UTC, 18 h at all other hourly cycles) means RRFS is materially different from both NAM (which produced 84-hour forecasts only 4× daily) and HRRR (which produces 18 h hourly with 48 h extended runs at 00/06/12/18 UTC). For downstream applications that depended on the NAM/HiresW 84-hour synoptic schedule, RRFS preserves that cadence at the same cycles. For applications that depended on hourly short-range cycling, RRFS provides equivalent coverage at 18 h.
- **The 65-level count cannot be verified from the distributed output.** RRFS ships
  `prslev` (pressure-level) and `2dfld` (single-level) products only — there is no
  native- or hybrid-level file in the parallel feed, and none existed on the AWS
  prototype either. The figure comes from the NOMADS model description and is recorded
  here on that authority alone, unlike everything else in this entry's grid and encoding
  sections.
- **The model itself did not change across the transition.** Matched-cycle file pairs
  were decoded from both distributions (AWS 06 UTC and 10 UTC against NOMADS 12 UTC,
  15 UTC and 16 UTC, 2026-08-12) and compared on a per-record key tuple of shortName,
  typeOfLevel, level, stepType, lengthOfTimeRange, discipline/category/parameter number,
  typeOfStatisticalProcessing, threshold limits, probabilityType and percentileValue.
  **Zero records were unique to either side** in every pair tested: `prslev` on the
  Hawaii, 13 km North America and fire-weather grids; `2dfld` on Hawaii and Puerto Rico;
  and `2dfld … subh` on Hawaii. Header keys match as well — GRIB2 edition 2, centre
  `kwbc`, subCentre 0, `tablesVersion` 2, `localTablesVersion` 1,
  `generatingProcessIdentifier` 134, `typeOfGeneratingProcess` 2, `shapeOfTheEarth` 6,
  and `grid_complex_spatial_differencing` packing on every record. The transition is a
  distribution change only.
- **Ignore the 2026-08-12 12 UTC cycle when characterising the feed.** Three apparent
  product gaps in that cycle are switch-on artifacts, each disproved by the 18 UTC cycle
  the same day:
  - 13 km North America output began at f011 rather than f000 (18 UTC has f000).
  - The fire-weather directory carried `prslev` only (18 UTC carries both `prslev` and
    `2dfld`).
  - [REFS](../../../ensemble_models/regional/usa/refs.md) began at f09 rather than f01.

  Whatever each run had already written before the feed was switched on at 13:49 UTC was
  never copied across. Anyone who enumerated the tree on day one and hard-coded the
  observed start offsets will break on the next cycle.

---

## Official documentation
- NWS Service Change Notice 26-48 (RRFS and REFS implementation; May 12, 2026, updated July 6, 2026):  
  https://www.weather.gov/media/notification/pdf_2026/scn26-048_RRFS_and_REFS_Implementation.aab.pdf
- NWS Service Change Notice 26-47 (termination of NAM/SREF/HREF/HiresW/NAM MOS; updated July 6, 2026):  
  https://www.weather.gov/media/notification/pdf_2026/scn26-47_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS.aaa.pdf
- NWS Public Information Statement 25-41 (legacy model retirement proposal, June 26, 2025):  
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- RRFS product description at NCO:  
  https://www.nco.ncep.noaa.gov/pmb/products/rrfs
- RRFS output grids:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/rrfs_grids.txt
- NOAA RRFS prototype on AWS:  
  https://registry.opendata.aws/noaa-rrfs/
- RRFS/REFS evaluation page (EMC):  
  https://www.emc.ncep.noaa.gov/users/meg/rrfsv1/index.html
- RRFS feedback contact: rrfs.feedback@noaa.gov
