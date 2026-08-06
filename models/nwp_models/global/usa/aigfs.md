# AIGFS (Artificial Intelligence Global Forecast System)

## What this model is
The Artificial Intelligence Global Forecast System (AIGFS) is NOAA's operational machine-learning-based global deterministic weather forecast model.

It is a fine-tuned productionization of Google DeepMind's GraphCast architecture (Lam et al., 2023), retrained by NOAA's Environmental Modeling Center (EMC) using NOAA's own Global Data Assimilation System (GDAS) analyses together with ECMWF ERA5 and HRES as training targets. Unlike traditional physics-based NWP, AIGFS does not solve the equations of fluid dynamics explicitly; instead it predicts atmospheric evolution directly from learned patterns in historical weather data, producing 16-day forecasts in roughly 40 minutes on GPU hardware.

AIGFS is the operational descendant of NOAA's experimental EAGLE SOLO program (itself based on the experimental GraphCastGFS system) and runs alongside the physics-based [GFS](./gfs.md) as a complement, not a replacement. It became operational at 12 UTC on December 17, 2025, alongside its ensemble counterparts [AIGEFS](../../../ensemble_models/global/usa/aigefs.md) and [HGEFS](../../../ensemble_models/global/usa/hgefs.md), as the first AI-based global weather forecast systems implemented by NOAA in operations.

