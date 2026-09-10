# REFS (RRFS Ensemble Forecast System)

## What this model is
The RRFS Ensemble Forecast System (REFS) is the **ensemble component** of NOAA's next-generation Rapid Refresh Forecast System (RRFS).

REFS is a regional, convection-allowing ensemble designed to provide probabilistic short-range forecast guidance for high-impact weather across North America. It is built on the UFS framework and is intended to replace the legacy HREF, SREF, and NARRE ensemble systems.

REFS is scheduled to become operational on **October 14, 2026 at 12 UTC** under NWS Service Change Notice 26-48 (May 12, 2026; updated July 6, August 24 and September 9, 2026), subject to the standard CWD/ECE postponement contingency, alongside the deterministic [RRFS](../../../nwp_models/regional/usa/rrfs.md). A pre-implementation real-time parallel feed has been live since the 12 UTC cycle on **August 12, 2026**, on NOMADS and on AWS S3 via NOAA Open Data Dissemination. The five contributing RRFS ensemble members, unavailable through August, began publishing separately on **September 9, 2026** — see [Data availability](#data-availability).

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
- **Convection-allowing:** Yes — deep convection is explicitly resolved at the 3 km CONUS/AK and 2.5 km HI/PR domains; no cumulus parameterization is used in the RRFS members. The HRRR members (contributing to the CONUS and Alaska REFS domains) are also convection-allowing at 3 km on WRF-ARW.
- **Horizontal resolution:**  
  - 3 km (CONUS, Alaska)  
  - 2.5 km (Hawaii, Puerto Rico)
- **Forecast length:** 60 hours
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly through 60 h

---

## Data assimilation
- **Data assimilation:** REFS does not perform its own data assimilation.
- **Method:** REFS inherits the analyses of its contributing systems — primarily the hourly cycling **GSI-based hybrid 3DEnVar** analysis from the deterministic and ensemble [RRFS](../../../nwp_models/regional/usa/rrfs.md) (current and 6 h time-lagged cycles), and additionally the HRRR hourly cycling hybrid ensemble-variational analysis for the two HRRR members contributed to the CONUS and Alaska REFS domains. See the RRFS and [HRRR](../../../nwp_models/regional/usa/hrrr.md) entries for the analysis system details.

---

## Ensemble methodology

REFS is an **ensemble product generation system** that combines forecasts from the
deterministic and ensemble components of RRFS, time-lagged across cycles, with HRRR as an
additional contributor for two of the four domains. The two most recent runs of each
contributing system are used, giving:

| Domain | Members | Composition |
|---|---|---|
| CONUS | **14** | RRFS deterministic + 5 RRFS ensemble members, current and 6 h old (12), plus HRRR current and 6 h old (2) |
| Alaska | **14** | As CONUS |
| Hawaii | **12** | RRFS deterministic + 5 RRFS ensemble members, current and 6 h old; no HRRR |
| Puerto Rico | **12** | As Hawaii |

> **These counts are confirmed in the data, not just the documentation.** Every `mean`
> and `sprd` record carries `numberOfForecastsInEnsemble` — decoded on the 2026-08-12
> 12 UTC f12 files, it reads **14** on CONUS and Alaska and **12** on Hawaii and Puerto
> Rico, on all 76 and 82 records respectively. The arithmetic implied by the
> [RRFS](../../../nwp_models/regional/usa/rrfs.md) entry (one deterministic run plus five
> ensemble members, doubled by 6 h time-lagging, plus two HRRR members for CONUS/AK)
> reproduces both numbers exactly.

> ⚠️ **`numberOfForecastsInEnsemble` is absent from the probabilistic products** — it is
> undefined on every record of `prob`, `eas` and `ffri`. The same value is carried there
> under a different key; see
> [Data availability](#data-availability) for how to read it, including straight from the
> `.idx` sidecar.

For each REFS cycle, the membership is drawn from:

- **Current-cycle RRFS deterministic** forecast (run hourly; the cycle-current run feeds REFS)
- **Current-cycle RRFS ensemble members** — RRFS produces 5 ensemble members at the 00/06/12/18 UTC cycles, using different initial conditions, lateral boundary conditions, and model physics relative to the deterministic forecast. These members are themselves publicly distributed since 2026-09-09; see [Data availability](#data-availability).
- **6-hour-old RRFS deterministic** forecast (time-lagged)
- **6-hour-old RRFS ensemble members** (time-lagged)
- **For CONUS and Alaska only:** two additional HRRR members — the current cycle and a 6-hour-old cycle

The HRRR contribution to CONUS and Alaska REFS is operationally notable: HRRR is not a UFS system and is not retired in the first wave, so REFS will rely on HRRR as an explicit input during the RRFSv1 era. When HRRR is eventually retired alongside RAP under the future RRFSv2 transition, the REFS membership composition will change correspondingly.

The exact member composition and perturbation strategy are expected to evolve as RRFS transitions from v1 to v2 (MPAS-based).

---

## What it provides
Probabilistic regional forecasts of:
- Precipitation amount, type, and thresholds
- Severe convection indicators
- Near-surface temperature, wind, and humidity
- Neighborhood and probability-based guidance for high-impact weather

REFS provides the primary short-range, convection-allowing probabilistic guidance across CONUS, Alaska, Hawaii, and Puerto Rico.

### Ensemble product types
REFS ensemble products are published under `refs.YYYYMMDD/CC/ensprod/` with the file
pattern `refs.tCCz.${type}.fFF.${dom}.grib2`, each accompanied by a matching
`.grib2.idx` sidecar, where `dom` is one of `conus`, `ak`, `hi`,
`pr`. Lead time is a **two-digit** token (`f09`) — the deterministic
[RRFS](../../../nwp_models/regional/usa/rrfs.md) uses three digits (`f009`). Sharing a
lead-time formatter between the two systems is the most common way to generate 404s
against this feed.

Record counts below are decoded from the CONUS f12 files of the 2026-08-12 12 UTC cycle
and are identical to the corresponding AWS prototype files.

| Type | Description | Records (CONUS) | Parameter scope (decoded) |
|---|---|---|---|
| `mean` | Arithmetic mean of all members | 76 | Broad — 34 distinct parameters across mass, wind, moisture, stability, cloud and precipitation-type fields |
| `sprd` | Ensemble spread — how far apart the members are at a point; smaller means better agreement | 82 | Broad — 35 distinct parameters, tracking `mean` |
| `pmmn` | Probability-matched mean, computed over the full domain at once | 8 | Narrow — total precipitation (2), orography, reflectivity, and 4 radar/local-table fields |
| `lpmm` | Localized probability-matched mean, computed over small regions then assembled | 3 | **Total precipitation only** |
| `avrg` | Average of the `mean` and `pmmn` fields | 2 | **Total precipitation only** |
| `prob` | Probabilistic output — fraction of members meeting a threshold; a mix of point and neighbourhood-maximum probabilities | 183 | Broad — 23 parameters including precipitation, snowfall, wind, gusts, freezing rain, icing, visibility, flight category and lightning |
| `eas` | Ensemble Agreement Scale probability — a smoothed fractional probability whose neighbourhood radius varies from 10 to 100 km, narrowing where members agree | 29 | **Precipitation (22) and snowfall (7) only** |
| `ffri` | Flash Flood and Average Recurrence Interval exceedance probabilities — **CONUS only** | 10 | Total precipitation (7) and flash-flood guidance exceedance (3) |

The "precipitation only" restrictions on `lpmm`, `avrg` and `eas` are stated in the
NOMADS description and confirmed by decoding: `lpmm` and `avrg` contain nothing but `tp`,
and `eas` contains `tp` plus total snowfall at 1, 3 and 6 h accumulations and nothing
else. The description does not restrict `pmmn`, and correctly so — but `pmmn` is far
narrower than `mean` despite both being described as means, which is easy to miss when
choosing a product.

Product Definition Templates differ by product and are worth knowing before writing a
reader: `mean` and `sprd` use PDT 2 and 12 (derived forecast, instantaneous and
interval); `prob` uses 5 and 9 (probability, instantaneous and interval); `eas` and
`ffri` use 9 exclusively.

All eight are written on the same grids as the deterministic RRFS subset domains — CONUS
is `lambert` 1799 × 1059 at 3000 m, matching RRFS exactly. Encoding is GRIB2 edition 2,
centre `kwbc`, `tablesVersion` 2, `localTablesVersion` 1,
**`generatingProcessIdentifier` 136** (RRFS deterministic uses 134), with
`grid_complex_spatial_differencing` packing throughout.

Every product runs **f01–f60 hourly** on every domain it covers.

#### Parameters that stock ecCodes cannot name

Decoding with ecCodes 2.48.0 leaves a number of records with `shortName = unknown`. Two
distinct causes, and the first is the surprising one:

| discipline/category/number | Meaning | Where it appears |
|---|---|---|
| **0/1/29** | **Total snowfall — a standard WMO number** | `mean` (4 records, 1/3/6/12 h), `sprd`, `prob` (14), `eas` (7) |
| 0/2/220, 0/6/202, 0/7/199, 0/16/198, 0/19/235 | NCEP local-table numbers (≥192) | `mean`, `pmmn`, `prob` |
| 0/16/3, 0/16/5 | Forecast-radar-imagery category | `pmmn`, `prob` |

The local-table numbers are expected — the files declare `localTablesVersion = 1` and
resolving them requires NCEP's tables. **Total snowfall is not**: 0/1/29 is a standard
WMO entry that ecCodes 2.48.0 still fails to name, and it is the parameter carrying every
snow product in `eas` and a substantial share of `prob`. Anyone filtering REFS by
`shortName` will silently drop all snowfall guidance. Filter on the
discipline/category/number triplet instead.

Full descriptions of variables and encoding are at
https://www.nco.ncep.noaa.gov/pmb/products/refs.

---

## Relationship to other models
REFS is intended to fully replace the following legacy NCEP ensemble systems on October 14, 2026:
- **HREF** (High-Resolution Ensemble Forecast)
- **SREF** (Short-Range Ensemble Forecast)
- **NARRE** (North American Rapid Refresh Ensemble) — **on the authority of PNS 25-41 only.** SCN 26-47 does not name NARRE in either its subject line or its body, through the AAB update of September 9, 2026. The replacement is signalled but not formally scheduled.

Compared to the legacy systems:
- REFS extends forecasts to **60 hours** (HREF ran to 48 hours)
- REFS provides **00, 06, 12, and 18 UTC** cycles for all regions, including non-CONUS domains (HREF only ran twice daily for AK, HI, and PR — at 06Z/18Z for AK and PR, and at 00Z/12Z for HI)
- NARRE's hourly 12-hour ensemble guidance is replaced by REFS's 60-hour forecasts updated every 6 hours
- SREF, which had already lost its largest downstream consumer when NBM v5.0 (May 2026) eliminated SREF as an input, is folded into the REFS replacement set under SCN 26-48

REFS shares similarities with HREF in product types (mean, spread, PMM, LPMM, probabilities, EAS), but differs in membership composition and ensemble design. Where HREF was a "post-processing ensemble of opportunity" combining whatever convection-allowing models happened to be operationally available, REFS is built around the deterministic and ensemble components of a single UFS-based system (RRFS) with HRRR contributing supplemental members for CONUS/AK.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download locations:**
  - **AWS S3 (NODD):** `s3://noaa-rrfs-ops-pds/refs.YYYYMMDD/CC/ensprod/` —
    https://noaa-rrfs-ops-pds.s3.amazonaws.com/index.html
  - **NOMADS, pre-implementation:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/refs/para/
  - **NOMADS, NOAAPORT subset:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/para/noaaport/refs/
  - **NOMADS, post-implementation (from October 14, 2026):**
    https://nomads.ncep.noaa.gov/pub/data/nccf/com/refs/prod/

REFS shares the deterministic
[RRFS](../../../nwp_models/regional/usa/rrfs.md#data-availability) bucket rather than
having its own — there is no `noaa-refs-ops-pds` (404). Both channels use the
**`ensprod/`** subdirectory, so the `enspost/` ÷ `ensprod/` discrepancy that existed on
the old prototype is gone; the naming is now consistent everywhere.

A synoptic cycle is **1740 GRIB2 files and 1740 `.idx` sidecars** on each channel —
7 product types × 4 domains × 60 lead times, plus `ffri` on CONUS. Verified set-symmetric
on 2026-08-22. Both the GRIB2 files and the sidecars are **byte-identical** between
channels (MD5-verified on `refs.t12z.avrg.f12.conus.grib2` and its sidecar).

Indexing matters more for REFS than for the deterministic model: a CONUS `prob` step
carries 183 records at ~68 MB, so a full f01–f60 series is roughly 4 GB per cycle per
domain for that product type alone. Either channel now supports pulling a single threshold
field. **Prefer S3 for anything older than 48 hours**, since NOMADS `para` retains only
two days while the bucket has kept every date since it came up.

> **Individual members are now disseminated, as of 2026-09-09.** This reverses the
> position recorded here through August 2026, when raw member output had had no open
> channel since the 2026-08-12 cutover. SCN 26-48 AAD (2026-09-09) lists the members for
> the first time, and they appeared in the bucket the same day — the first object under
> `s3://noaa-rrfs-ops-pds/rrfsens.20260909/` at 13:40:14 UTC, at the 12 UTC cycle.
>
> Members live under the top-level **`rrfsens.YYYYMMDD/CC/m00#/`** prefix — a sibling of
> `rrfs.YYYYMMDD/` and `refs.YYYYMMDD/`, not a subdirectory of either — with the pattern
> `rrfs.tCCz.m00#.{prslev|2dfld}nomads.{3km|2p5km}.fFFF.${dom}.grib2`. Note the
> **three-digit** lead-time token, matching the deterministic RRFS rather than the
> two-digit REFS convention: code that handles all three streams needs two formatters, not
> one. Five members, 00/06/12/18 UTC, f000–f060 hourly, `.idx` on every file from the
> first object.
>
> **What the restored stream does not give back.** The prototype carried members on five
> grids including North America; the replacement carries four — CONUS and Alaska at 3 km,
> Hawaii and Puerto Rico at 2.5 km, with no NA grid, despite the SCN describing the
> members as running "over the same NA region as the deterministic RRFS". The parameter
> set is also much narrower than the deterministic files: `prslevnomads` carries 112
> records (8 parameters on 14 pressure levels) against 675 in deterministic `prslev`, and
> `2dfldnomads` carries 58 against 318. There is **no ensemble BUFR** — the AAD lists
> `class1.bufr` and `bufrsnd` lines for the members but marks both with a literal `(??)`,
> and neither exists.
>
> The practical consequence for REFS users is that custom post-processing — bespoke
> percentiles, neighbourhood probabilities at non-standard thresholds, member clustering —
> is possible again, but only within the reduced member parameter set. Anything needing a
> field that survives in deterministic `prslev`/`2dfld` but not in the member files still
> has to come from the eight combined `ensprod` products.
>
> Full breakdown in the [RRFS
> entry](../../../nwp_models/regional/usa/rrfs.md#ensemble-member-output-disseminated-since-september-9-2026).

> **Ensemble size is encoded, but under two different keys depending on product.** On
> `mean` and `sprd` it is `numberOfForecastsInEnsemble`, which reads 14 on CONUS and
> Alaska and 12 on Hawaii and Puerto Rico across all records. On `prob`, `eas` and `ffri`
> that key is **undefined**; the same value appears instead as
> `totalNumberOfForecastProbabilities` in PDT 5 and 9, which wgrib2 renders as
> `prob fcst 0/14`. Verified to agree with the `mean`/`sprd` counts on every domain.
>
> Two consequences. First, code that sizes member arrays from
> `numberOfForecastsInEnsemble` alone will fail on three of the eight products — read
> both keys. Second, because the value appears in the wgrib2 inventory, **ensemble size
> can be read straight from the few-kilobyte `.idx` sidecar without fetching any GRIB2**.
>
> Note that `totalNumberOfForecastProbabilities` would ordinarily denote a count of
> probability thresholds, not members. NCEP populating it with the ensemble size is a
> local convention that happens to hold across all four REFS domains; do not carry the
> assumption to other centres' output.
>
> **A third value appears on the member files themselves.** The RRFS member output now
> published under `rrfsens.YYYYMMDD/` declares `numberOfForecastsInEnsemble = 5` — the
> size of the RRFS ensemble, not of the REFS membership that time-lagging and the HRRR
> contribution build from it. Both encodings are correct for what they describe, but a
> reader that takes a single ensemble size from whichever file it opens first will be
> wrong about the other. The member files also carry
> **`generatingProcessIdentifier` 136 — the same value as REFS `ensprod`**, not the 134
> used by deterministic RRFS, so that key cannot be used to tell member output from
> combined products either. Distinguish on the path prefix or on
> `productDefinitionTemplateNumber` (members are PDT 1 with `typeOfProcessedData = pf`
> and a populated `perturbationNumber`; `ensprod` uses PDT 2/12/5/9).

---

## Status
- **2026-09-09 — SCN 26-48 updated (AAD); implementation moved to October 14, 2026.**
  The third slip, from October 6, with no reason given. REFS `ensprod` output is unchanged
  by the update — the eight product types, four domains and f01–f60 coverage are as
  listed in AAC, and a 12 UTC cycle still carries 1740 GRIB2 files and 1740 sidecars
  (re-verified on 2026-09-09). What changed is the RRFS side: the five contributing
  members are now documented and published.
- **2026-09-09, 12 UTC — RRFS ensemble members published.** First object under
  `s3://noaa-rrfs-ops-pds/rrfsens.20260909/` at 13:40:14 UTC, ending the gap that had run
  since 2026-08-12. Four grids and a reduced parameter set relative to the prototype; see
  [Data availability](#data-availability).
- **2026-08-24 — SCN 26-48 updated (AAC).** Documents `.idx` sidecars for all eight REFS
  product types. Implementation date unchanged at October 6, 2026. Confirms the five RRFS
  ensemble members but still gives no dissemination path for them.
- **~2026-08-15 to 08-21 — `.idx` sidecars added to NOMADS.** Complete coverage, 1740 of
  1740 files per synoptic cycle, byte-identical to the S3 sidecars.
- **2026-08-13, ~21:30 UTC — NODD replacement bucket live.** `s3://noaa-rrfs-ops-pds`
  carries REFS under `refs.YYYYMMDD/CC/ensprod/`, registered CC0-1.0. Combined products
  only; no members.
- **2026-08-12, 12 UTC — parallel feed live on NOMADS; AWS prototype frozen.** The
  pre-implementation real-time feed began at the 12 UTC cycle at
  `/pub/data/nccf/com/refs/para/`, one day later than the "on or about August 11" date in
  SCN 26-48 — a date NOAA has since corrected to August 12 in its AWS Open Data Registry
  entry, though not in the SCN itself. The prototype bucket stopped after the 06 UTC
  cycle. Combined `ensprod` products carried across unchanged; individual members did
  not.
- Proposed retirement of HREF and NARRE was announced in NWS Public Information Statement 25-41 (June 26, 2025); SREF was added to the same retirement wave by SCN 26-48. HREF and SREF are named in SCN 26-47; NARRE is not, in any revision through AAB.
- Targeted for operational implementation alongside the deterministic RRFS, originally "early 2026"; slipped through pre-operational evaluation.
- SCN 26-48 was updated July 6, 2026 (AAB), moving implementation from August 31, 2026 to October 6, 2026 at 12 UTC and setting the real-time parallel feed to begin on or about August 11, 2026. A further update on August 24, 2026 (AAC) documented the `.idx` and BUFR files added to NOMADS, without changing the implementation date. The September 9, 2026 update (AAD) moved implementation to **October 14, 2026 at 12 UTC** and added the ensemble member output listing.
- **NWS Service Change Notice 26-48 (May 12, 2026)** scheduled REFS operational implementation for August 31, 2026 at 12 UTC, with HREF, SREF, and NARRE retiring on the same day. Per SCN 26-48, if the implementation date is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, implementation moves to 12 UTC on the next eligible weekday.
- 2025 NOAA Hazardous Weather Testbed Spring Forecasting Experiment evaluations indicated REFS performed competitively with HREF for Day 1 and Day 2 forecasts, and slightly better for some objective metrics including deep convection (>40 dBZ) prediction. This supported the decision to proceed with HREF→REFS replacement.

---

## Notes
- **The prototype AWS layout is now historical.** Individual members were stored under
  `rrfs_a/rrfsens.<date>/<cycle>/m###`, and combined products under
  `rrfs_public/refs.<date>/<cycle>/`**`enspost`**`/` — note `enspost`, not `ensprod`; the
  earlier description of that directory as "reflecting the planned operational NOMADS
  structure" was wrong on the directory name. Both current channels use `ensprod/`, so
  this discrepancy affects only code written against the pre-2026-08-12 bucket. The
  restored member stream is at top-level `rrfsens.<date>/<cycle>/m00#/` — same directory
  name, one level shallower, and the filenames now carry a `nomads` suffix
  (`prslevnomads`, `2dfldnomads`) that the prototype's did not. Prototype member code
  needs its prefixes and its filename patterns rewritten, not just its bucket name.
- **The products themselves did not change across the transition.** All eight CONUS
  product types were decoded at f12 from both distributions (AWS 06 UTC against NOMADS
  12 UTC, 2026-08-12) and compared on a per-record key tuple including shortName, level,
  statistical processing, threshold limits and probability type. **Zero records were
  unique to either side** in any of the eight, and record counts, grid definitions,
  `generatingProcessIdentifier` (136) and packing all match. What changed is the
  channel, the directory name, and what is no longer shipped alongside.
- **Ignore the 2026-08-12 12 UTC cycle when characterising lead-time coverage.** That
  cycle starts at f09 on CONUS and Alaska, f12 on Hawaii and Puerto Rico, and f06 with
  f07 missing for `eas.conus` — everything written before the feed was switched on at
  14:21 UTC was never copied across. The 18 UTC cycle the same day starts cleanly at f01
  for `mean` and `ffri` on CONUS, confirming the day-one pattern is an artifact and not
  a design.
- As with all ensemble systems, REFS output should be interpreted probabilistically rather than as a single deterministic forecast.
- The member composition and ensemble design are expected to evolve as RRFS transitions to v2 with the MPAS dynamical core; when HRRR is eventually retired under that transition, the HRRR member contributions to the CONUS/AK REFS domains will change as well.

---

## Official documentation
- NWS Service Change Notice 26-48, **AAD update of September 9, 2026** — current version.
  Moves implementation to October 14, 2026 and gives output paths for the five RRFS
  ensemble members for the first time:  
  https://www.weather.gov/media/notification/pdf_2026/scn26-048_Updated_RRFS_and_REFS_Implementation_aad.pdf
- NWS Service Change Notice 26-48 (AAC update, August 24, 2026 — superseded). The last
  revision that documented the members without giving them a dissemination path:  
  https://www.weather.gov/media/notification/pdf_2026/scn26-48_updated_RRFS_and_REFS_Implementation_aac.pdf
- NWS Service Change Notice 26-47, **AAB update of September 9, 2026** (termination of NAM/SREF/HREF/HiresW/NAM MOS):  
  https://www.weather.gov/media/notification/pdf_2026/SCN26-47_Updated_Retire_NAM_SREF_HREF_HiresW_NAM_MOS.aab.pdf
- NWS Service Change Notice 26-48 (original, May 12, 2026 — superseded):  
  https://www.weather.gov/media/notification/pdf_2026/scn26-48_RRFS_and_REFS_Implementation.pdf
- NWS Public Information Statement 25-41 (legacy model retirement proposal, June 26, 2025):  
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- REFS product description at NCO:  
  https://www.nco.ncep.noaa.gov/pmb/products/refs
- HREF-to-REFS product changes:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/href_product_changes.txt
- NARRE-to-REFS product changes:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/narre_replacement.txt
- NOAA RRFS/REFS [Operational] on AWS (current bucket, CC0-1.0):  
  https://registry.opendata.aws/noaa-rrfs-ops/
- NOAA RRFS [Prototype] on AWS (frozen bucket, historical):  
  https://registry.opendata.aws/noaa-rrfs/
