# AICON-Global

## What this model is
AICON-Global is DWD's global, machine-learning-based deterministic numerical weather prediction model. It is designed to complement and extend — not replace — the operational physics-based [ICON](./icon-global.md) system.

Unlike ICON, AICON does not integrate explicit equations of motion or physical parameterizations. Instead, it learns the temporal evolution of the atmospheric state directly from DWD's ICON reanalysis (ICON-DREAM), using a GraphCast-like encoder–processor–decoder neural network built on the [Anemoi](https://github.com/ecmwf/anemoi-core) framework. It is the DWD counterpart to ECMWF's [AIFS Single](../eu/aifs-single.md), with which it shares the Anemoi codebase; DWD discontinued its own in-house ML development in June 2024 in favour of the shared Anemoi effort.

AICON-Global was introduced on 3 September 2025 and its forecasts were made available as Open Data in early 2026. DWD's AICON Database Description (version dated 30 June 2026) describes the current release as **AICON v1.0** and refers to it as the operational AI forecasting system (*operationelles KI-Vorhersagesystem*), a change in framing from the 2025 introduction. It remains a complement to, not a component of, the physics-based ICON suite.

---

## Who runs it
- **Organization:** Deutscher Wetterdienst (DWD — German Weather Service)
- **Country / region:** Germany
- **Framework:** Anemoi (collaborative European initiative led by ECMWF; also underpins ECMWF's AIFS and MetNorway's Bris)

---

## What area it covers
- **Coverage:** Global
- **Native grid:** ICON R3B07 triangular icosahedral grid, 2,949,120 cells, ~13 km mesh — byte-identical grid identity to [ICON Global](./icon-global.md). Confirmed from live GRIB2 headers: `gridType = unstructured_grid`, `numberOfDataPoints = 2949120`, `numberOfGridUsed = 26`, `uuidOfHGrid = a27b8de618c411e4820ab5b098c6a5c0`.
- **Regular lat–lon rendering:** produced by DWD but **not distributed on Open Data** — see *Data availability*.

---

