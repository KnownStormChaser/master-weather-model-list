# AIFS Wave (AIFS Single — Deterministic Wave)

## What this model is
AIFS Wave is the ocean wave component of ECMWF's machine-learning deterministic forecast system, [AIFS Single](../../../nwp_models/global/eu/aifs-single.md). Introduced with AIFS Single v2 on 12 May 2026, it produces **the first data-driven ocean wave forecasts ECMWF has issued**.

Unlike the physics-based [ECWAM](./ecwam.md), AIFS Wave does not integrate the wave action balance equation or discretise a wave spectrum. It is a trained neural network that predicts integral sea-state parameters directly, as additional output channels of the same encoder–processor–decoder network that produces AIFS Single's atmospheric fields. There is no separate wave model, no coupling step, and no spectral computation — the wave fields emerge from the same forward pass.

ECMWF reports that AIFS significant wave height forecasts improve by approximately 10% over the physics-based IFS Cycle 50r1 wave model.

> **Scope of this entry.** This covers the **deterministic** AI wave forecast (`stream=wave`, `type=fc`, under `aifs-single/`). The AI wave ensemble is catalogued separately as [AIFS ENS Wave](./aifs-ens-wave.md), per the repository's convention that ensemble marine systems get their own entry filed alongside the deterministic sibling.

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts (ECMWF)
- **Country / region:** International (intergovernmental organisation)

---

