# AI-Based and Hybrid Weather Models

This page indexes all weather models in this repository that use machine learning or artificial intelligence as a core part of the forecast process.

AI-based weather models have rapidly moved from research into operational use. This index groups them together to make discovery easier, since they are otherwise distributed across the repository by operator and model type (global/regional, deterministic/ensemble).

---

## How AI is being used in operational NWP

Different meteorological centres are integrating AI into weather prediction in different ways. Four patterns have emerged:

1. **Standalone AI forecast models.** A trained neural network replaces the physics-based forecast integration entirely. The model starts from a physics-derived analysis (initial conditions) and rolls forward autoregressively using learned dynamics. *Examples: AIFS Single, AIGFS, GEML.*

2. **AI-based ensembles.** Same as above, but trained probabilistically to produce ensemble members, usually by injecting random noise during inference. *Examples: AIFS ENS, AIGEFS.*

3. **Hybrid physics–AI systems.** The physics-based model continues to run the forecast, but AI predictions guide or constrain parts of it — typically through spectral nudging toward AI-generated large-scale fields. *Examples: GDPS (ECCC, operational since v10.0.0 — physics forecast nudged toward the GEML AI model).*

4. **Grand ensembles combining physics and AI members.** Members from a physics-based ensemble and an AI-based ensemble are combined into a single larger ensemble for probabilistic guidance. *Examples: HGEFS.*

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
- **Operational since:** December 17, 2025 (replaced EAGLE SOLO)
- **Approach:** Standalone AI (GraphCast-derived), emulates GFS behavior
- **Resolution:** 0.25°
- **Forecast length:** Up to ~16 days

### [GEML (ECCC)](./models/nwp_models/global/canada/gdps-geml.md)
- **Operator:** ECCC / Canadian Meteorological Centre
- **Status:** Operational (v1.1; AI component of GDPS v10.0.0, operational since May 26, 2026)
- **Approach:** Standalone AI (GraphCast-derived), retrained on ERA5 + ECMWF HRES analyses; weights publicly distributed via HuggingFace
- **Resolution:** 0.25° (~28 km)
- **Forecast length:** 10 days
- **Note:** Also serves as the spectral nudging target for the operational [GDPS](./models/nwp_models/global/canada/gem-global.md) (since v10.0.0, May 26, 2026), giving it a dual role as both a standalone product and a component of ECCC's hybrid forecasting system.

### [AICON-Global (DWD)](./models/nwp_models/global/germany/aicon-global.md)
- **Operator:** Deutscher Wetterdienst (DWD)
- **Status:** Provided for evaluation, research, and training (introduced September 3, 2025; Open Data available early 2026); not part of the operational ICON suite
- **Approach:** Standalone AI, Anemoi framework; GraphCast-like encoder–processor–decoder with a Graph-Transformer GNN, built directly on ICON's triangular mesh
- **Training data:** ICON-DREAM reanalysis (2010–2023)
- **Resolution:** ~13 km (native ICON R3B07 icosahedral grid; also regular lat–lon)
- **Vertical structure:** 13 reduced ICON model levels (SLEVE), surface to ~50 hPa
- **Forecast length:** Up to 180 hours
- **Note:** DWD's first AI-based forecast model; complements but does not replace the physics-based [ICON](./models/nwp_models/global/germany/icon-global.md). A limited-area extension (AICON-LAM) is on the roadmap for 2026–2027.

### [Pangu-Weather — AIWP reforecast archive (NOAA/CIRA)](./models/nwp_models/global/usa/pangu-aiwp.md)
- **Operator:** CIRA (Colorado State University) + NOAA-GSL, distributed via NOAA Open Data Dissemination (NODD); underlying model by Huawei Cloud (Bi et al., 2023)
- **Status:** Reforecast archive + near-real-time (not an operational forecast product)
- **Approach:** Standalone AI (3D Earth-Specific Transformer), initialized from operational analyses in two parallel streams — GFS and IFS
- **Resolution:** 0.25° (~28 km), 13 pressure levels
- **Forecast length:** 240 h (10 days), 6-hourly steps; 2× daily (00/12 UTC)
- **Period of record:** GFS-initialized ~10/2020 → present; IFS-initialized 01/2022 → present
- **Note:** Part of the AIWP archive (`noaa-oar-mlwp-data`) alongside Aurora, FourCastNet, and GraphCast — one bucket, shared NetCDF-4 format, cadence, and open license. Distinct from Pangu-Weather in its original research form (see "What this page does not cover"). Described in Radford et al. (2025, BAMS).

### [Aurora — AIWP reforecast archive (NOAA/CIRA)](./models/nwp_models/global/usa/aurora-aiwp.md)
- **Operator:** CIRA (Colorado State University) + NOAA-GSL, distributed via NODD; underlying model by Microsoft Research AI for Science (Bodnar et al., 2025)
- **Status:** Reforecast archive + near-real-time (not an operational forecast product); undocumented in the AIWP registry/README as of this writing — verified from live bucket inspection
- **Approach:** Standalone AI foundation model (3D Swin Transformer U-Net), initialized from operational analyses in two parallel streams — GFS and IFS
- **Resolution:** 0.25° (~28 km), 13 pressure levels
- **Forecast length:** 240 h (10 days), 6-hourly steps; 2× daily (00/12 UTC)
- **Period of record:** both GFS- and IFS-initialized from January 2025 → present (shorter record than the other AIWP models)
- **Note:** Part of the AIWP archive (`noaa-oar-mlwp-data`); files are large (~4.6 GB per cycle at full 0.25°). This is the deterministic base Aurora 0.25° model — not Microsoft's newer Aurora 1.5 (ensemble/hourly). Described in Radford et al. (2025, BAMS).

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
- **Operational since:** December 17, 2025 (replaced EAGLE Ensemble)
- **Approach:** Standalone AI, ensemble companion to AIGFS
- **Resolution:** ~0.25°

