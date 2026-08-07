# AI-Based and Hybrid Weather Models

This page indexes all weather models in this repository that use machine learning or artificial intelligence as a core part of the forecast process.

AI-based weather models have rapidly moved from research into operational use. This index groups them together to make discovery easier, since they are otherwise distributed across the repository by operator and model type (global/regional, deterministic/ensemble).

---

## How AI is being used in operational NWP

Different meteorological centres are integrating AI into weather prediction in different ways. Four patterns have emerged:

1. **Standalone AI forecast models.** A trained neural network replaces the physics-based forecast integration entirely. The model starts from a physics-derived analysis (initial conditions) and rolls forward autoregressively using learned dynamics. *Examples: AIFS Single, AIGFS, GEML.*

2. **AI-based ensembles.** Same as above, but producing ensemble members rather than a single forecast. Centres differ in how spread is generated: ECMWF's AIFS ENS is a single probabilistically-trained network that injects random noise during inference, while NOAA's AIGEFS runs members with different sets of learned model weights and inherits its initial-condition perturbations from GEFS. The two are not interchangeable in what their spread represents. *Examples: AIFS ENS, AIGEFS.*

3. **Hybrid physics–AI systems.** The physics-based model continues to run the forecast, but AI predictions guide or constrain parts of it — typically through spectral nudging toward AI-generated large-scale fields. *Examples: GDPS (ECCC, operational since v10.0.0 — physics forecast nudged toward the GEML AI model).*

4. **Grand ensembles combining physics and AI members.** Members from a physics-based ensemble and an AI-based ensemble are combined into a single larger ensemble for probabilistic guidance. The combination happens upstream of distribution: HGEFS publishes only the pooled ensemble mean and spread, not the 62 members. *Examples: HGEFS.*

These approaches reflect genuinely different theories about how AI should enter operational forecasting. The physics-based NWP community has not converged on a single answer, and this repository documents multiple approaches side by side.

---

## Deterministic AI global models

### [AIFS Single (ECMWF)](./models/nwp_models/global/eu/aifs-single.md)
- **Operator:** ECMWF
- **Operational since:** February 25, 2025 (v1.0.0); currently **v2** (May 12, 2026), deployed jointly with IFS Cycle 50r1
- **Approach:** Standalone AI, MSE-trained, encoder–processor–decoder architecture with graph neural networks and transformer
- **Resolution:** ~0.25° (Open Data)
- **Forecast length:** Up to 15 days
- **Note:** v2 added a 10 hPa pressure level (14 total), a snow cover parameter, and ECMWF's first data-driven ocean wave forecasts.

### [AIGFS (NOAA)](./models/nwp_models/global/usa/aigfs.md)
- **Operator:** NOAA / NCEP
- **Operational since:** December 17, 2025 (replaced EAGLE SOLO); currently **v1.1** (July 27, 2026)
- **Approach:** Standalone AI (GraphCast-derived), emulates GFS behavior
- **Resolution:** 0.25° (1440 × 721), 13 pressure levels, top at 50 hPa
- **Forecast length:** 384 h (16 days), 6-hourly, 4× daily
- **Note:** v1.1 replaced the grid-point MSE loss with a spherical harmonic loss, fine-tuned on four years of GDAS analysis, and trained autoregressively to 72 h; product content was unchanged. Distributed on NOMADS only — the `aigfs.*` prefixes in the AWS EAGLE bucket are experimental development output with a different parameter set, not an operational mirror.

### [GEML (ECCC)](./models/nwp_models/global/canada/gdps-geml.md)
- **Operator:** ECCC / Canadian Meteorological Centre
- **Status:** Operational (v1.1; AI component of GDPS v10.0.0, operational since May 26, 2026)
- **Approach:** Standalone AI (GraphCast-derived), retrained on ERA5 + ECMWF HRES analyses; weights publicly distributed via HuggingFace
- **Resolution:** 0.25° (~28 km)
- **Forecast length:** 10 days
- **Note:** Also serves as the spectral nudging target for the operational [GDPS](./models/nwp_models/global/canada/gem-global.md) (since v10.0.0, May 26, 2026), giving it a dual role as both a standalone product and a component of ECCC's hybrid forecasting system.

