# AIFS Single (Artificial Intelligence Forecasting System – Deterministic)

## What this model is
AIFS Single is ECMWF's operational machine-learning-based global deterministic weather forecast model.

Unlike the physics-based [IFS](./ifs.md), AIFS Single does not solve the equations of fluid dynamics explicitly. Instead, it uses a trained neural network to predict the evolution of the atmosphere directly from historical weather data, producing medium-range forecasts at a fraction of the computational cost of traditional NWP.

AIFS Single runs operationally alongside the physics-based IFS and is designed to complement rather than replace it. It became the world's first machine-learning-driven forecast model fully supported in operations on 25 February 2025, and as of v1.1 had demonstrated forecast skill gains of approximately 12 to 24 hours over IFS in the medium range for several variables. Since v2 (12 May 2026) it also drives a separate data-driven ocean wave stream, catalogued separately (see *Relationship to other models*).

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts
- **Country / region:** International (European consortium of 23 Member States and 12 Co-operating States)

---

## What area it covers
- **Coverage:** Global
- **Open Data grid (verified):** 1440 × 721 regular latitude–longitude, 0.25° × 0.25°, 1,038,240 points per field
- **Grid origin:** first grid point 90°N / 180°, scanning mode 0 (west→east, north→south) — the same dateline-first raw layout as the IFS distribution. See the [IFS entry](./ifs.md#what-area-it-covers).
- **Sea mask:** the atmospheric `oper` stream carries **no bitmap** — all 1,038,240 points are valid.

---

## Basic details
- **Model type:** Global deterministic (AI-based)
- **Architecture:** Encoder–processor–decoder with attention-based graph neural networks (encoder/decoder) and a sliding-window transformer (processor)
- **Dynamical formulation:** Not applicable — AIFS Single does not solve dynamical equations. It is a trained neural network that predicts atmospheric evolution directly from learned patterns in historical weather data.
- **Convection-allowing:** No (AIFS does not represent or parameterize convection explicitly; convective behaviour is implicit in what the network has learned at ~31 km resolution)
- **Training framework:** Anemoi (open-source AI-NWP framework co-developed with ECMWF Member States)
- **Training data:** ERA5 reanalysis (1979–2022) plus ECMWF operational analyses (fine-tuning)
- **Native grid:** N320 reduced Gaussian grid (~31 km, ~0.25°)
- **Initialization grid:** IFS analyses are interpolated from their native O1280 grid (~0.1°) down to N320 (~0.25°) for input to the AIFS model
- **Open Data resolution:** 0.25° regular latitude–longitude
- **Pressure levels:** 14 — 10, 50, 100, 150, 200, 250, 300, 400, 500, 600, 700, 850, 925, 1000 hPa. **Specific humidity (`q`) is the exception at 13 levels**, absent at 10 hPa — see *What it provides*.
- **Soil levels:** 2 (`sol` 1–2) — half the IFS distribution's four layers
- **Forecast timestep:** 6 hours, uniform throughout the run — no 3-hourly section
- **Forecast length (verified):** **360 h (15 days) at every cycle**, 61 steps
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Initialization:** ECMWF operational analyses (the same analyses used to initialize the deterministic IFS, regridded from O1280 to N320)

> **AIFS Single reaches 15 days four times a day; IFS does not.** The physics-based [IFS](./ifs.md) runs to 360 h only at 00 and 12 UTC, dropping to 144 h at 06 and 18 UTC. Directory enumeration on 2026-07-30 confirms AIFS Single carries the full 61-step, 360 h run at **all four cycles**. For any workflow needing a fresh 15-day deterministic forecast at 06 or 18 UTC, AIFS is currently the only ECMWF option.

- **Observed publication latency (verified):** **T+5h45m at every cycle**, with the whole run published as a single batch. Measured 2026-07-30: 00z at 05:45 UTC, 06z at 11:45 UTC, 12z at 17:45 UTC — exactly 5h45m each time. Compare the same cycles of IFS `oper`: 07:34, 12:27 and 19:34 UTC. **AIFS Single lands roughly 1h49m ahead of IFS at 00/12 UTC and 42 minutes ahead at 06 UTC.** This confirms the removal of the AIFS dissemination delay at operational status — AIFS is released when produced rather than at the end of the IFS dissemination schedule.

### Volume

| Stream | Per step file | Per cycle (61 steps) |
|---|---|---|
| `oper` | 84.9 MB | 5.18 GB |

The `aifs-single/0p25/` directory also contains a `wave/` stream, documented separately.

---

## What it provides

Verified by decoding `20260730000000-24h-oper-fc.grib2` (122 GRIB messages).

### Atmospheric stream (`stream=oper`) — 30 parameters, 122 records per step

**Pressure levels** (7 parameters): `z`, `gh`, `t`, `u`, `v`, `w`, `q`

**Surface** (21): `2t`, `2d`, `10u`, `10v`, `100u`, `100v`, `msl`, `sp`, `skt`, `tp`, `cp`, `sf`, `rowe`, `fscov`, `tcc`, `hcc`, `mcc`, `lcc`, `tcw`, `ssrd`, `strd`

**Soil** (2 × 2 layers): `sot`, `vsw`

> ⚠️ **`q` is missing at 10 hPa.** Six of the seven pressure-level parameters (`z`, `gh`, `t`, `u`, `v`, `w`) carry all 14 levels; specific humidity carries only 13, starting at 50 hPa. This gives 97 pressure-level records rather than the 98 a naive 7 × 14 would predict. It follows from what v2 actually added — ECMWF's changelog lists the new 10 hPa level for geopotential, temperature, horizontal winds and vertical velocity, and humidity was not among them. Code that assembles a uniform (parameter × level) cube will find a hole at `q`/10 hPa.

Relative to the [IFS](./ifs.md) open subset, AIFS Single is a smaller and differently-shaped set. It **lacks** relative humidity (`r`), divergence (`d`), vorticity (`vo`), wind gusts, min/max 2 m temperature, precipitation type, radiation net fluxes, snow depth and density, surface stresses, the coupled ocean/sea-ice fields (`zos`, `sithick`, `sve`, `svn`), CAPE, and all orography fields. It **adds** convective precipitation (`cp`), fraction of snow cover (`fscov`), runoff water equivalent (`rowe`), and split cloud-cover layers. It also has no step-dependent parameter switching — the 122-record structure is constant across all 61 steps, unlike IFS's gust and temperature-extreme switchovers.

**No orography is distributed.** Users needing model orography must take it from the IFS `oper` step-0 file.

### Tropical cyclone tracks (`type=tf`)
The `oper` stream also carries a BUFR trajectory file per cycle (`...-360h-oper-tf.bufr`). Verified decode of the 2026-07-30 00z file: 14 BUFR messages, **`numberOfSubsets = 1` for every message** — one deterministic track per feature, with no ensemble dimension. This contrasts with the [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md) TC product, whose messages carry up to 51 subsets.

> **Scope note.** These are point trajectories rather than gridded fields and sit outside this repository's gridded-data scope rule. Documented for completeness because they ship in the same directory as the in-scope GRIB2 products.

---

## GRIB2 encoding — AIFS differs from IFS in ways that break naive filters

Verified across all 122 messages of the step-24 `oper` file.

| Key | AIFS Single | IFS |
|---|---|---|
| `mars.class` | **`ai`** | `od` |
| `generatingProcessIdentifier` | **5** | 161 (`oper`), 109 (`wave`/`waef`) |
| `tablesVersion` | **36** | 32 |
| `localTablesVersion` | 0 on every message | 1 on six parameters |
| Packing | `grid_ccsds` | `grid_ccsds` |

Three consequences worth planning around:

1. **`mars.class = ai` is the cleanest discriminator** between AIFS and IFS output in a mixed archive, and `generatingProcessIdentifier` separates all four streams unambiguously (5 / 161 / 109).

2. **AIFS is already GRIB2-native; IFS is not yet.** Zero AIFS parameters use ECMWF local tables. The [IFS](./ifs.md#upcoming-changes) atmospheric stream still has six, including `tp`. **AIFS is effectively on the far side of the encoding migration that IFS completes in Cycle 50r2.**

3. **The same variable can carry a different `paramId` in the two models.** Total precipitation is the clearest case:

   | | paramId | discipline/category/number | Local tables |
   |---|---|---|---|
   | AIFS Single `tp` | **228228** | 0 / 1 / 52 | No |
   | IFS `tp` | **228** | 0 / 1 / 193 | Yes (version 1) |

   Both decode to `shortName = tp`. **Code that filters IFS output on `paramId == 228` will silently return nothing from AIFS.** Match on `shortName`, or handle both identifiers. Expect IFS to converge on the AIFS-style identifiers at Cycle 50r2, which will break the reverse assumption.

`tablesVersion = 36` also means older ecCodes installations may not carry the definitions AIFS needs; ecCodes 2.42.0 or newer is recommended for the ECMWF Open Data tree generally, and CCSDS packing requires `libaec` support compiled in.

---

## Relationship to other models
AIFS Single is the **deterministic AI companion** to ECMWF's physics-based [IFS](./ifs.md). It does not replace IFS — ECMWF continues to run both operationally and views them as complementary.

AIFS Single shares the same **initial conditions** as IFS (both start from ECMWF's operational analyses), which means their forecast differences reflect model behavior rather than initialization differences.

[AIFS ENS](../../../ensemble_models/global/eu/aifs-ens.md) is the ensemble counterpart trained with probabilistic methods.

**AIFS wave is catalogued separately.** From v2 the `aifs-single` tree carries a `wave/` stream alongside `oper/` — ECMWF's first data-driven wave forecasts. Following the repository's convention of filing by phenomenon, it belongs in `models/wave_models/` next to the physics-based [ECWAM](../../../wave_models/global/eu/ecwam.md) and [ENS-WAM](../../../wave_models/global/eu/ecwam-ens.md) rather than inside this entry. Not yet catalogued.

ECMWF stopped running its experimental machine learning model suite (Aurora, FourCastNet, GraphCast, Pangu-Weather) as of the joint AIFS/IFS upgrade on 12 May 2026.

Indexed in [`AI_MODELS.md`](../../../../AI_MODELS.md).

---

## Data availability
- **Is the data free?** Yes (Open Data subset)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 with CCSDS compression (`grid_ccsds` packing, `tablesVersion = 36`); BUFR edition 4 for tropical cyclone tracks; JSON-lines `.index` sidecars
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0) plus the ECMWF Terms of Use; attribution required
- **Retention on the ECMWF portal:** rolling archive of the most recent 12 forecast runs (~2–3 days)
- **Official download location:**
  https://data.ecmwf.int/forecasts/
  - Path pattern: `[ROOT]/<YYYYMMDD>/<HH>z/aifs-single/0p25/<stream>/<YYYYMMDD><HH>0000-<step>h-<stream>-<type>.grib2`
  - Example: `20260730/00z/aifs-single/0p25/oper/20260730000000-24h-oper-fc.grib2`

### Access channels
Verified 2026-07-30: the `24h-oper-fc.index` object returned matching MD5 (`261f1aa164d6...`, 29,574 bytes) from the ECMWF portal, GCS **and** the AWS regional endpoint.

| Channel | Endpoint | Anonymous? | Archive depth |
|---|---|---|---|
| **ECMWF portal** | `https://data.ecmwf.int/forecasts/` | Yes (500-connection cap) | Rolling ~12 runs |
| **AWS S3** | `s3://ecmwf-forecasts` (`eu-central-1`) | Yes — unsigned | AIFS from ~2024-02-29 |
| **Azure Blob** | `https://ai4edataeuwest.blob.core.windows.net/ecmwf` | **No — SAS token required** | as above |
| **Google Cloud Storage** | `gs://ecmwf-open-data` | Yes — fully unauthenticated | as above |

Full access mechanics — the Azure SAS-token exchange, the GCS bucket identity, and `.index` byte-range retrieval — are documented once in the [IFS entry](./ifs.md#data-availability).

> **Archive path history for AIFS specifically.** Bisection of the AWS mirror places the `aifs/` directory's first appearance at **2024-02-29**, the same day the `[model]` path component was introduced. It was renamed **`aifs-single/`** between 2025-02-01 and 2025-04-01, tracking v1.0's operational status. `aifs-ens/` appeared separately between 2025-07-01 and 2025-08-01. Crawlers walking the AIFS archive must handle the `aifs/` → `aifs-single/` rename. Full schema table in the [IFS entry](./ifs.md#archive-path-schema-changes-verified-against-the-aws-mirror).

Unlike IFS, AIFS Single is distributed at its native resolution (0.25°) — there is no higher-resolution version behind a paywall. Like IFS, the entire ECMWF Real-time Catalogue (including AIFS) became CC-BY-4.0 licensed on 1 October 2025.

---

## Version history

### AIFS Single v2 — operational 12 May 2026 (current)
Major upgrade deployed jointly with [IFS Cycle 50r1](./ifs.md#recent-version-history) and [AIFS ENS v2](../../../ensemble_models/global/eu/aifs-ens.md). Key changes:

- **New 10 hPa pressure level** for geopotential, temperature, horizontal winds, and vertical velocity — **not** for specific humidity, which remains on 13 levels (verified). Sudden stratospheric warmings are now represented in AIFS forecasts.
- **New wave stream** (`aifs-single/0p25/wave/`) — the first data-driven wave forecasts ECMWF has issued. Significant wave height forecasts improve by ~10% compared to the physics-based IFS Cycle 50r1 wave model. Catalogued separately under `models/wave_models/`; see *Relationship to other models*.
- **New snow cover parameter** (`fscov`) — fraction of snow cover, with improved performance over Cycle 50r1.
- **Vertical velocity (`w`) changed from prognostic to diagnostic**, producing more physically consistent vertical velocities (Hadley cells now properly formed and reasonable in strength).
- **Improved training regime** — fine-tuning extended from 2018–2022 to 2018–2024 (two additional years of data, including IFS Cycle 50r1 esuite analyses), over 7,900 fine-tuning training steps. Pre-training is unchanged from v1.1 (ERA5 1979–2022 over 260,000 steps).
- **No changes to model architecture** — v2 is the same network as v1.1, just retrained.
- **Dissemination priority changes** — AIFS Single output priority drops from 90 to 20 on ECPDS (the new wave stream uses priority 30, matching the IFS deterministic wave stream).

The release candidate phase ran from 14 April 2026 through the go-live; test data was available via MARS (expver=105) and the open data testdata endpoint. v2 was upgraded jointly with IFS Cycle 50r1 because v1.1 showed degraded performance when initialized from 50r1 esuite analyses — v2 was fine-tuned specifically on 50r1 data.

Known v2 limitations include 2 m temperature degradations in the Arctic during winter (linked to changes in IFS Cycle 50r1 sea-ice–atmosphere coupling that v2 has not yet seen enough training examples of), some smoothing along the Antarctic sea-ice edge in summer wave forecasts, and continued limited skill at 50 and 100 hPa in the tropics.

### AIFS Single v1.1 — operational 27 August 2025
Upgraded to address a precipitation forecast issue (point rain artefacts) caused by an over-emphasis of soil moisture in v1.0's training. The fix reduced the soil moisture training contribution by a factor of 100, yielding equivalent skill and bias but without the unphysical artefacts.

v1.1 used a 13-pressure-level vertical structure (50 hPa upward) — the 10 hPa level was added in v2.

Initially implemented on 31 July 2025 but reverted on 1 August due to a `stepRange` GRIB2 metadata issue affecting six accumulated parameters (`cp`, `sf`, etc.). Re-implemented on 27 August 2025 after the metadata issue was fixed. The technical paper documenting v1.1 was published in September 2025 (Moldovan et al., arXiv:2509.18994).

### AIFS Single v1.0 — operational 25 February 2025
First operational version. Marked a historic milestone as the first machine-learning-driven forecast model to be made fully operational by any major weather centre.

### AIFS Single v0.2.1 — pre-operational, October 2023
First publicly available AIFS, distributed via ECMWF Open Data as an experimental model running 4× daily. Used a 13-pressure-level vertical structure and was initialized from regridded IFS analyses.

---

## Notes
- AIFS Single runs more than 10× faster than the physics-based IFS at comparable or better skill for many variables, with roughly 1,000× lower energy consumption per forecast. The measured 1h49m publication advantage over IFS at 00/12 UTC is the operational expression of that.
- A known limitation of MSE-trained AI models is under-prediction of distribution tails — for example, AIFS Single produces a flatter distribution for total cloud cover than observations, under-representing the frequencies of completely clear and fully overcast conditions. AIFS v2 addresses this somewhat by being less smooth than v1.1, though this also produces small "skill" degradations at longer lead times for some variables.
- Forecasts blur at longer lead times (a known issue with weighted-MSE-trained AI models). The blurring is reduced but not eliminated in v2.
- Stratospheric skill has historically been a weakness due to the model's coarse vertical structure (13 pressure levels in v1.1, with the highest at 50 hPa). v2's addition of the 10 hPa level significantly improves skill at 50 and 100 hPa in the extratropics, though the tropical stratosphere remains a known weakness.
- Tropical cyclone intensity has historically been under-predicted; AIFS does not currently fully resolve high-impact storm intensity.
- Due to the non-determinism of GPU computation, users running AIFS Single themselves cannot exactly reproduce ECMWF's official forecasts.
- **Two soil layers, not four.** The AIFS distribution carries `sot` and `vsw` on layers 1–2 where the IFS distribution carries layers 1–4. Land-surface workflows ported from IFS will find the deeper layers absent.

---

## Open questions / pending verification
- **Whether `q` at 10 hPa is a deliberate omission or an oversight** — the v2 changelog's list of variables receiving the new level excludes humidity, which is consistent with deliberate, but ECMWF does not say so explicitly. Worth confirming alongside the other ECMWF queries accumulating in the [IFS](./ifs.md#open-questions--pending-verification) and [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md#open-questions--pending-verification) entries.

---

## Verification record
Established on **2026-07-30** by direct inspection rather than from documentation:
- Directory enumeration of `aifs-single` across the 00z, 06z and 12z cycles for stream inventory, step lists and forecast horizons at every cycle
- ecCodes 2.48.0 decode of the full `24h-oper-fc.grib2` file (122 messages) for grid geometry, packing, tables versions, local-table status and parameter inventory
- `.index` sidecar parsing for per-level parameter coverage, establishing the `q` gap at 10 hPa
- Direct `paramId` and encoding comparison of `tp` between the AIFS and IFS step files
- ecCodes BUFR decode of the tropical cyclone track file for message and subset counts
- `Last-Modified` header sampling across three cycles for both AIFS and IFS, for the latency comparison
- `Content-Length` sampling for per-step and per-cycle volumes
- MD5 comparison of the same object across the ECMWF portal, GCS and the AWS regional endpoint

Architecture, training regime, skill claims and version history are **not live-verifiable** and are sourced from ECMWF implementation pages, the AIFS blog, and the Moldovan et al. and Lang et al. papers.

Where live observation and ECMWF documentation disagree, the live observation is recorded and the disagreement flagged rather than silently resolved.

---

## Official documentation
- AIFS Single v2 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+AIFS+Single+v2
- AIFS Single v1 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+AIFS+Single+v1
- ECMWF open data user documentation: https://confluence.ecmwf.int/display/DAC/ECMWF+open+data%3A+real-time+forecasts+from+IFS+and+AIFS
- ECMWF AIFS overview: https://www.ecmwf.int/en/forecasts/documentation-and-support/changes-ecmwf-model
- AIFS Blog: https://www.ecmwf.int/en/about/media-centre/aifs-blog
- Anemoi framework: https://anemoi.readthedocs.io/
- ECMWF Open Data portal: https://www.ecmwf.int/en/forecasts/datasets/open-data
- ECMWF Open Data Python client: https://github.com/ecmwf/ecmwf-opendata
- AWS Open Data Registry entry: https://registry.opendata.aws/ecmwf-forecasts/

### Key references
- Moldovan et al. (2025). *An update to ECMWF's machine-learned weather forecast model AIFS.* arXiv:2509.18994. https://arxiv.org/abs/2509.18994
- Lang et al. (2024). *AIFS — ECMWF's data-driven forecasting system.* arXiv:2406.01465. https://arxiv.org/abs/2406.01465
