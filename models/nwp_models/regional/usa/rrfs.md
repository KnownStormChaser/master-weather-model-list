# RRFS (Rapid Refresh Forecast System)

## What this model is
The Rapid Refresh Forecast System (RRFS) is NOAA's convection-allowing, hourly-updating regional numerical weather prediction system for North America.

RRFS is built on the Unified Forecast System (UFS) framework. It has been **operational since the 12 UTC cycle on November 3, 2026** (v1.0), when it replaced the legacy NAM, NAM Nest and HiresW (except the Guam domain). It provides both deterministic and ensemble guidance. Five ensemble members are published alongside the deterministic run, and the combined ensemble products are distributed as [REFS](../../../ensemble_models/regional/usa/refs.md) (RRFS Ensemble Forecast System).

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** North America
- **Domain details:**
  Full North America parent domain, with output distributed on seven grids:

  | Output grid | Projection (decoded) | Ni × Nj | Spacing | Anchor / notes |
  |---|---|---|---|---|
  | North America | `rotated_ll`, south pole 35°S / 247°E, no rotation angle | 1127 × 683 | 0.1083° (≈ 12 km) | First point −36.9303° / 299.0° in rotated coordinates; 769,741 points. Filename token is `13km`. |
  | CONUS | `lambert`, LoV 262.5°E, Latin1 = Latin2 = LaD = 38.5°N | 1799 × 1059 | 3000 m | First point 21.138123°N, 237.280472°E |
  | Alaska | `polar_stereographic`, LaD 60°N | 1649 × 1105 | 2976 m | First point 40.53°N, 181.429°E; 1,822,145 points |
  | Hawaii | `mercator`, LaD 20°N | 321 × 225 | 2500 m | First point 18.072699°N, 198.474999°E |
  | Puerto Rico | `mercator` | 544 × 310 | 2500 m | — |
  | AWIPS subset (NA) | `polar_stereographic`, LaD 60°N | **4680 × 2830** | 3000 m | First point 6.5°N, 208.5°E; 13,244,400 points. Filename token is `2dfld_awipsubset.3km … .na` |
  | Fire weather | `lambert`, LoV 265°E, Latin1 = Latin2 = LaD = 25°N | 522 × 390 or 561 × 355 | **1270 m** | Relocatable; see below |

  All grids use `shapeOfTheEarth = 6` (spherical, 6371229 m).

  **The AWIPS subset grid is a separate geometry, not a subset of any other grid.** Despite the `3km` and `na` tokens in its filename, it has nothing in common with the `13km` North America grid. By point count it is the largest grid RRFS publishes, but it carries only the AWIPS surface staples, so the files stay small:
  - 14 records at f000 and 18 at later steps: `REFC`, `VIS`, `GUST`, `MSLET`, `REFD`, 2 m `TMP`/`DPT`, 10 m winds, `CAPE`, `CIN`, `TCDC`, ceiling `HGT` and `HLCY`, plus `APCP` and `ASNOW` past f000.
  - Steps run 3-hourly f000–f060, then 6-hourly to f084: 25 steps, at the 00/06/12/18 UTC cycles only.

  **The fire-weather domain is a relocatable Lambert conformal grid at 1270 m.** It is not the "5 x 5-degree rotated latitude longitude region" SCN 26-48 describes, and not the 1500 m its `1p5km` filename token implies. In practice it has taken only two placements, strictly alternating by cycle hour. This was observed from August 2026 through the November 3, 2026 cutover.

  | Cycles | Nest centre | Ni × Nj | First grid point | Extent |
  |---|---|---|---|---|
  | 00 and 12 UTC | 39.0°N, 112.0°W (central Utah) | 522 × 390 | 36.454°N, 244.665°E | ~663 × 495 km |
  | 06 and 18 UTC | 42.0°N, 122.3°W (southern Oregon) | 561 × 355 | 39.442°N, 234.191°E | ~713 × 451 km |

  Each cycle publishes the nest centre in `firewx.YYYYMMDD/CC/input/rrfs.tCCz.firewxnest_center_latlon`, a single line of decimal latitude and longitude (e.g. `39.0 -112.0`). It is the RRFS counterpart of the NAM's old `namsf.FW.center.txt`. **Do not hard-code the two grids.** Read the location file, or the grid definition, every cycle.

  **The Alaska grid increment is 2976 m, not 3000 m**, despite the `3km` filename token. CONUS is a true 3000 m.

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing), with a five-member ensemble
- **Framework:** Unified Forecast System (UFS)
- **Dynamical core (v1):** FV3 (Finite-Volume Cubed-Sphere), in its limited-area configuration (FV3-LAM)
- **Dynamical formulation:** Non-hydrostatic, finite-volume on the cubed-sphere limited-area grid
- **Convection-allowing:** Yes. Deep convection is explicitly resolved at all RRFS resolutions, and no cumulus parameterization is used.
- **Horizontal resolution:**
  - ≈ 12 km (North America parent output grid, `13km` token)
  - 3 km (CONUS), 2976 m (Alaska)
  - 3 km (AWIPS subset North America grid, polar stereographic, separate geometry)
  - 2.5 km (Hawaii, Puerto Rico)
  - 1270 m (relocatable fire-weather domain, `1p5km` token)
