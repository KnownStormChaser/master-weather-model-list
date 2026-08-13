# REFS (RRFS Ensemble Forecast System)

## What this model is
The RRFS Ensemble Forecast System (REFS) is the **ensemble component** of NOAA's next-generation Rapid Refresh Forecast System (RRFS).

REFS is a regional, convection-allowing ensemble designed to provide probabilistic short-range forecast guidance for high-impact weather across North America. It is built on the UFS framework and is intended to replace the legacy HREF, SREF, and NARRE ensemble systems.

REFS is scheduled to become operational on **October 6, 2026 at 12 UTC** under NWS Service Change Notice 26-48 (May 12, 2026; updated July 6, 2026), subject to the standard CWD/ECE postponement contingency, alongside the deterministic [RRFS](../../../nwp_models/regional/usa/rrfs.md). A pre-implementation real-time parallel feed is expected on NOMADS on or about **August 11, 2026**.

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

> ⚠️ **`numberOfForecastsInEnsemble` is absent from the probabilistic products.** It is
> encoded on `mean` and `sprd` but is undefined on every record of `prob`, `eas` and
> `ffri` (183, 29 and 10 records checked). Do not use this key to size member arrays or
> to detect domain — it is only present on the two products that happen not to need it.

For each REFS cycle, the membership is drawn from:

- **Current-cycle RRFS deterministic** forecast (run hourly; the cycle-current run feeds REFS)
- **Current-cycle RRFS ensemble members** — RRFS produces 5 ensemble members at the 00/06/12/18 UTC cycles, using different initial conditions, lateral boundary conditions, and model physics relative to the deterministic forecast
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
pattern `refs.tCCz.${type}.fFF.${dom}.grib2`, where `dom` is one of `conus`, `ak`, `hi`,
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

Full descriptions of variables and encoding are at
https://www.nco.ncep.noaa.gov/pmb/products/refs.

---

## Relationship to other models
REFS is intended to fully replace the following legacy NCEP ensemble systems on October 6, 2026:
- **HREF** (High-Resolution Ensemble Forecast)
- **SREF** (Short-Range Ensemble Forecast)
- **NARRE** (North American Rapid Refresh Ensemble)

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
  - **NOMADS (pre-implementation parallel feed, live since 2026-08-12 12 UTC):**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/refs/para/
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/para/noaaport/refs/
  - **NOMADS (post-implementation, from October 6, 2026):**
    - https://nomads.ncep.noaa.gov/pub/data/nccf/com/refs/prod/