### [AICON-Global (DWD)](./models/nwp_models/global/germany/aicon-global.md)
- **Operator:** Deutscher Wetterdienst (DWD)
- **Status:** Operational — **AICON v1.0** (introduced September 3, 2025; Open Data from early 2026). DWD describes it as its operational AI forecasting system; it complements the physics-based ICON suite rather than forming part of it.
- **Approach:** Standalone AI, Anemoi framework; GraphCast-like encoder–processor–decoder with a Graph-Transformer GNN, built directly on ICON's triangular mesh (hierarchical multi-mesh, skip connections so the network learns forecast increments)
- **Training data:** ICON-DREAM reanalysis (2010–2023), 3-hourly, 13 km
- **Resolution:** ~13 km — native ICON R3B07 icosahedral grid, same DWD grid number (26) and grid UUID as [ICON Global](./models/nwp_models/global/germany/icon-global.md). A regular lat–lon rendering is produced internally but is **not** distributed on Open Data.
- **Vertical structure:** 13 reduced ICON model levels (SLEVE), ICON levels 49–119, top at 21,115 m (~50 hPa). Distributed with levels renumbered 1–13.
- **Forecast length:** 180 h at 00/12 UTC, 120 h at 06/18 UTC; 3-hourly steps. Cycled every 3 h, but only the four main cycles reach Open Data.
- **Note:** DWD's first AI-based forecast model; complements but does not replace the physics-based [ICON](./models/nwp_models/global/germany/icon-global.md). The regional variant **AICON-EU** became operational on June 30, 2026 (see *Regional AI models*).

### [Pangu-Weather — AIWP reforecast archive (NOAA/CIRA)](./models/nwp_models/global/usa/pangu-aiwp.md)
- **Operator:** CIRA (Colorado State University) + NOAA-GSL, distributed via NOAA Open Data Dissemination (NODD); underlying model by Huawei Cloud (Bi et al., 2023)
- **Status:** Reforecast archive + near-real-time (not an operational forecast product)
- **Approach:** Standalone AI (3D Earth-Specific Transformer), initialized from operational analyses in two parallel streams — GFS and IFS
- **Resolution:** 0.25° (~28 km), 13 pressure levels
- **Forecast length:** 240 h (10 days), 6-hourly steps; 2× daily (00/12 UTC)
- **Period of record:** GFS-initialized ~10/2020 → present; IFS-initialized 01/2022 → present
- **Note:** Part of the AIWP archive (`noaa-oar-mlwp-data`) alongside [Aurora](./models/nwp_models/global/usa/aurora-aiwp.md), [FourCastNet](./models/nwp_models/global/usa/fourcastnet-aiwp.md), and [GraphCast](./models/nwp_models/global/usa/graphcast-aiwp.md) — one bucket, shared NetCDF-4 format, cadence, and open license. Distinct from Pangu-Weather in its original research form (see "What this page does not cover"). Described in Radford et al. (2025, BAMS).

### [Aurora — AIWP reforecast archive (NOAA/CIRA)](./models/nwp_models/global/usa/aurora-aiwp.md)
- **Operator:** CIRA (Colorado State University) + NOAA-GSL, distributed via NODD; underlying model by Microsoft Research AI for Science (Bodnar et al., 2025)
- **Status:** Reforecast archive + near-real-time (not an operational forecast product); undocumented in the AIWP registry/README as of this writing — verified from live bucket inspection
- **Approach:** Standalone AI foundation model (3D Swin Transformer U-Net), initialized from operational analyses in two parallel streams — GFS and IFS
- **Resolution:** 0.25° (~28 km), 13 pressure levels
- **Forecast length:** 240 h (10 days), 6-hourly steps; 2× daily (00/12 UTC)
- **Period of record:** both GFS- and IFS-initialized from January 2025 → present (shorter record than the other AIWP models)
- **Note:** Part of the AIWP archive (`noaa-oar-mlwp-data`), alongside [Pangu-Weather](./models/nwp_models/global/usa/pangu-aiwp.md), [FourCastNet](./models/nwp_models/global/usa/fourcastnet-aiwp.md), and [GraphCast](./models/nwp_models/global/usa/graphcast-aiwp.md); files are large (~4.6 GB per cycle at full 0.25°). This is the deterministic base Aurora 0.25° model — not Microsoft's newer Aurora 1.5 (ensemble/hourly). Described in Radford et al. (2025, BAMS).

