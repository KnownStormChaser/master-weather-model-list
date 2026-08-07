# HGEFS (Hybrid Global Ensemble Forecast System)

## What this model is
The Hybrid Global Ensemble Forecast System (HGEFS) is NOAA's operational **62-member "grand ensemble"** combining the 31 physics-based members of [GEFS](./gefs.md) with the 31 AI-based members of [AIGEFS](./aigefs.md) into a single pooled ensemble, on the premise that a mixed physics-plus-AI membership samples forecast uncertainty better than either system alone (Wang et al., 2025, NCEP Office Note 522).

HGEFS became operational at 12 UTC on **December 17, 2025** (SCN 25-89), alongside [AIGFS](../../../nwp_models/global/usa/aigfs.md) and [AIGEFS](./aigefs.md). It does not replace either parent — GEFS and AIGEFS continue to run and are distributed independently.

The current operational version is **HGEFS v1.0**.

> ⚠️ **HGEFS is a statistics-only product. The 62 members are not distributed.** The public feed contains **only ensemble mean and ensemble spread** — there are no member files, no probability products, and no percentile products. Live-verified: the cycle directory holds a single `ensstat/` subtree, and probes for `memMMM/` paths return the same response as an invented directory name (see [Data availability](#data-availability)). Anyone needing HGEFS members must reconstruct the pool themselves from the separately distributed [GEFS](./gefs.md) and [AIGEFS](./aigefs.md) member sets.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States
- **Development:** NOAA Environmental Modeling Center (EMC), with NOAA research laboratories and the Earth Prediction Innovation Center (EPIC), under Project EAGLE

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Uniform latitude–longitude grid (`regular_ll`), **1440 × 721 = 1,038,240 points**, 0.25° in both directions, first grid point 90.0°N / 0.0°E, last 90.0°S / 359.75°E, `scanningMode = 0`. Identical to the AIGFS and AIGEFS grids. Live-decoded with ecCodes 2.48.0 from the 2026-08-06 12 UTC cycle.

---

## Basic details
- **Model type:** Global hybrid ensemble (pooled physics + AI membership; combination product, not an integration)
- **Model system / core:** Not applicable — HGEFS runs no forecast model of its own. Its members are the FV3-based [GEFS](./gefs.md) and the GraphCast-derived [AIGEFS](./aigefs.md).
- **Ensemble size:** **62** — 31 [GEFS](./gefs.md) physics members + 31 [AIGEFS](./aigefs.md) AI members. Confirmed in the GRIB2 headers: every distributed message declares `numberOfForecastsInEnsemble = 62`, an accurate count (contrast [NAEFS](./naefs.md), whose derived products misdeclare theirs).
- **Horizontal resolution:** 0.25° (~28 km)
- **Grid dimensions:** 1440 × 721
- **Vertical levels:** 13 pressure levels — 1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa (the GraphCast-canonical set, matching AIGEFS)
- **Model top:** 50 hPa (highest distributed pressure level)
- **Forecast length:** **240 hours (10 days)** — 41 output steps, f000–f240, live-verified across all four cycles
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC) — all four verified present and complete to f240 on 2026-08-05
- **Temporal output resolution:** 6 hours (step deltas verified uniform at 6 h)
- **Observed publication latency:** first file at ~**T+6 h 36 m**, last at ~**T+6 h 42 m** — a remarkably tight **6-minute** window, measured on the 2026-08-06 12 UTC cycle. This is roughly **3 hours later than AIGFS and AIGEFS** (~T+3 h 52 m) because HGEFS must wait for the physics-based GEFS run to finish before statistics can be computed; the narrow window reflects that the job is only a statistics computation over already-complete inputs.

> ⚠️ **The 240-hour range is 6 days shorter than both parents.** [AIGEFS](./aigefs.md) runs to 384 h, and [GEFS](./gefs.md) runs to 384 h at 06/12/18 UTC (840 h at 00 UTC). HGEFS stops at 240 h at **every** cycle, including 00 UTC. NOAA's product description gives the 10-day figure without explaining it, and no SCN states a reason (**TBD**). It is suggestive that GEFS's public 0.25° product (`pgrb2sp25`) is likewise capped at +240 h, but that product is a 38-record surface subset that could not supply HGEFS's 13-level pressure fields, so the parallel is not an explanation — the constraint most likely sits in internal 0.25° product generation rather than in the public feed.

---

## Methodology

> *The template's "Data assimilation", "Initial and boundary conditions", and "Perturbations and design" sections do not apply — HGEFS performs no assimilation, runs no integration, and generates no perturbations of its own. They are replaced by this section, following the pattern used for [NAEFS](./naefs.md) and [557th WW GEPS](./557wg-geps.md).*

HGEFS pools the completed member forecasts of its two parent ensembles and computes ensemble statistics across the combined 62-member set. All uncertainty representation is inherited:

- **From [GEFS](./gefs.md) (31 members):** EnKF-derived initial-condition perturbations plus stochastic physics (SPPT, SKEB, SHUM) for model uncertainty.
- **From [AIGEFS](./aigefs.md) (31 members):** the same GEFS initial-condition perturbations, with model uncertainty represented instead by running members with different sets of learned model weights.

Because AIGEFS takes its initial-condition perturbations from GEFS, the two halves of HGEFS are **not initial-condition-independent** — they share a perturbation source and differ chiefly in how the forecast is propagated (physics integration vs learned network) and in how model uncertainty is represented. The pooled spread is therefore weighted toward structural/model diversity rather than toward a doubling of initial-condition sampling.

- **Member weighting:** Whether the two halves are weighted equally in the mean and spread, or whether any bias correction or calibration is applied before pooling, is not stated in the product description or the file headers (**TBD**). The absence of `typeOfGeneratingProcess = 11` (bias-corrected ensemble forecast) in the headers — HGEFS carries 4 (ensemble forecast), unlike NAEFS's bias-corrected products — is consistent with a straight unweighted pooling, but does not confirm it.
- **Member correspondence:** whether AIGEFS member *n* pairs with GEFS member *n* is likewise undocumented (**TBD**). This matters for interpreting the pooled spread and is flagged in the [AIGEFS entry](./aigefs.md#perturbations-and-design) as well.

---

## What it provides

Two derived products, each split per step into a pressure-level file (`pres`) and a surface file (`sfc`):

| Product | File suffix | GRIB2 encoding |
|---|---|---|
| Ensemble mean | `.avg.` | PDT 2, `derivedForecast = 0` |
| Ensemble spread (standard deviation) | `.spr.` | PDT 2, `derivedForecast = 2` |

**Pressure-level fields — 78 records at every step, f000 through f240 (6 variables × 13 levels):**

| GRIB2 name | Description |
|---|---|
| `HGT` | Geopotential height |
| `TMP` | Temperature |
| `UGRD` | U (zonal) wind component |
| `VGRD` | V (meridional) wind component |
| `VVEL` | Vertical velocity (pressure) |
| `SPFH` | Specific humidity |

**Surface fields — 4 records at f000, 5 records at f006–f240:**

| GRIB2 name | Level | Steps |
|---|---|---|
| `UGRD` | 10 m above ground | f000–f240 |
| `VGRD` | 10 m above ground | f000–f240 |
| `TMP` | 2 m above ground | f000–f240 |
| `PRMSL` | Mean sea level | f000–f240 |
| `APCP` | Surface | f006–f240 — 6-hour accumulation bucket only |

The parameter set is **identical to [AIGEFS](./aigefs.md)**, not to GEFS. HGEFS therefore inherits the AI system's narrow field list rather than GEFS's much broader one: no cloud variables, radiation fluxes, soil fields, ozone, convective indices, or aviation parameters, and — as in AIGEFS — **no run-total precipitation accumulation**, only the 6-hour bucket. Users needing GEFS's parameter breadth or a run-total accumulation must go to [GEFS](./gefs.md) directly.

**Total GRIB2 messages per cycle:** 6,796 (78 × 41 × 2 pressure-level records, plus (4 + 5 × 40) × 2 surface records).

### GRIB2 encoding (live-decoded, ecCodes 2.48.0)
- **Edition:** 2, `tablesVersion = 2`
- **Centre:** `kwbc` (NCEP), `subCentre = 2`
- **Generating process identifier:** **139** (HGEFS; AIGFS is 137, AIGEFS is 138)
- **Type of generating process:** 4 (ensemble forecast)
- **Product definition template:** 2 (derived forecast based on all ensemble members) throughout — there is no PDT 1 (individual member) content in this dataset
- **`numberOfForecastsInEnsemble`:** 62
- **Packing:** `grid_complex_spatial_differencing`, `bitsPerValue` 10–11 observed on surface files

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data formats:** GRIB2, with a companion `.idx` byte-range index for every file
- **Official download location:**
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/hgefs/prod/

  File layout: `hgefs.YYYYMMDD/CC/ensstat/products/atmos/grib2/hgefs.tCCz.[pres,sfc].[avg,spr].fFFF.grib2[.idx]`, where `CC` is the cycle (00/06/12/18) and `FFF` is the forecast hour (000–240 in steps of 6). The `prod/` path resolves internally to `hgefs/v1.0/`; reference `prod/` rather than pinning the version path.

  **There is no member path.** `ensstat/` is the only subtree.
- **Additional distribution:** NSF Unidata IDD/CONDUIT feed for users with CONDUIT access.
- **Not available via FTP.** Per SCN 25-89, the AI model suite is distributed on the NCEP NOMADS server only.
- **Not available on AWS Open Data / NODD.** An `noaa-hgefs-pds` S3 bucket **exists and responds**, but is **empty** — a `list-objects-v2` request returns `KeyCount = 0`. It appears provisioned but never populated. It is not registered on the AWS Open Data registry. Do not build against it.
- **Per-cycle data volume:** **4.98 GiB** across 164 GRIB2 files (plus 164 `.idx` sidecars — 328 objects per cycle):

  | Product | Files | Per-file size | Total |
  |---|---|---|---|
  | `pres.avg` | 41 | 60.1–71.9 MiB | 2.46 GiB |
  | `pres.spr` | 41 | 54.4–58.3 MiB | 2.30 GiB |
  | `sfc.avg` | 41 | 2.6–3.0 MiB | 0.11 GiB |
  | `sfc.spr` | 41 | 1.7–2.7 MiB | 0.11 GiB |

  At four cycles a day this is roughly **20 GiB/day** — about 3% of [AIGEFS](./aigefs.md)'s ~650 GiB/day, a direct consequence of shipping statistics rather than members.
- **Retention:** **Only 2 daily directories** on NOMADS as of 2026-08-06 (`hgefs.20260805`, `hgefs.20260806`), matching [AIGEFS](./aigefs.md) despite the far smaller volume. Assume roughly a 48-hour window and mirror promptly. There is no NOAA-operated public archive of operational HGEFS output.
- **NCEP Product Inventory:** https://www.nco.ncep.noaa.gov/pmb/products/hgefs/ (note: the NCEP inventory pages for the AI suite have not been refreshed since December 2025)

> **Note on HTTP status codes when probing this tree.** NOMADS returns **403** for a path whose *directory* does not exist and **404** for a missing file inside a directory that does exist. A probe of `hgefs.YYYYMMDD/CC/mem000/...` returns 403, identical to the response for an invented directory name, while a bogus filename under the real `ensstat/products/atmos/grib2/` returns 404. Scripts that treat 403 as "access denied, retry with credentials" will mis-diagnose a simply-absent path.

---

## Relationship to other models

### Parent ensembles
- **[GEFS](./gefs.md)** (NCEP) — 31 physics-based members, FV3 core with stochastic physics. Continues to run and be distributed independently, with a far broader parameter set and longer forecast range.
- **[AIGEFS](./aigefs.md)** (NCEP) — 31 AI-based members, GraphCast lineage. Supplies HGEFS's parameter set and grid conventions. Note that AIGEFS remains at v1.0 while its deterministic sibling AIGFS moved to v1.1 in July 2026.

### Related NOAA AI systems
- **[AIGFS](../../../nwp_models/global/usa/aigfs.md)** (operational) — the deterministic GraphCast-lineage model, implemented on the same date. Now at v1.1.

### Related multi-source ensembles
- **[NAEFS](./naefs.md)** — the older multi-center bias-corrected combination of GEFS with the Canadian GEPS. A useful contrast: NAEFS bias-corrects each contributing ensemble before combining and publishes bias-corrected *members* as well as derived products, whereas HGEFS publishes derived products only and carries no bias-correction marker in its headers.
- **[557th WW GEPS](./557wg-geps.md)** — the U.S. Air Force statistical multi-center ensemble, another pooled-membership system distributed as statistics.

---

## Notes
- **The defining practical fact about HGEFS is what it does *not* ship.** Statistics-only distribution means HGEFS cannot support probabilistic work beyond mean and spread — no exceedance probabilities, no percentiles, no member-based clustering or extreme-value analysis. For those, pool [GEFS](./gefs.md) and [AIGEFS](./aigefs.md) members yourself, noting that they are distributed on different grids and parameter sets and that GEFS's 0.25° public product is a surface subset.
- **Grid and parameter conventions come from the AI side, not the physics side.** HGEFS matches AIGEFS's 0.25° grid, 13-level pressure structure, 6-hourly cadence, and field list exactly. Any pipeline already reading AIGEFS `ensstat/` output can read HGEFS with no changes other than the shorter 41-step range.
- **The two halves are not independent in their initial conditions**, since AIGEFS inherits GEFS's perturbations — worth bearing in mind when interpreting the pooled spread as a measure of total forecast uncertainty.
- HGEFS inherits AIGEFS's **limited stratospheric coverage** (highest level 50 hPa).
- HGEFS is part of **Project EAGLE**, the joint NOAA Research / EPIC / NWS effort that also produced AIGFS and AIGEFS.
- See [`AI_MODELS.md`](../../../../AI_MODELS.md) for the repository-wide index of AI-based and hybrid forecast systems.

---

## Recent version history

### v1.0 — operational 12 UTC, December 17, 2025 (SCN 25-89)
First operational implementation, jointly with [AIGFS](../../../nwp_models/global/usa/aigfs.md) and [AIGEFS](./aigefs.md). Established the NOMADS-only distribution, the `ensstat/products/atmos/grib2/` layout, the `hgefs.tCCz.[pres,sfc].[avg,spr].fFFF.grib2` naming convention, and generating process ID 139.

No HGEFS upgrade has been announced since. The AIGFS v1.1 upgrade of July 27, 2026 (SCN 26-68) applied to the deterministic system only; because HGEFS draws on AIGEFS rather than AIGFS, it is unaffected by that change.

---

## Status
- HGEFS is **operational** at version **v1.0** as of 12 UTC on December 17, 2025.
- It runs alongside, and does not replace, either [GEFS](./gefs.md) or [AIGEFS](./aigefs.md).
- No operational successor to v1.0 has been announced as of August 2026.

---

## Official documentation
- NCEP HGEFS product inventory: https://www.nco.ncep.noaa.gov/pmb/products/hgefs/
- SCN 25-89 (v1.0 implementation of AIGFS, AIGEFS and HGEFS): https://www.weather.gov/media/notification/pdf_2025/scn25-89_AIGFS_AIGEFS_and_HGEFS.pdf
- NOAA press release (December 2025): https://www.noaa.gov/news-release/noaa-deploys-new-generation-of-ai-driven-global-weather-models
- EPIC EAGLE AI overview: https://epic.noaa.gov/ai/eagle-overview/
- EPIC EAGLE AI changelog: https://epic.noaa.gov/ai/eagle-change-log/
- NSF Unidata announcement: https://www.unidata.ucar.edu/news/ai-driven-global-model-output-availability

### Key references
- Wang, J., S. S. Tabas, B. Fu, L. Cui, Z. Zhang, L. Zhu, J. Peng, and J. R. Carley (2025). *Development of a Hybrid ML and Physical Model Global Ensemble System.* NCEP Office Note 522. https://doi.org/10.25923/7kpr-5e68
- Tabas, S. S., et al. (2025). *GFS-Powered Machine Learning Weather Prediction: A Comparative Study on Training GraphCast with NOAA's GDAS Data for Global Weather Forecasts.* NCEP Office Note 521. https://doi.org/10.25923/xd3y-wy31
- Lam, R., et al. (2023). *Learning skillful medium-range global weather forecasting.* Science, 382(6677), 1416–1421. https://doi.org/10.1126/science.adi2336
