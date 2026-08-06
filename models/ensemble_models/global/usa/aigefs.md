# AIGEFS (Artificial Intelligence Global Ensemble Forecast System)

## What this model is
The Artificial Intelligence Global Ensemble Forecast System (AIGEFS) is NOAA's **operational 31-member AI-based global ensemble** weather forecast system — an AI emulator of the physics-based [GEFS](./gefs.md).

It shares the GraphCast-derived architecture of the deterministic [AIGFS](../../../nwp_models/global/usa/aigfs.md) and represents forecast uncertainty two ways: it takes the **same perturbed initial conditions as operational GEFS**, and it represents model uncertainty by running members with **different sets of model weights produced during the training process**. The result is a 31-member ensemble capturing both initial-condition and model uncertainty (Wang et al., 2025, NCEP Office Note 522).

AIGEFS became operational at 12 UTC on **December 17, 2025** (SCN 25-89), alongside [AIGFS](../../../nwp_models/global/usa/aigfs.md) and the hybrid [HGEFS](./hgefs.md), replacing the experimental EAGLE Ensemble. It runs alongside — not in place of — the physics-based [GEFS](./gefs.md).

The current operational version is **AIGEFS v1.0**.

> **Version asymmetry with AIGFS.** The deterministic AIGFS was upgraded to v1.1 on July 27, 2026 (SCN 26-68). AIGEFS was **not** included in that upgrade: the NOMADS `prod/` path still resolves to `aigefs/v1.0/` as of 2026-08-06. The two systems are therefore no longer at matched model versions, which matters for any study comparing AIGFS against its own ensemble.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States
- **Development:** NOAA Environmental Modeling Center (EMC), with NOAA research laboratories and the Earth Prediction Innovation Center (EPIC), under Project EAGLE
- **Architecture origin:** Derived from Google DeepMind's GraphCast (Lam et al., 2023)

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Uniform latitude–longitude grid (`regular_ll`), **1440 × 721 = 1,038,240 points**, 0.25° in both directions, first grid point 90.0°N / 0.0°E, last 90.0°S / 359.75°E, `scanningMode = 0`. Identical to the AIGFS grid. Live-decoded with ecCodes 2.48.0 from the 2026-08-06 12 UTC cycle.

---

## Basic details
- **Model type:** Global ensemble NWP (AI-based)
- **Model system / core:** GraphCast-derived graph neural network (GNN), encode–process–decode, ~37 million learned parameters per member
- **Dynamical formulation:** Not applicable — no dynamical core; forecast evolution is a learned autoregressive mapping between successive atmospheric states
- **Convection-allowing:** No
- **Ensemble size:** **31 members**, distributed as `mem000` through `mem030` (live-verified; `mem031` returns HTTP 404)
- **Horizontal resolution:** 0.25° (~28 km)
- **Grid dimensions:** 1440 × 721
- **Vertical levels:** 13 pressure levels — 1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa (the GraphCast-canonical set, identical to AIGFS)
- **Model top:** 50 hPa (highest distributed pressure level)
- **Forecast length:** 384 hours (16 days) — **65 output steps, f000–f384, live-verified for every member and every ensstat product**
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 6 hours (step deltas verified uniform at 6 h)
- **Observed publication latency:** first file of a cycle at ~**T+3 h 52 m**, last at ~**T+4 h 45 m** (a ~53-minute transfer window), measured on the 2026-08-06 12 UTC cycle from NOMADS directory timestamps. Ensemble statistics complete at roughly the same time as the members (T+3 h 54 m to T+4 h 40 m), not after them.

---

## Data assimilation
- **Data assimilation:** No. AIGEFS performs no assimilation of its own.
- **Initialization:** GDAS-derived analyses with the **GEFS initial-condition perturbations applied** — the same perturbed initial states used by the operational physics-based [GEFS](./gefs.md).

---

## Initial conditions

> *The template's "Initial and boundary conditions" section is scoped to limited-area ensembles. AIGEFS is global and has no lateral boundary conditions; the initial-condition half is recorded here and the boundary half is not applicable.*

- **Source:** NCEP 0.25° GDAS analysis with operational GEFS initial-condition perturbations. As with all GraphCast-lineage models, each member requires **two consecutive analysis times** (T and T−6 h) as input.
- **Boundary conditions:** Not applicable (global domain).

---