- **Vertical levels:** 65 (NOMADS model description; not verifiable from the output, which has no native-level files)
- **Forecast length:**
  - **84 hours** at the 00, 06, 12 and 18 UTC synoptic cycles
  - **18 hours** at the other 20 hourly cycles
  - **36 hours** for the fire-weather domain
  - **60 hours** for the ensemble members
- **Update frequency / cycles:** Hourly (24× daily); fire weather and ensemble members 4× daily (00/06/12/18 UTC)
- **Temporal output resolution:** Hourly, plus 15-minute subhourly 2D output f001–f018 at every cycle. **The full product set is not published at every cycle**; see [Output organization](#output-organization).

---

## Data assimilation
- **Data assimilation:** Yes
- **Method:** Hourly cycling **GSI-based hybrid 3DEnVar** (Gridpoint Statistical Interpolation). Flow-dependent background-error covariances drawn from an associated convective-scale ensemble are combined with a static background-error covariance term. The configuration follows the convective-scale DA lineage developed for HRRR and RAP, adapted to the FV3-LAM dynamical core.
- **Cadence:** Hourly analysis updates, with each cycle providing the first-guess fields for the next hour.
- **Direct radar reflectivity assimilation:** RRFS assimilates radar reflectivity observations directly, within the GSI EnVar framework. This differs from the indirect cloud-analysis approach used in some earlier NCEP convection-allowing systems. A non-variational complex cloud analysis is also available as an optional post-variational step, as in HRRR/RAP.
- **Assimilated observations:**
  - Conventional surface, aircraft and radiosonde observations
  - Satellite radiances and GPS radio occultation
  - Mesonet observations and radar reflectivity
  - Further convective-scale observations characteristic of the HRRR-lineage DA design
- **Notes:** The detailed operational configuration (static/ensemble weight, localization radii, ensemble source) is documented in the EMC RRFSv1 evaluation materials and the peer-reviewed literature on the GSI-based EnVar system for FV3-LAM.

---

## What it provides
Deterministic short-range forecasts of:
- Near-surface temperature, humidity, wind, and pressure
- Convective and severe storm evolution
- Precipitation amount and type
- Low clouds, ceilings, and visibility
- Aviation-relevant fields
- Fire-weather–relevant fields (dedicated 1270 m domain)
- Subhourly (15-minute) output in early forecast hours

Plus five ensemble members with a reduced parameter set (see [Ensemble member output](#ensemble-member-output)).

---

## Output organization
Output is split across three top-level prefixes:
- deterministic output under `rrfs.YYYYMMDD/CC/`
- fire-weather output under `firewx.YYYYMMDD/CC/`
- ensemble members under `rrfsens.YYYYMMDD/CC/m00#/`

Every GRIB2 file has a matching `.grib2.idx` wgrib2 inventory sidecar. Lead time is a **three-digit** token (`f000`). [REFS](../../../ensemble_models/regional/usa/refs.md) uses two digits, which is a frequent source of 404s when code is shared between the two.

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
| `rrfs.tCCz.2dfld_awipsubset.3km.fFFF.na.grib2` | AWIPS 2D subset; synoptic cycles only, 25 steps |
| `rrfs.tCCz.prslev.1p5km.fFFF.firewx_lcc.grib2` | Pressure-level, fire weather (under `firewx.YYYYMMDD/CC/`) |
| `rrfs.tCCz.2dfld.1p5km.fFFF.firewx_lcc.grib2` | 2D fields, fire weather (under `firewx.YYYYMMDD/CC/`) |
| `rrfs.tCCz.firewxnest_center_latlon` | Fire-weather nest centre (under `firewx.YYYYMMDD/CC/input/`) |
| `rrfs.tCCz.m00#.{prslev\|2dfld}nomads.{3km\|2p5km}.fFFF.${dom}.grib2` | Ensemble members (under `rrfsens.YYYYMMDD/CC/m00#/`) |
| `rrfs.tCCz.bufrsnd.tar.gz` | BUFR sounding bundle, synoptic cycles only |
| `rrfs.tCCz.class1.bufr` | BUFR class-1 soundings, synoptic cycles only |
| `bufrsnd.tCCz/bufr.NNNNNN.YYYYMMDDCC` | Per-station BUFR soundings (1,875 stations), synoptic cycles only — **NOMADS only** |

### The 24 hourly cycles are not equivalent — three distinct tiers
This is the single most consequential thing to know before scripting against RRFS. **Sixteen of the twenty-four cycles publish nothing but the 15-minute subhourly files.**

| Cycles | Hourly `prslev` + `2dfld` | 13 km NA | AWIPS subset | Members | 15-min `subh` | Deterministic GRIB2 files/cycle |
|---|---|---|---|---|---|---|
| **00, 06, 12, 18 UTC** | f000–f084 on all four subset grids | f000–f084 hourly | 25 steps | 5 × 488 files | f001–f018 | **947** |
| **03, 09, 15, 21 UTC** | f000–f018 on all four subset grids | No | No | No | f001–f018 | 224 |
| **All other 16 cycles** | **None** | No | No | No | f001–f018 | 72 |

Re-verified in production on 2026-11-03. A 3-hourly forecast pulled from the "hourly" model will silently fall back to subhourly 2D fields for two cycles out of every three.

> ⚠️ **Both official descriptions say otherwise.** The NOMADS model description says hourly
> `prslev` and `2dfld` output "is generated for all cycles", and SCN 26-48 gives only the
> 84 h / 18 h split. **The directory listing is authoritative; the descriptions are not.**

### Records per file
At a mid-range step (f012; f001 for `subh`). The counts are identical on both channels and unchanged across the November 3 cutover:

| Family | Records |
|---|---|
| `prslev` (every grid, including 13 km NA and fire weather) | 675 |
| `2dfld` (CONUS, AK, HI, PR, 13 km NA) | 314 |
| `2dfld` (fire weather) | 299 |
| `2dfld … subh` | 149 |
| `2dfld_awipsubset` | 18 |

**Precipitation type changed method on 2026-10-15 at 12 UTC**, under SCN 26-48 AAF. The `CSNOW`/`CICEP`/`CFRZR`/`CRAIN` fields are now diagnosed thermodynamically rather than taken from the microphysics. They keep their names, positions and encoding, so **nothing in the file distinguishes the two methods**. Any climatology or verification of precipitation type must split at that cycle by date. See [Recent version history](#recent-version-history).

### Encoding
- GRIB2 edition 2, centre `kwbc`, subCentre 0, `tablesVersion` 2, `localTablesVersion` 1
- `generatingProcessIdentifier` **134** (deterministic), `productionStatusOfProcessedData` 0
- `shapeOfTheEarth` 6, `grid_complex_spatial_differencing` packing throughout

### Publication latency (NOMADS, measured 2026-11-03)
- **12 UTC synoptic cycle:** first file 13:48 UTC (T+1h48m), last file 15:32 UTC (T+3h32m).
- **15 UTC cycle:** first file 16:13 UTC (T+1h13m), last 16:48 UTC (T+1h48m).
- **16 UTC cycle** (subhourly only): first file 17:19 UTC (T+1h19m), last 17:46 UTC (T+1h46m).

Subhourly files trail the hourly ones by about eight minutes. These figures match the parallel feed in August, so implementation did not change latency on NOMADS.

A full description of RRFS products including variables and encoding is at
https://www.nco.ncep.noaa.gov/pmb/products/rrfs.

---

## Ensemble member output
The RRFS run produces **five ensemble members** at the 00, 06, 12 and 18 UTC cycles, running to 60 hours. They use different initial conditions, lateral boundary conditions and model physics from the deterministic forecast. The members feed [REFS](../../../ensemble_models/regional/usa/refs.md), along with 6 h time-lagged copies of the deterministic and ensemble RRFS and, for CONUS and Alaska, HRRR. The members themselves are published separately:

| Filename pattern | Content |
|---|---|
| `rrfs.tCCz.m00#.prslevnomads.3km.fFFF.{conus\|ak}.grib2` | Pressure-level member output, CONUS / Alaska |
| `rrfs.tCCz.m00#.prslevnomads.2p5km.fFFF.{hi\|pr}.grib2` | Pressure-level member output, Hawaii / Puerto Rico |
| `rrfs.tCCz.m00#.2dfldnomads.3km.fFFF.{conus\|ak}.grib2` | 2D member fields, CONUS / Alaska |
| `rrfs.tCCz.m00#.2dfldnomads.2p5km.fFFF.{hi\|pr}.grib2` | 2D member fields, Hawaii / Puerto Rico |

- **Five members**, `m001`–`m005`, at the **00/06/12/18 UTC cycles only**.
- **f000–f060 hourly**, 61 steps: the REFS horizon, not the deterministic 84 h.
- **Four domains, no North America grid.** CONUS and Alaska are at 3 km and Hawaii and Puerto Rico at 2.5 km, on grids identical to the deterministic subset grids. SCN 26-48 describes the members as running "over the same NA region as the deterministic RRFS", **but no 13 km North America member output is published**.
- **488 GRIB2 files per member per cycle**, each with an `.idx` sidecar.
- **No member BUFR.**

### The `nomads` suffix means a reduced parameter set
The member files carry a **much narrower parameter set** than their deterministic counterparts:

| File family | Records | Scope (decoded) |
|---|---|---|
| `prslevnomads` | **112** | 8 parameters — `HGT`, `TMP`, `RH`, `DPT`, `SPFH`, `UGRD`, `VGRD`, `ABSV` — on 14 pressure levels (1000, 975, 950, 925, 900, 850, 800, 750, 700, 600, 500, 400, 300, 250 hPa) |
| `2dfldnomads` | **56** | 41 distinct parameters, surface and near-surface |
| *deterministic* `prslev` | *675* | *for comparison* |
| *deterministic* `2dfld` | *314* | *for comparison* |

The record counts are the same at every lead time and on every domain. The pressure-level members carry no vertical velocity, no cloud or hydrometeor fields, and no geopotential above 250 hPa.

### Member encoding differs from the deterministic files
- **`generatingProcessIdentifier` is 136**, the same as [REFS](../../../ensemble_models/regional/usa/refs.md) `ensprod` output rather than the deterministic value of **134**. Code that branches on this key to tell RRFS from REFS will misclassify member files as REFS. Use the path prefix, or `productDefinitionTemplateNumber`, instead.
- **PDT 1** (individual ensemble forecast), with `typeOfProcessedData = pf` and subCentre 2.
- **`perturbationNumber` carries the member number** (1–5), matching the `m00#` filename token.
- **`numberOfForecastsInEnsemble` reads 5**, not the 14 or 12 that REFS products declare. The member files describe the RRFS ensemble; the REFS products describe the combined time-lagged and HRRR-augmented membership.

### Volume
Member output roughly doubles the size of an RRFS synoptic cycle. A 12 UTC member cycle is about **140 GB across 4,880 objects** (≈ 28 GB per member), against about 215 GB for the deterministic cycle. A CONUS `prslevnomads` step is ~177 MB and a `2dfldnomads` step ~54 MB, against ~599 MB for a deterministic CONUS `prslev` step. Sidecar-driven byte-range subsetting is the only practical way to work with the full member set.

---

## Relationship to other models
RRFS v1.0 replaced the following legacy NCEP regional systems on November 3, 2026. All were retired under SCN 26-47 and no longer have catalogue entries:
- **NAM**: the 12 km parent domain and all 3 km nests, including fire weather
- **NAM Nest**
- **HiresW**: CONUS, Alaska, Hawaii and Puerto Rico. The Guam domain continues as [HiresW Guam](./hiresw-guam.md), because RRFS does not reach Guam.
- **HREF** and **SREF**, replaced by [REFS](../../../ensemble_models/regional/usa/refs.md)
- **NAM MOS**, retired alongside NAM

NARRE, sometimes listed in the same group (PNS 25-41), was retired separately on or about January 7, 2026 under [SCN 25-87](https://www.weather.gov/media/notification/pdf_2025/scn25-87_EMC_services_termination.pdf). The retirement record is in [STATUS.md](../../../../STATUS.md#recent-retirements).

[HRRR](./hrrr.md) and [RAP](./rap.md) were not retired with RRFSv1. They are expected to be retired later with RRFSv2, which is planned to move to the MPAS dynamical core. HRRR also contributes two members (current and 6 h old) to the CONUS and Alaska REFS domains.

**Downstream systems moved to RRFS on the same cycle:**
- **[National Blend of Models](./nbm.md):** [SCN 26-89](https://www.weather.gov/media/notification/pdf_2026/scn26-89_RRFS_Downstream.pdf) replaced the NBM's NAM and HiresW inputs with RRFS and the RRFS-based REFS configurations, in both the Core and QMD systems.
- **HYSPLIT 9.1:** [SCN 26-78](https://www.weather.gov/media/notification/pdf_2026/scn26_78_Updated_HYSPLIT_%20v9.1_aaa.pdf) couples RRFS into HYSPLIT, replacing the NAM and HREF couplings. RRFS-derived HYSPLIT input files appear on NOMADS from the 12 UTC cycle of November 2. See [HYSPLIT-Dust](../../../air_quality_models/regional/usa/hysplit-dust.md).

**Cycle structure compared with the systems it replaced:** RRFS keeps the NAM/HiresW 84-hour runs at the synoptic cycles, and adds HRRR-style hourly cycling to 18 h in between.

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. Government work); the NODD bucket is registered CC0-1.0
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (with `.idx` sidecars); BUFR soundings
- **Official download locations:**
  - **NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/rrfs/prod/
    - Versioned mirror, identical content: https://nomads.ncep.noaa.gov/pub/data/nccf/com/rrfs/v1.0/
  - **AWS S3 (NODD):** `s3://noaa-rrfs-ops-pds/` — https://noaa-rrfs-ops-pds.s3.amazonaws.com/index.html
    - Top-level prefixes `rrfs.YYYYMMDD/`, `firewx.YYYYMMDD/`, `rrfsens.YYYYMMDD/` and `refs.YYYYMMDD/`
    - Anonymous access, no requester-pays, `us-east-1`
    - Registry entry: **NOAA Rapid Refresh Forecast System (RRFS) and RRFS Ensemble Forecast System (REFS) [Operational]**, https://registry.opendata.aws/noaa-rrfs-ops/
    - New-object notifications: `arn:aws:sns:us-east-1:123901341784:NewRRFSObject` (Lambda and SQS only)

The two channels carry byte-identical GRIB2 files. They differ in the following ways:

| | NOMADS `rrfs/prod/` | `s3://noaa-rrfs-ops-pds` |
|---|---|---|
| Retention | 2 days | Every date since 2026-08-13 |
| Per-station BUFR (`bufrsnd.tCCz/`) | Yes, 1,875 stations at synoptic cycles | **No** — tarball and `class1.bufr` only |
| Sidecar parameter names | 21 NCEP local-table records in `2dfld` named (`QPFARI`, `QPFFFG`, `SBTA18x`, `FRADPOW`, `WFIREPOT`, …) | Same records printed as `var discipline=… local_table=1 …` |
| Directory listing | Frequently truncated; needs retries and HTTP/1.1 (see below) | Reliable S3 `list-type=2` |
| Latency | Leads S3 by about a minute in normal running | — |
| Licence statement | US Government work, no per-file statement | CC0-1.0 in the AWS Open Data Registry |

**Choose on retention and listing reliability, not content.** Use NOMADS for the freshest file. Use S3 for anything older than two days, and for scripted enumeration.

> **The sidecars differ in naming only.** Record numbers and byte offsets are identical, so
> byte-range downloads work the same from either channel. Code that selects records by
> *name* will not: a pattern for `QPFARI` matches on NOMADS and finds nothing on S3. Filter
> on the offsets or decode the GRIB2 instead. `prslev` and REFS `ensprod` sidecars are
> byte-identical between the channels.

> ⚠️ **NOMADS directory listings fail over HTTP/2.** The server sends a `Content-Length`
> header padded with trailing spaces, which curl rejects with error 92 ("Invalid HTTP header
> field"). The request returns HTTP 200 but an empty body, so a script that does not check
> curl's exit code sees an empty directory. Force HTTP/1.1 (`curl --http1.1`). File
> downloads are not affected, only the directory index pages.

> ⚠️ **The S3 bucket can lag or pause.** Ingestion stopped from about 20:20 UTC on
> 2026-11-02 to 15:28 UTC on 2026-11-03 across the implementation and was then backfilled
> completely. Backfilled objects carry the backfill time as `LastModified`, so that field
> cannot be used to date when a cycle ran. For time-critical use, poll NOMADS.

Indexing matters here more than for most models. A CONUS `prslev` step is ~580 MB, and a full 84-hour synoptic series is roughly 48 GB, so without a sidecar there is no practical way to pull a handful of fields. The sidecars also make grid checks cheap. Take the record-1 byte range from line 2's offset field and fetch it with `curl -r 0-<offset-1>`: a few hundred KB is enough for ecCodes to read the full grid definition. NOMADS occasionally serves an HTML error page in place of a sidecar, so sanitize the offset before using it.

> **`s3://noaa-rrfs-pds`, the old prototype bucket, is frozen and should not be used for
> current data.** It stopped on 2026-08-12 and has a different internal layout
> (`rrfs_public/`, `rrfs_a/`). It remains the only source of the `retro_output_final/`
> retrospective parallel output.

### NOAAPORT (SBN) subset
Only a subset of RRFS output goes over the Satellite Broadcast Network, because of bandwidth limits. For RRFS that subset is the North America grid alone (SCN 26-48, since the AAE update):

| Region | Grid spacing | WMO headers |
|---|---|---|
| North America (RRFS) | 3 km | `Y*A*## KWDA` |
| North America (RRFS) | 13 km | `Y*H*## KWDA` |

The REFS headers are listed in the [REFS entry](../../../ensemble_models/regional/usa/refs.md#data-availability), and the full header list is at https://www.nco.ncep.noaa.gov/pmb/products/noaaport/.
- **No public NOMADS copy of the SBN products exists since implementation.** The pre-operational `para/noaaport/rrfs/` directory stopped at the 06 UTC cycle of November 3, 2026, and has no production equivalent.
- **Which 3 km North America geometry the SBN product uses is not established (TBD).** It may be the 4680 × 2830 AWIPS subset grid.
- NWS PNS 26-63 proposes retiring SBN/NOAAPort itself as early as August 31, 2027.

---

## Status
- **Operational since 2026-11-03, 12 UTC** (RRFS v1.0), under NWS SCN 26-48 (AAF update of October 15, 2026). Implementation was confirmed in the live data. The file sets, record inventories and GRIB2 encoding are unchanged from the parallel feed, while the NOMADS path moved from `para/` to `prod/`; see [Recent version history](#recent-version-history).
- **RRFSv2**, based on the MPAS dynamical core, is under development and will drive the next phase of legacy retirements (HRRR, RAP). No implementation date has been announced.

---

## Notes
- **SCN 26-48 still disagrees with the data on three points** as of the AAF update:
  - It describes the fire-weather output as a 5° × 5° rotated lat-lon region; the output is Lambert conformal at 1270 m, as shown under [What area it covers](#what-area-it-covers).
  - It gives the 84 h / 18 h cycle split without noting that sixteen cycles publish no `prslev` or `2dfld` at all; see [Output organization](#output-organization).
  - It says the members run over the North America region, but they are published on the four subset grids only.
- **Not all legacy NAM and HiresW products are reproduced in RRFS.** SCN 26-47 says some HiresW products would be generated by SmartInit processing RRFS output. All public SmartInit products were terminated on July 21, 2026 under [SCN 26-59](https://www.weather.gov/media/notification/pdf_2026/scn26-59_SmartInit_Termination.pdf), which directs users to the [NBM](./nbm.md) instead. EMC's lists of products not carried over are linked under [Official documentation](#official-documentation).
- **No native-level output is distributed.** RRFS ships `prslev` and `2dfld` products only, so the 65-level figure rests on the NOMADS model description alone.
- **Verification graphics.** EMC's verification pages moved from NAM/HiresW to RRFS and REFS on implementation (SCN 26-100, November 3, 2026). These are graphics, outside catalogue scope.

---

## Recent version history
- **v1.0 — operational 2026-11-03, 12 UTC.** Implementation of the configuration run in the parallel feed, with no change to the output. On NOMADS, `para/` was replaced by `prod/` and the `v1.0/` mirror about ten hours before the cutover (at 01:49 UTC). The S3 bucket paused and backfilled. The NOAAPORT parallel directory stopped. Same cycle: NAM, NAM Nest, HiresW (except Guam), HREF and SREF retired, and the NBM moved to RRFS/REFS inputs. The implementation date had moved four times: from early 2026 (PNS 25-41) to August 31, October 6, October 14 and finally November 3. No reason was given for any of the moves.
- **2026-10-15, 12 UTC (pre-operational) — precipitation-type change (SCN 26-48 AAF).** Precipitation type is now diagnosed thermodynamically, replacing the microphysics-based product, in response to over-forecast freezing rain and under-forecast sleet. Freezing-rain (`FRZR`) and sleet (`FROZR`) accumulations were removed from:
  - `2dfld` on all grids: 318 → 314 records
  - fire-weather `2dfld`: 303 → 299
  - `subh`: 157 → 149
  - member `2dfldnomads`: 58 → 56 (`FRZR` only; members never carried `FROZR`)

  The fire-weather nest location file was added at the same cycle.
- **2026-09-09 (pre-operational) — ensemble members published.** The first public distribution of the five members (SCN 26-48 AAD), on four grids and with the reduced parameter set described above.
- **2026-08-27 (pre-operational) — AWIPS subset stream added.** `2dfld_awipsubset.3km.na` at synoptic cycles; documented from SCN 26-48 AAD.
- **2026-08-12 to 08-23 (pre-operational) — parallel feed established.**
  - 2026-08-12, 12 UTC: the real-time feed began on NOMADS `para/`. The prototype bucket `s3://noaa-rrfs-pds` froze the same day.
  - 2026-08-13: the replacement bucket `s3://noaa-rrfs-ops-pds` came up.
  - 2026-08-15 to 21: `.idx` sidecars were added on NOMADS.
  - 2026-08-23, 18 UTC: BUFR soundings were added on NOMADS.

  The model output itself was unchanged across these moves. The North America grid went from 3-hourly on the prototype to hourly in the parallel feed, and native-level output was dropped. Five-grid member output and the prototype's `retro_output_final/` were not carried over; members returned in reduced form on 2026-09-09.

---

## Official documentation
- NWS Service Change Notice 26-48, AAF update of October 15, 2026 (RRFS/REFS implementation; current revision):
  https://www.weather.gov/media/notification/pdf_2026/scn26-048_Updated_RRFS_and_REFS_Implementation.aaf.pdf
- NWS Service Change Notice 26-47, AAC update of October 2, 2026 (termination of NAM/SREF/HREF/HiresW/NAM MOS):
  https://www.weather.gov/media/notification/pdf_2026/scn26-47_Updated_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS_aac.pdf
- NWS Service Change Notice 26-89 (October 2, 2026) — downstream NBM, ObsProc and MAG changes:
  https://www.weather.gov/media/notification/pdf_2026/scn26-89_RRFS_Downstream.pdf
- NWS Service Change Notice 26-78, AAA update of October 2, 2026 (HYSPLIT 9.1, coupling RRFS):
  https://www.weather.gov/media/notification/pdf_2026/scn26_78_Updated_HYSPLIT_%20v9.1_aaa.pdf
- NWS Service Change Notice 26-59 (June 17, 2026) — termination of all SmartInit products:
  https://www.weather.gov/media/notification/pdf_2026/scn26-59_SmartInit_Termination.pdf
- NWS Public Information Statement 25-41 (legacy model retirement proposal, June 26, 2025):
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- RRFS product description at NCO:
  https://www.nco.ncep.noaa.gov/pmb/products/rrfs
- RRFS output grids:
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/rrfs_grids.txt
- Legacy products not generated by RRFS (EMC): https://www.emc.ncep.noaa.gov/rrfs_info/hiresw_retirements.txt (HiresW; NAM and HREF equivalents in the same directory)
- NOAA RRFS/REFS [Operational] on AWS (CC0-1.0):
  https://registry.opendata.aws/noaa-rrfs-ops/
- NOAA RRFS [Prototype] on AWS (frozen):
  https://registry.opendata.aws/noaa-rrfs/
- RRFS/REFS evaluation page (EMC):
  https://www.emc.ncep.noaa.gov/users/meg/rrfsv1/index.html
- RRFS feedback contact: rrfs.feedback@noaa.gov
