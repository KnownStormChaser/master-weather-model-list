# REFS (RRFS Ensemble Forecast System)

## What this model is
The RRFS Ensemble Forecast System (REFS) is the **ensemble component** of NOAA's Rapid Refresh Forecast System (RRFS).

REFS is a regional, convection-allowing ensemble that provides probabilistic short-range forecast guidance for high-impact weather across North America. It is built on the UFS framework. It has been **operational since the 12 UTC cycle on November 3, 2026** (v1.0), alongside the deterministic [RRFS](../../../nwp_models/regional/usa/rrfs.md), when it replaced the legacy HREF and SREF ensembles.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States

---

## What area it covers
- **Coverage:** North America
- **Domain details:**
  REFS output is provided on the same subset grids as the deterministic RRFS:
  - CONUS (3 km)
  - Alaska (3 km)
  - Hawaii (2.5 km)
  - Puerto Rico (2.5 km)

---

## Basic details
- **Model type:** Regional ensemble NWP (convection-allowing)
- **Model system / core:** RRFS (UFS / FV3-LAM in v1)
- **Dynamical formulation:** Non-hydrostatic, finite-volume on the cubed-sphere limited-area grid (inherited from RRFS / FV3-LAM)
- **Convection-allowing:** Yes. Deep convection is explicitly resolved on the 3 km CONUS/AK and 2.5 km HI/PR domains, and no cumulus parameterization is used in the RRFS members. The HRRR members (CONUS and Alaska domains) are also convection-allowing, at 3 km on WRF-ARW.
- **Horizontal resolution:**
  - 3 km (CONUS, Alaska)
  - 2.5 km (Hawaii, Puerto Rico)
- **Forecast length:** 60 hours
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly, f01–f60

---

## Data assimilation
- **Data assimilation:** REFS does not perform its own data assimilation.
- **Method:** REFS inherits the analyses of its contributing systems:
  - mainly the hourly cycling **GSI-based hybrid 3DEnVar** analysis from the deterministic and ensemble [RRFS](../../../nwp_models/regional/usa/rrfs.md), for both the current and the 6 h time-lagged cycles;
  - the HRRR hourly cycling hybrid ensemble-variational analysis, for the two HRRR members on the CONUS and Alaska domains.

  See the RRFS and [HRRR](../../../nwp_models/regional/usa/hrrr.md) entries for the analysis system details.

---

## Ensemble methodology
REFS is an **ensemble product generation system**. It combines the deterministic and ensemble components of RRFS, time-lagged across cycles, with HRRR as an extra contributor on two of the four domains. The two most recent runs of each contributing system are used:

| Domain | Members | Composition |
|---|---|---|
| CONUS | **14** | RRFS deterministic + 5 RRFS ensemble members, current and 6 h old (12), plus HRRR current and 6 h old (2) |
| Alaska | **14** | As CONUS |
| Hawaii | **12** | RRFS deterministic + 5 RRFS ensemble members, current and 6 h old; no HRRR |
| Puerto Rico | **12** | As Hawaii |

