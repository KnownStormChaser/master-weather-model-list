# AIFS ENS Wave (AIFS Ensemble — Wave)

## What this model is
AIFS ENS Wave is the ocean wave component of ECMWF's machine-learning ensemble forecast system, [AIFS ENS](../../../ensemble_models/global/eu/aifs-ens.md). Introduced with AIFS ENS v2 on 12 May 2026, it produces **the first probabilistic data-driven wave forecasts ECMWF has issued**.

Like its deterministic sibling [AIFS Wave](./aifs-single-wave.md), it is not a wave model in the conventional sense: there is no wave action balance equation, no spectral discretisation, and no coupling step. Wave fields are additional output channels of the same neural network that produces AIFS ENS's atmospheric fields, and ensemble spread arises from the same Gaussian noise injected into the transformer processor at inference.

The stream is `waef` under `aifs-ens/`, distributed at 0.25° through ECMWF Open Data.

> **This entry is filed under `wave_models/` rather than `ensemble_models/`**, following the repository's convention that ensemble marine systems use their phenomenon template. It uses `wave-model.template.md` with the optional **Ensemble configuration** section retained. Cross-linked in both directions with [AIFS Wave](./aifs-single-wave.md).

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts (ECMWF)
- **Country / region:** International (intergovernmental organisation)

---