## What area it covers
- **Coverage:** Global oceans
- **Open Data grid (verified):** regular latitude–longitude, **1440 × 721**, 0.25° × 0.25°, 1,038,240 total grid points
- **Grid origin:** first grid point 90°N / 180°, scanning mode 0 (west→east, north→south). The longitude axis begins at the dateline in the raw GRIB header — see the [IFS entry](../../../nwp_models/global/eu/ifs.md#what-area-it-covers).
- **Sea mask:** GRIB bitmap present, but **not internally consistent** — see *Notes*.

---

## Basic details
- **Model type:** Deterministic wave model (AI-based)
- **Grid system:** single regular latitude–longitude grid; the underlying network operates on the N320 reduced Gaussian grid (~31 km) shared with AIFS Single's atmospheric output
- **Core wave model:** None in the conventional sense. AIFS Wave is a set of output channels on the AIFS Single neural network — encoder–processor–decoder with attention-based graph neural networks and a sliding-window transformer processor. **No spectral discretisation exists**, so the frequency/direction bin counts that characterise ECWAM, WW3 and SWAN have no analogue here.
- **Model version:** AIFS Single v2 (operational 12 May 2026)
- **Horizontal resolution:** 0.25° (~28 km) as distributed; ~31 km native (N320)
- **Forecast length (verified):** **360 h (15 days) at every cycle**, 61 steps
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (verified):** 6-hourly throughout — no 3-hourly section
- **Observed publication latency (verified):** **T+5h45m at every cycle**, published in the same batch as the AIFS Single atmospheric stream. Measured 2026-07-30: 00z at 05:45 UTC, 06z at 11:45 UTC, 12z at 17:45 UTC.
- **Volume:** 8.1 MB per step file, **0.49 GB per cycle**, ~2 GB/day — the smallest product in the ECMWF Open Data tree. See the [IFS entry](../../../nwp_models/global/eu/ifs.md#volume-across-the-025-tree) for the cross-stream comparison.

> **Longer and earlier than the physics-based wave forecast.** [ECWAM](./ecwam.md) runs to 360 h only at 00 and 12 UTC, dropping to 144 h at 06 and 18 UTC, and publishes at T+7h34m (00/12) or T+6h27m (06). AIFS Wave carries the full 61-step, 360 h run at **all four cycles** and lands **1h49m earlier** at 00/12 UTC. For a fresh 15-day wave forecast at 06 or 18 UTC, it is currently the only ECMWF option.

---

## Forcing and nesting
- **Wind forcing:** None in the conventional sense. There is no wind field passed to a wave solver — the network predicts wave and atmospheric fields jointly from the same initial state, so wind–wave consistency is learned rather than imposed. **Initialization** is from ECMWF operational analyses (the same analyses that initialize the deterministic IFS), regridded from O1280 to N320.
- **Ice forcing:** Not applied as an external forcing. Sea-ice effects are implicit in the training data. ECMWF documents smoothing along the Antarctic sea-ice edge in summer wave forecasts as a known v2 limitation.
- **Current forcing:** Not applicable — no propagation scheme to modify.
- **Bathymetry:** A `wmb` field is distributed at step 0. Whether it participates in the forecast or is emitted for compatibility with the ECWAM product layout is **TBD** — a network with no propagation scheme has no obvious use for a depth field.
- **Nested inside / parent for:** Neither. Global standalone; provides no boundary conditions to regional systems.

---

## Data assimilation
- **Assimilates wave observations:** Not directly. AIFS Wave runs no analysis of its own and consumes the IFS analysis as its initial state. Altimeter wave height reaches it only indirectly, through the ECWAM analysis embedded in that initial state and through the ERA5/operational-analysis training data.
- **Observation sources:** Not applicable at inference time.
- **Method / cadence:** Not applicable. See [ECWAM](./ecwam.md#data-assimilation) for the Optimum Interpolation scheme operating in the physics-based system.

---

## What it provides

Verified by decoding `20260730000000-24h-wave-fc.grib2` (10 GRIB messages) and the step-0 file (11 messages).

| shortName | Description |
|---|---|
| `swh` | Significant height of combined wind waves and swell |
| `mwp` | Mean wave period |
| `mwd` | Mean wave direction |
| `cdww` | Coefficient of drag with waves |
| `h1012` | Significant wave height, 10–12 s period band |
| `h1214` | Significant wave height, 12–14 s period band |
| `h1417` | Significant wave height, 14–17 s period band |
| `h1721` | Significant wave height, 17–21 s period band |
| `h2125` | Significant wave height, 21–25 s period band |
| `h2530` | Significant wave height, 25–30 s period band |
| `wmb` | Model bathymetry — **step 0 only** |

**Against the [ECWAM](./ecwam.md) open subset (13 parameters), AIFS Wave omits `mp2`** (mean zero-crossing wave period) **and `pp1d`** (peak wave period). The six period-band partitions are identical in definition. No 2D spectra, wind-sea/swell partition triplets, Stokes drift, or wave-induced stress are distributed by either product.

### Sanity check against ECWAM
Matched valid time (2026-07-30 00z + 24 h), `swh`:

| | Min | Max | Mean |
|---|---|---|---|
| AIFS Wave | 0.000 m | 11.718 m | **2.0620 m** |
| ECWAM | 0.004 m | 12.049 m | **2.0670 m** |

Means agree to within 0.25%; the AI model's maximum is ~2.8% lower. That is mild tail smoothing, consistent with the known behaviour of MSE-trained networks, but far from the wholesale flattening sometimes assumed. Full per-parameter ranges at the same valid time:

| param | min | max | mean |
|---|---|---|---|
| `swh` | 0.0000 | 11.718 | 2.0620 |
| `mwp` | 0.0000 | 21.321 | 8.8219 |
| `mwd` | 0.0005 | 360.000 | 189.3312 |
| `h1012` | 0.0000 | 5.560 | 0.7715 |
| `h1214` | 0.0000 | 4.769 | 0.5703 |
| `h1417` | 0.0000 | 6.900 | 0.5314 |
| `h1721` | 0.0000 | 4.365 | 0.3127 |
| `h2125` | 0.0000 | 0.710 | 0.0929 |
| `h2530` | 0.0000 | 0.189 | 0.0266 |
| `cdww` | 0.0009 | 0.004 | 0.0013 |

---

## Data availability
- **Is the data free?** Yes (Open Data subset)
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0), plus the ECMWF Terms of Use. Redistribution and commercial use permitted with attribution to ECMWF.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, `grid_ccsds` packing, `tablesVersion = 36`, bitmap present. Decoding requires ecCodes 2.42.0 or newer built with `libaec`; note the newer tables version relative to the IFS streams.
- **Official download location:**
  https://data.ecmwf.int/forecasts/
  - Path pattern: `[ROOT]/<YYYYMMDD>/<HH>z/aifs-single/0p25/wave/<YYYYMMDD><HH>0000-<step>h-wave-fc.grib2`
  - Example: `20260730/00z/aifs-single/0p25/wave/20260730000000-24h-wave-fc.grib2`

### Access channels
Verified 2026-07-30: the `24h-wave-fc.index` object returned matching MD5 (`24fc9f2343965a...`, 2,346 bytes) from the ECMWF portal, GCS **and** the AWS regional endpoint.

| Channel | Endpoint | Anonymous? | Archive depth |
|---|---|---|---|
| **ECMWF portal** | `https://data.ecmwf.int/forecasts/` | Yes (500-connection cap) | Rolling ~12 runs |
| **AWS S3** | `s3://ecmwf-forecasts` (`eu-central-1`) | Yes — unsigned | From 2026-05-12 (v2 go-live) |
| **Azure Blob** | `https://ai4edataeuwest.blob.core.windows.net/ecmwf` | **No — SAS token required** | as above |
| **Google Cloud Storage** | `gs://ecmwf-open-data` | Yes — fully unauthenticated | as above |

The archive begins at the v2 go-live; there is no AIFS wave data before 12 May 2026. Full access mechanics — the Azure SAS-token exchange, the GCS bucket identity, and `.index` byte-range retrieval — are documented once in the [IFS entry](../../../nwp_models/global/eu/ifs.md#data-availability).

At 8.1 MB per step file, whole-file retrieval is entirely practical here; the byte-range machinery that matters for the ensembles is optional for this stream.

---

## Notes

- **Verified 2026-07-30.** Stream inventory, step lists, forecast horizons at all four cycles, grid geometry, bitmap behaviour, parameter inventory, encoding, publication latency, volumes and mirror parity confirmed by direct inspection of live GRIB2 files.

- ⚠️ **The sea mask is internally inconsistent, and differs from ECWAM's.** Three masks are in play when comparing the two wave products:

  | Mask | Valid points | Parameters |
  |---|---|---|
  | AIFS Wave, sea-state fields | **665,822** | `swh`, `mwp`, `mwd`, `h1012`…`h2530` (9 params) |
  | AIFS Wave, drag and bathymetry | **665,872** | `cdww`, `wmb` |
  | [ECWAM](./ecwam.md) | **665,628** | all 13 parameters |

  The 50-point split *within a single AIFS file* reproduces at steps 0, 24, 120 and 360 and in both the 00z and 12z cycles — systematic, not transient. Anyone stacking AIFS wave parameters into one array, or regridding onto an ECWAM mask for comparison, will hit shape mismatches at all three boundaries. Unmask to the full 1440 × 721 grid before combining. The same split appears in [AIFS ENS Wave](./aifs-ens-wave.md).

- **Static-field handling is better than ECWAM's.** `wmb` appears at step 0 only. ECWAM re-emits identical bathymetry at all 85 of its steps (~42.8 MB per cycle of pure redundancy), and [ENS-WAM](./ecwam-ens.md) at every step for every member (4,250 copies, ~2.17 GB per cycle). AIFS Wave costs one copy per cycle.

- **No spectral discretisation exists.** Requests for "the model's frequency and direction bins" have no answer — the network predicts integral parameters directly. This also means the period-band fields `h1012`…`h2530` are *learned outputs* rather than integrals over a resolved spectrum, which is worth bearing in mind when using them as swell diagnostics.

- **GRIB2 encoding differs from the IFS wave streams:**

  | Key | AIFS Wave | [ECWAM](./ecwam.md) / [ENS-WAM](./ecwam-ens.md) |
  |---|---|---|
  | `mars.class` | `ai` | `od` |
  | `generatingProcessIdentifier` | **5** | 109 |
  | `tablesVersion` | 36 | 32 |
  | `localTablesVersion` | 0 | 0 |

  `generatingProcessIdentifier = 5` is shared with the AIFS Single *atmospheric* stream, so it separates AIFS from IFS but **not** AIFS wave from AIFS atmospheric — use `mars.stream` for that. Both AIFS and ECWAM wave streams are already fully WMO-standard GRIB2 and unaffected by the IFS Cycle 50r2 parameter-encoding migration.

- **Family relationships.** Parent model: [AIFS Single](../../../nwp_models/global/eu/aifs-single.md). Ensemble counterpart: [AIFS ENS Wave](./aifs-ens-wave.md). Physics-based counterparts: [ECWAM](./ecwam.md) and [ENS-WAM](./ecwam-ens.md). Indexed in [`AI_MODELS.md`](../../../../AI_MODELS.md).

- **Upcoming resolution change.** ECMWF has announced extension of the open subset toward native 9 km later in 2026. AIFS runs natively at ~31 km, so unlike the IFS streams this would represent an upsampling of the distribution rather than release of finer model output — worth watching for how ECMWF handles the AI streams in that change.

---

## Open questions / pending verification
- **The 50-point mask split** between `cdww`/`wmb` and the sea-state fields, and the 194-to-244-point difference against the ECWAM mask. Systematic and reproducible, but undocumented. **TBD** whether this reflects a different land–sea decision in the AI model or an artefact of the output pipeline.
- **Whether `wmb` is a model input or a compatibility artefact.** A network with no propagation scheme has no obvious use for a depth field. **TBD.**
- **Whether the stream will gain `mp2` and `pp1d`** to reach parity with the ECWAM subset — not stated.
- **How the period-band fields are produced** — as direct network outputs or as post-processing of a predicted spectrum-like intermediate. Not documented. **TBD.**

---

## Recent version history

### AIFS Single v2 — operational 12 May 2026 (current)
- **Wave stream introduced.** ECMWF's first data-driven ocean wave forecasts, deployed jointly with [IFS Cycle 50r1](../../../nwp_models/global/eu/ifs.md#recent-version-history) and [AIFS ENS v2](../../../ensemble_models/global/eu/aifs-ens.md#version-history).
- Significant wave height forecasts reported ~10% better than the physics-based IFS Cycle 50r1 wave model.
- Dissemination priority 30 on ECPDS, matching the IFS deterministic wave stream.
- Known limitation: smoothing along the Antarctic sea-ice edge in summer wave forecasts.

No wave output existed in AIFS Single v1.0, v1.1 or the pre-operational v0.2.1 releases. See the [AIFS Single entry](../../../nwp_models/global/eu/aifs-single.md#version-history) for the full model version history.

---

## Verification record
Established on **2026-07-30** by direct inspection rather than from documentation:
- Directory enumeration of `aifs-single/0p25/wave/` across the 00z, 06z and 12z cycles for step lists and forecast horizons at every cycle
- ecCodes 2.48.0 decode of complete wave files at steps 0, 24, 120 and 360 in the 00z cycle, and step 24 in the 12z cycle, for parameter inventory, mask behaviour and step-0 static-field placement
- Per-parameter value extraction at 00z + 24 h, and comparison of `swh` against the ECWAM field at matched valid time
- Grid geometry, packing, tables version and local-table status from GRIB headers
- `Last-Modified` header sampling across three cycles for publication latency, compared against ECWAM measurements from the same session
- `Content-Length` sampling for per-step and per-cycle volumes
- MD5 comparison of the same object across the ECMWF portal, GCS and the AWS regional endpoint

Architecture, training regime and skill claims are **not live-verifiable** and are sourced from the AIFS Single v2 implementation page and the AIFS blog.

Where live observation and ECMWF documentation disagree, the live observation is recorded and the disagreement flagged rather than silently resolved.

---

## Official documentation
- AIFS Single v2 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+AIFS+Single+v2
- ECMWF open data user documentation: https://confluence.ecmwf.int/display/DAC/ECMWF+open+data%3A+real-time+forecasts+from+IFS+and+AIFS
- Open data licence and parameter subset: https://www.ecmwf.int/en/forecasts/datasets/open-data
- AIFS Blog: https://www.ecmwf.int/en/about/media-centre/aifs-blog
- Anemoi framework: https://anemoi.readthedocs.io/
- ECMWF Open Data Python client: https://github.com/ecmwf/ecmwf-opendata
- AWS Open Data Registry entry: https://registry.opendata.aws/ecmwf-forecasts/

### Key references
- Moldovan et al. (2025). *An update to ECMWF's machine-learned weather forecast model AIFS.* arXiv:2509.18994. https://arxiv.org/abs/2509.18994
- Lang et al. (2024). *AIFS — ECMWF's data-driven forecasting system.* arXiv:2406.01465. https://arxiv.org/abs/2406.01465