The counts are confirmed in the data: the ensemble-size keys in every product read 14 or 12 (see [Data availability](#data-availability) for where to find them).

The five RRFS ensemble members run at the 00/06/12/18 UTC cycles. They use different initial conditions, lateral boundary conditions and model physics from the deterministic forecast, and they are published individually; see [Individual members](#individual-members).

HRRR is not a UFS system and was not retired with RRFSv1, so REFS relies on HRRR as an explicit input during the RRFSv1 era. When HRRR is retired alongside RAP under the future RRFSv2 transition, the CONUS and Alaska membership will change accordingly. The member composition and perturbation strategy are expected to evolve with RRFSv2 (MPAS-based).

---

## What it provides
Probabilistic regional forecasts of:
- Precipitation amount, type, and thresholds
- Severe convection indicators
- Near-surface temperature, wind, and humidity
- Neighborhood and probability-based guidance for high-impact weather

REFS provides the primary short-range, convection-allowing probabilistic guidance across CONUS, Alaska, Hawaii, and Puerto Rico.

### Ensemble product types
REFS ensemble products are published under `refs.YYYYMMDD/CC/ensprod/` with the file pattern `refs.tCCz.${type}.fFF.${dom}.grib2`, where `dom` is one of `conus`, `ak`, `hi`, `pr`. Each file has a matching `.grib2.idx` sidecar. Lead time is a **two-digit** token (`f09`), while the deterministic [RRFS](../../../nwp_models/regional/usa/rrfs.md) uses three digits (`f009`). Sharing a lead-time formatter between the two systems is the most common way to generate 404s against this feed.

Record counts are from the CONUS f12 files, re-checked on the first operational cycle (2026-11-03, 12 UTC) and unchanged from the parallel feed:

| Type | Description | Records (CONUS) | Parameter scope (decoded) |
|---|---|---|---|
| `mean` | Arithmetic mean of all members | 76 | Broad — 34 distinct parameters across mass, wind, moisture, stability, cloud and precipitation-type fields |
| `sprd` | Ensemble spread — how far apart the members are at a point; smaller means better agreement | 80 | Broad — tracking `mean`, but with no composite reflectivity (`REFC`) spread; 1 km `REFD` spread is kept |
| `pmmn` | Probability-matched mean, computed over the full domain at once | 8 | Narrow — total precipitation (2), orography, reflectivity, and 4 radar/local-table fields. `REFC` and 1 km `REFD` are floored at 0 |
| `lpmm` | Localized probability-matched mean, computed over small regions then assembled | 3 | **Total precipitation only** |
| `avrg` | Average of the `mean` and `pmmn` fields | 2 | **Total precipitation only** |
| `prob` | Probabilistic output — fraction of members meeting a threshold; a mix of point and neighbourhood-maximum probabilities | 169 | Broad — precipitation, snowfall, wind, gusts, icing, visibility, flight category and lightning. No freezing-rain accumulation probabilities |
| `eas` | Ensemble Agreement Scale probability — a smoothed fractional probability whose neighbourhood radius varies from 10 to 100 km, narrowing where members agree | 29 | **Precipitation (22) and snowfall (7) only** |
| `ffri` | Flash Flood and Average Recurrence Interval exceedance probabilities — **CONUS only** | 10 | Total precipitation (7) and flash-flood guidance exceedance (3) |

- **`pmmn` is far narrower than `mean`**, although both are described as means. This is easy to miss when choosing a product.
- **The `pmmn` reflectivity floor is a hard 0.** Code that treats values ≤ 0 as distinct from "no data" will not find any negative echo.
- **Product Definition Templates differ by product.** It is worth knowing before writing a reader:
  - `mean` and `sprd` use PDT 2 and 12 (derived forecast, instantaneous and interval).
  - `prob` uses 5 and 9 (probability, instantaneous and interval).
  - `eas` and `ffri` use 9 exclusively.
- **Every product runs f01–f60 hourly** on every domain it covers.

All eight are written on the same grids as the deterministic RRFS subset domains; CONUS is `lambert` 1799 × 1059 at 3000 m, matching RRFS exactly. Encoding:
- GRIB2 edition 2, centre `kwbc`, subCentre 2, `localTablesVersion` 1
- **`generatingProcessIdentifier` 136** (RRFS deterministic uses 134)
- `productionStatusOfProcessedData` 0
- `grid_complex_spatial_differencing` packing throughout

#### Parameters that stock ecCodes cannot name
Decoding with ecCodes 2.48.0 leaves a number of records with `shortName = unknown`. There are two distinct causes, and the first is the surprising one:

| discipline/category/number | Meaning | Where it appears |
|---|---|---|
| **0/1/29** | **Total snowfall — a standard WMO number** | `mean` (4 records, 1/3/6/12 h), `sprd`, `prob` (14), `eas` (7) |
| 0/2/220, 0/6/202, 0/7/199, 0/16/198, 0/19/235 | NCEP local-table numbers (≥192) | `mean`, `pmmn`, `prob` |
| 0/16/3, 0/16/5 | Forecast-radar-imagery category | `pmmn`, `prob` |

The local-table numbers are expected: resolving them requires NCEP's tables. **Total snowfall is not.** 0/1/29 is a standard WMO entry that ecCodes 2.48.0 still fails to name, and it carries every snow product in `eas` and a substantial share of `prob`. Anyone filtering REFS by `shortName` will silently drop all snowfall guidance; filter on the discipline/category/number triplet instead.

Full descriptions of variables and encoding are at
https://www.nco.ncep.noaa.gov/pmb/products/refs.

---

## Relationship to other models
REFS v1.0 replaced the following legacy NCEP ensemble systems on November 3, 2026. Both were retired under SCN 26-47 and no longer have catalogue entries:
- **HREF** (High-Resolution Ensemble Forecast)
- **SREF** (Short-Range Ensemble Forecast)

NARRE (North American Rapid Refresh Ensemble), grouped with them in PNS 25-41, had already been decommissioned on or about January 7, 2026 under [SCN 25-87](https://www.weather.gov/media/notification/pdf_2025/scn25-87_EMC_services_termination.pdf). REFS takes over a role that had been vacant since then. The retirement record is in [STATUS.md](../../../../STATUS.md#recent-retirements).

Compared with the systems it replaced:
- REFS runs to **60 hours** (HREF ran to 48 hours).
- REFS runs **00, 06, 12 and 18 UTC** cycles for every region. HREF ran only twice daily for AK, HI and PR: 06Z/18Z for AK and PR, and 00Z/12Z for HI.
- REFS is built around the deterministic and ensemble components of a single UFS-based system, with HRRR adding members on CONUS/AK. HREF was a "post-processing ensemble of opportunity", combining whichever convection-allowing models were operationally available. Its product types (mean, spread, PMM, LPMM, probabilities, EAS) carry over.

**Downstream use:**
- **[National Blend of Models](../../../nwp_models/regional/usa/nbm.md):** From the same cycle, the NBM uses "all RRFS-based ensemble configurations" of REFS, alongside RRFS, in both the Core and QMD systems ([SCN 26-89](https://www.weather.gov/media/notification/pdf_2026/scn26-89_RRFS_Downstream.pdf)).
- **MAG website:** It moved from HREF to REFS on the same cycle.
- **HYSPLIT 9.1:** It couples the deterministic RRFS, not REFS ([SCN 26-78](https://www.weather.gov/media/notification/pdf_2026/scn26_78_Updated_HYSPLIT_%20v9.1_aaa.pdf)).

---

## Data availability
- **Is the data free?** Yes
- **License:** Public domain (U.S. Government work); the NODD bucket is registered CC0-1.0
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (with `.idx` sidecars)
- **Official download locations:**
  - **NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/refs/prod/
    - Versioned mirror, identical content: https://nomads.ncep.noaa.gov/pub/data/nccf/com/refs/v1.0/
  - **AWS S3 (NODD):** `s3://noaa-rrfs-ops-pds/refs.YYYYMMDD/CC/ensprod/` — https://noaa-rrfs-ops-pds.s3.amazonaws.com/index.html

REFS shares the deterministic [RRFS](../../../nwp_models/regional/usa/rrfs.md#data-availability) bucket rather than having its own; there is no `noaa-refs-ops-pds`. Both channels use the same `ensprod/` subdirectory.

- **File set:** A cycle is **1,740 GRIB2 files and 1,740 `.idx` sidecars**: 7 product types × 4 domains × 60 lead times, plus `ffri` on CONUS. The GRIB2 files and sidecars are **byte-identical** between the channels (re-checked 2026-11-03).
- **Retention:** NOMADS keeps two days. The bucket has every date since 2026-08-13, so **use S3 for anything older than two days**.
- **Size and indexing:** A CONUS `prob` step is ~48 MB, so a full f01–f60 series is about 3 GB per cycle per domain for that product type alone. The sidecars make single-threshold pulls practical.
- **Latency:** On NOMADS on 2026-11-03, the 12 UTC cycle ran from 14:06 UTC (f01) to 15:59 UTC (f60).
- **Directory listings:** The NOMADS HTTP/2 listing problem described in the RRFS entry applies here too; use `curl --http1.1`.

> **Ensemble size is encoded under two different keys, depending on product.**
> - On `mean` and `sprd` it is `numberOfForecastsInEnsemble`, which reads 14 on CONUS and
>   Alaska and 12 on Hawaii and Puerto Rico across all records.
> - On `prob`, `eas` and `ffri` that key is **undefined**. The same value appears instead as
>   `totalNumberOfForecastProbabilities` in PDT 5 and 9, which wgrib2 renders as
>   `prob fcst 0/14`.
>
> Code that sizes member arrays from `numberOfForecastsInEnsemble` alone will fail on three
> of the eight products, so read both keys. Because the value appears in the wgrib2
> inventory, **ensemble size can be read straight from the few-kilobyte `.idx` sidecar**.
> `totalNumberOfForecastProbabilities` would ordinarily denote a count of probability
> thresholds, so this is an NCEP local convention: do not carry the assumption to other
> centres' output.

### Individual members
The five RRFS ensemble members that REFS is built from are published separately under the top-level **`rrfsens.YYYYMMDD/CC/m00#/`** prefix. It is a sibling of `rrfs.YYYYMMDD/` and `refs.YYYYMMDD/`, under `/rrfs/prod/` on NOMADS and at the bucket root on S3. Files follow `rrfs.tCCz.m00#.{prslev|2dfld}nomads.{3km|2p5km}.fFFF.${dom}.grib2`. Note the **three-digit** lead-time token: code that handles REFS and the members needs two formatters, not one.

- **What they cover:** five members, 00/06/12/18 UTC, f000–f060 hourly, on the four REFS domains, with no North America grid.
- **What they leave out:**
  - The parameter set is much narrower than the deterministic files: 112 records in `prslevnomads` and 56 in `2dfldnomads`.
  - The HRRR and time-lagged members that REFS adds are not included.
  - There is no member BUFR.
- **What this means for custom post-processing:** Bespoke percentiles, neighbourhood probabilities at non-standard thresholds and member clustering are possible, but only within that reduced parameter set. Anything else has to come from the eight combined `ensprod` products.
- **How to tell them apart from REFS products:** The member files declare `numberOfForecastsInEnsemble = 5` and **`generatingProcessIdentifier` 136, the same as `ensprod`**. Distinguish them on the path prefix or on PDT: members are PDT 1 with `typeOfProcessedData = pf` and a populated `perturbationNumber`.

Full details are in the [RRFS entry](../../../nwp_models/regional/usa/rrfs.md#ensemble-member-output).

### NOAAPORT (SBN) subset
SCN 26-48 (since the AAE update) gives the WMO headers for the REFS products sent over the Satellite Broadcast Network:

| Region | Grid spacing | WMO headers |
|---|---|---|
| Alaska | 3 km | `A*[A-Q]*## KWDB` |
| CONUS | 3 km | `Y*[A-Q]*## KWDB` |
| Hawaii | 2.5 km | `H*[A-Q]*## KWDB` |
| Puerto Rico | 2.5 km | `P*[A-Q]*## KWDB` |

- **Cadence:** Per SCN 26-47 the SBN products are **3-hourly from f03 to f48, then 6-hourly to f60**, for all domains and cycles. That is coarser than the hourly f01–f60 of the full `ensprod` feed, and differs from HREF's SBN products, which were hourly to f30.
- **Products dropped from HREF's SBN set:** The REFS WMO product list differs from HREF's; the removals are listed at https://www.emc.ncep.noaa.gov/rrfs_info/href_sbn_removals.txt.
- **No public NOMADS copy since implementation:** The pre-operational `para/noaaport/refs/` directory stopped at the 06 UTC cycle of November 3, 2026.
- **Future of SBN:** NWS PNS 26-63 proposes retiring SBN/NOAAPort itself as early as August 31, 2027.

---

## Status
- **Operational since 2026-11-03, 12 UTC** (REFS v1.0), under NWS SCN 26-48 (AAF update of October 15, 2026). The first operational cycles carry the same 1,740-file set, record inventories and encoding as the parallel feed. The NOMADS path moved from `para/` to `prod/`.
- Evaluations at the 2025 NOAA Hazardous Weather Testbed Spring Forecasting Experiment found REFS competitive with HREF for Day 1 and Day 2, and slightly better on some objective metrics, including deep convection (>40 dBZ). This supported the HREF → REFS replacement.

---

## Notes
- As with all ensemble systems, REFS output should be interpreted probabilistically rather than as a single deterministic forecast.
- **REFS products computed from member precipitation type changed method on 2026-10-15 at 12 UTC**, with no change in file layout. This includes the precipitation-type fields in `mean`. Split any climatology at that cycle; see [Recent version history](#recent-version-history).

---

## Recent version history
- **v1.0 — operational 2026-11-03, 12 UTC.** The parallel-feed configuration went into operations with no change to the output. NOMADS `para/` was replaced by `prod/` and the `v1.0/` mirror at about 02:05 UTC. On S3, the REFS 12 UTC cycle arrived about three hours late on cutover day. HREF and SREF retired on the same cycle. The implementation date had moved four times, from early 2026 (PNS 25-41) to August 31, October 6, October 14 and finally November 3.
- **2026-10-15, 12 UTC (pre-operational) — SCN 26-48 AAF.** This was the REFS side of an RRFS-wide fix for over-forecast freezing rain and under-forecast sleet. RRFS switched to a thermodynamically diagnosed precipitation type at the same cycle.
  - **`prob`:** 183 → 169 records. All 14 freezing-rain (`FRZR`) accumulation probabilities were removed.
  - **`sprd`:** 82 → 80 records. The duplicate `HGT:surface` record was removed, and so was the composite reflectivity (`REFC`) spread, **which the SCN does not mention**.
  - **`pmmn`:** Negative values were removed from the `REFC` and 1 km `REFD` fields. Before the change about half the grid was negative, down to −20 dBZ; afterwards the minimum is exactly 0.
- **2026-09-09 (pre-operational) — individual RRFS members published** for the first time (SCN 26-48 AAD), on four grids and with a reduced parameter set.
- **2026-08-12 to 08-21 (pre-operational) — parallel feed established.**
  - 2026-08-12, 12 UTC: the feed began on NOMADS `para/`, and the AWS prototype bucket froze.
  - 2026-08-13: the replacement bucket `s3://noaa-rrfs-ops-pds` came up.
  - 2026-08-15 to 21: `.idx` sidecars were added on NOMADS.

  The combined products did not change in the move; a per-record comparison of all eight product types found no differences. On the prototype, the combined products had sat under `rrfs_public/refs.<date>/<cycle>/enspost/`, and members under `rrfs_a/rrfsens.<date>/`. Code written against that layout needs its paths rewritten.

---

## Official documentation
- NWS Service Change Notice 26-48, AAF update of October 15, 2026 (RRFS/REFS implementation; current revision):
  https://www.weather.gov/media/notification/pdf_2026/scn26-048_Updated_RRFS_and_REFS_Implementation.aaf.pdf
- NWS Service Change Notice 26-47, AAC update of October 2, 2026 (termination of NAM/SREF/HREF/HiresW/NAM MOS):
  https://www.weather.gov/media/notification/pdf_2026/scn26-47_Updated_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS_aac.pdf
- NWS Service Change Notice 26-89 (October 2, 2026) — downstream changes, including REFS as an NBM input:
  https://www.weather.gov/media/notification/pdf_2026/scn26-89_RRFS_Downstream.pdf
- NWS Public Information Statement 25-41 (legacy model retirement proposal, June 26, 2025):
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- REFS product description at NCO:
  https://www.nco.ncep.noaa.gov/pmb/products/refs
- HREF-to-REFS product changes:
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/href_product_changes.txt
- NARRE-to-REFS product changes:
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/narre_replacement.txt
- NOAA RRFS/REFS [Operational] on AWS (CC0-1.0):
  https://registry.opendata.aws/noaa-rrfs-ops/