---

## Regional AI models

Regional (limited-area) AI forecast systems. Unlike the global systems above, these cover a single national or continental domain, typically at convection-permitting resolution. This is a young category, expected to grow as centres extend AI methods to limited-area modelling (e.g., DWD's planned AICON-LAM).

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
- **Operational since:** December 17, 2025
- **Approach:** Grand ensemble combining 31 GEFS (physics) members with 31 AIGEFS (AI) members
- **Total members:** 62
- **Note:** Does not replace either GEFS or AIGEFS; provides a larger, more diverse ensemble

### [GDPS (ECCC)](./models/nwp_models/global/canada/gem-global.md)
- **Operator:** ECCC / Canadian Meteorological Centre
- **Operational since:** May 26, 2026 (v10.0.0, replacing v9.1.0)
- **Approach:** GEM physics model spectrally nudged toward GEML at large scales (>2750 km) in the mid-troposphere (250–850 hPa); physics handles everything else
- **Resolution:** ~15 km, 84 levels, 10-day forecast 2× daily
- **Note:** The configuration previously distributed as the experimental GDPS, now operational. Beyond AI-physics hybridization, v10.0.0 carries forward the v9.0.0 framework (4DEnVar with 256-member LETKF backgrounds from GEPS 8.0.0, CICE 6.2.0 sea ice, NEMO 3.6 ice-ocean coupling).

---

## Experimental AI productionizations

The following systems are productionizations of research AI architectures that are run experimentally by NWP centres but have not (or have not yet) become operational forecast products.

### GraphCastGFS (NOAA)
- **Operator:** NOAA / NCEP
- **Status:** Experimental
- **Approach:** Productionization of Google DeepMind's GraphCast architecture, fine-tuned on GDAS+ERA5 data
- **Lineage:** Predecessor to the operational AIGFS

### FourCastNetGFS (NOAA)
- **Operator:** NOAA / NCEP
- **Status:** Experimental
- **Approach:** Productionization of NVIDIA's FourCastNet architecture using Spherical Fourier Neural Operators
- **Lineage:** No operational counterpart announced

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

- **GraphCast** (research) → **GraphCastGFS** (NOAA experimental) → **[AIGFS](./models/nwp_models/global/usa/aigfs.md)** (NOAA operational)
- **GraphCast** (research) → **[GEML](./models/nwp_models/global/canada/gdps-geml.md)** (ECCC experimental)

The two operational lineages share architecture and a 13-pressure-level vertical structure but differ in training data, fine-tuning procedures, and operational status.

### FourCastNet lineage (NVIDIA, 2022)
- **FourCastNet** (research) → **FourCastNetGFS** (NOAA experimental, no operational descendant announced)

### Pangu-Weather lineage (Huawei, 2023)
- **Pangu-Weather** (3D Earth-Specific Transformer, research) → redistributed as open GFS- and IFS-initialized reforecasts via the **[NOAA/CIRA AIWP archive](./models/nwp_models/global/usa/pangu-aiwp.md)**

### Aurora lineage (Microsoft, 2024/2025)
- **Aurora** (3D Swin Transformer U-Net foundation model, research) → redistributed as open GFS- and IFS-initialized reforecasts via the **[NOAA/CIRA AIWP archive](./models/nwp_models/global/usa/aurora-aiwp.md)**. Microsoft's later **Aurora 1.5** (ensemble/hourly) is a separate release, not in the archive.

### ECMWF in-house architecture
- **[AIFS Single](./models/nwp_models/global/eu/aifs-single.md)** and **[AIFS ENS](./models/ensemble_models/global/eu/aifs-ens.md)** use ECMWF's own encoder–processor–decoder architecture with attention-based GNN encoder/decoder and sliding-window transformer processor. Not derived from GraphCast or FourCastNet.
- **[AICON-Global](./models/nwp_models/global/germany/aicon-global.md)** (DWD) shares the Anemoi encoder–processor–decoder framework with AIFS, applied to ICON's icosahedral mesh and model-level vertical structure rather than ECMWF's lat–lon / pressure-level setup.

---

## What this page does not cover

- Research-only AI models with no public data, unless they have been productionized or openly redistributed by an NWP centre. Pangu-Weather and Aurora in their original research forms fall here; the NOAA/CIRA AIWP reforecast archive redistributes both as open data and *is* covered — see the [Pangu-Weather](./models/nwp_models/global/usa/pangu-aiwp.md) and [Aurora](./models/nwp_models/global/usa/aurora-aiwp.md) entries.
- AI post-processing and statistical correction systems that operate downstream of physics-based forecasts without participating in the forecast integration itself.
- Climate reanalysis ML applications.

---

## Further reading

For background on the transition from physics-based to AI and hybrid NWP, ECMWF Newsletters 181 (AIFS ensembles) and 185 (AIFS ENS operational, IFS Cycle 50r1) are useful starting points. For the Canadian hybrid approach, Husain et al. (2024) on AI-based spectral nudging via arXiv (arXiv:2407.06100) is the primary reference. For NOAA's direction, the NOAA press release on AI-driven global weather models (December 2025) covers AIGFS, AIGEFS, and HGEFS in one place.
