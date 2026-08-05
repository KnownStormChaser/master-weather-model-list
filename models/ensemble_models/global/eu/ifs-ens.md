# IFS ENS (Integrated Forecasting System – Ensemble)

## What this model is
IFS ENS is ECMWF's flagship global ensemble numerical weather prediction system, providing probabilistic medium-range forecasts and quantifying forecast uncertainty for the 0–15 day range.

It is the ensemble counterpart of the deterministic [IFS](../../../nwp_models/global/eu/ifs.md), built on the same model core, the same 9 km native horizontal resolution (since Cycle 48r1 in June 2023), and the same atmospheric initial analysis. The ensemble comprises **50 perturbed members plus 1 control**, designed to sample uncertainty in both initial conditions (via singular vectors and an Ensemble of Data Assimilations) and in the model itself (via the Stochastically Perturbed Parametrization scheme).

IFS ENS is widely regarded as one of the highest-skill global ensemble forecast systems in operational use. ECMWF distributes a curated subset of ENS output through the free Open Data programme at 0.25° resolution; the full native 9 km ensemble is also available under CC-BY-4.0 (since 1 October 2025) but typically requires service charges for high-volume delivery.

> **Critical change at Cycle 50r1 (12 May 2026).** The control member is **no longer distributed in the `enfo` stream**. Since the ex-HRES deterministic forecast and the ENS control became one and the same product, the control is now published under `stream=oper, type=fc` and documented in the [IFS entry](../../../nwp_models/global/eu/ifs.md). Open Data `enfo` files contain **50 members, not 51**. Anything written against the pre-50r1 layout will silently lose the control member. See [Recent version history](#recent-version-history).

This entry describes the **medium-range ensemble**. The separate 101-member sub-seasonal (extended-range) ensemble at 36 km running daily out to 46 days is a different operational system — see Notes below.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts
- **Country / region:** International (European consortium of 23 Member States and 12 Co-operating States)

---

## What area it covers
- **Coverage:** Global
- **Open Data grid:** 1440 × 721 regular latitude–longitude, 0.25° × 0.25°, 1,038,240 points per field — identical to the deterministic IFS distribution
- **Grid origin (verified):** first grid point 90°N / 180°, scanning mode 0 (west→east, north→south). The longitude axis begins at the dateline in the raw GRIB header. See the [IFS entry](../../../nwp_models/global/eu/ifs.md#what-area-it-covers) for the full explanation of this quirk.

---

## Basic details
- **Model type:** Global ensemble NWP
- **Model system / core:** IFS (hybrid spectral / reduced Gaussian-grid)
- **Dynamical formulation:** Hydrostatic, semi-Lagrangian, semi-implicit time integration
- **Convection-allowing:** No (deep convection is parameterized at 9 km native resolution)
- **Ensemble size:** 51 (1 control + 50 perturbed) — but **only the 50 perturbed members are distributed in the Open Data `enfo` stream**; the control is in `oper`
- **Native horizontal resolution:** ~9 km (Tco1279 octahedral reduced Gaussian grid)
- **Open Data resolution:** 0.25° (~28 km) regular latitude–longitude
- **Vertical levels:** 137 (model); **14 pressure levels in Open Data** — 10, 50, 100, 150, 200, 250, 300, 400, 500, 600, 700, 850, 925, 1000 hPa
- **Soil levels in Open Data:** 4 (`sol` 1–4)
- **Model top:** 0.01 hPa (~80 km)
- **Forecast length (verified):**
  - **360 h (15 days)** for 00 and 12 UTC cycles
  - **144 h (6 days)** for 06 and 18 UTC cycles
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (Open Data, verified):**
  - 00 / 12 UTC: 3-hourly to +144 h, then 6-hourly +150 h to +360 h — **85 step files**
  - 06 / 18 UTC: 3-hourly to +144 h — **49 step files**
- **Observed publication latency:** whole run published as a single batch. Measured 2026-07-30: 00z at 07:40 UTC (**T+7h40m**), 06z at 13:04 UTC (**T+7h04m**), 12z at 19:40 UTC (**T+7h40m**). The ensemble lands consistently ~6 minutes after the deterministic `oper` files of the same cycle, and ~37 minutes after at 06z.

---

## Data assimilation
- **Data assimilation:** Yes — the ensemble is initialized from ECMWF's operational analysis system rather than running its own separate assimilation for the unperturbed state.
- **Method:** 12-hour incremental 4D-Var with weak-constraint formulation, the same system that initializes the deterministic IFS.
- **Ensemble of Data Assimilations (EDA):** A 50-member ensemble of perturbed 4D-Var analyses at TCo639 outer-loop resolution runs alongside, supplying flow-dependent initial-state perturbations to ENS and flow-dependent background error covariances to the deterministic analysis.

---

## Initial conditions

> *The template's "Initial and boundary conditions" section is scoped to limited-area ensembles. IFS ENS is global and has no lateral boundary conditions; the initial-condition half is recorded here and the boundary half is not applicable.*

- **Source:** ECMWF operational analyses produced by the deterministic IFS 4D-Var system — the same analysis used to initialize the deterministic IFS forecast.
- **Boundary conditions:** Not applicable (global domain).

---

## Perturbations and design
- **Initial condition perturbations:** Two complementary methods combined:
  - **Singular vectors (SVs):** Perturbations constructed from the leading singular vectors of the linearized model propagator, identifying the directions of fastest error growth over a finite optimization interval. Computed in the extratropics and in selected tropical regions where tropical cyclones are forecast.
  - **Ensemble of Data Assimilations (EDA):** 50-member ensemble of perturbed 4D-Var analyses at TCo639, providing flow-dependent initial-state perturbations.
- **Model/physics perturbations:** **Stochastically Perturbed Parametrization (SPP)** scheme, targeting uncertain elements within the parametrizations of individual physical processes (documented as 27 perturbed elements as of IFS Cycle 49r1).
- **Stochastic schemes — historical note:** SPP replaced the long-standing **SPPT** (Stochastically Perturbed Parametrization Tendencies) scheme in IFS Cycle 49r1 (12 November 2024). SPPT had been the operational stochastic physics scheme since 1998, with several revisions over its 25+ years in operations. SPP improves physical consistency of the perturbations and particularly helps with boundary-layer underdispersion (relevant for 2 m temperature assimilation).
- **Cycle 50r1 SPP revision:** the SPP configuration was modified to address excessive 10 m wind spread — the previous configuration produced over-dispersion in near-surface wind extremes.
- **Recentering:** Soft re-centring of EDA perturbations is applied alongside the SPP changes from Cycle 49r1 onward.

### Member packaging (verified)
- **Packaging:** All members for a given step are concatenated into a **single GRIB2 file per step** (`...-{step}h-enfo-ef.grib2`), one GRIB message per member per field. There is no per-member file split.
- **Member indexing:** `perturbationNumber` runs **1 to 50**, matching the `number` key in the `.index` sidecar. There is no member 0.
- **GRIB2 encoding:** `productDefinitionTemplateNumber = 1` (individual ensemble forecast), `typeOfProcessedData = pf`, `mars.type = pf`, `tablesVersion = 32`, `grid_ccsds` packing.
- **Records per member:** 170 at every step (9 pressure-level parameters × 14 levels, 36 surface parameters, 2 soil parameters × 4 layers). Total 8,500 GRIB messages per step file.

> ⚠️ **`numberOfForecastsInEnsemble` still reports 51.** Every message in the post-50r1 `enfo` files declares an ensemble size of 51 while the file contains 50 members. The GRIB header was not updated when the control moved to `oper`. Any tool that pre-allocates a member array from this key, or that infers "one member is missing" from it, will misbehave. Size the array from the distinct `perturbationNumber` values actually present, not from the declared count. Note also that `typeOfEnsembleForecast` is set to **255** (missing), so it cannot be used to discriminate control from perturbed either.

---

## What it provides

### Direct model output — `type=ef` files
Per-member forecasts of 47 parameters:

**Pressure levels** (9 parameters × 14 levels): `t`, `u`, `v`, `w`, `q`, `r`, `gh`, `d`, `vo`

**Surface** (36): `2t`, `2d`, `10u`, `10v`, `100u`, `100v`, `10fg` / `10fg3`, `mn2t3` / `mn2t6`, `mx2t3` / `mx2t6`, `msl`, `sp`, `skt`, `lsm`, `tp`, `tprate`, `sf`, `ptype`, `ro`, `sd`, `rsn`, `asn`, `ssr`, `ssrd`, `str`, `strd`, `ttr`, `ewss`, `nsss`, `tcc`, `tcw`, `tcwv`, `mucape`, `sithick`, `zos`, `sve`, `svn`

**Soil** (2 × 4 layers): `sot`, `vsw`

> **The ENS parameter set is a strict subset of the deterministic set.** Diffed against the `oper` step-0 file, ENS is missing exactly four entries: **`z` on pressure levels** (geopotential — only geopotential height `gh` is provided), and the three static surface fields **`z`, `sdor`, `slor`**. IFS ENS therefore carries **no model orography at all**. Workflows needing orography or pressure-level geopotential must take them from the deterministic `oper` step-0 file — a cross-stream dependency that is easy to miss when building an ensemble-only pipeline.

#### Step-dependent parameter availability (verified)
Identical switchover behaviour to the deterministic stream:

| Parameter | Steps 0–90 | Steps 93–144 | Steps 150–360 |
|---|---|---|---|
| Wind gust | `10fg` | `10fg3` | `10fg` |
| Min 2 m temperature | `mn2t3` | `mn2t3` | `mn2t6` |
| Max 2 m temperature | `mx2t3` | `mx2t3` | `mx2t6` |

Parameter count stays at 47 throughout — unlike `oper`, ENS has no step-0-only static fields.

### Derived products — `type=ep` files
Despite the `ep` filename token, these files are **mixed containers holding three distinct MARS types**. This trips people up: requesting "the probability file" gets ensemble mean and spread as well.

| MARS type | Contents | Parameters | Levels | Step convention |
|---|---|---|---|---|
| `em` | Ensemble mean | `gh`, `t`, `ws`, `msl` | `gh` 300/500/1000; `t` 250/500/850; `ws` 250/850; `msl` surface | Instantaneous, matches the main step list |
| `es` | Ensemble spread (standard deviation) | same as `em` | same as `em` | same as `em` |
| `ep` | Event probabilities | `tpg{1,5,10,20,25,50,100}`, `10fgg{10,15,25}`, `ptsa_{gt,lt}_{1,1p5,2}stdev` | precipitation and gust at surface; `ptsa_*` at 850 hPa | `tpg*` / `10fgg*` on **overlapping 24 h windows stepping 12 h** (`0-24`, `12-36`, `24-48`, …); `ptsa_*` instantaneous 12-hourly |

Parameter naming decodes as: `tpg25` = probability of total precipitation exceeding 25 mm; `10fgg15` = probability of 10 m wind gust exceeding 15 m/s; `ptsa_gt_2stdev` = probability of temperature standardized anomaly greater than 2 standard deviations.

Note that **`ws` (wind speed) appears only in the mean/spread products** — the raw member files carry `u`/`v` components, not scalar speed.

> **Mean and spread cover four parameters, not the full set.** For any other field, users must compute their own statistics across the 50 members. Since the control is no longer in `enfo`, a statistically correct 51-member mean also requires fetching `oper` separately and combining.

**File-to-step mapping:** two `-ep` files per cycle. `...-240h-enfo-ep.grib2` covers steps 0–240 h (1,480 records); `...-360h-enfo-ep.grib2` covers steps 246–360 h (520 records). The step in the filename is a range label, not the step of the contained fields.

> ⚠️ **Documentation discrepancy — probability product cycles.** ECMWF's Open Data documentation states that ENS probability products are available "at all times 00, 06, 12 and 18 UTC." Deduplicated directory listings on 2026-07-30 found **`-ep` files only at 00 and 12 UTC**; the 06z and 18z `enfo` directories contain `-ef` and `-tf` files exclusively, with zero `-ep` objects. The documentation correctly restricts the *wave* ensemble's probability products to 00/12 but appears to state the atmospheric case incorrectly. Live observation recorded as authoritative.

### Tropical cyclone tracks — `type=tf` files
BUFR edition 4 trajectory products, published only when tropical cyclones are observed or forecast. One file per cycle: step `360h` at 00/12 UTC, step `144h` at 06/18 UTC.

Verified decode of the 2026-07-30 00z file: 54 BUFR messages, one per tracked feature, master tables version 35, originating centre 98. `numberOfSubsets` **varies per message from 1 to 51** — it is the count of ensemble members carrying that feature, not a fixed ensemble size. Only features tracked by every member reach 51 subsets, consistent with the post-50r1 reduction from 52 to 51 TC ensemble members. Storm identifiers include both real designations (`15W`, `06E`, `07E`) and ensemble-only candidate systems, which use identifiers numbered from 70 upward within each basin (`70W`–`83W`, `70L`–`77L`, and so on).

> **Scope note.** These are point trajectories, not gridded fields, and therefore sit outside this repository's gridded-data scope rule. They are documented here for completeness because they ship in the same directory as the in-scope GRIB2 products, but they are not the reason this entry is catalogued.

---

## Data availability

- **Is the data free?** Yes (Open Data subset); the full Real-time Catalogue is open-licensed but delivery may be charged
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0) plus the ECMWF Terms of Use; attribution required, commercial use and redistribution permitted
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 with CCSDS compression (`grid_ccsds` packing) for gridded fields; BUFR edition 4 for tropical cyclone trajectories; JSON-lines `.index` sidecars for metadata
- **Decoder requirement:** ecCodes 2.42.0 or newer; CCSDS packing additionally requires `libaec` support compiled in

### Free Open Data subset
- **Resolution:** 0.25° only. As of 2026-07-30 no 9 km path exists; the announced 9 km extension has not been delivered.
- **Streams and file types actually present** (verified by enumeration):

| Stream | File type | Cycles | Contents |
|---|---|---|---|
| `enfo` | `-ef.grib2` | 00, 06, 12, 18 | 50 perturbed members, direct model output |
| `enfo` | `-ep.grib2` | **00, 12 only** | Ensemble mean (`em`), spread (`es`), event probabilities (`ep`) |
| `enfo` | `-tf.bufr` | 00, 06, 12, 18 | Tropical cyclone tracks (when active) |

> **Corrects a previous version of this entry**, which listed the Open Data streams as `type=ef / cf / pf / es / ep / tf`. Only three file types exist. `cf` was discontinued at Cycle 50r1; `pf` is the MARS type *inside* the `-ef` files rather than a separate file; `es` and `em` are types *inside* the `-ep` files. The MARS type vocabulary and the Open Data filename vocabulary are different namespaces and do not map one-to-one.

- **Retention on the ECMWF portal:** rolling archive of the most recent 12 forecast runs (~2–3 days).
- **Connection limit:** the portal is capped at 500 simultaneous connections.

### File naming convention
```
[ROOT]/[yyyymmdd]/[HH]z/ifs/0p25/enfo/[yyyymmdd][HH]0000-[step]h-enfo-[type].[format]
```
Example: `20260730/00z/ifs/0p25/enfo/20260730000000-24h-enfo-ef.grib2`

### Access channels
All four channels serve identical files. Verified 2026-07-30 for the ensemble products specifically: the `240h-enfo-ep.index` object retrieved from the ECMWF portal, GCS and the AWS regional endpoint produced matching MD5 checksums (`8fe6f60a5e42...`, 327,471 bytes).

| Channel | Endpoint | Anonymous? | Archive depth |
|---|---|---|---|
| **ECMWF portal** | `https://data.ecmwf.int/forecasts/` | Yes (500-connection cap) | Rolling ~12 runs |
| **AWS S3** | `s3://ecmwf-forecasts` (`eu-central-1`) | Yes — unsigned | 2023-01-18 → present |
| **Azure Blob** | `https://ai4edataeuwest.blob.core.windows.net/ecmwf` | **No — SAS token required** | 2022-01-21 → present |
| **Google Cloud Storage** | `gs://ecmwf-open-data` | Yes — fully unauthenticated | 2023-07-12 → present |

Full access mechanics — the Azure SAS-token exchange, the GCS bucket identity, the archive path-schema changes over time, and the `.index` byte-range retrieval pattern — are documented once in the [IFS entry](../../../nwp_models/global/eu/ifs.md#data-availability) rather than duplicated here.

> ⚠️ **AWS rate-limits the ensemble workload.** Sustained requests to the global endpoint `ecmwf-forecasts.s3.amazonaws.com` returned HTTP 503 `SlowDown` ("Please reduce your request rate") repeatedly during verification, with successes interleaved. The regional endpoint `ecmwf-forecasts.s3.eu-central-1.amazonaws.com` served the same objects without throttling in the same session. This matters far more for ENS than for the deterministic stream: a full 00z ensemble pull is 85 step files against 49 for a 06z deterministic run, and per-member byte-range extraction multiplies request counts by up to 50. Prefer the regional endpoint and implement exponential backoff.

**Byte-range retrieval is especially valuable here.** A single 00z `enfo` step-0 file holds 8,500 messages; individual member fields are ~650 KB. Pulling one parameter for one member via the `.index` offsets avoids downloading the entire multi-gigabyte step file.

### Full ECMWF Real-time Catalogue
Since 1 October 2025 the entire ECMWF Real-time Catalogue is licensed under CC-BY-4.0 — full-resolution 9 km IFS ENS output can be redistributed and used commercially with attribution. *Delivery* of high-volume data may incur service charges to cover infrastructure costs, and full-catalogue access typically requires a Real-time Dissemination Service Agreement.

### Tooling
The `ecmwf-opendata` Python client supports individual member selection and handles the Azure SAS-token exchange automatically:
- https://github.com/ecmwf/ecmwf-opendata

---

## Notes
- **Relationship to the deterministic counterpart.** IFS ENS is the ensemble companion to the deterministic [IFS](../../../nwp_models/global/eu/ifs.md). The two share the same model core, the same 9 km native resolution (since Cycle 48r1, June 2023), and the same atmospheric analysis. Since Cycle 49r1 (12 November 2024) the deterministic forecast and the ENS control have been scientifically and computationally bit-identical; since Cycle 50r1 they are formally the same product, distributed once under `oper`.
- **Coupled components.** IFS ENS runs as a coupled system with ocean, sea ice, and wave components. The ENS-WAM wave ensemble has run on the same Tco1279 grid as the atmosphere since Cycle 49r1 and is distributed as the separate `waef` stream — see [ECWAM](../../../wave_models/global/eu/ecwam.md). Coupled ocean surface fields (`zos`, `sithick`, `sve`, `svn`) are delivered within the atmospheric `enfo` stream rather than separately.
- **Public data is a curated subset.** The freely-distributed Open Data is a reduced-resolution subset of the operational ENS. The full native 9 km ensemble and the complete parameter list require ECMWF's dissemination services, or the 9 km Open Data subset still planned for later in 2026.
- **Cycle transitions matter for backtesting.** Each major cycle can shift skill characteristics, spread–skill relationships, and variable distributions. Evaluation windows should be split around cycle transition dates — and for anything spanning 12 May 2026, the member count change must be handled explicitly.
- The current IFS cycle is **50r1** (operational since 12 May 2026).

### Sub-seasonal (extended-range) ensemble — separate system
ECMWF also runs a separate **101-member sub-seasonal ensemble** at 36 km (TCo319) horizontal resolution out to 46 days, run daily at 00 UTC. This was restructured in IFS Cycle 48r1 (June 2023) from a 51-member system running twice weekly as an extension of the medium-range ensemble. It is now a fully independent forecast system rather than a continuation of the medium-range run, with its own re-forecast configuration. It is open-licensed (since July 2024 for data ≥0.4° resolution) but is **not** part of the medium-range ENS described above and does not appear in the `0p25/enfo` Open Data tree.

---

## Recent version history

### Cycle 50r1 — operational 12 May 2026 (current)
Part of the joint IFS Cycle 50r1 / AIFS v2 deployment. See the [IFS entry](../../../nwp_models/global/eu/ifs.md#recent-version-history) for the full atmospheric model and data-assimilation upgrade list. Changes specific to ENS:

- **No change in horizontal resolution, vertical resolution, or forecast steps.**
- **Modified SPP configuration** addressing excessive 10 m wind spread — the previous scheme produced over-dispersion in near-surface wind extremes; the revised configuration yields more realistic spread.
- **The control member left the `enfo` stream.** `stream=enfo, type=cf` is deprecated; the control is now `stream=oper, type=fc`. Open Data `enfo` files carry 50 members instead of 51. Verified: `enfo` index files at steps 0, 90, 93, 150, 240 and 360 h contain `type=pf` only, `number` 1–50, with no `cf` record at any step.
- **`stream=scda` and `stream=scwv` deprecated** — 06/18 UTC data folded into `oper` and `wave` respectively, changing the directory layout.
- **Tropical cyclone ensemble products** drop from 52 to 51 members (the redundant ex-HRES member is removed). Verified: maximum `numberOfSubsets` in the TC BUFR is 51.
- Coupled atmosphere–ocean assimilation, weak-constraint 4D-Var extended to the boundary layer, and revised convection/microphysics all apply to the analyses used to initialize ENS.

> **Corrects a previous version of this entry**, which carried Cycle 50r1 under "Upcoming changes," stated the current cycle as 49r1, and rendered the control migration as `stream=oper, type=fc` → `stream=enfo, type=cf` — the wrong direction. It also advised migrating 06/18 UTC users to `stream=scda`, which 50r1 deprecated.

### Cycle 49r1 — operational 12 November 2024
- SPP replaced SPPT as the operational stochastic physics scheme
- HRES and ENS Control made scientifically and computationally bit-identical
- ENS extended to 15 days at 00/12 UTC and 6 days at 06/18 UTC
- Soft re-centring of EDA perturbations introduced
- New land surface model upgrades including urban tiles

### Cycle 48r1 — operational 27 June 2023
- ENS horizontal resolution increased from 18 km to 9 km, matching HRES
- Extended-range ensemble restructured into the independent 101-member, 46-day sub-seasonal system
- GRIB2 output began using CCSDS compression

### Cycle 41r2 — operational 8 March 2016
- ENS resolution upgraded to 18 km on the octahedral reduced Gaussian grid; HRES to 9 km

---

## Verification record
All claims marked "verified" were established on **2026-07-30** by direct inspection rather than from documentation:
- Deduplicated directory enumeration of `enfo` across the 00z, 06z, 12z (2026-07-30) and 18z (2026-07-29) cycles for file-type inventory, step lists, and the probability-product cycle discrepancy
- `.index` sidecar parsing at steps 0, 90, 93, 150, 240 and 360 h for member numbering, per-member record counts, parameter inventory, and step-dependent parameter switchovers
- Set difference of the ENS and deterministic parameter inventories to establish the missing-orography finding
- ecCodes 2.48.0 decode of byte-range-extracted `2t` messages for members 1, 2 and 50, for ensemble encoding keys (`perturbationNumber`, `typeOfEnsembleForecast`, `numberOfForecastsInEnsemble`, `productDefinitionTemplateNumber`)
- Full index breakdown of both `-ep` files for the `em` / `es` / `ep` type split, parameter and level coverage, and step conventions
- ecCodes BUFR decode of the tropical cyclone track file for message count, subset counts and storm identifiers
- MD5 comparison of the same ensemble object across the ECMWF portal, GCS and the AWS regional endpoint
- `Last-Modified` header sampling across the 00z, 06z and 12z cycles for publication latency
- Repeated AWS requests establishing the `SlowDown` throttling behaviour and the regional-endpoint workaround

Where live observation and ECMWF documentation disagree, the live observation is recorded and the disagreement flagged rather than silently resolved.

---

## Official documentation
- Open Data dataset page: https://www.ecmwf.int/en/forecasts/datasets/open-data
- Open Data user documentation (access, naming, index files): https://confluence.ecmwf.int/display/DAC/ECMWF+open+data%3A+real-time+forecasts+from+IFS+and+AIFS
- Dissemination schedule: https://confluence.ecmwf.int/display/DAC/Dissemination+schedule
- Changes to the forecasting system: https://confluence.ecmwf.int/display/FCST/Changes+to+the+forecasting+system
- IFS Cycle 50r1 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+IFS+Cycle+50r1
- IFS Cycle 49r1 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+IFS+Cycle+49r1
- ECMWF Newsletter 185 (Cycle 50r1 overview): https://www.ecmwf.int/en/newsletter/185/earth-system-science/upgrade-ifs-cycle-50r1
- ECMWF Open Data Python client: https://github.com/ecmwf/ecmwf-opendata
- ECMWF Open Data community forum: https://forum.ecmwf.int/c/open-data
- AWS Open Data Registry entry: https://registry.opendata.aws/ecmwf-forecasts/
- Planetary Computer dataset page: https://planetarycomputer.microsoft.com/dataset/ecmwf-forecast
- Google Cloud marketplace listing: https://console.cloud.google.com/marketplace/product/bigquery-public-data/open-data-ecmwf
