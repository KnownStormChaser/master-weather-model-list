# AIFS ENS (Artificial Intelligence Forecasting System – Ensemble)

## What this model is
AIFS ENS is ECMWF's operational machine-learning-based global ensemble weather forecast system.

Like the deterministic [AIFS Single](../../../nwp_models/global/eu/aifs-single.md), AIFS ENS is a trained neural network rather than a physics-based solver. Unlike AIFS Single, it is specifically trained to produce probabilistic forecasts using a CRPS-derived loss function (replaced by a multi-scale loss in v2) and generates ensemble members by injecting random Gaussian noise into the transformer processor during inference.

AIFS ENS runs operationally alongside the physics-based [IFS ENS](./ifs-ens.md) and is designed to complement rather than replace it. It became operational on 1 July 2025, making it ECMWF's second operational machine-learning forecast system after AIFS Single (operational 25 February 2025). Since v2 (12 May 2026) it also drives a separate data-driven wave ensemble stream, catalogued separately (see *Notes*).

---

## Who runs it
- **Organization:** European Centre for Medium-Range Weather Forecasts
- **Country / region:** International (European consortium of 23 Member States and 12 Co-operating States)

---

## What area it covers
- **Coverage:** Global
- **Open Data grid (verified):** 1440 × 721 regular latitude–longitude, 0.25° × 0.25°, 1,038,240 points per field
- **Grid origin:** first grid point 90°N / 180°, scanning mode 0 (west→east, north→south) — the same dateline-first raw layout as the rest of the ECMWF Open Data tree. See the [IFS entry](../../../nwp_models/global/eu/ifs.md#what-area-it-covers).
- **Sea mask:** no bitmap on the atmospheric stream — all 1,038,240 points valid.

---

## Basic details
- **Model type:** Global ensemble (AI-based)
- **Model system / core:** AIFS — encoder–processor–decoder with attention-based graph neural networks (encoder/decoder) and a sliding-window transformer (processor), the same architecture as AIFS Single
- **Dynamical formulation:** Not applicable — AIFS ENS does not solve dynamical equations. It is a trained neural network that predicts atmospheric evolution directly from learned patterns in historical weather data.
- **Convection-allowing:** No (AIFS does not represent or parameterize convection explicitly; convective behaviour is implicit in what the network has learned at ~31 km resolution)
- **Training framework:** Anemoi (open-source AI-NWP framework co-developed with ECMWF Member States)
- **Training data:** ERA5 reanalysis (1979–2022) plus ECMWF operational analyses (2018 onwards for fine-tuning)
- **Model parameters:** ~229 million
- **Ensemble size:** **51 — 1 control + 50 perturbed, all distributed** (verified)
- **Native grid:** N320 reduced Gaussian grid (~31 km, ~0.25°)
- **Open Data resolution:** 0.25° regular latitude–longitude only. The 0.1° tier ECMWF added on 2026-10-20 covers IFS only; `aifs-ens/` still contains `0p25` only (verified through the 2026-10-21 06z run).
- **Pressure levels:** 14 — 10, 50, 100, 150, 200, 250, 300, 400, 500, 600, 700, 850, 925, 1000 hPa. **Specific humidity (`q`) is the exception at 13 levels**, absent at 10 hPa — the same gap as AIFS Single.
- **Soil levels:** 2 (`sol` 1–2)
- **Forecast timestep:** 6 hours, uniform throughout — no 3-hourly section
- **Forecast length (verified):** **360 h (15 days) at every cycle**, 61 steps
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Observed publication latency (verified) — changed on 2026-10-20 06z:**
  - **Until the 2026-10-20 00z run:** the July version of this entry recorded **T+6h40m** at every cycle from portal timestamps (00z 06:40, 06z 12:40, 12z 18:40 UTC), control and perturbed files together. On the AWS mirror the whole run — `-cf`, `-pf` and `-ep` — arrived over 12–16 minutes, starting *earlier* than that: 05:48–06:00 UTC on 2026-10-19 00z, 05:50–06:05 on 2026-10-20 00z.
  - **From the 2026-10-20 06z run:** steps are released **one at a time, about one minute apart**. On the 2026-10-21 00z run: step 0 at 06:40 (**T+6h40m**), the +360 h `-pf` file at 07:40 (**T+7h40m**), and the second `-ep` file at 08:01 UTC (**T+8h01m**). Same offsets at every cycle from 2026-10-20 06z to 2026-10-21 12z, with portal and AWS timestamps identical to within seconds.
  - **The complete run now finishes at the same minutes as IFS ENS 0.25°** — whose `enfo` files arrive 07:40–08:01 UTC for 00z.

> **AIFS ENS is longer than IFS ENS, and its first steps are earlier — but no longer the whole run.** The physics-based [IFS ENS](./ifs-ens.md) runs to 360 h only at 00 and 12 UTC, dropping to 144 h at 06 and 18 UTC. AIFS ENS carries the full 61-step, 360 h run at **all four cycles**, so for a fresh 15-day ensemble at 06 or 18 UTC it is still the only ECMWF option. Previous versions of this entry said AIFS ENS "lands exactly one hour ahead" of IFS ENS at 00/12 UTC. Since 2026-10-20 06z that holds for step 0 only: the final AIFS ENS files arrive at the same time as the IFS ENS ones. No ECMWF announcement of this change has been found. As with [AIFS Single](../../../nwp_models/global/eu/aifs-single.md#basic-details), the portal's own timestamps were already staggered per step before the change; what verifiably changed is when the files reach the cloud mirrors.

### Volume

| File | Per step (before → from 2026-10-20 06z) | Per cycle, current (61 steps) |
|---|---|---|
| `-cf.grib2` (control) | 89.0 MB → **~58 MB** | ~3.5 GB |
| `-pf.grib2` (50 members) | 4,481.9 MB → **~2,930 MB** | ~179 GB |
| **Combined, per cycle (incl. `-ep`)** | | **~184 GB** |
| **Per day (4 cycles)** | | **~738 GB** (~1.1 TB before) |

Current per-day figures are summed from AWS listings over four consecutive cycles (2026-10-20 12z to 2026-10-21 06z). The drop of about a third comes from lower packing precision on 2026-10-20 — see *GRIB2 encoding*. Still the second-largest product in the 0.25° tree, behind [IFS ENS](./ifs-ens.md) at ~1,178 GB/day (and far behind the 0.1° IFS ENS at ~5.3 TB/day). AIFS ENS carries fewer parameters per member (108 records against IFS ENS's 177) but runs the full 61-step, 360 h forecast at all four cycles where IFS ENS runs 49 steps at two of them, which closes most of the gap. Cross-stream comparison table in the [IFS entry](../../../nwp_models/global/eu/ifs.md#volume-across-the-open-data-tree).

---

## Data assimilation
- **Data assimilation:** No — AIFS ENS runs no assimilation of its own. It consumes analyses produced by the physics-based IFS 4D-Var system and propagates them forward with the neural network.
- **Method / cadence:** Not applicable. See [IFS](../../../nwp_models/global/eu/ifs.md#data-assimilation) for the analysis system that supplies the initial state, and [IFS ENS](./ifs-ens.md#data-assimilation) for the EDA that supplies the perturbations.

---

## Initial conditions

> *The template's "Initial and boundary conditions" section is scoped to limited-area ensembles. AIFS ENS is global and has no lateral boundary conditions; the initial-condition half is recorded here and the boundary half is not applicable.*

AIFS ENS members are initialized member-by-member from **IFS ENS initial conditions**, not from a single shared analysis:

- **Control member:** initialized from the **IFS control analysis** — the same unperturbed analysis used to initialize AIFS Single and the deterministic IFS — regridded from O1280 (~0.1°) to N320 (~0.25°).
- **Perturbed members:** initialized from the corresponding **IFS ENS perturbed analyses**, member for member, likewise regridded to N320.
- **Boundary conditions:** not applicable (global domain).

Because initial conditions are shared with IFS ENS, differences between the two ensembles at a given lead time reflect model behaviour rather than initialization.

---

## Perturbations and design
- **Initial condition perturbations:** Inherited from [IFS ENS](./ifs-ens.md) on a member-by-member basis — singular vectors plus the Ensemble of Data Assimilations, generated by ECMWF's physics-based assimilation system.
- **Model/physics perturbations:** Not applicable in the conventional sense. AIFS ENS has no parametrizations to perturb. Model uncertainty is instead represented by **injecting random Gaussian noise into the transformer processor during inference** — the network is trained to map that noise onto realistic forecast spread.
- **Stochastic schemes:** None of the SPP/SPPT family. The noise-injection mechanism above is the entire model-uncertainty representation.
- **Loss function:** v1 used an "almost fair" CRPS (afCRPS) loss; v2 replaces this with a **multi-scale loss** (Lang, Leutbecher and Maciel, 2025; arXiv:2506.10868) and applies the same physical-consistency variable bounding used by AIFS Single. The probabilistic training approach is designed to produce ensembles with realistic spread and atmospheric variability, avoiding the over-smoothing problem common in deterministically-trained (MSE) AI forecast models.

### Member packaging (verified)
AIFS ENS packages its members **differently from every other ensemble in the ECMWF Open Data tree**, and more conventionally:

- **Control and perturbed members are in separate files.** Each step produces a `-cf.grib2` (108 messages, the control alone) and a `-pf.grib2` (5,400 messages, 50 members × 108). This contrasts with [IFS ENS](./ifs-ens.md), which ships a single `-ef.grib2` per step containing only the perturbed members, with the control relocated entirely to a different stream.
- **The control is present.** `-cf` files exist at all four cycles and all 61 steps. Anyone porting an IFS ENS workflow to AIFS ENS should not carry over the post-50r1 assumption that the control must be fetched from elsewhere.
- **`number` is absent from `-cf` index records** and present with values 1–50 in `-pf` records.
- **Encoding is properly set**, unlike IFS ENS:

| Key | AIFS ENS control | AIFS ENS perturbed | IFS ENS |
|---|---|---|---|
| `mars.type` | `cf` | `pf` | `pf` only |
| `perturbationNumber` | **0** | 1–50 | 1–50 |
| `typeOfEnsembleForecast` | **5** | **6** | **255 (missing)** |
| `numberOfForecastsInEnsemble` | 51 | 51 | 51 |
| Members actually shipped | 1 | 50 | 50 |

> **`numberOfForecastsInEnsemble = 51` is correct here.** Both AIFS ENS and IFS ENS declare 51, but AIFS ENS actually distributes 51 forecasts while IFS ENS distributes 50. The same header value is accurate in one stream and stale in the other — a good reason to size member arrays from the data rather than the header regardless of which model you are reading. AIFS ENS also sets `typeOfEnsembleForecast` meaningfully (5 for control, 6 for perturbed), where IFS ENS leaves it at 255.

---

## What it provides

Verified by decoding `20260730000000-24h-enfo-cf.index` and `-pf.index` (108 and 5,400 records respectively).

### Direct model output — `-cf` and `-pf` files, 29 parameters

**Pressure levels** (6 parameters): `z`, `t`, `u`, `v`, `w`, `q`

**Surface** (21): `2t`, `2d`, `10u`, `10v`, `100u`, `100v`, `msl`, `sp`, `skt`, `tp`, `cp`, `sf`, `rowe`, `fscov`, `tcc`, `hcc`, `mcc`, `lcc`, `tcw`, `ssrd`, `strd`

**Soil** (2 × 2 layers): `sot`, `vsw`

Two gaps worth knowing:

> ⚠️ **`q` is missing at 10 hPa** — 13 levels against 14 for everything else, giving 83 pressure-level records rather than 84. Consistent with the v2 changelog, which lists the new 10 hPa level for geopotential, temperature, horizontal winds and vertical velocity but not humidity. Same behaviour as [AIFS Single](../../../nwp_models/global/eu/aifs-single.md#what-it-provides).

> ⚠️ **Geopotential height (`gh`) is not distributed.** AIFS Single carries both `z` and `gh` on pressure levels; AIFS ENS carries only `z`. Workflows moving between the deterministic and ensemble AI streams must derive `gh` from `z` themselves. The surface and soil parameter lists are otherwise identical between the two.

Relative to [IFS ENS](./ifs-ens.md), AIFS ENS **lacks** relative humidity, divergence, vorticity, wind gusts, min/max 2 m temperature, precipitation type, radiation net fluxes, snow depth and density, surface stresses, the coupled ocean/sea-ice fields, and CAPE. It **adds** convective precipitation (`cp`), fraction of snow cover (`fscov`), runoff water equivalent (`rowe`), and split cloud-cover layers. There is no step-dependent parameter switching — the 108-record structure is constant across all 61 steps.

**No orography is distributed.** Take it from the IFS `oper` step-0 file.

### Derived products — `-ep` files

As with IFS ENS, the `ep` filename token is misleading: these are **mixed containers holding `em`, `es` and `ep`**. The 240h file carries 1,192 records (492 `em`, 492 `es`, 208 `ep`); the 360h file carries 585.

**Ensemble mean (`em`) and spread (`es`) — 7 parameters each**

| Level type | Parameters | Levels |
|---|---|---|
| Pressure | `t` | 250, 500, 850 hPa |
| Pressure | `ws` (wind speed) | 250, 850 hPa |
| Pressure | `z` | 300, 500, 1000 hPa |
| Surface | `2t`, `msl`, `10si`, `100si` | — |

This is a **richer mean/spread set than IFS ENS**, which distributes only `gh`, `t`, `ws` and `msl`. AIFS ENS adds 2 m temperature and wind speed at 10 m and 100 m (`10si`, `100si`), and uses geopotential rather than geopotential height.

**Event probabilities (`ep`) — 14 parameters**

| Group | Parameters | Step convention |
|---|---|---|
| Precipitation accumulation | `tpg1`, `tpg5`, `tpg10`, `tpg20`, `tpg25`, `tpg50`, `tpg100` | 24 h windows stepping 12 h (`0-24`, `12-36`, … `216-240`) — 19 windows |
| Wind speed | `10spg10`, `10spg15` | Instantaneous, 12-hourly (12–240 h) |
| Freezing | `2tl273` | Instantaneous |
| Precipitation rate and dry spells | `tpl01`, `tprl1`, `tprg3`, `tprg5` | **Multi-day aggregates only** — `120-168`, `120-240`, `168-240` |

Naming decodes as: `tpg25` = P(total precipitation > 25 mm); `10spg15` = P(10 m wind speed > 15 m/s); `2tl273` = P(2 m temperature < 273 K), i.e. a frost probability; `tpl01` = P(total precipitation < 0.1 mm), i.e. a dry probability; `tprg3` / `tprl1` = P(precipitation rate above / below a threshold).

Three differences from IFS ENS worth noting:
- **AIFS gives wind *speed* probabilities where IFS gives wind *gust* probabilities** (`10spg*` vs `10fgg*`). This follows directly from AIFS not producing gusts at all.
- **AIFS adds frost (`2tl273`) and dry-spell (`tpl01`, `tprl1`) probabilities** that IFS ENS does not distribute.
- **IFS ENS has temperature standardized-anomaly probabilities (`ptsa_*`) that AIFS ENS does not.**

> **Three different step conventions coexist in one file** — 24-hour rolling windows, instantaneous 12-hourly, and 5-to-10-day aggregates. Code that assumes a single step semantics across the `-ep` container will mis-associate the extended-range precipitation products.

> ⚠️ **Unlike IFS ENS, AIFS ENS publishes `-ep` products at all four cycles.** Deduplicated listings confirm two `-ep` files each at 00z, 06z, 12z and 18z. IFS ENS restricts them to 00 and 12 UTC — and its documentation incorrectly claims otherwise, as recorded in the [IFS ENS entry](./ifs-ens.md#what-it-provides).

### Tropical cyclone tracks (`type=tf`)
One BUFR trajectory file per cycle. Verified decode of the 2026-07-30 00z file: 56 messages, `numberOfSubsets` ranging **1 to 52**.

The maximum of 52 is worth flagging — [IFS ENS](./ifs-ens.md#what-it-provides) tops out at 51 after Cycle 50r1 reduced its TC ensemble from 52 to 51. AIFS ENS carries 51 forecasts (1 control + 50 perturbed), so a 52-subset message implies one additional track per feature beyond the member count. **TBD** what the extra subset represents.

> **Scope note.** Point trajectories rather than gridded fields, so outside this repository's gridded-data scope rule. Documented for completeness because they ship in the same directory as the in-scope GRIB2 products.

---

## GRIB2 encoding

| Key | AIFS ENS | AIFS Single | IFS |
|---|---|---|---|
| `mars.class` | `ai` | `ai` | `od` |
| `generatingProcessIdentifier` | **2** | **5** | 161 (`oper`), 109 (`wave`/`waef`) |
| `tablesVersion` | 36 | 36 | 32 |
| `localTablesVersion` | 0 throughout | 0 throughout | 1 on six parameters |
| Packing | `grid_ccsds` | `grid_ccsds` | `grid_ccsds` |

`generatingProcessIdentifier` cleanly separates all four systems: **2** (AIFS ENS), **5** (AIFS Single), **161** (IFS atmospheric), **109** (IFS wave family). `mars.class` separates AI from physics at a coarser level.

Like AIFS Single, AIFS ENS uses **no ECMWF local tables** — it is already on the far side of the GRIB2 parameter-encoding migration that IFS completes at Cycle 50r2. Expect `paramId` mismatches when comparing AIFS and IFS output for the same variable; `tp` is the clearest case, documented in the [AIFS Single entry](../../../nwp_models/global/eu/aifs-single.md#grib2-encoding--aifs-differs-from-ifs-in-ways-that-break-naive-filters). Match on `shortName`, not `paramId`.

`tablesVersion = 36` means older ecCodes installations may lack the necessary definitions; 2.42.0 or newer is recommended, with `libaec` compiled in for CCSDS.

### Packing precision reduced on 2026-10-20 06z
Re-packed with fewer bits per value on the same cycle as the AIFS Single and IFS 0.25° changes. Parameter inventory unchanged: 112 / 5,600 messages at step 0 and 108 / 5,400 at +24 h in `-cf` / `-pf`, and 1,192 / 585 records in the 240h / 360h `-ep` files, before and after. Measured on `-pf` member 1 and the `-cf` control at +24 h, 2026-10-20 00z vs 2026-10-21 00z:

| Field | Bits per value before → after | Approx. quantisation step before → after |
|---|---|---|
| `2t` (control and members) | 12 → **9** | 0.03 K → **0.25 K** |
| `tp` (control and members) | 16 → **12** | 0.008 mm → **0.0625 mm** |
| `msl` | 12 → 12 (unchanged) | 4 Pa |
| `z` 500 hPa | 16 → 16 (unchanged) | 0.25 m²/s² |

Steps depend on each message's value range. Ensemble statistics computed from archive data spanning 2026-10-20 mix two quantisation regimes. Fuller field-by-field figures for the deterministic stream are in the [AIFS Single entry](../../../nwp_models/global/eu/aifs-single.md#packing-precision-reduced-on-2026-10-20-06z).

---

## Data availability
- **Is the data free?** Yes (Open Data subset)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 with CCSDS compression (`tablesVersion = 36`); BUFR edition 4 for tropical cyclone tracks; JSON-lines `.index` sidecars
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0) plus the ECMWF Terms of Use; attribution required
- **Retention on the ECMWF portal:** rolling archive of the most recent 12 forecast runs (~2–3 days)
- **Open Data streams:**
  - `stream=enfo` — atmospheric ensemble (this entry)
  - `stream=waef` — wave ensemble (new in v2, catalogued separately; see *Notes*)
- **Official download location:**
  https://data.ecmwf.int/forecasts/
  - Path pattern: `[ROOT]/<YYYYMMDD>/<HH>z/aifs-ens/0p25/enfo/<YYYYMMDD><HH>0000-<step>h-enfo-<type>.grib2`
  - Example: `20260730/00z/aifs-ens/0p25/enfo/20260730000000-24h-enfo-pf.grib2`

### Stream inventory (verified)

| File type | Cycles | Contents |
|---|---|---|
| `-cf.grib2` | 00, 06, 12, 18 | Control forecast, 108 records per step |
| `-pf.grib2` | 00, 06, 12, 18 | 50 perturbed members, 5,400 records per step |
| `-ep.grib2` | 00, 06, 12, 18 | Ensemble mean, spread, event probabilities |
| `-tf.bufr` | 00, 06, 12, 18 | Tropical cyclone tracks |

Identical structure at every cycle — 61 + 61 + 2 + 1 files.

### Access channels
Verified 2026-07-30: the `24h-enfo-cf.index` object returned matching MD5 (`a6199c6a970f...`, 25,806 bytes) from the ECMWF portal, GCS **and** the AWS regional endpoint.

| Channel | Endpoint | Anonymous? | Archive depth |
|---|---|---|---|
| **ECMWF portal** | `https://data.ecmwf.int/forecasts/` | Yes (500-connection cap) | Rolling ~12 runs |
| **AWS S3** | `s3://ecmwf-forecasts` (`eu-central-1`) | Yes — unsigned | `aifs-ens/` from ~2025-07/08 |
| **Azure Blob** | `https://ai4edataeuwest.blob.core.windows.net/ecmwf` | **No — SAS token required** | as above |
| **Google Cloud Storage** | `gs://ecmwf-open-data` | Yes — fully unauthenticated | as above |

Bisection of the AWS mirror places the first `aifs-ens/` directory between 2025-07-01 and 2025-08-01, consistent with the 1 July 2025 operational date. Full access mechanics are documented once in the [IFS entry](../../../nwp_models/global/eu/ifs.md#data-availability).

> **Byte-range retrieval is effectively mandatory.** A single `-pf` step file is ~2.9 GB (4.48 GB before the 2026-10-20 packing change) holding 5,400 messages; one member-parameter field is under 1 MB. Pulling one parameter across all 50 members costs a few tens of MB against the whole file — roughly a 100× saving. The `.index` sidecar gives `_offset` and `_length` per message; offsets change every run. At ~738 GB/day, expect AWS `SlowDown` throttling on bulk work and prefer GCS for archive pulls, as documented in the [ENS-WAM entry](../../../wave_models/global/eu/ecwam-ens.md#access-channels).

> **Since 2026-10-20, don't treat the first file as a complete run.** With per-step release, a poller that sees step 0 at T+6h40m and immediately walks the step list will hit missing files for the next hour. Wait for the expected count — 61 `-cf`, 61 `-pf` and 2 `-ep` files — or poll each step individually.

AIFS ENS is distributed at its native 0.25° resolution, with no 0.1° tier. Like AIFS Single, it has been available at 0.25° from initial release; the entire ECMWF Real-time Catalogue (including AIFS) became CC-BY-4.0 licensed on 1 October 2025.

---

## Notes
- **Relationship to the deterministic counterpart.** AIFS ENS shares the encoder–processor–decoder architecture of [AIFS Single](../../../nwp_models/global/eu/aifs-single.md) but differs in training methodology, loss function, and the addition of stochastic noise injection at inference. The two are **not equivalent** — the AIFS ENS control is a separately trained probabilistic network, not AIFS Single re-run. Their parameter sets also differ: AIFS ENS omits `gh`.
- **Relationship to the physics-based sibling.** Runs alongside [IFS ENS](./ifs-ens.md), considered complementary rather than a replacement. Depends on IFS for initial conditions — it inherits IFS ENS's perturbed analyses member by member and relies on the physics-based 4D-Var system to produce them, then propagates forward with the neural network.
- **AIFS wave ensemble is catalogued separately.** From v2 the `aifs-ens` tree carries a `waef/` stream alongside `enfo/` — ECMWF's first probabilistic data-driven wave forecasts, verified present on 2026-07-30. Following the repository's convention of filing by phenomenon, it is catalogued as [AIFS ENS Wave](../../../wave_models/global/eu/aifs-ens-wave.md) in `models/wave_models/`, next to [ENS-WAM](../../../wave_models/global/eu/ecwam-ens.md).
- ECMWF stopped running its experimental machine learning model suite (Aurora, FourCastNet, GraphCast, Pangu-Weather) as of the joint AIFS/IFS upgrade on 12 May 2026.
- **Two soil layers, not four**, as with AIFS Single. Land-surface workflows ported from IFS ENS will find the deeper layers absent.
- Due to the non-determinism of GPU computation, users running AIFS ENS themselves cannot exactly reproduce ECMWF's official forecasts.
- Indexed in [`AI_MODELS.md`](../../../../AI_MODELS.md).

---

## Version history

### Open Data distribution change — 20 October 2026, 06 UTC run
Not a model change — AIFS ENS is still v2:
- **Steps released one at a time**, about one minute apart, from T+6h40m (step 0) to T+7h40m (+360 h), with the second `-ep` file at T+8h01m. Before, the whole run reached the cloud mirrors in under 20 minutes.
- **Lower packing precision** (e.g. `2t` 12 → 9 bits, `tp` 16 → 12 bits); files about a third smaller.
- **No change** to members, parameters, levels, steps, grid or paths. No 0.1° tier.
- Same cycle as the launch of the 0.1° IFS tier; see the [IFS entry](../../../nwp_models/global/eu/ifs.md#recent-version-history).

### AIFS ENS v2 — operational 12 May 2026 (current)
Major upgrade deployed jointly with [IFS Cycle 50r1](../../../nwp_models/global/eu/ifs.md#recent-version-history) and [AIFS Single v2](../../../nwp_models/global/eu/aifs-single.md#version-history). Architectural and scientific changes:

- **New 10 hPa pressure level** for geopotential, temperature, horizontal winds and vertical velocity — **not** for specific humidity, which remains on 13 levels (verified).
- **New wave ensemble stream** (`stream=waef`) — the first probabilistic data-driven wave forecasts ECMWF has issued. Catalogued separately.
- **New parameters:** fraction of snow cover (`fscov`), convective precipitation (`cp`), volumetric soil moisture (`vsw`). The latter two harmonize AIFS ENS output with AIFS Single.
- **Multi-scale loss function** replaces the afCRPS loss used in v1, improving physical consistency at multiple spatial scales (Lang, Leutbecher and Maciel, 2025; arXiv:2506.10868).
- **Variable bounding** matching AIFS Single is now applied, improving physical consistency.
- **Revised graph features** — the decoder now uses more edges with new edge features.

### AIFS ENS v1 — operational 1 July 2025
First operational version of the AIFS ensemble. Replaced earlier research configurations including AIFS–Diffusion (12-hour timestep) and early AIFS–CRPS (6-hour timestep). v1 used the afCRPS ("almost fair" CRPS) loss function.

Training: pre-training on ERA5 (1979–2022, 300,000 steps), then fine-tuning on ERA5 + IFS operational analyses (2018–2023). Initial conditions derived from IFS ENS perturbations on a member-by-member basis; ensemble spread generated through injected Gaussian noise during inference.

v1 used a 13-pressure-level vertical structure (50 hPa upward) — the 10 hPa level was added in v2.

---

## Verification record
Established on **2026-07-30** by direct inspection rather than from documentation:
- Directory enumeration of `aifs-ens` across the 00z, 06z and 12z cycles (2026-07-30) and the 18z cycle (2026-07-29), for stream inventory, file types, step lists and forecast horizons at every cycle
- `.index` sidecar parsing of `-cf` and `-pf` files at step 24 for record counts, member numbering, per-member record counts and per-level parameter coverage — establishing both the `q` gap at 10 hPa and the absence of `gh`
- ecCodes 2.48.0 decode of byte-range-extracted `2t` messages for perturbed members 1 and 50, and the leading message of the control file, for ensemble encoding keys
- Full index breakdown of both `-ep` files for the `em` / `es` / `ep` split, parameter and level coverage, and the three coexisting step conventions
- ecCodes BUFR decode of the tropical cyclone track file for message and subset counts
- `Last-Modified` header sampling across three cycles for publication latency, compared against IFS ENS and AIFS Single measurements from the same session
- `Content-Length` sampling for per-step and per-cycle volumes
- MD5 comparison of the same object across the ECMWF portal, GCS and the AWS regional endpoint

The 2026-10-20 distribution changes were verified on **2026-10-21**:
- AWS `LastModified` windows for every cycle from 2026-10-19 00z to 2026-10-21 12z (the 12z run observed arriving step by step), plus portal and AWS `Last-Modified` for steps 0 and 360 and the 360h `-ep` file on four cycles either side of the change
- Directory listing confirming `aifs-ens/` holds `0p25` only on the 2026-10-21 00z and 06z runs
- `.index` comparison of `-cf`, `-pf` and both `-ep` files before (2026-10-20 00z) and after (2026-10-21 00z)
- ecCodes 2.50.0 bits-per-value decode of `2t`, `tp`, `msl` and `z` 500 hPa before and after
- `Content-Length` sampling across dates and cycles, and summed AWS listing sizes over four consecutive cycles for volume

Architecture, training regime, loss functions, model parameter count and version history are **not live-verifiable** and are sourced from ECMWF implementation pages, the AIFS blog, and the Lang et al. papers.

Where live observation and ECMWF documentation disagree, the live observation is recorded and the disagreement flagged rather than silently resolved.

---

## Official documentation
- AIFS ENS v2 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+AIFS+ENS+v2
- AIFS ENS v1 implementation page: https://confluence.ecmwf.int/display/FCST/Implementation+of+AIFS+ENS+v1
- ECMWF open data user documentation: https://confluence.ecmwf.int/display/DAC/ECMWF+open+data%3A+real-time+forecasts+from+IFS+and+AIFS
- ECMWF open data: what's new at 0.1° (IFS only; context for the 2026-10-20 changes): https://confluence.ecmwf.int/spaces/DAC/pages/738041087/ECMWF+open+data+what+s+new+at+0.1%C2%B0
- ECMWF AIFS overview: https://www.ecmwf.int/en/forecasts/documentation-and-support/changes-ecmwf-model
- AIFS Blog: https://www.ecmwf.int/en/about/media-centre/aifs-blog
- Anemoi framework: https://anemoi.readthedocs.io/
- ECMWF Open Data portal: https://www.ecmwf.int/en/forecasts/datasets/open-data
- ECMWF Open Data Python client: https://github.com/ecmwf/ecmwf-opendata
- AWS Open Data Registry entry: https://registry.opendata.aws/ecmwf-forecasts/

### Key references
- Lang, Leutbecher and Maciel (2025). *A multi-scale loss function for training probabilistic machine-learning weather models.* arXiv:2506.10868. https://arxiv.org/abs/2506.10868
- Lang et al. (2024). *AIFS — ECMWF's data-driven forecasting system.* arXiv:2406.01465. https://arxiv.org/abs/2406.01465
- Moldovan et al. (2025). *An update to ECMWF's machine-learned weather forecast model AIFS.* arXiv:2509.18994. https://arxiv.org/abs/2509.18994
