# AI-Based and Hybrid Weather Models

This page indexes all weather models in this repository that use machine learning or artificial intelligence as a core part of the forecast process.

AI-based weather models have rapidly moved from research into operational use. This index groups them together to make discovery easier, since they are otherwise distributed across the repository by operator and model type (global/regional, deterministic/ensemble).

---

## How AI is being used in operational NWP

Different meteorological centres are integrating AI into weather prediction in different ways. Three patterns have emerged:

1. **Standalone AI forecast models.** A trained neural network replaces the physics-based forecast integration entirely. The model starts from a physics-derived analysis (initial conditions) and rolls forward autoregressively using learned dynamics. *Examples: AIFS Single, AIGFS.*

2. **AI-based ensembles.** Same as above, but trained probabilistically to produce ensemble members, usually by injecting random noise during inference. *Examples: AIFS ENS, AIGEFS.*

3. **Hybrid physics–AI systems.** The physics-based model continues to run the forecast, but AI predictions guide or constrain parts of it — typically through spectral nudging toward AI-generated large-scale fields. *Examples: GDPS-SN (experimental).*

4. **Grand ensembles combining physics and AI members.** Members from a physics-based ensemble and an AI-based ensemble are combined into a single larger ensemble for probabilistic guidance. *Examples: HGEFS.*

These approaches reflect genuinely different theories about how AI should enter operational forecasting. The physics-based NWP community has not converged on a single answer, and this repository documents multiple approaches side by side.

---

## Deterministic AI global models

### [AIFS Single (ECMWF)](./models/nwp_models/global/eu/aifs-single.md)
- **Operator:** ECMWF
- **Operational since:** February 25, 2025 (v1.0.0); currently v1.1.0 (August 27, 2025)
- **Next upgrade:** v2 scheduled for May 12, 2026
- **Approach:** Standalone AI, MSE-trained, encoder–processor–decoder architecture with graph neural networks and transformer
- **Resolution:** ~0.25° (Open Data)
- **Forecast length:** Up to 15 days

### [AIGFS (NOAA)](./models/nwp_models/global/usa/aigfs.md)
- **Operator:** NOAA / NCEP
- **Status:** Experimental
- **Approach:** Standalone AI (GraphCast-based), emulates GFS behavior
- **Resolution:** 0.25°
- **Forecast length:** Up to ~16 days

---

## Ensemble AI global models

### [AIFS ENS (ECMWF)](./models/ensemble_models/global/eu/aifs-ens.md)
- **Operator:** ECMWF
- **Operational since:** July 1, 2025
- **Next upgrade:** v2 scheduled for May 12, 2026
- **Approach:** Standalone AI, CRPS-trained for probabilistic forecasting; ensemble members generated via noise injection
- **Resolution:** ~0.25° (Open Data); native ~30 km
- **Members:** 51 (1 control + 50 perturbed)

### [AIGEFS (NOAA)](./models/ensemble_models/global/usa/aigefs.md)
- **Operator:** NOAA / NCEP
- **Status:** Experimental
- **Approach:** Standalone AI, ensemble companion to AIGFS
- **Resolution:** ~0.25°

---

## Hybrid physics–AI systems

### [HGEFS (NOAA)](./models/ensemble_models/global/usa/hgefs.md)
- **Operator:** NOAA / NCEP
- **Approach:** Grand ensemble combining 31 GEFS (physics) members with 31 AIGEFS (AI) members
- **Total members:** 62
- **Note:** Does not replace either GEFS or AIGEFS; provides a larger, more diverse ensemble

### [GDPS-SN (ECCC) — experimental](./models/nwp_models/global/canada/gem-global.md#experimental-ai-hybrid-configuration-gdps-sn)
- **Operator:** ECCC / Canadian Meteorological Centre
- **Status:** Experimental
- **Approach:** GEM physics model spectrally nudged toward GEML (ECCC's GraphCast-derived AI model) at large scales; physics handles everything else
- **Documented within:** the main GDPS entry
- **Note:** Not yet a replacement for operational GDPS, but represents the clearest direction of Canadian global NWP development

---

## Models consuming AI-based inputs

Some operational models do not themselves use AI but incorporate AI-model outputs as inputs to their blending or post-processing logic.

### [NBM v5.0 (NOAA)](./models/nwp_models/regional/usa/nbm.md)
As of NBM v5.0 (operational April 15, 2026), the National Blend of Models incorporates ECAIFS (ECMWF's AI/IFS hybrid) and AIGFS as inputs for temperature, wind speed, and QPF products.

---

## What this page does not cover

- Research-only AI models with no public data (e.g., GraphCast, Pangu-Weather, FourCastNet in their original research forms) unless they have been productionized by an NWP centre.
- AI post-processing and statistical correction systems that operate downstream of physics-based forecasts without participating in the forecast integration itself.
- Climate reanalysis ML applications.

---

## Further reading

For background on the transition from physics-based to AI and hybrid NWP, ECMWF Newsletters 181 (AIFS ensembles) and 185 (AIFS ENS operational, IFS Cycle 50r1) are useful starting points. For the Canadian hybrid approach, Husain et al. (2024/2025) on GDPS-SN via arXiv is the primary reference. For NOAA's direction, the NOAA press release on AI-driven global weather models (December 2025) covers AIGFS, AIGEFS, and HGEFS in one place.