## Perturbations and design
- **Initial condition perturbations:** Inherited directly from operational [GEFS](./gefs.md) — AIGEFS uses the same perturbed initial conditions rather than generating its own. Whether AIGEFS member *n* corresponds to GEFS member *n* is not stated in the NOMADS description and is not encoded in the files (**TBD**); this matters directly for [HGEFS](./hgefs.md), which combines the two member sets into one 62-member grand ensemble.
- **Model/physics perturbations:** Not applicable in the conventional sense — AIGEFS has no parametrizations to perturb. Model uncertainty is instead represented by **running members with different sets of learned model weights generated during the training process**. This is the AI analogue of a multi-physics or multi-model ensemble: spread arises from genuinely different trained networks, not from stochastic tendencies applied to one network.
- **Stochastic schemes:** None. There is no SPP/SPPT-family scheme, and (unlike [AIFS ENS](../eu/aifs-ens.md)) no inference-time noise injection into the network — the model-uncertainty mechanism is the weight-set variation described above.

### Member packaging (verified)
- **Packaging:** One directory per member, `mem000/` … `mem030/`, each holding a complete `model/atmos/grib2/` tree of 130 GRIB2 files (65 `pres` + 65 `sfc`) plus 130 `.idx` sidecars. There is no concatenated multi-member file.
- **GRIB2 encoding:** `productDefinitionTemplateNumber = 1` (individual ensemble forecast, control and perturbed), `typeOfProcessedData = pf`, `perturbationNumber` = member index (0–30), **`numberOfForecastsInEnsemble = 31`** — an accurate declaration, unlike the misdeclared counts in [NAEFS](./naefs.md).

> ⚠️ **No member is flagged as the control.** Every member — `mem000` included — carries `typeOfEnsembleForecast = 6` (verified on `mem000` and `mem015`), rather than `mem000` carrying one of the standard control codes (0 or 1 in GRIB2 Code Table 4.6). Control status must therefore be inferred from `perturbationNumber = 0` / the `mem000` directory name, not read from the ensemble-type field. The exact meaning of code 6 in the table revision NCEP is encoding against was not resolved (**TBD**); what is verified is that it is uniform across members and does not distinguish a control.

---

## What it provides

Output is split per step into a pressure-level file (`pres`) and a surface file (`sfc`), for each member and for each ensemble statistic.

**Pressure-level fields — 78 records at every step, f000 through f384 (6 variables × 13 levels):**

| GRIB2 name | Description |
|---|---|
| `HGT` | Geopotential height |
| `TMP` | Temperature |
| `UGRD` | U (zonal) wind component |
| `VGRD` | V (meridional) wind component |
| `VVEL` | Vertical velocity (pressure) |
| `SPFH` | Specific humidity |

**Surface fields — 4 records at f000, 5 records at f006–f384:**

| GRIB2 name | Level | Steps |
|---|---|---|
| `UGRD` | 10 m above ground | f000–f384 |
| `VGRD` | 10 m above ground | f000–f384 |
| `TMP` | 2 m above ground | f000–f384 |
| `PRMSL` | Mean sea level | f000–f384 |
| `APCP` | Surface | f006–f384 — 6-hour accumulation bucket only |

> ⚠️ **AIGEFS carries one fewer surface field than AIGFS, and the difference is precipitation.** The deterministic [AIGFS](../../../nwp_models/global/usa/aigfs.md) distributes **two** `APCP` records per surface file — a 6-hour bucket *and* a run-total accumulation since initialization. AIGEFS distributes **only the 6-hour bucket**. Users porting AIGFS precipitation code to AIGEFS will not find the run-total field, and must accumulate the 6-hour buckets themselves. This applies to the ensemble mean and spread products as well as to the raw members.

### Ensemble statistics products
Two derived products are distributed under `ensstat/products/atmos/grib2/`, each covering the full 65-step range with **exactly the same parameter set as the members**:

| Product | File suffix | GRIB2 encoding |
|---|---|---|
| Ensemble mean | `.avg.` | PDT 2, `derivedForecast = 0` |
| Ensemble spread (standard deviation) | `.spr.` | PDT 2, `derivedForecast = 2` |

**No ensemble probability, percentile, or cluster products are distributed** — only raw members, mean, and spread. Probabilistic guidance beyond mean and spread must be derived by the user from the members.

