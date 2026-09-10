# RRFS (Rapid Refresh Forecast System)

## What this model is
The Rapid Refresh Forecast System (RRFS) is NOAA's next-generation convection-allowing, hourly-updating regional numerical weather prediction system for North America.

RRFS is built on the Unified Forecast System (UFS) framework and is designed to consolidate and replace several legacy NCEP regional modeling systems, including the NAM, HiresW (except the Guam domain), HREF, SREF, and NARRE. It provides both deterministic and ensemble guidance, with the ensemble component distributed as REFS (RRFS Ensemble Forecast System).

RRFS and REFS are scheduled to become operational on **October 14, 2026 at 12 UTC** under NWS Service Change Notice 26-48 (May 12, 2026; updated July 6, August 24 and September 9, 2026), subject to the standard CWD/ECE postponement contingency. A pre-implementation real-time parallel feed has been live since the 12 UTC cycle on **August 12, 2026**, on NOMADS and on AWS S3 via NOAA Open Data Dissemination. The five RRFS ensemble members, unavailable through August, began publishing on **September 9, 2026** — see [Ensemble member output](#ensemble-member-output-disseminated-since-september-9-2026).

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** North America
- **Domain details:**
  Full North America parent domain, with output distributed on six grids:

  | Output grid | Projection (decoded) | Ni × Nj | Spacing | Anchor / notes |
  |---|---|---|---|---|
  | North America | `rotated_ll`, south pole 35°S / 247°E, no rotation angle | 1127 × 683 | 0.1083° (≈ 12 km) | First point −36.9303° / 299.0° in rotated coordinates; 769,741 points. Filename token is `13km`. |
  | CONUS | `lambert`, LoV 262.5°E, Latin1 = Latin2 = LaD = 38.5°N | 1799 × 1059 | 3000 m | First point 21.138123°N, 237.280472°E |
  | Alaska | `polar_stereographic`, LaD 60°N | 1649 × 1105 | 2976 m | First point 40.53°N, 181.429°E; 1,822,145 points |
  | Hawaii | `mercator`, LaD 20°N | 321 × 225 | 2500 m | First point 18.072699°N, 198.474999°E |
  | Puerto Rico | `mercator` | 544 × 310 | 2500 m | — |
  | AWIPS subset (NA) | `polar_stereographic`, LaD 60°N | **4680 × 2830** | 3000 m | First point 6.5°N, 208.5°E; 13,244,400 points. Filename token is `2dfld_awipsubset.3km … .na`. Added 2026-08-27 — see below |
  | Fire weather | `lambert`, LoV 265°E, Latin1 = Latin2 = LaD = 25°N | 522 × 390 or 561 × 355 | **1270 m** | Relocatable, but only two placements observed — see below |

  All grids use `shapeOfTheEarth = 6` (spherical, 6371229 m).

  **The AWIPS subset grid is a distinct sixth geometry, not a subset of any of the
  others.** Despite carrying `3km` and `na` in the filename it shares nothing with the
  `13km` North America grid: it is polar stereographic at LaD 60°N, 4680 × 2830 at a true
  3000 m, 13.2 million points — by point count the largest grid RRFS publishes, roughly
  seven times the CONUS grid and seventeen times the 13 km North America grid. It carries
  only 14 records at f000 and 18 at later steps (surface and near-surface AWIPS staples:
  `REFC`, `VIS`, `GUST`, `MSLET`, `REFD`, 2 m `TMP`/`DPT`, 10 m winds, `CAPE`, `CIN`,
  `TCDC`, ceiling `HGT`, `HLCY`, plus `APCP` and `ASNOW` accumulations once past f000), so
  the files stay small despite the point count. Cadence is 3-hourly f000–f060 then 6-hourly
  to f084, 25 steps, at the 00/06/12/18 UTC cycles only.

  This stream **began at the 2026-08-27 12 UTC cycle** (first object 14:06:36 UTC) and is
  documented for the first time in SCN 26-48 AAD. It is absent from the AAC listing and
  from earlier revisions of this entry, and it is distinct from the flat NOAAPORT rolling
  directory described under [Data availability](#noaaport-parallel-stream).

  **The fire-weather domain is a relocatable Lambert conformal grid, not the 5° × 5°
  rotated latitude-longitude region the SCN describes.** SCN 26-48, through the AAC
  update of 2026-08-24, states the fire-weather run provides "output provided over a
  5 x 5-degree rotated latitude longitude region." Decoding the actual output contradicts
  this on all three counts: the grid is `lambert` with LoV 265°E and
  Latin1 = Latin2 = LaD = 25°N, the increment is **1270 m** rather than the 1500 m implied
  by the `1p5km` filename token, and the extent is roughly 663 × 495 km rather than
  5° × 5°.

  The domain is genuinely relocatable — two distinct configurations have been observed —
  but **it does not move freely from cycle to cycle**. Across 2026-08-12 and
  2026-08-23/24 it took exactly two placements, strictly alternating by cycle hour:

  | Cycles | Ni × Nj | First grid point | Extent |
  |---|---|---|---|
  | 00 and 12 UTC | 522 × 390 | 36.454°N, 244.665°E | ~663 × 495 km |
  | 06 and 18 UTC | 561 × 355 | 39.442°N, 234.191°E | ~713 × 451 km |

  Both placements were identical on 2026-08-12 and 2026-08-24, twelve days apart. Whether
  the domain is repositioned in response to fire-weather conditions, or is fixed per cycle
  hour in this configuration, cannot be determined from a twelve-day sample (**TBD**).
  **Do not assume a fixed grid**: code that caches grid geometry must key it on cycle
  hour at minimum, and should re-read the grid definition per file rather than relying on
  the observed pattern holding.

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
  - 3 km (AWIPS subset North America grid, polar stereographic, separate geometry)
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

## Ensemble member output (disseminated since September 9, 2026)
In addition to the deterministic forecast, the RRFS run produces **five ensemble forecast members** at the 00, 06, 12, and 18 UTC cycles, running to 60 hours over the same North America domain. These members use different initial conditions, lateral boundary conditions, and model physics relative to the deterministic forecast. The members are consumed by [REFS](../../../ensemble_models/regional/usa/refs.md) (along with 6 h time-lagged copies of both the deterministic and ensemble RRFS, and HRRR members for CONUS/AK) to generate combined ensemble products.

**The members became publicly available on 2026-09-09**, documented for the first time in
SCN 26-48 AAD and appearing in the bucket the same day — first object at 13:40:14 UTC, at
the 12 UTC cycle. This reverses the position recorded here through August, when the
prototype bucket's member stream had been lost at the 2026-08-12 cutover with no
replacement.

Members live under `rrfsens.YYYYMMDD/CC/m00#/` — a **sibling top-level prefix** to
`rrfs.YYYYMMDD/` and `refs.YYYYMMDD/`, not a subdirectory of either:

| Filename pattern | Content |
|---|---|
| `rrfs.tCCz.m00#.prslevnomads.3km.fFFF.{conus\|ak}.grib2` | Pressure-level member output, CONUS / Alaska |
| `rrfs.tCCz.m00#.prslevnomads.2p5km.fFFF.{hi\|pr}.grib2` | Pressure-level member output, Hawaii / Puerto Rico |
| `rrfs.tCCz.m00#.2dfldnomads.3km.fFFF.{conus\|ak}.grib2` | 2D member fields, CONUS / Alaska |
| `rrfs.tCCz.m00#.2dfldnomads.2p5km.fFFF.{hi\|pr}.grib2` | 2D member fields, Hawaii / Puerto Rico |

Verified structure (full enumeration of `rrfsens.20260909/`, 9,760 objects):

- **Five members**, `m001`–`m005`, at the **00/06/12/18 UTC cycles only**. Both complete
  cycles on the first day (12 and 18 UTC) carry all five.
- **f000–f060 hourly**, 61 steps — matching the REFS horizon, not the deterministic 84 h.
- **Four domains, no North America grid.** CONUS and Alaska at 3 km, Hawaii and Puerto
  Rico at 2.5 km, on grids byte-for-byte identical in definition to the deterministic
  subset grids (CONUS `lambert` 1799 × 1059 at 3000 m; Hawaii `mercator` 321 × 225 at
  2500 m). **The SCN describes the members as running "over the same NA region as the
  deterministic RRFS", but no 13 km North America member output is published** — and the
  prototype bucket did carry a member NA grid, so this is a reduction relative to what
  existed before August 2026.
- **`.idx` sidecars from the first object.** Unlike the deterministic feed, which went
  eleven days without them, member output has been indexed since it came up.
- **No BUFR.** SCN 26-48 AAD lists `rrfsens.…class1.bufr` and `bufrsnd.CC/bufr.*` but
  marks **both lines with a literal `(??)`** in the notice text, and neither exists in the
  bucket. Treat ensemble BUFR as not distributed.

### The `nomads` suffix means a reduced parameter set

The member filenames read `prslevnomads` and `2dfldnomads`, not `prslev` and `2dfld`. This
is not cosmetic — the member files carry a **much narrower parameter set** than their
deterministic counterparts:

| File family | Records | Scope (decoded) |
|---|---|---|
| `prslevnomads` | **112** | 8 parameters — `HGT`, `TMP`, `RH`, `DPT`, `SPFH`, `UGRD`, `VGRD`, `ABSV` — on 14 pressure levels (1000, 975, 950, 925, 900, 850, 800, 750, 700, 600, 500, 400, 300, 250 hPa) |
| `2dfldnomads` | **58** | 42 distinct parameters, surface and near-surface |
| *deterministic* `prslev` | *675* | *for comparison* |
| *deterministic* `2dfld` | *318* | *for comparison* |

Record counts are constant across lead time and domain (checked at f000, f012 and f060 on
CONUS, Alaska, Hawaii and Puerto Rico). Anyone expecting member files to mirror the
deterministic parameter set will find roughly one sixth of the pressure-level fields and
one fifth of the 2D fields. In particular the pressure-level members carry no vertical
velocity, no cloud or hydrometeor fields, and no geopotential above 250 hPa.

### Member encoding differs from the deterministic files

- **`generatingProcessIdentifier` is 136**, matching [REFS](../../../ensemble_models/regional/usa/refs.md) `ensprod` output rather than the deterministic RRFS value of **134**. Code that branches on this key to distinguish RRFS from REFS will misclassify member files as REFS.
- **PDT 1** (individual ensemble forecast) rather than PDT 0, with `typeOfProcessedData = pf` (perturbed forecast).
- **`perturbationNumber` carries the member number** (1–5), matching the `m00#` filename token.
- **`numberOfForecastsInEnsemble` reads 5**, not the 14 or 12 that REFS `ensprod` products declare. The member files describe the RRFS ensemble; the REFS products describe the combined time-lagged and HRRR-augmented membership. Both are correct for what they encode, and a reader that assumes one value across the whole system will be wrong on one of them.

Everything else matches the deterministic encoding: GRIB2 edition 2, centre `kwbc`,
`tablesVersion` 2, `localTablesVersion` 1, `shapeOfTheEarth` 6, and
`grid_complex_spatial_differencing` packing.

### Volume

Member output roughly doubles the size of an RRFS synoptic cycle. A single 12 UTC cycle of
`rrfsens.20260909/` is **140.3 GB across 4,880 objects** (≈ 28 GB per member), against
215.5 GB for the deterministic `rrfs.20260909/12/`. A CONUS `prslevnomads` step is ~177 MB
and a `2dfldnomads` step ~54 MB, versus ~599 MB for a deterministic CONUS `prslev` step.
Sidecar-driven byte-range subsetting is the only practical way to work with the full
member set.

---

## Output organization

Deterministic output lives under `rrfs.YYYYMMDD/CC/`; fire-weather output under a
separate `firewx.YYYYMMDD/CC/`; ensemble member output under a third top-level
`rrfsens.YYYYMMDD/CC/m00#/` (see [Ensemble member
output](#ensemble-member-output-disseminated-since-september-9-2026)). Every GRIB2 file is
accompanied by a matching `.grib2.idx` wgrib2 inventory sidecar on both channels, and
synoptic cycles additionally carry two BUFR sounding files (see [Data
availability](#data-availability)). Lead time is a **three-digit** token (`f000`) —
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
| `rrfs.tCCz.2dfld_awipsubset.3km.fFFF.na.grib2` | AWIPS 2D subset on the 4680 × 2830 polar stereographic grid; synoptic cycles only, 25 steps |
| `rrfs.tCCz.prslev.1p5km.fFFF.firewx_lcc.grib2` | Pressure-level, fire weather (under `firewx.YYYYMMDD/CC/`) |
| `rrfs.tCCz.2dfld.1p5km.fFFF.firewx_lcc.grib2` | 2D fields, fire weather (under `firewx.YYYYMMDD/CC/`) |
| `rrfs.tCCz.m00#.{prslev\|2dfld}nomads.{3km\|2p5km}.fFFF.${dom}.grib2` | Ensemble members (under `rrfsens.YYYYMMDD/CC/m00#/`); reduced parameter set |
| `rrfs.tCCz.bufrsnd.tar.gz` | BUFR sounding bundle, synoptic cycles only |
| `rrfs.tCCz.class1.bufr` | BUFR class-1 soundings, synoptic cycles only |

### The 24 hourly cycles are not equivalent — three distinct tiers

This is the single most consequential thing to know before scripting against RRFS, and
it is stated in neither the SCN (through the AAD update of 2026-09-09) nor the NOMADS
model description. **Sixteen of the twenty-four cycles publish nothing but the 15-minute
subhourly files.**

| Cycles | Hourly `prslev` + `2dfld` | 13 km NA | AWIPS subset | Members | 15-min `subh` | Deterministic GRIB2 files/cycle |
|---|---|---|---|---|---|---|
| **00, 06, 12, 18 UTC** | f000–f084 on all four subset grids | f000–f084 hourly | 25 steps | 5 × 488 files | f001–f018 | **947** |
| **03, 09, 15, 21 UTC** | f000–f018 on all four subset grids | No | No | No | f001–f018 | 224 |
| **All other 16 cycles** | **None** | No | No | No | f001–f018 | 72 |

The synoptic-cycle count was 922 before the AWIPS subset stream appeared on 2026-08-27;
the 25 additional files bring it to 947. The member files are counted separately because
they live under a different top-level prefix.

Verified by enumeration across both distributions: NOMADS cycles 12–17 on 2026-08-12,
and AWS cycles 00/01/03/04/09/15/21 on 2026-08-11 plus 03/09 on 2026-08-12, re-checked on
the AWS bucket for 2026-09-09. The pattern is identical on both, so it is a property of
the model suite and not of the transition.
A 3-hourly forecast pulled from the "hourly" model will silently fall back to subhourly
2D fields for two cycles out of every three.

> ⚠️ **Both official descriptions state the opposite of what the feed publishes.** The
> NOMADS model description reads: "Hourly deterministic output is generated for all cycles
> and parameters are available in pressure level (prslev) and two-dimensional (2dfld)
> files over CONUS, Alaska, Hawaii, and Puerto Rico." SCN 26-48 AAD likewise gives only
> the 84 h / 18 h split without noting that most cycles carry no `prslev` or `2dfld` at
> all. **The directory listing is authoritative; the descriptions are not.**
>
> The NOMADS description also omits two output streams that demonstrably exist and that
> the SCN does document: the 13 km North America grid and the relocatable 1.5 km
> fire-weather domain.

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
RRFS is intended to replace the following legacy NCEP regional systems on October 14, 2026:
- **NAM** (12 km parent domain and 3 km nests – CONUS, AK, HI, PR, fire weather)
- **NAM Nest**
- **HiresW** (all domains except Guam)
- **HREF** (replaced by REFS)
- **SREF** (replaced by REFS)
- **NARRE** (replaced by REFS)
- **NAM MOS** (retired alongside NAM)

HRRR and RAP are not retired with RRFSv1. They are expected to be retired later in conjunction with RRFSv2, which is planned to transition to the MPAS dynamical core. The NAM 12 km parent domain is **not** in this group — SCN 26-47 discontinues the NAM North America (12 km) grid together with all nests on October 14, 2026. HRRR additionally contributes two members (current and 6 h old cycles) to the CONUS and Alaska REFS domains, making it an explicit operational input to REFS during the RRFSv1 era.

**NARRE is listed above on the authority of PNS 25-41, not SCN 26-47.** Neither the AAB
subject line ("Termination of the NAM, SREF, HREF, HiresW, and NAM MOS") nor the body of
SCN 26-47 names NARRE or gives a NARRE product path. Its replacement by REFS is signalled
but not formally scheduled by a Service Change Notice.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**
  - **AWS S3 (NODD), pre-implementation parallel feed:**
    - `s3://noaa-rrfs-ops-pds/` — https://noaa-rrfs-ops-pds.s3.amazonaws.com/index.html
    - Top-level prefixes `rrfs.YYYYMMDD/`, `firewx.YYYYMMDD/`, `rrfsens.YYYYMMDD/` (members, since 2026-09-09) and `refs.YYYYMMDD/`
    - Anonymous access, no requester-pays, `us-east-1`
  - **NOMADS, pre-implementation parallel feed:**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/rrfs/para/
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/para/noaaport/rrfs/
  - **NOMADS, post-implementation (from October 14, 2026):**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/rrfs/prod/

The two channels carry the same data, and as of late August 2026 they carry the same
*set* of files. GRIB2 files, `.idx` sidecars and BUFR bundles were each compared by MD5
and are byte-identical.

| | `s3://noaa-rrfs-ops-pds` | NOMADS `rrfs/para/` |
|---|---|---|
| `.idx` sidecars | Yes, on every object | Yes, on every file since ~mid-August 2026 |
| Byte-range subsetting | Yes | Yes |
| BUFR soundings | Yes at synoptic cycles | Yes, since the 2026-08-23 18 UTC cycle |
| AWIPS subset grid | Yes, since the 2026-08-27 12 UTC cycle | Unverified |
| Ensemble members | Yes, since the 2026-09-09 12 UTC cycle | Unverified |
| Retention | Every date since 2026-08-12; nothing expired yet | 2 days |
| Latency (2026-08-14 12 UTC) | first object 13:51 UTC | first file 13:50 UTC |
| Directory listing | Reliable S3 `list-type=2` | Frequently truncated; needs retries |
| Licence | CC0-1.0, stated in the AWS Open Data Registry | US Government work, no per-file statement |

A deterministic synoptic cycle is **947 GRIB2 files, 947 sidecars and 2 BUFR files** on
S3 as of 2026-09-09, up from 922 when the AWIPS subset stream was added on 2026-08-27.
Member output adds a further **2,440 GRIB2 files and 2,440 sidecars** under
`rrfsens.YYYYMMDD/CC/`. The 922-file figure was verified set-symmetric across both
channels on 2026-08-24; **the two streams added since then have been verified on S3 only**,
because NOMADS was unreachable at the time of checking. SCN 26-48 AAD presents both as
NOMADS additions, so the expectation is that they are on both channels, but that is not
confirmed here.

**Choose on retention and listing reliability, not content.** NOMADS `para` retains 48
hours; the bucket has kept every date since it came up. S3 also gives reliable directory
listings, which NOMADS does not — expect truncated responses and build in retries. Use
NOMADS when you want the freshest file, since it leads S3 by about a minute.

Indexing matters here more than for most models: a CONUS `prslev` step is ~580 MB and a
full 84-hour synoptic series is roughly 48 GB, so without a sidecar there is no practical
way to pull a handful of fields. Both channels now support it.

The bucket is registered in the AWS Open Data Registry as **NOAA Rapid Refresh Forecast
System (RRFS) and RRFS Ensemble Forecast System (REFS) [Operational]**
(https://registry.opendata.aws/noaa-rrfs-ops/), under the **Creative Commons 1.0
Universal Public Domain Dedication (CC0-1.0)**. New-object notifications are available
via `arn:aws:sns:us-east-1:123901341784:NewRRFSObject` (Lambda and SQS only) — note the
different AWS account from the prototype topic, so existing subscriptions do not carry
over.

> ⚠️ **`s3://noaa-rrfs-pds` — the old prototype bucket — is frozen and should not be
> used.** It stopped at the 11 UTC cycle on 2026-08-12 for RRFS and 06 UTC for REFS. It
> retains a separate registry entry titled "[Prototype]" and a different internal layout
> (`rrfs_public/`, `rrfs_a/`, `retro_output_final/`); the replacement uses a flat
> `rrfs.YYYYMMDD/` layout mirroring NOMADS. Code written against the prototype needs its
> prefixes rewritten, not just its bucket name. The prototype bucket is still the only
> source of the `retro_output_final/` retrospective parallel output, which has no
> equivalent in the replacement.

### What the August 2026 transitions cost, and what came back

The 2026-08-12 cutover to NOMADS-only briefly removed three capabilities the AWS
prototype had provided. All three have now been restored, in stages, over the following
four weeks.

- **`.idx` sidecars — restored.** Absent from NOMADS at the cutover; present on every
  object in `noaa-rrfs-ops-pds` from 2026-08-13, and on every NOMADS file from some point
  between August 15 and 21, 2026. The addition cannot be dated more precisely because
  NOMADS retains only two days.
- **BUFR soundings — restored.** `rrfs.tCCz.bufrsnd.tar.gz` and `rrfs.tCCz.class1.bufr`
  at synoptic cycles, on S3 from 2026-08-13 and on NOMADS from the **2026-08-23 18 UTC
  cycle** — the 00 and 12 UTC cycles that day return 404, so that is the first cycle
  carrying them. The exploded per-station `bufr.CC/` directory the prototype carried is
  still on neither channel, **notwithstanding SCN 26-48 AAD, which now lists the BUFR
  output as `rrfs.YYYYMMDD/CC/bufrsnd.tCCz/bufr.*.YYYYMMDDCC` where AAC listed the
  tarball.** The bucket still carries the tarball and no exploded directory. Point
  soundings are outside catalog scope, but the loss was worth naming and so is the
  recovery.
- **Individual ensemble members — restored 2026-09-09, in reduced form.** The prototype
  carried five members under `rrfs_a/rrfsens.YYYYMMDD/CC/m001…m005`, each with `prslev`
  and `2dfld` on five grids. The replacement, live from the 2026-09-09 12 UTC cycle under
  the top-level `rrfsens.YYYYMMDD/` prefix, carries **four grids rather than five** (no
  13 km North America) and a **substantially reduced parameter set** in both file
  families. See [Ensemble member
  output](#ensemble-member-output-disseminated-since-september-9-2026) for the full
  comparison, and the [REFS
  entry](../../../ensemble_models/regional/usa/refs.md#data-availability).
- **Native-level output — never restored.** The prototype's `natlev.3km.na` files have no
  successor in either channel, and SCN 26-48 AAD adds no path for them.

### NOAAPORT parallel stream

`https://nomads.ncep.noaa.gov/pub/data/nccf/com/para/noaaport/rrfs/` is a **flat rolling
directory**, not a dated tree, and carries a different product subset:
`grib2.rrfs.tCCz.{3km|13km}.fFFF.na` — North America grid only, at the 00/06/12/18 UTC
cycles, roughly 25 steps per resolution per cycle, over a window of about 24 hours. This
stream predates the main parallel feed. It is the AWIPS/NOAAPORT distribution subset and
is not a substitute for either full channel.

---

## Status
- **2026-09-09 — SCN 26-48 updated (AAD); implementation moved to October 14, 2026.**
  The third slip, from October 6, with no reason given. The update also documents the
  ensemble member output, the `2dfld_awipsubset` stream, and further `.idx` and BUFR
  files. Its stated scope claims to add "13 km North America output", but the AAC listing
  already carried both 13 km lines — see [Notes](#notes).
- **2026-09-09, 12 UTC — ensemble members published.** First object under
  `s3://noaa-rrfs-ops-pds/rrfsens.20260909/` at 13:40:14 UTC. Five members, four domains,
  f000–f060 hourly, `.idx` from the first object, no BUFR. See [Ensemble member
  output](#ensemble-member-output-disseminated-since-september-9-2026).
- **2026-08-27, 12 UTC — AWIPS subset stream added.** `rrfs.tCCz.2dfld_awipsubset.3km.
  fFFF.na.grib2` on a sixth grid geometry, first object 14:06:36 UTC. Absent from SCN
  26-48 AAC; documented for the first time in AAD.
- **2026-08-24 — SCN 26-48 updated (AAC).** Documents the `.idx` sidecars and the NOMADS
  BUFR files. The implementation date is unchanged at October 6, 2026. The update does
  not correct the August 11 feed date, the fire-weather domain description, or the
  per-cycle product set; see [Notes](#notes).
- **2026-08-23, 18 UTC — BUFR soundings added to NOMADS.** `bufrsnd.tar.gz` and
  `class1.bufr` at synoptic cycles, byte-identical to the S3 copies. This brought the two
  channels to full parity.
- **~2026-08-15 to 08-21 — `.idx` sidecars added to NOMADS.** Complete coverage, 922 of
  922 files per synoptic cycle, byte-identical to the S3 sidecars. The exact date cannot
  be recovered because NOMADS retains only two days.
- **2026-08-13, ~21:30 UTC — NODD replacement bucket live.** `s3://noaa-rrfs-ops-pds`
  began ingesting, backfilling what NOMADS still held and running in near real time
  since. Registered CC0-1.0 as an `[Operational]` dataset.
- **2026-08-12, 12 UTC — parallel feed live on NOMADS; AWS prototype frozen.** The
  pre-implementation real-time feed began at the 12 UTC cycle at
  `/pub/data/nccf/com/rrfs/para/`, one day later than the "on or about August 11" date in
  SCN 26-48 — a date NOAA has since corrected to August 12 in its AWS Open Data Registry
  entry, though not in the SCN itself. The `s3://noaa-rrfs-pds` prototype bucket stopped
  at the 11 UTC cycle the same day, leaving NOMADS briefly as the sole channel.
- Proposal for legacy model retirement published in NWS Public Information Statement 25-41 (June 26, 2025), with a public comment period through July 26, 2025.
- Originally targeted for operational implementation in early 2026; implementation slipped through pre-operational evaluation.
- **NWS Service Change Notice 26-48 (May 12, 2026; updated July 6, August 24 and September 9, 2026)** scheduled RRFS and REFS operational implementation for October 14, 2026 at 12 UTC, with retirement of NAM, HREF, SREF, and HiresW (except Guam) on the same day (terminations under companion SCN 26-47, updated to AAB on the same date). Per the SCN, if the implementation date is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, implementation moves to 12 UTC on the next eligible weekday. The July 6, 2026 update was the second slip, moving the date from August 31 to October 6; the September 9, 2026 update is the third, moving it to October 14.
- RRFSv2 (based on the MPAS dynamical core) is under development and will drive the next phase of legacy model retirements (HRRR, RAP).

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
- **Three points in SCN 26-48 disagree with the data, and survived the AAD update.**
  Recorded here because the catalog now differs from the current authority, not a
  superseded one:
  - The SCN says the parallel feed began "on or about August 11, 2026." It began at the
    12 UTC cycle on **August 12** — observed directly, and since corrected by NOAA in its
    AWS Open Data Registry entry, which now reads August 12th. The SCN and the registry
    disagree with each other.
  - The SCN describes the fire-weather output as covering "a 5 x 5-degree rotated
    latitude longitude region." It is Lambert conformal at 1270 m, roughly 663 × 495 km.
    See [What area it covers](#what-area-it-covers).
  - The SCN gives the 84 h / 18 h cycle split without noting that sixteen of the
    twenty-four cycles publish no `prslev` or `2dfld` files at all. See
    [Output organization](#output-organization).
- **Four further problems are specific to the AAD update of 2026-09-09.** All four were
  checked against the AAC text and against the bucket:
  - **The stated scope is wrong about the 13 km grid.** AAD says it is "Updated to …
    include ensemble member output, 13 km North America output, and additional idx and
    BUFR files." The AAC listing already carried both
    `rrfs.tCCz.prslev.13km.fFFF.na` and `rrfs.tCCz.2dfld.13km.fFFF.na`, and the stream
    itself has been in the replacement bucket since it opened on 2026-08-13. What is
    actually new in the AAD listing is the ensemble member block and the
    `2dfld_awipsubset` line, neither of which appears in AAC — and the AWIPS subset is
    not mentioned in the scope statement at all.
  - **The ensemble BUFR lines carry a literal `(??)`.** Both
    `rrfsens.…m00#.class1.bufr` and `rrfsens.…bufrsnd.CC/bufr.*.YYYYMMDDCC` are printed
    with a trailing `(??)` in the notice text, apparently an unresolved internal query
    left in the published document. Neither file exists in the bucket.
  - **The BUFR listing changed without explanation.** AAC listed
    `rrfs.tCCz.bufrsnd.tar.gz`; AAD lists `bufrsnd.tCCz/bufr.*.YYYYMMDDCC`, the exploded
    per-station form. The bucket still carries the tarball and no exploded directory.
  - **The AWIPS subset line has a typo** — `{grib2,rib2.idx}` rather than
    `{grib2,grib2.idx}`. The sidecars are named `.grib2.idx` as everywhere else.
- **The SCN's description of the member domain does not match the output.** AAD says the
  five members run "over the same NA region as the deterministic RRFS", but member files
  are published on the CONUS, Alaska, Hawaii and Puerto Rico grids only. There is no
  13 km North America member output, although the frozen prototype bucket carried one.
- **The `.idx` sidecars make grid verification cheap.** Sampling grid geometry across
  cycles previously meant downloading whole files — 75–90 MB for a fire-weather step,
  ~580 MB for a CONUS `prslev` step. With sidecars, the record-1 byte range comes from
  line 2's offset field, so `curl -r 0-<offset-1>` pulls a few hundred KB and ecCodes
  reads the full grid definition from it. This is how the fire-weather domain alternation
  was established. Note that NOMADS occasionally serves an HTML error page in place of the
  sidecar, so sanitize the offset before using it.

---

## Official documentation
- NWS Service Change Notice 26-48, **AAD update of September 9, 2026** — current version;
  supersedes AAC. Moves implementation to October 14, 2026 and documents the ensemble
  member output and the `2dfld_awipsubset` stream. Unreliable on several points verified
  against the data — see [Notes](#notes):  
  https://www.weather.gov/media/notification/pdf_2026/scn26-048_Updated_RRFS_and_REFS_Implementation_aad.pdf
- NWS Service Change Notice 26-48 (AAC update, August 24, 2026 — superseded). Retained
  because it is the last revision without the member and AWIPS-subset listings, and so is
  the reference point for dating those additions:  
  https://www.weather.gov/media/notification/pdf_2026/scn26-48_updated_RRFS_and_REFS_Implementation_aac.pdf
- NWS Service Change Notice 26-48 (AAB update, July 6, 2026 — superseded):  
  https://www.weather.gov/media/notification/pdf_2026/scn26-048_RRFS_and_REFS_Implementation.aab.pdf
- NWS Service Change Notice 26-47, **AAB update of September 9, 2026** (termination of NAM/SREF/HREF/HiresW/NAM MOS):  
  https://www.weather.gov/media/notification/pdf_2026/SCN26-47_Updated_Retire_NAM_SREF_HREF_HiresW_NAM_MOS.aab.pdf
- NWS Public Information Statement 25-41 (legacy model retirement proposal, June 26, 2025):  
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- RRFS product description at NCO:  
  https://www.nco.ncep.noaa.gov/pmb/products/rrfs
- RRFS output grids:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/rrfs_grids.txt
- NOAA RRFS/REFS [Operational] on AWS (current bucket, CC0-1.0):  
  https://registry.opendata.aws/noaa-rrfs-ops/
- NOAA RRFS [Prototype] on AWS (frozen bucket, historical):  
  https://registry.opendata.aws/noaa-rrfs/
- RRFS/REFS evaluation page (EMC):  
  https://www.emc.ncep.noaa.gov/users/meg/rrfsv1/index.html
- RRFS feedback contact: rrfs.feedback@noaa.gov