## Basic details
- **Model type:** Global deterministic NWP (machine-learning / data-driven)
- **Model version:** AICON v1.0
- **Model system / core:** AICON, built on the Anemoi framework (`anemoi-models`); GraphCast-like encoder–processor–decoder with a Graph-Transformer GNN (multi-head attention message passing) constructed directly on ICON's triangular mesh. Uses a hierarchical multi-mesh built from successive refinement levels of the ICON grid, with skip connections so the network primarily learns forecast *increments* relative to the input state.
- **Dynamical formulation:** Not applicable — data-driven; no explicit dynamical core. Learns atmospheric evolution from the ICON-DREAM reanalysis.
- **Convection-allowing:** No (~13 km grid)
- **Horizontal resolution:** ~13 km (native ICON R3B07 icosahedral grid)
- **Vertical levels:** 13 reduced ICON model levels — see the mapping table below
- **Model top:** AICON level 1 sits at **21,115 m** above sea-level grid points (DWD's own figure). Measured pressure on that level at +24 h from the 2026-08-05 00 UTC run ranges 28.6–50.7 hPa (mean ~46 hPa), so "surface to about 50 hPa" holds as a round description but varies with the tropopause.
- **Forecast step:** 3 h, applied autoregressively (each step uses the model's own previous output as input)
- **Forecast length:**
  - 180 hours for the 00 and 12 UTC cycles (61 output steps, 0–180 h)
  - 120 hours for the 06 and 18 UTC cycles (41 output steps, 0–120 h)
  - 48 hours for the intermediate 03, 09, 15, and 21 UTC cycles (not published on Open Data — see Notes)
- **Update frequency / cycles:** Initialized every 3 hours (8 cycles/day). The Open Data server publishes only the four main cycles: 00, 06, 12, 18 UTC.
- **Temporal output resolution:** 3-hourly

### Vertical level mapping
The distributed GRIB2 encodes AICON levels as `typeOfFirstFixedSurface = 150` (general vertical) with `level` values **1–13** — a compact renumbering, not the ICON native indices. The correspondence is published in the AICON Database Description and cannot be recovered from the files themselves:

| AICON level | ICON Global level | ICON EU nest level | Height above sea grid points (m) |
|---|---|---|---|
| 1 | 49 | 3 | 21,115 |
| 2 | 57 | 11 | 16,694 |
| 3 | 64 | 18 | 14,088 |
| 4 | 70 | 24 | 12,283 |
| 5 | 75 | 29 | 10,783 |
| 6 | 79 | 33 | 9,583 |
| 7 | 86 | 40 | 7,483 |
| 8 | 91 | 45 | 5,983 |
| 9 | 96 | 50 | 4,483 |
| 10 | 101 | 55 | 3,037 |
| 11 | 108 | 62 | 1,421 |
| 12 | 112 | 66 | 739 |
| 13 | 119 | 73 | 42 |

Ordering is top-down: level 1 is the model top, level 13 the lowest. Verified from decoded values — level 1 temperature at +24 h spans 181–231 K, level 13 spans 208–319 K. These are ICON's native SLEVE terrain-following levels, unlike GraphCast-family models that output on pressure levels.

---

## Data assimilation and initialization
AICON-Global does **not** perform its own data assimilation. Inference is initialized from a GRIB2 analysis produced by DWD's operational [ICON](./icon-global.md#data-assimilation) system (hybrid LETKF + EnVar), and the model rolls the forecast forward autoregressively in 3-hour steps.

Since **6 May 2026**, three-dimensional input variables are taken from the *initialized* analysis fields rather than the uninitialized ones used previously — a bugfix that improved consistency with the training data and notably reduced very-short-range surface pressure error.

**Training:** Trained exclusively on the ICON-DREAM reanalysis (Dual-resolution Reanalysis for Emulators, Applications and Monitoring), a 13 km global (6.5 km European nest) reanalysis built on the operational ICON setup with 3-hourly EnVar cycling, covering 2010-01-01 to 2023-12-31. A staged transfer-learning curriculum across mesh resolutions (~50 km → ~25 km → 13 km) was used to keep training cost-efficient. Training ran on the HoreKa supercomputer (A100-40G) at KIT; inference is containerized for multi-GPU deployment and slots into DWD's existing 24/7 NWP process chain.

DOI for ICON-DREAM: `10.5676/dwd/icon-dream_v1`

---

## What it provides
Twelve parameters, all 3-hourly. DWD's Database Description separates them by provenance, which is worth carrying because only ten are actually predicted by the network:

| Provenance | Parameters | Levels |
|---|---|---|
| `inout` — network input **and** output | `T`, `QV`, `U`, `V`, `P` | 13 model levels |
| `inout` — network input **and** output | `T_2M`, `RELHUM_2M`, `U_10M`, `V_10M`, `PS` | 2 m / 10 m / surface |
| `derived` — post-processing | `PMSL` | mean sea level |
| `diag` — AI diagnostic head | `TOT_PREC` | surface |

The Open Data tree carries exactly these twelve — the public set is the complete documented output set, not a subset.

**Step-000 availability is not uniform.** Since the 6 May 2026 extension, the ten `inout` fields carry a step-000 file (the nominal forecast start). `PMSL` (derived) and `TOT_PREC` (diagnostic) do not, and start at +3 h. This is documented behaviour (`vv3h_no0h` in DWD's lead-time nomenclature), not a gap: 61 vs 60 files per parameter for a 180 h run, 41 vs 40 for 120 h.

**`TOT_PREC` accumulates from run start, not per 3-hour step.** DWD's concept text describes the network producing three-hourly accumulated precipitation, but the distributed field is the running total: `stepType = accum` with `stepRange = 0-3`, `0-24`, and so on. Verified from the 2026-08-05 00 UTC run — the +3 h field maxes at 99.8 kg m⁻² and the +24 h field at 438.1 kg m⁻². Differencing consecutive steps is required to recover 3-hourly amounts.

Verification against SYNOP observations shows good near-surface skill in the short range (roughly 0–72 h), with AICON outperforming ICON for near-surface fields at short lead times; ICON remains stronger for surface pressure at longer leads.

---

## Data availability
- **Is the data free?** Yes — anonymous HTTPS, no registration
- **License:** **CC BY 4.0**, attribution required. DWD's legal notice states that all open spatial data and spatial data services of DWD, as well as all DWD services designated as **EU High Value Datasets (HVD)**, may be re-used under CC BY 4.0 with source acknowledgement.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, one message per file, **uncompressed** — plain `.grib2`, unlike the bzip2-wrapped `.grib2.bz2` of the `/weather/nwp/icon/` tree
- **GRIB2 packing:** `grid_ccsds` (CCSDS/AEC lossless), `bitsPerValue = 16`, `missingValue = 9999`. A GRIB library built without libaec/CCSDS support will not decode these files.
- **GRIB2 tables:** `tablesVersion = 33`, `localTablesVersion = 1`, `centre = edzw`. Note this is a **later master-table version than ICON Global's 19** — the two DWD products in the same catalog do not share a tables version.
- **Official download location:**
  - https://opendata.dwd.de/weather/nwp/v1/m/aicon/

### Path structure
Two layouts, depending on whether the parameter is multi-level. This is a common source of 404s: `/p/T/r/…` does not exist.

- **Single-level** (`T_2M`, `RELHUM_2M`, `U_10M`, `V_10M`, `PS`, `PMSL`, `TOT_PREC`):
  `/p/{PARAM}/r/{YYYY-MM-DD}T{HH}%3A00/s/PT{hhh}H{mm}M.grib2`
- **Model-level** (`T`, `QV`, `U`, `V`, `P`):
  `/p/{PARAM}/lvt1/150/lv1/{1..13}/r/{YYYY-MM-DD}T{HH}%3A00/s/PT{hhh}H{mm}M.grib2`

`lvt1` is the first fixed-surface type (`150` = general vertical layer, the only value present); `lv1` is the level value. The run token is an ISO-8601 timestamp with the colon percent-encoded as `%3A` — it must be sent encoded.

Example: `https://opendata.dwd.de/weather/nwp/v1/m/aicon/p/T/lvt1/150/lv1/13/r/2026-08-05T00%3A00/s/PT024H00M.grib2`

This is the same `p/…/r/…/s/…` scheme used by the other `/weather/nwp/v1/m/` products (ICON-ART, ICON-D2-RUC), and differs entirely from the flat `/weather/nwp/icon/grib/<cycle>/<param>/` layout of the older tree.

### Retention, volume, and timing
- **Retention:** four runs, a ~24 h rolling window. The `content.log` manifest captured at 2026-08-05 20:44 UTC held `2026-08-04T18:00` alongside `2026-08-05T00:00`, `06:00`, and `12:00`; the incoming 18 UTC run displaced the oldest shortly after. Because the run timestamp is a directory segment rather than a filename token, runs do not collide during upload — the mixed-run hazard documented for [ICON Global](./icon-global.md) does not apply here.
- **File count:** 4,390 files per 180 h run (5 single-level × 61 steps + 2 × 60 steps + 5 params × 13 levels × 61 steps); 2,950 per 120 h run.
- **Volume:** ~14.3 GB per 180 h run, ~9.5 GB per 120 h run. About **24× smaller than the ICON Global feed** at ~341 GB per 180 h run — a consequence of 12 parameters on 13 levels at 3-hourly steps versus 102 parameters on up to 121 levels at hourly steps.
- **Publication timing:** the run appears ~2 h 53 min after cycle time and uploads in 1.5–3.5 minutes. Measured 2026-08-05: 00 UTC run 02:53:19 → 02:55:56 UTC; 06 UTC 08:54:55 → 08:56:18; 12 UTC 14:53:44 → 14:57:23. The whole product is therefore complete roughly **48 minutes before ICON Global finishes uploading** (T+2h57 vs T+3h45), despite starting later — inference is near-instant and the upload is small. The ~2 h 50 min floor is set by the upstream ICON analysis, not by AICON.

### Regular lat–lon output is produced but not published
DWD's introduction notices for both AICON-Global and AICON-EU state that output on a regular lat–lon grid is "provided as well", and the Database Description lists a second output variant, `r3b7_cells_remapped` ("ICON R3B07 remapped to latlon grid", with grid number and UUID marked `NA`), interpolated using weights from the YAC Coupling Library. DWD's internal SKY database exposes it under the `acrgl` suite (`r` = regular/rotated lat–lon) alongside the native-grid `acogl`.

**None of it reaches Open Data.** The `/weather/nwp/v1/m/aicon/` tree contains a single `p/` branch, and every file under it decodes as `unstructured_grid` on the R3B07 icosahedral mesh — verified by full tree enumeration and header decoding on 2026-08-05.

> **Correction to earlier versions of this entry.** This entry previously listed a regular lat–lon grid under *What area it covers* as "additional output … also provided", following DWD's change-notice wording. That wording describes DWD's internal product suite, not the public distribution. Users needing lat–lon must interpolate the native grid themselves; the CDO weight files DWD ships for ICON Global (`ICON_GLOBAL2WORLD_025_EASY.tar.bz2`, `ICON_GLOBAL2WORLD_0125_EASY.tar.bz2` at https://opendata.dwd.de/weather/lib/cdo/) apply directly, since AICON uses the identical R3B07 grid and grid UUID.

---

## Notes
- **Public data is a subset of the cycles, not of the fields.** AICON-Global is initialized every 3 hours (eight cycles), but Open Data publishes only 00, 06, 12, and 18 UTC. The intermediate 48 h cycles (03/09/15/21 UTC) are not distributed publicly. The twelve parameters published *are* the complete documented output set.
- **AICON-EU exists and is not yet on Open Data.** DWD introduced **AICON-EU** on 30 June 2026 — a limited-area AI model on the ICON-EU domain at ~6.5 km (R3B08 grid, DWD grid number 27, `uuidOfHGrid = ec13b8bc-b82d-11e4-b13f-4d55411d42e6`), same 13 reduced levels, 3-hourly, 120 h at 00/06/12/18 UTC and 48 h at the intermediate cycles. It is initialized from AICON-Global rather than from an analysis, using an embedded-grid training approach in which the global forecast in and around the LAM domain feeds the regional model. It carries the same twelve parameters, including `TOT_PREC` and `PMSL` from the outset. **No `aicon-eu` directory exists under `/weather/nwp/v1/m/` as of 2026-08-05** — worth monitoring for an Open Data release and a separate catalog entry.
- **Roadmap:** beyond AICON-EU, a ~2 km German-domain variant (AICON-LAM DE, matching the ICON-D2 domain) is planned; DWD's SKY nomenclature reserves the `la` domain code for it. DWD's LAM approach merges global and regional reanalysis input datasets rather than using the stretched-grid approach of Nipen et al. (2024).
- **Energy footprint:** a single AICON inference run consumes ~0.13 kWh, versus ~60.24 kWh for a deterministic 180 h ICON Global run — roughly a 460× reduction at inference time (DWD figures, German electricity mix 2023/2024). The publication-timing measurements above are the operational expression of this: the forecast itself is effectively free, and the schedule is bounded by the upstream analysis.
- **`typeOfGeneratingProcess = 2` ("forecast")** in every message, identical to physics-based ICON. Nothing in the GRIB2 header distinguishes AICON output from ICON output as machine-learning-derived — the only discriminators are the file path, the 13-level vertical structure, and `tablesVersion = 33`. Worth flagging for anyone merging DWD GRIB streams.
- **Soil variables are described but not distributed.** DWD's concept text lists "soil variables relevant for memory effects beyond the short range" among the quantities the model predicts, but no soil field appears in either the documented input/output tables or the Open Data tree. Presumably internal model state rather than published output.
- **AI approach:** standalone data-driven forecast model (initialized from a physics analysis, rolled forward by a neural network). Listed in [`AI_MODELS.md`](../../../../AI_MODELS.md) under the standalone-deterministic group, alongside AIFS Single and AIGFS.

---

## Relationship to other models
- **[ICON Global](./icon-global.md)** (DWD, operational) — the physics-based global model AICON complements. AICON is trained on ICON's reanalysis (ICON-DREAM), uses ICON's R3B07 grid (same DWD grid number 26 and same grid UUID) and ICON analyses for initialization, and the publicly distributed cycles (00, 06, 12, 18 UTC) match ICON Global's four main cycles with the same 180/120 h split.
- **AICON-EU** (DWD, operational since 30 June 2026) — the regional nest variant, not yet catalogued and not yet on Open Data. Driven by AICON-Global, not by an analysis.
- **[ICON-EU](../../regional/germany/icon-eu.md)** — the physics-based counterpart to AICON-EU, and the source of the ICON EU nest level indices in the mapping table above.
- **[AIFS Single](../eu/aifs-single.md)** (ECMWF, operational) — architectural and framework peer. Both are built on the Anemoi encoder–processor–decoder stack; AICON applies it on ICON's icosahedral mesh and ICON-level vertical structure rather than ECMWF's lat–lon / pressure-level setup.
- **Bris** (MetNorway) — another Anemoi-based model (extends AIFS); part of the same collaborative framework.

---

## Recent version history

### 30 June 2026 — AICON-EU introduced (effective 06 UTC)
DWD's first regional AI forecast model, on the ICON-EU domain at ~6.5 km, initialized from AICON-Global every 3 hours. 120 h at the four main cycles, 48 h at the intermediate ones. Same twelve parameters and same 13 reduced levels as AICON-Global. The AICON Database Description was re-versioned on the same date to cover both configurations as **AICON v1.0 / AICON-EU v1.0**.

### 6 May 2026 — step-000 output and initial-condition bugfix (effective 09 UTC run)
- Output extended to lead time 0 h for the ten `inout` fields: `T_2M`, `RELHUM_2M`, `U_10M`, `V_10M`, `PS`, and the 13-level `T`, `QV`, `U`, `V`, `P`. `PMSL` and `TOT_PREC` were not included and still start at +3 h.
- Input changed from uninitialized to initialized analysis fields for 3D variables. Main measurable effect was a significant reduction in very-short-range surface pressure error; most other variables were unchanged. DWD's stated motivation was consistency with the training data to reduce the risk of unintended artifacts.

*(DWD indexes this notice under 18 February 2026 while the notice text gives 6 May 2026 as the effective date — the change list and the effective date disagree. The effective date is used here.)*

### 3 September 2025 — AICON-Global introduced (effective 06 UTC)
DWD's first global AI-based forecast model. Cycled every 3 hours in step with deterministic ICON-Global: 180 h at 00/12 UTC, 120 h at 06/18 UTC, 48 h at 03/09/15/21 UTC. Initial parameter set: `T_2M`, `RELHUM_2M`, `U_10M`, `V_10M`, `PS`, `TOT_PREC`, and 13-level `T`, `QV`, `U`, `V`, `P`. `PMSL` was added subsequently and is documented in the current Database Description.

---

## Official documentation
- **AICON Database Description (authoritative field/level reference):** https://aicon-anemoi-inference-database-description-98e462.gitlab-pages.dkrz.de/
- DWD AICON page: https://www.dwd.de/DWD/forschung/nwv/aicon.html
- DWD AICON change notices (Änderungsmitteilungen): https://www.dwd.de/DE/fachnutzer/forschung_lehre/numerische_wettervorhersage/nwv_aenderungen/_functions/DownloadBox_modellaenderungen/nwv_aenderungen_aicon_gesamt.html
- DWD-Geoportal AICON product page: https://www.dwd-geoportal.de/products/aicon/
- DWD Open Data AICON tree: https://opendata.dwd.de/weather/nwp/v1/m/aicon/
- DWD Open Data root and terms: https://opendata.dwd.de/README.txt
- DWD legal notice / licensing (CC BY 4.0, HVD): https://www.dwd.de/EN/service/legal_notice/legal_notice_node.html
- DWD ICON Database Reference Manual (grid and level definitions AICON inherits): https://www.dwd.de/DWD/forschung/nwv/fepub/icon_database_main.pdf
- Anemoi framework: https://github.com/ecmwf/anemoi-core

### Key references
- Prill, F., Jacob, M., & DWD AICON Team (2025). *AICON – Introducing ML-based weather forecasting at DWD.* ECMWF Workshop on HPC in Meteorology, September 2025.
- Ulbrich, S., Prill, F., Keller, J., and the AICON Team (2026). *AICON Database Description — Meteorological Fields.* Deutscher Wetterdienst. Version 962874a, 30 June 2026.
- Lang, S., et al. (2024). *AIFS — ECMWF's data-driven forecasting system.* arXiv. https://doi.org/10.48550/arXiv.2406.01465
- Valmassoi, A., et al. (2024). *ICON-DREAM: A new dual resolution reanalysis from DWD.* 6th WCRP International Conference on Reanalysis. DOI: 10.5676/dwd/icon-dream_v1

---

*Live verification performed 2026-08-05 against `https://opendata.dwd.de/weather/nwp/v1/m/aicon/` (00, 06, 12, 18 UTC cycles of 2026-08-05) and the `/weather/nwp/content.log.bz2` manifest. GRIB2 headers decoded with ecCodes 2.48.0.*