### [GraphCast — AIWP reforecast archive (NOAA/CIRA)](./models/nwp_models/global/usa/graphcast-aiwp.md)
- **Operator:** CIRA (Colorado State University) + NOAA-GSL, distributed via NODD; underlying model by Google DeepMind (Lam et al., 2023)
- **Status:** Reforecast archive + near-real-time (not an operational forecast product)
- **Approach:** Standalone AI (encode–process–decode GNN on a multi-mesh icosahedral representation), running the upstream research weights unmodified, initialized from operational analyses in two parallel streams — GFS and IFS
- **Resolution:** 0.25° (~28 km), 13 pressure levels
- **Forecast length:** 240 h (10 days), 6-hourly steps; 2× daily (00/12 UTC)
- **Period of record:** GFS-initialized 2021-12-31 → present; IFS-initialized 2022-01-01 → present
- **Note:** **Not AIGFS and not GraphCastGFS** — same architecture, different operator, weights, format, and purpose. The richest AIWP model by field coverage: the only one carrying vertical velocity or precipitation. Files ~5.7 GB (GFS) / ~3.9 GB (IFS) per cycle.

### [FourCastNet — AIWP reforecast archive (NOAA/CIRA)](./models/nwp_models/global/usa/fourcastnet-aiwp.md)
- **Operator:** CIRA (Colorado State University) + NOAA-GSL, distributed via NODD; underlying model by NVIDIA (Pathak et al., 2022; Bonev et al., 2023)
- **Status:** Reforecast archive + near-real-time (not an operational forecast product)
- **Approach:** Standalone AI (Spherical Fourier Neural Operator), running the upstream research weights via ECMWF's `ai-models-fourcastnetv2` plugin, initialized from operational analyses in two parallel streams — GFS and IFS
- **Resolution:** 0.25° (~28 km), 13 pressure levels
- **Forecast length:** 240 h (10 days), 6-hourly steps; 2× daily (00/12 UTC)
- **Period of record:** v2-small GFS-initialized 2020-09-30 → present, IFS-initialized 2022-01-01 → present; legacy v1 (`FOUR_v100_GFS`) frozen at 2020-09-30 → 2023-10-31 and fully superseded by the backfilled v2
- **Note:** **Not FourCastNetGFS**, which ended 2026-03-01. The only AIWP model carrying 100-m winds, surface pressure, or total column water vapour — and the only one using *relative* rather than specific humidity on pressure levels, so cross-model humidity comparison needs a conversion. Carries no vertical velocity or precipitation.

---

## Ensemble AI global models