The current operational version is **AIGFS v1.1**, implemented at 12 UTC on July 27, 2026 (SCN 26-68). See [Recent version history](#recent-version-history).

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country / region:** United States
- **Development:** NOAA Environmental Modeling Center (EMC), in collaboration with NOAA research laboratories and the Earth Prediction Innovation Center (EPIC) in OAR, under Project EAGLE (Experimental AI Global and Limited-area Ensemble forecast system)
- **Architecture origin:** Derived from Google DeepMind's GraphCast (Lam et al., 2023)

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Uniform latitude–longitude grid (`regular_ll`), **1440 × 721 = 1,038,240 points**, 0.25° in both directions, first grid point 90.0°N / 0.0°E, last grid point 90.0°S / 359.75°E, `scanningMode = 0`. Live-decoded with ecCodes 2.48.0 from the 2026-08-06 12 UTC cycle.

---

## Basic details
- **Model type:** Global deterministic NWP (AI-based)
- **Model system / core:** GraphCast-derived graph neural network (GNN) in an encode–process–decode configuration, ~37 million learned parameters
- **Dynamical formulation:** Not applicable — AIGFS has no dynamical core and does not integrate the primitive equations. Forecast evolution is a learned autoregressive mapping between successive atmospheric states.
- **Convection-allowing:** No (no explicit or parameterized convection; precipitation is a directly predicted field)
- **Horizontal resolution:** 0.25° (~28 km)
- **Grid dimensions:** 1440 × 721
- **Vertical levels:** 13 pressure levels (see [Vertical structure and variables](#vertical-structure-and-variables))
- **Model top:** 50 hPa (highest distributed pressure level)
- **Forecast length:** 384 hours (16 days) — **65 output steps, f000–f384, live-verified**
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 6 hours (step deltas verified uniform at 6 h across all 65 steps)
- **Forecast timestep:** 6 hours (autoregressive)
- **Initialization:** GDAS analysis (the same analysis stream that initializes operational GFS)
- **Computational efficiency:** A complete 16-day global forecast runs in approximately 40 minutes on GPU hardware, requiring approximately 0.3% of the computing resources used by the operational physics-based [GFS](./gfs.md)
- **Observed publication latency:** first file of a cycle at ~**T+3 h 37 m**, last file at ~**T+4 h 30 m** (a **53-minute** transfer window), measured on the 2026-08-06 12 UTC cycle from NOMADS directory timestamps.

---

## Data assimilation
AIGFS does **not** perform its own data assimilation. It is initialized from analyses produced by NOAA's Global Data Assimilation System ([GDAS](./gfs.md#data-assimilation)), the same hybrid 4DEnVar analysis stream that initializes operational GFS. This means AIGFS forecasts inherit any analysis-quality differences from GDAS, and pre- and post-GDASv17 (proposed October 2026) AIGFS forecasts will be initialized from materially different analyses.

GraphCast-style models like AIGFS require **two consecutive analysis times** (T and T−6 h) to produce a forecast — the architecture uses both as input to step the forecast forward in 6-hour increments. A single analysis time is not sufficient to initialize the model.

---

## Vertical structure and variables

### Pressure levels (13)
1000, 925, 850, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa

This 13-pressure-level vertical structure is the canonical GraphCast operational configuration, identical to the one used by GraphCastGFS, ECCC's [GEML](../canada/gdps-geml.md), and ECMWF's [AIFS Single](../eu/aifs-single.md) v1 (which has since added a 14th level at 10 hPa in v2).

### Distributed fields (live-verified, 2026-08-06 12 UTC)

Output is split across two files per step: a pressure-level file (`pres`) and a surface file (`sfc`).

**Pressure-level file — 78 records at every step, f000 through f384 (6 variables × 13 levels):**

| GRIB2 name | Description |
|---|---|
| `HGT` | Geopotential height |
| `TMP` | Temperature |
| `UGRD` | U (zonal) wind component |
| `VGRD` | V (meridional) wind component |
| `VVEL` | Vertical velocity (pressure) |
| `SPFH` | Specific humidity |

**Surface file — 4 records at f000, 6 records at f006–f384:**

| GRIB2 name | Level | Steps | Notes |
|---|---|---|---|
| `UGRD` | 10 m above ground | f000–f384 | |
| `VGRD` | 10 m above ground | f000–f384 | |
| `TMP` | 2 m above ground | f000–f384 | |
| `PRMSL` | Mean sea level | f000–f384 | |
| `APCP` | Surface | f006–f384 | 6-hour accumulation bucket (`0-6`, `18-24`, `378-384 hour acc`) |
| `APCP` | Surface | f006–f384 | Run-total accumulation from f000 (`0-6 hour`, `0-1 day`, … `0-16 day acc`) |

> ⚠️ **Two `APCP` records per surface file, and they are not interchangeable.** Each surface file from f006 onward contains *both* a 6-hour accumulation bucket and a run-total accumulation since initialization. They share the same GRIB2 short name and differ only in the time-range indicator. At f006 the two are numerically identical (both cover 0–6 h) and appear in the index as two `0-6 hour acc fcst` lines, which readers that key on parameter name alone will silently collapse or mis-select. Decoders should discriminate on `stepRange` / `startStep`, not on `shortName`.

**Total messages per cycle:** 5,458 (78 × 65 pressure-level records, plus 4 + 6 × 64 surface records).

### GRIB2 encoding (live-decoded, ecCodes 2.48.0)
- **Edition:** 2, `tablesVersion = 2`
- **Centre:** `kwbc` (NCEP), `subCentre = 0`
- **Generating process identifier:** **137** (AIGFS; AIGEFS and HGEFS use 138 and 139 respectively)
- **Type of generating process:** 2 (forecast)
- **Packing:** `grid_complex_spatial_differencing`, with `bitsPerValue` varying per field (9–15 observed on the surface file)

Compared with the physics-based [GFS](./gfs.md) (which provides ~30 isobaric levels and a much wider parameter set), AIGFS is a deliberately stripped-down dataset focused on the variables the GraphCast architecture directly predicts.

---

## What it provides
Deterministic global forecasts of:
- Temperature (2 m and upper-air pressure levels)
- Wind components (10 m and upper-air, including vertical velocity)
- Geopotential height
- Specific humidity
- Mean sea level pressure
- Total precipitation (6-hourly buckets and run-total accumulation)

Forecasts are output every 6 hours out to 16 days ahead.

AIGFS is intended primarily for **synoptic-scale and large-scale forecasting**, including improved tropical cyclone track guidance. AIGFS does not include cloud variables, radiation fluxes, soil moisture/temperature, ozone, wave forecasts, or the broader land/aviation/composition fields that GFS provides — users with downstream pipelines built around GFS's wider parameter set will need to either continue using GFS or merge AIGFS output with GFS for missing variables.

---

## Performance characteristics

### Strengths (per NOAA evaluation)
- **Synoptic-scale skill:** Significantly improved synoptic patterns compared to operational GFS at medium range
- **Tropical cyclone track:** Reduced track errors at longer forecast lead times — one of the headline results from the EAGLE program
- **Tropical cyclone intensity (v1.1):** The v1.1 upgrade specifically targeted and improved TC intensity forecasts, addressing the most-cited weakness of v1.0
- **Precipitation (v1.1):** Improved forecast precipitation relative to v1.0
- **Blurring at long leads (v1.1):** The spherical harmonic loss function reduces the smoothing of fields at longer lead times that affected v1.0
- **Computational efficiency:** ~0.3% of GFS computing resources for an equivalent forecast, enabling far faster delivery of guidance to forecasters

### Known limitations
- **Limited variable set:** As listed above, AIGFS does not produce many fields downstream pipelines have historically expected from operational global guidance.
- **Stratospheric coverage:** With the highest pressure level at 50 hPa, AIGFS has limited ability to represent stratospheric processes including sudden stratospheric warmings — comparable to AIFS Single v1 (before its v2 upgrade added a 10 hPa level) and GEML.
- **Distribution tails:** AI weather models of this class under-predict extreme values relative to physics-based guidance. The v1.1 loss-function change mitigates but does not eliminate this.
- **Complement, not replacement:** NOAA continues to operate AIGFS alongside GFS rather than in place of it.

---

## Relationship to other models

### Lineage within NOAA's AI program
- **GraphCastGFS** (NCEP, experimental) — earlier productionization that AIGFS evolved from. GraphCastGFS v2.0 introduced GDAS-based fine-tuning; AIGFS v1.0 inherited this approach with refinements. The `graphcastgfs.*` experimental stream on AWS ran from 2024-02-05 and ended 2026-05-05.
- **EAGLE SOLO** (decommissioned operationally) — the experimental demonstration system that AIGFS replaced. EAGLE SOLO v1.0 ran from April 24, 2024 to December 18, 2025.
- **[AIGEFS](../../../ensemble_models/global/usa/aigefs.md)** (operational) — the 31-member AI-based ensemble counterpart to AIGFS, implemented on the same date.
- **[HGEFS](../../../ensemble_models/global/usa/hgefs.md)** (operational) — the hybrid "grand ensemble" combining 31 GEFS physics members with 31 AIGEFS AI members, also implemented on the same date.

### Architectural peers (GraphCast family)
AIGFS is part of a broader family of operational productionizations of the GraphCast architecture. All share the same ~37M-parameter GNN architecture and the 13-pressure-level vertical structure, but differ in training data, fine-tuning procedures, and operational role:
- **GraphCast** (Google DeepMind, 2023) — the original research architecture
- **GraphCastGFS** (NCEP, experimental) — predecessor of AIGFS
- **[GEML](../canada/gdps-geml.md)** (ECCC, experimental) — Canadian productionization, fine-tuned on ERA5 + ECMWF HRES analyses; also serves as the spectral nudging target for ECCC's operational hybrid [GDPS](../canada/gem-global.md) (since v10.0.0, May 26, 2026)
- **AIGFS** (NOAA, operational; this entry)

### Architectural peers (different lineages)
- **[AIFS Single](../eu/aifs-single.md)** (ECMWF, operational) — encoder-processor-decoder architecture with attention-based GNN encoder/decoder and sliding-window transformer processor; trained with the Anemoi framework
- **FourCastNetGFS** (NCEP, experimental) — productionization of NVIDIA's FourCastNet using Spherical Fourier Neural Operators

### Companion physics-based system
- **[GFS](./gfs.md)** (NOAA, operational) — the physics-based global model that AIGFS complements. AIGFS is initialized from the same GDAS analyses and runs at the same 4× daily cadence; the two systems are intended to be used together rather than as substitutes.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data formats:** GRIB2, with a companion `.idx` byte-range index for every file
- **Official download location:**
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/aigfs/prod/

  File layout: `aigfs.YYYYMMDD/CC/model/atmos/grib2/aigfs.tCCz.[pres,sfc].fFFF.grib2[.idx]`, where `CC` is the cycle (00/06/12/18) and `FFF` is the forecast hour (000–384 in steps of 6).

  The `prod/` path resolves internally to a version-numbered tree — currently `aigfs/v1.1/`, visible in the directory listing title. The `v1.0/` tree still exists but returns HTTP 403; users should always reference `prod/` rather than a pinned version path.
- **Additional distribution:** NSF Unidata IDD/CONDUIT feed for users with CONDUIT access; appears in IDV's default catalog as "Artificial Intelligence GFS - Global Coverage"
- **Not available via FTP.** Per SCN 25-89, AIGFS is distributed on the NCEP NOMADS server only.
- **Not available on AWS Open Data / NODD.** See [Notes](#notes) — the `noaa-nws-graphcastgfs-pds` bucket hosts *experimental* AIGFS development output, not the operational product.
- **Per-cycle data volume:** **5.62 GiB (6.04 GB)** across 130 GRIB2 files — 65 `pres` files at 80.7–84.9 MiB each (5.33 GiB total) and 65 `sfc` files at 3.5–4.9 MiB each (304 MiB total), plus 130 `.idx` files. Live-measured on the 2026-08-06 12 UTC cycle.
- **Retention:** ~10 daily directories on NOMADS (rolling). There is no NOAA-operated public archive of operational AIGFS output beyond this window.
- **NCEP Product Inventory:** https://www.nco.ncep.noaa.gov/pmb/products/aigfs/ (note: this page has not been refreshed since December 2025 and does not reflect v1.1)

---

## Notes

### The AWS `noaa-nws-graphcastgfs-pds` bucket is not an operational mirror
The AWS Open Data bucket `noaa-nws-graphcastgfs-pds` (registered as "NOAA EAGLE Global Deterministic and Ensemble Forecasts", CC0) contains `aigfs.YYYYMMDD/` prefixes using the same directory layout and filenames as operational NOMADS output, dating from 2026-04-16 onward. **These are experimental development forecasts, not the operational product**, and treating them as an operational archive will produce silently different results. Live comparison of the same forecast day:

| | NOMADS (operational v1.1) | AWS `aigfs.*` (experimental) |
|---|---|---|
| `pres` records at f000 | 78 (includes `VVEL`) | 65 (no `VVEL` at analysis time) |
| `pres` records at f024+ | 78 | 78 |
| `sfc` records at f024 | 6 | 12 |
| Additional surface fields | — | 2 m `DPT`, 100 m `UGRD`/`VGRD`, surface `TMP`, surface `PRES`, `ACPCP` |
| `pres.f024` file size | ~83.3 MiB | ~68.5 MiB |

Supporting evidence that this is a development stream:
- The AWS surface parameter set expanded from 6 to 12 records at **2026-07-29 12 UTC** — two days *after* the v1.1 operational implementation, and with no corresponding change on NOMADS.
- A parallel `test/aigfs.YYYYMMDD/` tree exists in the same bucket covering the same dates with the same record counts but different byte sizes, mirroring the registry's documented `forecast_13_levels` / `forecast_13_levels_test` split.
- The AWS registry description states the bucket continues to host experimental forecasts supporting ongoing EAGLE global and S2S development, and documents only the `graphcastgfs.*` and `EAGLE_ensemble/` prefixes — the `aigfs.*` prefixes are undocumented there.
- On the AWS files, `ACPCP` is encoded with `bitsPerValue = 0` (a constant all-zero field), and `subCentre` is inconsistent between files (0 on `sfc.f000`, 2 on `pres`/`sfc` forecast steps). Neither is true of operational NOMADS output.

No operational AIGFS bucket exists on NODD: `noaa-nws-aigfs-pds` and `noaa-aigfs-pds` return 404, and `noaa-gfs-bdp-pds` carries no `aigfs.*` prefixes. (A `noaa-hgefs-pds` bucket does exist and is relevant to the [HGEFS](../../../ensemble_models/global/usa/hgefs.md) entry.)

> **TBD — encoding difference.** Operational NOMADS `pres` files are consistently ~20% larger than the AWS experimental files at identical record counts (83.3 MiB vs 68.5 MiB at f024). Both use `regular_ll` on the same grid with `grid_complex_spatial_differencing`, so the difference is a per-field precision (`bitsPerValue`) or scaling choice between the operational and development encoders. Packing was decoded directly on the AWS files and on the operational surface file; the operational `pres` file has not been decoded to confirm the cause.

### Other notes
- AIGFS is a **stripped-down dataset compared to GFS**. Users running WRF or other downstream models that depend on AIGFS for boundary conditions have reported that the limited variable set is insufficient on its own and have had to merge AIGFS data with GFS to fill gaps. Users planning to swap AIGFS in as a drop-in replacement for GFS in existing pipelines should verify their required variables are actually produced.
- The `forecast_13_levels` naming convention used in NOAA's earlier GraphCastGFS distribution reflects the GraphCast operational configuration (13 pressure levels), distinguishing it from earlier 37-level GraphCast experiments that NOAA decommissioned for storage and accuracy reasons.
- The pressure-level structure (1000 to 50 hPa, 13 levels) is the GraphCast-canonical set and is identical to the levels used by GraphCastGFS, ECCC's [GEML](../canada/gdps-geml.md), and AIFS Single v1. ECMWF's AIFS Single v2 (May 2026) extended this to 14 levels by adding 10 hPa for stratospheric coverage. AIGFS v1.1 retains the 13-level configuration; whether future AIGFS versions follow AIFS into the stratosphere has not been publicly stated.
- **Ongoing AIGFS development continues in the EAGLE bucket**, versioned as `AIGFSdev{X.Y}`. AIGFSdev2.1 (started 19 December 2025) introduced the spectral harmonic loss function that subsequently reached operations in v1.1. Later experiments are present as `hindcast_aigfsdev2.5/` and `hindcast_amse12/` hindcast sets. These are research iterations, not operational output.
- Due to the non-determinism of GPU computation, users running AIGFS or its underlying GraphCast weights themselves cannot exactly reproduce NOAA's official forecasts.
- AIGFS (along with AIGEFS and HGEFS) is part of the broader **Project EAGLE**, a joint effort between NOAA Research Laboratories, EPIC (within OAR), and the National Weather Service. EAGLE continues to develop AI-based regional, sub-seasonal, and seasonal forecast systems beyond the operational AIGFS / AIGEFS / HGEFS suite.
- See [`AI_MODELS.md`](../../../../AI_MODELS.md) for the repository-wide index of AI-based forecast systems.

---

## Recent version history

### v1.1 — operational 12 UTC, July 27, 2026 (SCN 26-68)
Three changes to the training and fine-tuning procedure:
1. The grid-point mean squared error (MSE) loss function was replaced with a **spherical harmonic loss function**, using wind speed and wind direction rather than standard u- and v-components.
2. The model was fine-tuned using **four years of GDAS analysis**.
3. The fine-tuning process was updated to train the model **autoregressively to a 72-hour lead time**.

Together these were reported to improve tropical cyclone intensity forecasts, improve forecast precipitation, and reduce the blurring of fields at longer lead times. The distributed parameter set, grid, cycle schedule, and forecast length were unchanged from v1.0 — v1.1 is a model-quality upgrade, not a product-content upgrade.

### v1.0 — operational 12 UTC, December 17, 2025 (SCN 25-89)
First operational implementation, alongside AIGEFS and HGEFS. Established the NOMADS-only distribution, the `aigfs.tCCz.[pres,sfc].fFFF.grib2` naming convention, and generating process IDs 137/138/139 for AIGFS/AIGEFS/HGEFS. The implementation was preceded by an evaluation window opened on December 9, 2025, during which NOAA distributed evaluation products via NOMADS so external users could compare AIGFS output against legacy baselines before cutover.

---

## Status
- AIGFS is **operational** at version **v1.1** as of 12 UTC on July 27, 2026.
- It runs alongside, not as a replacement for, the operational physics-based [GFS](./gfs.md).
- Future AIGFS upgrades continue to be staged through experimental AIGFSdev configurations in the EAGLE AWS bucket; no successor to v1.1 has been announced as of August 2026.

---

## Official documentation
- NCEP AIGFS product inventory: https://www.nco.ncep.noaa.gov/pmb/products/aigfs/
- SCN 26-68 (v1.1 implementation): https://www.weather.gov/media/notification/pdf_2026/scn26-68_AIGFS_v1.1.pdf
- SCN 25-89 (v1.0 implementation of AIGFS, AIGEFS and HGEFS): https://www.weather.gov/media/notification/pdf_2025/scn25-89_AIGFS_AIGEFS_and_HGEFS.pdf
- NOAA press release (December 2025): https://www.noaa.gov/news-release/noaa-deploys-new-generation-of-ai-driven-global-weather-models
- EPIC EAGLE AI overview: https://epic.noaa.gov/ai/eagle-overview/
- EPIC EAGLE AI changelog: https://epic.noaa.gov/ai/eagle-change-log/
- EAGLE experimental / AIGFSdev data on AWS: https://registry.opendata.aws/noaa-nws-graphcastgfs-pds/
- GraphCastGFS documentation: https://graphcastgfs.readthedocs.io/en/latest/index.html
- NSF Unidata announcement: https://www.unidata.ucar.edu/news/ai-driven-global-model-output-availability

### Key references
- Tabas, S. S., J. Wang, W. Lei, M. Row, Z. Zhang, L. Zhu, J. Peng, and J. R. Carley (2025). *GFS-Powered Machine Learning Weather Prediction: A Comparative Study on Training GraphCast with NOAA's GDAS Data for Global Weather Forecasts.* NCEP Office Note 521, 33 pp. https://doi.org/10.25923/xd3y-wy31
- Wang, J., S. S. Tabas, B. Fu, L. Cui, Z. Zhang, L. Zhu, J. Peng, and J. R. Carley (2025). *Development of a Hybrid ML and Physical Model Global Ensemble System.* NCEP Office Note 522.
- Lam, R., et al. (2023). *Learning skillful medium-range global weather forecasting.* Science, 382(6677), 1416–1421. https://doi.org/10.1126/science.adi2336