> ⚠️ **NOMADS is currently the only channel, and the combined products are all that
> survives.**
>
> `s3://noaa-rrfs-pds` last wrote REFS products at the **06 UTC** cycle on 2026-08-12
> (final object 09:28:56 UTC); the NOMADS parallel feed picked up at **12 UTC**. No
> replacement cloud mirror exists — `noaa-refs-pds`, `noaa-nws-refs-pds` and
> `noaa-nws-rrfs-pds` all return 404. See the
> [RRFS entry](../../../nwp_models/regional/usa/rrfs.md#data-availability) for the full
> cutover record and the AWS registry-text discrepancy.

### Individual members are gone

The AWS prototype carried the five RRFS ensemble members under
`rrfs_a/rrfsens.YYYYMMDD/CC/m001` … `m005`, each with `prslev` (24 steps) and `2dfld`
(61 steps) on the CONUS, Alaska, Hawaii, Puerto Rico and North America grids, `.idx`
sidecars for all of them, and per-member BUFR. The NOMADS parallel tree contains
`ensprod/` and nothing else — probes for `members/`, `mem01/` and `m001/` all return 403.

**Raw REFS member output therefore has no open channel as of 2026-08-12.** Users doing
their own ensemble post-processing — custom percentiles, neighbourhood probabilities at
non-standard thresholds, member clustering, or anything else not covered by the eight
`ensprod` product types — have lost the input they were working from. Whether members
appear at implementation on October 6, 2026 is not addressed in SCN 26-48 (**TBD**).

### No `.idx` sidecars

Every GRIB2 object on AWS carried a matching `.idx`. None exist on NOMADS. Byte-range
subsetting is unavailable, which bites hardest on `prob` (183 records) and `sprd`
(82 records) — a CONUS `prob` step is ~68 MB and a full f01–f60 CONUS `prob` series is
roughly 4 GB per cycle.

### Directory name differs between the two distributions

The AWS prototype used **`enspost/`**; NOMADS uses **`ensprod/`**. Verified 2026-08-12 —
`rrfs_public/refs.20260812/06/ensprod/` is empty on S3 while `enspost/` is populated, and
on NOMADS `…/12/ensprod/` returns 200 while `…/12/enspost/` returns 403. `ensprod` is the
name in SCN 26-48 and is the one that will carry forward to `refs/prod/`. Code written
against the prototype layout needs this one-word change.

### Publication latency

The 2026-08-12 12 UTC cycle published from 14:21 to 15:46 UTC, but that cycle is
contaminated by the feed switch-on and should not be used for scheduling. The 18 UTC
cycle had reached f09 by 20:45 UTC (**T+2h45m**), which is a better indication pending a
clean full-cycle measurement (**TBD**).

### NOAAPORT parallel stream

`https://nomads.ncep.noaa.gov/pub/data/nccf/com/para/noaaport/refs/` is a flat rolling
directory carrying only **three of the eight product types** —
`grib2.refs.tCCz.{mean|pmmn|prob}.fFF.{conus|ak|hi|pr}` — on a 3-hourly lead-time
spacing. Its first files are stamped 2026-08-12 14:07 UTC, so unlike the RRFS NOAAPORT
stream it began with the parallel feed rather than before it. Not a substitute for
`refs/para/`.

---

## Status
- **2026-08-12, 12 UTC — parallel feed live on NOMADS; AWS prototype frozen.** The
  pre-implementation real-time feed began at the 12 UTC cycle at
  `/pub/data/nccf/com/refs/para/`, one day later than the "on or about August 11" date
  in SCN 26-48. The prototype bucket stopped after the 06 UTC cycle. Combined `ensprod`
  products carried across unchanged; individual members did not.
- Proposed retirement of HREF and NARRE was announced in NWS Public Information Statement 25-41 (June 26, 2025); SREF was added to the same retirement wave by SCN 26-48.
- Targeted for operational implementation alongside the deterministic RRFS, originally "early 2026"; slipped through pre-operational evaluation.
- SCN 26-48 was updated July 6, 2026 (AAB), moving implementation from August 31, 2026 to **October 6, 2026 at 12 UTC** and setting the real-time parallel feed to begin on or about August 11, 2026.
- **NWS Service Change Notice 26-48 (May 12, 2026)** scheduled REFS operational implementation for August 31, 2026 at 12 UTC, with HREF, SREF, and NARRE retiring on the same day. Per SCN 26-48, if the implementation date is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, implementation moves to 12 UTC on the next eligible weekday.
- Pre-implementation parallel data feed expected on NOMADS on or about July 7, 2026.
- 2025 NOAA Hazardous Weather Testbed Spring Forecasting Experiment evaluations indicated REFS performed competitively with HREF for Day 1 and Day 2 forecasts, and slightly better for some objective metrics including deep convection (>40 dBZ) prediction. This supported the decision to proceed with HREF→REFS replacement.

---

## Notes
- **The prototype AWS layout is now historical.** Individual members were stored under
  `rrfs_a/rrfsens.<date>/<cycle>/m###`, and combined products under
  `rrfs_public/refs.<date>/<cycle>/`**`enspost`**`/` — note `enspost`, not `ensprod`;
  the earlier description of that directory as "reflecting the planned operational
  NOMADS structure" was wrong on the directory name. NOMADS uses `ensprod/`.
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
- NWS Service Change Notice 26-48 (RRFS and REFS implementation, May 12, 2026):  
  https://www.weather.gov/media/notification/pdf_2026/scn26-48_RRFS_and_REFS_Implementation.pdf
- NWS Public Information Statement 25-41 (legacy model retirement proposal, June 26, 2025):  
  https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- REFS product description at NCO:  
  https://www.nco.ncep.noaa.gov/pmb/products/refs
- HREF-to-REFS product changes:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/href_product_changes.txt
- NARRE-to-REFS product changes:  
  https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/narre_replacement.txt
- NOAA RRFS/REFS prototype on AWS:  
  https://registry.opendata.aws/noaa-rrfs/