### [AIFS ENS (ECMWF)](./models/ensemble_models/global/eu/aifs-ens.md)
- **Operator:** ECMWF
- **Operational since:** July 1, 2025 (v1); currently **v2** (May 12, 2026), deployed jointly with IFS Cycle 50r1
- **Approach:** Standalone AI, probabilistically trained (multi-scale loss in v2, replacing v1's afCRPS); ensemble members generated via noise injection
- **Resolution:** ~0.25° (Open Data); native ~30 km
- **Members:** 51 (1 control + 50 perturbed)
- **Note:** v2 added a 10 hPa pressure level, a probabilistic wave ensemble stream (ECMWF's first), and snow cover, convective precipitation, and volumetric soil moisture parameters.

### [AIGEFS (NOAA)](./models/ensemble_models/global/usa/aigefs.md)
- **Operator:** NOAA / NCEP
- **Operational since:** December 17, 2025 (replaced EAGLE Ensemble); still **v1.0**
- **Approach:** Standalone AI (GraphCast-derived), ensemble companion to AIGFS. Initial-condition perturbations inherited from GEFS; model uncertainty represented by running members with different sets of learned model weights — no stochastic physics and no inference-time noise injection
- **Resolution:** 0.25° (1440 × 721), 13 pressure levels, top at 50 hPa
- **Members:** 31 (`mem000`–`mem030`), with ensemble mean and spread distributed alongside
- **Forecast length:** 384 h (16 days), 6-hourly, 4× daily
- **Note:** Not upgraded alongside AIGFS v1.1. Carries one fewer surface field than AIGFS — only the 6-hour precipitation bucket, no run-total accumulation. ~162 GiB per cycle against a ~2-day NOMADS retention window, so mirror promptly.

---

## Regional AI models

Regional (limited-area) AI forecast systems. Unlike the global systems above, these cover a single national or continental domain, typically at convection-permitting resolution. This is a young category and still growing as centres extend AI methods to limited-area modelling.

> **AICON-EU (DWD)** — operational since June 30, 2026, but **not catalogued here yet because it is not on Open Data**. DWD's first regional AI model: ICON-EU domain at ~6.5 km (R3B08 grid, DWD grid number 27), the same 13 reduced levels and twelve parameters as [AICON-Global](./models/nwp_models/global/germany/aicon-global.md), 3-hourly, 120 h at 00/06/12/18 UTC and 48 h at the intermediate cycles. Initialized from AICON-Global rather than from an analysis, using an embedded-grid approach in which the global forecast in and around the LAM domain feeds the regional model. No `aicon-eu` directory exists under `/weather/nwp/v1/m/` as of 2026-08-05; worth monitoring. A ~2 km German-domain variant matching the ICON-D2 domain is planned (DWD's SKY nomenclature reserves the `la` domain code for it).

### [HRRR-Cast (NOAA)](./models/nwp_models/regional/usa/hrrrcast.md)
- **Operator:** NOAA / OAR — developed by Global Systems Laboratory (GSL); run experimentally at NWS/EMC
- **Status:** Experimental (not operational)
- **Approach:** Standalone AI emulator of the physics-based [HRRR](./models/nwp_models/regional/usa/hrrr.md); ensemble since V2 (members + ensemble mean)
- **Coverage:** Contiguous United States, on the native 3 km HRRR grid (1799 × 1059 Lambert conformal)
- **Resolution:** 3 km, 20 pressure levels
- **Forecast length:** 48 h, hourly cycles (24× daily)
- **Note:** NOAA's first regional experimental AI forecast system and a component of Project EAGLE. Reported at 100–1000× the efficiency of the operational HRRR (laptop-runnable). Live-feed member count was in transition when documented — a stable 9 members through 2026-07-10, expanding to 14 member tokens on 2026-07-11; likely a V3-era change but unconfirmed, so re-verify. Data: GRIB2 via the `noaa-gsl-experimental-pds` S3 bucket.

---

## Hybrid physics–AI systems

### [HGEFS (NOAA)](./models/ensemble_models/global/usa/hgefs.md)
- **Operator:** NOAA / NCEP
- **Operational since:** December 17, 2025; still **v1.0**
- **Approach:** Grand ensemble pooling 31 GEFS (physics) members with 31 AIGEFS (AI) members
- **Total members:** 62 (declared in every GRIB2 message as `numberOfForecastsInEnsemble = 62`) — **but the members are not distributed**
- **Resolution:** 0.25° (1440 × 721), 13 pressure levels, top at 50 hPa
- **Forecast length:** 240 h (10 days), 6-hourly, 4× daily — six days shorter than either parent, for reasons NOAA has not documented
- **Note:** Statistics-only product — the public feed carries just ensemble mean and spread, with no member files, probabilities, or percentiles. Grid and parameter set are inherited from AIGEFS, not GEFS. Does not replace either parent. The `noaa-hgefs-pds` S3 bucket exists but is empty.

### [GDPS (ECCC)](./models/nwp_models/global/canada/gem-global.md)
- **Operator:** ECCC / Canadian Meteorological Centre
- **Operational since:** May 26, 2026 (v10.0.0, replacing v9.1.0)
- **Approach:** GEM physics model spectrally nudged toward GEML at large scales (>2750 km) in the mid-troposphere (250–850 hPa); physics handles everything else
- **Resolution:** ~15 km, 84 levels, 10-day forecast 2× daily
- **Note:** The configuration previously distributed as the experimental GDPS, now operational. Beyond AI-physics hybridization, v10.0.0 carries forward the v9.0.0 framework (4DEnVar with 256-member LETKF backgrounds from GEPS 8.0.0, CICE 6.2.0 sea ice, NEMO 3.6 ice-ocean coupling).

---

## Experimental AI productionizations

The following systems are productionizations of research AI architectures run experimentally by NWP centres, which have not become operational forecast products. Both NOAA entries below are now **discontinued** — neither produces new forecasts, though their archives remain openly available.

### GraphCastGFS (NOAA) — discontinued
- **Operator:** NOAA / NCEP
- **Status:** Experimental; **ceased production 2026-05-05** (final day partial — 00 UTC only)
- **Approach:** Productionization of Google DeepMind's GraphCast architecture, fine-tuned on GDAS+ERA5 data
- **Lineage:** Predecessor to the operational [AIGFS](./models/nwp_models/global/usa/aigfs.md), which superseded it
- **Data:** GRIB2, `graphcastgfs.YYYYMMDD/` prefixes in the `noaa-nws-graphcastgfs-pds` bucket (2024-02-05 → 2026-05-05). That bucket is still active, but what updates in it now is experimental AIGFS/AIGEFS development output, not GraphCastGFS.

### FourCastNetGFS (NOAA) — discontinued
- **Operator:** NOAA / NCEP
- **Status:** Experimental; **ceased production 2026-03-01** (final day partial — 00/06/12 UTC only)
- **Approach:** Productionization of NVIDIA's FourCastNet v2 architecture using Spherical Fourier Neural Operators, initialized from NCEP 0.25° GDAS analyses
- **Lineage:** No operational descendant — NOAA's operational AI line went to GraphCast, not FourCastNet
- **Data:** GRIB2, `fcngfs.YYYYMMDD/` prefixes in `noaa-nws-fourcastnetgfs-pds` (2024-05-02 → 2026-03-01). The AWS registry entry still describes the system in the present tense.

---

## Models consuming AI-based inputs

Some operational models do not themselves use AI but incorporate AI-model outputs as inputs to their blending or post-processing logic.

### [NBM v5.0 (NOAA)](./models/nwp_models/regional/usa/nbm.md)
As of NBM v5.0 (operational May 5, 2026), the National Blend of Models incorporates two AI-based global models as inputs — **ECAIFS** (ECMWF's AI/IFS hybrid) and **AIGFS** (NOAA's operational GraphCast-lineage model) — for temperature, wind speed, and QPF products. Both are ingested as deterministic inputs. NBM's input set evolves version-to-version, so the AI-input mix is expected to grow as further AI systems go operational (see [STATUS.md](./STATUS.md) and the [NBM entry](./models/nwp_models/regional/usa/nbm.md) for the full input list and planned changes).

---

## Architectural lineages

Most operational AI weather models trace back to a small number of research architectures. This section maps the relationships:

### GraphCast lineage (Google DeepMind, 2023)
The GraphCast research architecture has been productionized by both NOAA and ECCC, with each centre fine-tuning it on different reanalysis and analysis data:

- **GraphCast** (research) → **GraphCastGFS** (NOAA experimental, ended 2026-05-05) → **[AIGFS](./models/nwp_models/global/usa/aigfs.md)** (NOAA operational)
- **GraphCast** (research) → **[GEML](./models/nwp_models/global/canada/gdps-geml.md)** (ECCC experimental)
- **GraphCast** (research) → redistributed *unmodified* as open GFS- and IFS-initialized reforecasts via the **[NOAA/CIRA AIWP archive](./models/nwp_models/global/usa/graphcast-aiwp.md)**

The two fine-tuned lineages share architecture and a 13-pressure-level vertical structure but differ in training data, fine-tuning procedures, and operational status. The AIWP arm is different in kind: it runs the upstream research weights without fine-tuning, so it is a redistribution rather than a productionization. Three distinct NOAA-affiliated datasets therefore carry GraphCast output — AIGFS, GraphCastGFS, and AIWP GraphCast — and they are not interchangeable.

### FourCastNet lineage (NVIDIA, 2022)
- **FourCastNet** (research) → **FourCastNetGFS** (NOAA experimental, ended 2026-03-01; no operational descendant)
- **FourCastNet** (research) → redistributed *unmodified* as open GFS- and IFS-initialized reforecasts via the **[NOAA/CIRA AIWP archive](./models/nwp_models/global/usa/fourcastnet-aiwp.md)**, in both v1 and v2-small configurations

### Pangu-Weather lineage (Huawei, 2023)
- **Pangu-Weather** (3D Earth-Specific Transformer, research) → redistributed as open GFS- and IFS-initialized reforecasts via the **[NOAA/CIRA AIWP archive](./models/nwp_models/global/usa/pangu-aiwp.md)**

### Aurora lineage (Microsoft, 2024/2025)
- **Aurora** (3D Swin Transformer U-Net foundation model, research) → redistributed as open GFS- and IFS-initialized reforecasts via the **[NOAA/CIRA AIWP archive](./models/nwp_models/global/usa/aurora-aiwp.md)**. Microsoft's later **Aurora 1.5** (ensemble/hourly) is a separate release, not in the archive.

### ECMWF in-house architecture
- **[AIFS Single](./models/nwp_models/global/eu/aifs-single.md)** and **[AIFS ENS](./models/ensemble_models/global/eu/aifs-ens.md)** use ECMWF's own encoder–processor–decoder architecture with attention-based GNN encoder/decoder and sliding-window transformer processor. Not derived from GraphCast or FourCastNet.
- **[AICON-Global](./models/nwp_models/global/germany/aicon-global.md)** (DWD) shares the Anemoi encoder–processor–decoder framework with AIFS, applied to ICON's icosahedral mesh and model-level vertical structure rather than ECMWF's lat–lon / pressure-level setup.

---

## What this page does not cover

- Research-only AI models with no public data, unless they have been productionized or openly redistributed by an NWP centre. Pangu-Weather, Aurora, GraphCast, and FourCastNet in their original research forms fall here; the NOAA/CIRA AIWP reforecast archive redistributes all four as open data and *is* covered — see the [Pangu-Weather](./models/nwp_models/global/usa/pangu-aiwp.md), [Aurora](./models/nwp_models/global/usa/aurora-aiwp.md), [GraphCast](./models/nwp_models/global/usa/graphcast-aiwp.md), and [FourCastNet](./models/nwp_models/global/usa/fourcastnet-aiwp.md) entries.
- AI post-processing and statistical correction systems that operate downstream of physics-based forecasts without participating in the forecast integration itself.
- Climate reanalysis ML applications.

---

## Further reading

For background on the transition from physics-based to AI and hybrid NWP, ECMWF Newsletters 181 (AIFS ensembles) and 185 (AIFS ENS operational, IFS Cycle 50r1) are useful starting points. For the Canadian hybrid approach, Husain et al. (2024) on AI-based spectral nudging via arXiv (arXiv:2407.06100) is the primary reference. For NOAA's direction, the NOAA press release on AI-driven global weather models (December 2025) covers AIGFS, AIGEFS, and HGEFS in one place.