## What area it covers
- **Coverage:** Global oceans
- **Open Data grid (verified):** regular latitude–longitude, **1440 × 721**, 0.25° × 0.25°, 1,038,240 total grid points
- **Grid origin:** first grid point 90°N / 180°, scanning mode 0 (west→east, north→south) — dateline-first in the raw GRIB header. See the [IFS entry](../../../nwp_models/global/eu/ifs.md#what-area-it-covers).
- **Sea mask:** GRIB bitmap present, internally inconsistent in the same way as the deterministic AI wave stream — see *Notes*.

---

## Basic details
- **Model type:** Ensemble wave model (AI-based)
- **Grid system:** single regular latitude–longitude grid; the underlying network operates on the N320 reduced Gaussian grid (~31 km)
- **Core wave model:** None in the conventional sense — output channels on the AIFS ENS neural network (encoder–processor–decoder with graph neural networks and a sliding-window transformer). **No spectral discretisation exists.**
- **Model version:** AIFS ENS v2 (operational 12 May 2026)
- **Horizontal resolution:** 0.25° (~28 km) as distributed; ~31 km native (N320)
- **Forecast length (verified):** **360 h (15 days) at every cycle**, 61 steps
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution (verified):** 6-hourly throughout
- **Observed publication latency (verified):** **T+6h40m at every cycle**, control and perturbed files published in the same batch as the AIFS ENS atmospheric stream. Measured 2026-07-30: 00z at 06:40 UTC, 06z at 12:40 UTC, 12z at 18:40 UTC.
- **Volume:** 8.3 MB per `-cf` step file and 416.9 MB per `-pf` step file — **25.9 GB per cycle**, ~104 GB/day. Fourth-largest product in the tree; see the [IFS entry](../../../nwp_models/global/eu/ifs.md#volume-across-the-025-tree) for the cross-stream comparison.

> **Longer, earlier, and at more cycles than the physics-based wave ensemble.** [ENS-WAM](./ecwam-ens.md) runs to 360 h only at 00 and 12 UTC, dropping to 144 h at 06 and 18 UTC, and publishes at T+7h40m (00/12). AIFS ENS Wave carries the full 61-step, 360 h run at **all four cycles** and lands **exactly one hour earlier** at 00/12 UTC. Its probability products also appear at all four cycles, where ENS-WAM restricts them to 00/12.

---

## Forcing and nesting
- **Wind forcing:** None in the conventional sense — the network predicts wave and atmospheric fields jointly from the same initial state within each member, so wind–wave consistency is learned rather than imposed by a coupling step.
- **Ice forcing:** Not applied as external forcing; implicit in training data. ECMWF documents smoothing along the Antarctic sea-ice edge in summer wave forecasts as a known v2 limitation.
- **Current forcing:** Not applicable — no propagation scheme to modify.
- **Bathymetry:** A `wmb` field is distributed at step 0 in both the control and every perturbed member. Whether it participates in the forecast is **TBD**.
- **Nested inside / parent for:** Neither. Global standalone.

---

## Ensemble configuration

- **Ensemble size:** **51 — 1 control + 50 perturbed, all distributed.** Unlike [ENS-WAM](./ecwam-ens.md), where the control was relocated to the deterministic `wave` stream at IFS Cycle 50r1, the AI wave ensemble ships its control in the same directory as a separate `-cf` file. No cross-stream fetch is needed to assemble a complete 51-member ensemble.

- **Source of perturbations:** Inherited from [AIFS ENS](../../../ensemble_models/global/eu/aifs-ens.md) — which in turn takes its initial-condition perturbations member-by-member from [IFS ENS](../../../ensemble_models/global/eu/ifs-ens.md) (singular vectors plus EDA), and represents model uncertainty by **injecting random Gaussian noise into the transformer processor during inference**. The wave channels are not perturbed separately; member *n* of `waef` is the wave output of member *n* of the AIFS ENS forward pass.

  This differs in kind from the physics-based case. [ENS-WAM](./ecwam-ens.md) spread arises because each atmospheric member drives a wave model with a different wind field. Here there is no driving step — atmospheric and wave channels are produced together by one perturbed network evaluation, so wind–wave spread consistency is a property of the trained model rather than of a coupling interface.

- **Resolution / output differences vs deterministic sibling:** **None.** Same 0.25° grid, same 1440 × 721 dimensions, same masks, same 61-step 6-hourly list, same 10 parameters, same cycles. The only additions are the probability products described below.

- **Member packaging:** Control and perturbed members in **separate files per step** — `-cf.grib2` (10 messages; 11 at step 0) and `-pf.grib2` (500 messages = 50 members × 10; 550 at step 0). This matches the AIFS ENS atmospheric convention and differs from [ENS-WAM](./ecwam-ens.md), which concatenates all members into a single `-ef` file with no control present.

  Encoding is **properly set**, unlike the IFS wave ensemble:

  | Key | AIFS ENS Wave control | AIFS ENS Wave perturbed | [ENS-WAM](./ecwam-ens.md) |
  |---|---|---|---|
  | `mars.type` | `cf` | `pf` | `pf` only |
  | `perturbationNumber` | **0** | 1–50 | 1–50 |
  | `typeOfEnsembleForecast` | **5** | **6** | **255 (missing)** |
  | `numberOfForecastsInEnsemble` | 51 | 51 | 51 |
  | Members actually shipped | 1 | 50 | 50 |

  > **`numberOfForecastsInEnsemble = 51` is accurate here.** Both ensembles declare 51, but this one actually distributes 51 forecasts while ENS-WAM distributes 50. The same header value is correct in one stream and stale in the other — size member arrays from the data regardless. `typeOfEnsembleForecast` is also meaningfully set (5 control, 6 perturbed) where ENS-WAM leaves it at 255.

- **Derived products distributed:** Event probabilities only — **no ensemble mean or spread**, matching ENS-WAM's behaviour and diverging from the atmospheric ensembles. Eight parameters:

  | shortName | Event |
  |---|---|
  | `swhg2` | P(significant wave height > 2 m) |
  | `swhg4` | P(significant wave height > 4 m) |
  | `swhg6` | P(significant wave height > 6 m) |
  | `swhg8` | P(significant wave height > 8 m) |
  | `mwpg8` | P(mean wave period > 8 s) |
  | `mwpg10` | P(mean wave period > 10 s) |
  | `mwpg12` | P(mean wave period > 12 s) |
  | `mwpg15` | P(mean wave period > 15 s) |

  > **The four `mwpg*` period-exceedance probabilities have no counterpart in [ENS-WAM](./ecwam-ens.md)**, which distributes only the four `swhg*` height thresholds. Probabilistic long-period swell guidance — the quantity that matters for harbour resonance, surf forecasting, and moored-vessel response — is available from the AI ensemble and not from the physics one. This is the clearest case in the ECMWF Open Data tree where the AI product is strictly richer than its physics counterpart.

  Delivered in `...-{240h,360h}-waef-ep.grib2` at **all four cycles**, on instantaneous steps: the 240h file carries 184 records over 23 steps, the 360h file 88 records over 11 steps (including one `240-360` aggregate window). The step in the filename is a range label, not the step of the contained fields.

  Any mean, spread, percentile, or threshold beyond these eight must be computed from the 51 members directly.

---

## Data assimilation
- **Assimilates wave observations:** Not directly. The ensemble runs no analysis of its own and consumes IFS ENS analyses as initial states. Altimeter wave height reaches it only indirectly, through the ECWAM analysis embedded in those initial states and through the training data.
- **Observation sources:** Not applicable at inference time.
- **Method / cadence:** Not applicable. See [ECWAM](./ecwam.md#data-assimilation) for the Optimum Interpolation scheme in the physics-based system.

---

## What it provides

Each of the 51 members carries the **same 10 parameters** as the deterministic [AIFS Wave](./aifs-single-wave.md) stream, plus `wmb` at step 0.

| shortName | Description |
|---|---|
| `swh` | Significant height of combined wind waves and swell |
| `mwp` | Mean wave period |
| `mwd` | Mean wave direction |
| `cdww` | Coefficient of drag with waves |
| `h1012`…`h2530` | Significant wave height in six period bands (10–12, 12–14, 14–17, 17–21, 21–25, 25–30 s) |
| `wmb` | Model bathymetry — **step 0 only** |

Against the [ENS-WAM](./ecwam-ens.md) open subset (13 parameters), this stream omits `mp2` (mean zero-crossing wave period) and `pp1d` (peak wave period). Period-band definitions are identical.

**Not distributed:** 2D spectra, wind-sea/swell partition triplets, Stokes drift, wave-induced stress, ensemble mean, ensemble spread.

---

## Data availability
- **Is the data free?** Yes (Open Data subset)
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0), plus the ECMWF Terms of Use.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2, `grid_ccsds` packing, `tablesVersion = 36`, bitmap present. Decoding requires ecCodes 2.42.0 or newer built with `libaec`. No BUFR products in this stream.
- **Official download location:**
  https://data.ecmwf.int/forecasts/
  - Path pattern: `[ROOT]/<YYYYMMDD>/<HH>z/aifs-ens/0p25/waef/<YYYYMMDD><HH>0000-<step>h-waef-<type>.grib2`
  - Example: `20260730/00z/aifs-ens/0p25/waef/20260730000000-24h-waef-pf.grib2`

### Stream inventory (verified)

| File type | Cycles | Contents |
|---|---|---|
| `-cf.grib2` | 00, 06, 12, 18 | Control forecast, 10 records per step (11 at step 0) |
| `-pf.grib2` | 00, 06, 12, 18 | 50 perturbed members, 500 records per step (550 at step 0) |
| `-ep.grib2` | 00, 06, 12, 18 | SWH and mean-wave-period exceedance probabilities |

Identical structure at every cycle — 61 + 61 + 2 files. No tropical cyclone product.

### Access channels
Verified 2026-07-30: the `24h-waef-cf.index` object returned matching MD5 (`e208d8baeb6e12...`, 2,257 bytes) from the ECMWF portal, GCS **and** the AWS regional endpoint.

| Channel | Endpoint | Anonymous? | Archive depth |
|---|---|---|---|
| **ECMWF portal** | `https://data.ecmwf.int/forecasts/` | Yes (500-connection cap) | Rolling ~12 runs |
| **AWS S3** | `s3://ecmwf-forecasts` (`eu-central-1`) | Yes — unsigned | From 2026-05-12 (v2 go-live) |
| **Azure Blob** | `https://ai4edataeuwest.blob.core.windows.net/ecmwf` | **No — SAS token required** | as above |
| **Google Cloud Storage** | `gs://ecmwf-open-data` | Yes — fully unauthenticated | as above |

The archive begins at the v2 go-live; there is no AI wave ensemble data before 12 May 2026. Full access mechanics are documented once in the [IFS entry](../../../nwp_models/global/eu/ifs.md#data-availability).

**Byte-range retrieval pays off here.** A `-pf` step file is 416.9 MB holding 500 messages; a single member-parameter field is under 1 MB. Pulling one parameter across all 50 members costs roughly 42 MB against 416.9 MB — a 10× saving. The `.index` sidecar gives `_offset` and `_length` per message; offsets change every run. At ~104 GB/day this stream is far less likely to provoke the AWS `SlowDown` throttling documented for the atmospheric ensembles, but exponential backoff remains sensible.

---

## Notes

- **Verified 2026-07-30.** Stream inventory at all four cycles, step lists, member numbering and counts, ensemble encoding keys, parameter inventory, mask behaviour, probability-product contents, publication latency, volumes and mirror parity confirmed by direct inspection of live files.

- ⚠️ **The sea mask is internally inconsistent**, exactly as in the deterministic AI wave stream: **665,822** valid points for the nine sea-state fields, **665,872** for `cdww` and `wmb`. Both differ from [ECWAM](./ecwam.md) and [ENS-WAM](./ecwam-ens.md) at **665,628**. Three masks are in play when comparing AI and physics wave products. Unmask to the full 1440 × 721 grid before stacking or regridding. Full detail in the [AIFS Wave entry](./aifs-single-wave.md#notes).

- **Static-field duplication is modest here.** `wmb` appears at step 0 only, but is repeated for all 50 perturbed members — 50 copies per cycle. Compare [ENS-WAM](./ecwam-ens.md), which emits identical bathymetry at every step for every member: 4,250 copies, ~2.17 GB per cycle. The AI ensemble's step-0-only placement reduces that by roughly two orders of magnitude.

- **No spectral discretisation exists.** The period-band fields `h1012`…`h2530` are learned outputs rather than integrals over a resolved spectrum. Worth bearing in mind when using them, or the `mwpg*` probabilities derived from `mwp`, as swell diagnostics.

- **GRIB2 encoding:** `mars.class = ai`, `generatingProcessIdentifier = 2`, `tablesVersion = 36`, `localTablesVersion = 0` throughout. The `generatingProcessIdentifier` separates all four ECMWF systems — **2** (AIFS ENS, both streams), **5** (AIFS Single, both streams), **161** (IFS atmospheric), **109** (IFS wave family) — but does **not** separate atmospheric from wave within a given AI system. Use `mars.stream` for that. Fully WMO-standard GRIB2, so unaffected by the IFS Cycle 50r2 parameter-encoding migration.

- **Family relationships.** Parent model: [AIFS ENS](../../../ensemble_models/global/eu/aifs-ens.md). Deterministic sibling: [AIFS Wave](./aifs-single-wave.md). Physics-based counterparts: [ENS-WAM](./ecwam-ens.md) and [ECWAM](./ecwam.md). Indexed in [`AI_MODELS.md`](../../../../AI_MODELS.md).

- **Upcoming resolution change.** ECMWF has announced extension of the open subset toward native 9 km later in 2026. AIFS runs natively at ~31 km, so for the AI streams this would be upsampling rather than release of finer output — worth watching for how ECMWF handles it.

---

## Open questions / pending verification
- **The 50-point mask split** between `cdww`/`wmb` and the sea-state fields, and the difference against the ECWAM mask. Systematic and reproducible, but undocumented. **TBD.**
- **Whether `wmb` is a model input or a compatibility artefact**, and why it is duplicated across perturbed members when its values are static. **TBD.**
- **Whether ensemble mean and spread will be added.** Their absence matches ENS-WAM but diverges from both atmospheric ensembles, which distribute `em` and `es`. Not stated.
- **Whether the stream will gain `mp2` and `pp1d`** to reach parity with the ENS-WAM subset — not stated.
- **How the `mwpg*` thresholds were chosen** (8, 10, 12, 15 s) and whether they are computed from `mwp` or from an internal representation — not documented. **TBD.**

---

## Recent version history

### AIFS ENS v2 — operational 12 May 2026 (current)
- **Wave ensemble stream introduced.** ECMWF's first probabilistic data-driven wave forecasts, deployed jointly with [IFS Cycle 50r1](../../../nwp_models/global/eu/ifs.md#recent-version-history) and [AIFS Single v2](../../../nwp_models/global/eu/aifs-single.md#version-history).
- Multi-scale loss function replaces the afCRPS loss used in v1; variable bounding matching AIFS Single applied.
- Known limitation: smoothing along the Antarctic sea-ice edge in summer wave forecasts.

No wave output existed in AIFS ENS v1. See the [AIFS ENS entry](../../../ensemble_models/global/eu/aifs-ens.md#version-history) for the full model version history.

---

## Verification record
Established on **2026-07-30** by direct inspection rather than from documentation:
- Directory enumeration of `aifs-ens/0p25/waef/` across the 00z, 06z and 12z cycles (2026-07-30) and the 18z cycle (2026-07-29), for file-type inventory, step lists and forecast horizons at every cycle
- `.index` sidecar parsing of `-cf` and `-pf` files at steps 0 and 24 for record counts, member numbering, per-member record counts and parameter inventory
- ecCodes 2.48.0 decode of byte-range-extracted `swh` messages for perturbed members 1 and 50, and the leading messages of the control file, for ensemble encoding keys and mask behaviour
- Full index breakdown of both `-ep` files for the probability parameter set and step conventions
- `Last-Modified` header sampling across three cycles for publication latency, compared against ENS-WAM measurements from the same session
- `Content-Length` sampling for per-step and per-cycle volumes
- MD5 comparison of the same object across the ECMWF portal, GCS and the AWS regional endpoint

Architecture, training regime and loss functions are **not live-verifiable** and are sourced from the AIFS ENS v2 implementation page, the AIFS blog, and the Lang et al. papers.

Where live observation and ECMWF documentation disagree, the live observation is recorded and the disagreement flagged rather than silently resolved.

---

## Official documentation
- AIFS ENS v2 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+AIFS+ENS+v2
- ECMWF open data user documentation: https://confluence.ecmwf.int/display/DAC/ECMWF+open+data%3A+real-time+forecasts+from+IFS+and+AIFS
- Open data licence and parameter subset: https://www.ecmwf.int/en/forecasts/datasets/open-data
- AIFS Blog: https://www.ecmwf.int/en/about/media-centre/aifs-blog
- Anemoi framework: https://anemoi.readthedocs.io/
- ECMWF Open Data Python client: https://github.com/ecmwf/ecmwf-opendata
- AWS Open Data Registry entry: https://registry.opendata.aws/ecmwf-forecasts/

### Key references
- Lang, Leutbecher and Maciel (2025). *A multi-scale loss function for training probabilistic machine-learning weather models.* arXiv:2506.10868. https://arxiv.org/abs/2506.10868
- Lang et al. (2024). *AIFS — ECMWF's data-driven forecasting system.* arXiv:2406.01465. https://arxiv.org/abs/2406.01465