### GRIB2 encoding (live-decoded, ecCodes 2.48.0)
- **Edition:** 2, `tablesVersion = 2`
- **Centre:** `kwbc` (NCEP), `subCentre = 2`
- **Generating process identifier:** **138** (AIGEFS; AIGFS is 137, HGEFS is 139)
- **Type of generating process:** 4 (ensemble forecast)
- **Packing:** `grid_complex_spatial_differencing`, `bitsPerValue` varying per field (10–11 observed on surface files)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data formats:** GRIB2, with a companion `.idx` byte-range index for every file
- **Official download location:**
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/aigefs/prod/

  File layout:
  - **Members:** `aigefs.YYYYMMDD/CC/memMMM/model/atmos/grib2/aigefs.tCCz.[pres,sfc].fFFF.grib2[.idx]`
  - **Statistics:** `aigefs.YYYYMMDD/CC/ensstat/products/atmos/grib2/aigefs.tCCz.[pres,sfc].[avg,spr].fFFF.grib2[.idx]`

  where `CC` is the cycle (00/06/12/18), `MMM` is the member (000–030), and `FFF` is the forecast hour (000–384 in steps of 6). The `prod/` path resolves internally to `aigefs/v1.0/`; users should reference `prod/` rather than pinning the version path.
- **Additional distribution:** NSF Unidata IDD/CONDUIT feed for users with CONDUIT access.
- **Not available via FTP.** Per SCN 25-89, the AI model suite is distributed on the NCEP NOMADS server only.
- **Not available on AWS Open Data / NODD.** See [Notes](#notes) — the EAGLE bucket hosts *experimental* ensemble output, not the operational product.
- **Per-cycle data volume:** **161.88 GiB** across 4,290 GRIB2 files (plus 4,290 `.idx` sidecars — 8,580 objects per cycle):
  - **Members:** 153.76 GiB across 31 × 130 = 4,030 files. Per-member volume 4.91–5.13 GiB (`pres` 73.5–77.8 MiB per file, `sfc` 2.6–3.6 MiB).
  - **Ensemble statistics:** 8.12 GiB across 260 files — `pres.avg` 4.17 GiB, `pres.spr` 3.60 GiB, `sfc.avg` 0.19 GiB, `sfc.spr` 0.16 GiB.

  All 31 member directories were enumerated individually; this is a measured total, not an extrapolation. At four cycles a day this is roughly **650 GiB/day**, about 27× the deterministic AIGFS volume.
- **Total GRIB2 messages per cycle:** 178,002 (167,214 across the 31 members, 10,788 across the two statistics products).
- **Retention:** **Only 2 daily directories** on NOMADS as of 2026-08-06 (`aigefs.20260805`, `aigefs.20260806`) — substantially shorter than the ~10-day window for [AIGFS](../../../nwp_models/global/usa/aigfs.md), and consistent with the 27× larger daily volume. **Anyone building a workflow on AIGEFS should assume roughly a 48-hour window and mirror promptly.** There is no NOAA-operated public archive of operational AIGEFS output.
- **NCEP Product Inventory:** https://www.nco.ncep.noaa.gov/pmb/products/aigefs/ (note: the NCEP inventory pages for the AI suite have not been refreshed since December 2025)

---

## Relationship to other models

### Within NOAA's AI program
- **[AIGFS](../../../nwp_models/global/usa/aigfs.md)** (operational) — the deterministic GraphCast-lineage sibling, sharing grid, level set, cycle schedule, and forecast length. Now at v1.1 while AIGEFS remains v1.0.
- **[HGEFS](./hgefs.md)** (operational) — the hybrid grand ensemble combining the 31 AIGEFS AI members with the 31 [GEFS](./gefs.md) physics members into a 62-member system. AIGEFS is a direct input to HGEFS.
- **EAGLE Ensemble** (decommissioned operationally) — the experimental predecessor AIGEFS replaced; ran from 00 UTC April 29, 2025 to 18 UTC December 17, 2025.

### Companion physics-based system
- **[GEFS](./gefs.md)** (operational) — the physics-based global ensemble AIGEFS emulates. AIGEFS uses GEFS's perturbed initial conditions and matches its 31-member size and 4× daily cadence. The two are intended to be used together; AIGEFS does not replace GEFS.

### Architectural peers
- **[AIFS ENS](../eu/aifs-ens.md)** (ECMWF, operational) — the closest international counterpart, and a useful contrast in ensemble design: AIFS ENS is a single trained network whose spread comes from inference-time noise injection and CRPS/multi-scale probabilistic training, whereas AIGEFS derives spread from multiple distinct weight sets plus inherited GEFS initial-condition perturbations.

---

## Notes
- **The AWS EAGLE bucket carries experimental ensemble output, not operational AIGEFS.** `noaa-nws-graphcastgfs-pds` contains `EAGLE_ensemble/aigefs.YYYYMMDD/` prefixes, still actively updated (through 2026-08-06). As with the `aigfs.*` prefixes documented in the [AIGFS entry](../../../nwp_models/global/usa/aigfs.md#notes), these are development forecasts supporting ongoing EAGLE work and should not be treated as an operational archive. No operational AIGEFS bucket exists on NODD — `noaa-nws-aigefs-pds` and `noaa-aigefs-pds` both return 404.
- **Member file sizes increase monotonically with member number** across the cycle, from 4.91 GiB (`mem000`) to 5.13 GiB (`mem030`), a smooth ~4.5% rise with no discontinuity. Since all members carry an identical record count and parameter set, this reflects compression entropy rather than content. Whether it indicates that members are ordered by perturbation magnitude, or by weight-set variant, is not documented (**TBD**) — but it does mean per-member storage estimates should not be taken from `mem000` alone.
- AIGEFS inherits AIGFS's **limited parameter set**: no cloud variables, radiation fluxes, soil fields, ozone, or the broader land/aviation package that GEFS provides. Pipelines built around GEFS's parameter breadth cannot switch to AIGEFS without gap-filling.
- **Stratospheric coverage is limited** — the highest level is 50 hPa, so sudden stratospheric warmings and related stratospheric processes are poorly represented, as in AIGFS and AIFS Single v1.
- Due to the non-determinism of GPU computation, users running the underlying weights themselves cannot exactly reproduce NOAA's official member forecasts.
- AIGEFS is part of **Project EAGLE**, the joint NOAA Research / EPIC / NWS effort that also produced AIGFS and HGEFS and continues to develop AI-based regional and sub-seasonal-to-seasonal systems.
- See [`AI_MODELS.md`](../../../../AI_MODELS.md) for the repository-wide index of AI-based forecast systems.

---

## Recent version history

### v1.0 — operational 12 UTC, December 17, 2025 (SCN 25-89)
First operational implementation, jointly with [AIGFS](../../../nwp_models/global/usa/aigfs.md) and [HGEFS](./hgefs.md). Established the NOMADS-only distribution, the `memMMM/` + `ensstat/` directory split, the `aigefs.tCCz.[pres,sfc][.avg|.spr].fFFF.grib2` naming convention, and generating process ID 138.

No AIGEFS upgrade has been announced since. The AIGFS v1.1 upgrade of July 27, 2026 (SCN 26-68) applied to the deterministic system only.

---

## Status
- AIGEFS is **operational** at version **v1.0** as of 12 UTC on December 17, 2025.
- It runs alongside, not as a replacement for, the physics-based [GEFS](./gefs.md), and feeds [HGEFS](./hgefs.md).
- No operational successor to v1.0 has been announced as of August 2026. Continued development is staged as `AIGEFSdev{X.Y}` experiments in the EAGLE AWS bucket.

---

## Official documentation
- NCEP AIGEFS product inventory: https://www.nco.ncep.noaa.gov/pmb/products/aigefs/
- SCN 25-89 (v1.0 implementation of AIGFS, AIGEFS and HGEFS): https://www.weather.gov/media/notification/pdf_2025/scn25-89_AIGFS_AIGEFS_and_HGEFS.pdf
- NOAA press release (December 2025): https://www.noaa.gov/news-release/noaa-deploys-new-generation-of-ai-driven-global-weather-models
- EPIC EAGLE AI overview: https://epic.noaa.gov/ai/eagle-overview/
- EPIC EAGLE AI changelog: https://epic.noaa.gov/ai/eagle-change-log/
- EAGLE experimental ensemble data on AWS: https://registry.opendata.aws/noaa-nws-graphcastgfs-pds/
- NSF Unidata announcement: https://www.unidata.ucar.edu/news/ai-driven-global-model-output-availability

### Key references
- Wang, J., S. S. Tabas, B. Fu, L. Cui, Z. Zhang, L. Zhu, J. Peng, and J. R. Carley (2025). *Development of a Hybrid ML and Physical Model Global Ensemble System.* NCEP Office Note 522. https://doi.org/10.25923/7kpr-5e68
- Tabas, S. S., et al. (2025). *GFS-Powered Machine Learning Weather Prediction: A Comparative Study on Training GraphCast with NOAA's GDAS Data for Global Weather Forecasts.* NCEP Office Note 521. https://doi.org/10.25923/xd3y-wy31
- Lam, R., et al. (2023). *Learning skillful medium-range global weather forecasting.* Science, 382(6677), 1416–1421. https://doi.org/10.1126/science.adi2336
