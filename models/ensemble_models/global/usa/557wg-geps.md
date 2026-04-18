# 557th WW GEPS (Global Ensemble Prediction Suite)

## What this model is
The 557th Weather Wing Global Ensemble Prediction Suite (GEPS) is a statistical multi-model ensemble forecast system produced operationally by the U.S. Air Force's 557th Weather Wing.

Unlike most ensembles, which generate members from a single modeling center's own perturbed runs, 557th WW GEPS ingests ensemble members from **three different operational numerical modeling centers** — NOAA/NCEP, the U.S. Navy's Fleet Numerical Meteorology and Oceanography Center (FNMOC), and Environment and Climate Change Canada's Canadian Meteorological Centre (CMC) — and combines them into a single unified statistical ensemble product.

This system is the operational distribution form of the multi-center ensemble known historically as the **National Unified Operational Prediction Capability (NUOPC)** ensemble.

---

## Who runs it
- **Organization:** U.S. Air Force 557th Weather Wing
- **Country / region:** United States (product); multinational (member inputs)
- **Headquarters:** Offutt Air Force Base, Nebraska

---

## What area it covers
- **Coverage:** Global

---

## Basic details
- **Model type:** Global statistical multi-model ensemble
- **Total members:** 63 (21 from each of three contributing centers)
- **Contributing systems:**
  - NOAA / NCEP: 21 members (GEFS-based)
  - U.S. Navy / FNMOC: 21 members (NAVGEM-based ensemble)
  - Environment Canada / CMC: 21 members (Canadian GEPS-based)
- **Horizontal resolution:** 1° gridded output
- **Forecast length:** 240 hours (10 days)
- **Update frequency:** 2× daily (00, 12 UTC)
- **Temporal output resolution:** 6-hourly

---

## Products
557th WW GEPS produces derived statistical products rather than distributing raw individual member output. Products include:

- Probabilities for precipitation
- Probabilities for snowfall
- Ensemble mean and standard deviation for:
  - Temperature
  - Wind
  - Pressure
  - Geopotential height
  - Relative humidity

These products are intended for probabilistic guidance applications including military mission planning, aviation forecasting, and general-purpose medium-range probabilistic weather prediction.

---

## Relationship to other models
557th WW GEPS is built from three single-center ensembles, each of which is documented separately in this repository:

- **NCEP contribution:** based on [GEFS](./gefs.md)
- **FNMOC contribution:** the Navy's operational ensemble built around [NAVGEM](../../../nwp_models/global/usa/navgem.md)
- **CMC contribution:** based on [Canada's GEPS](../canada/geps.md) (note: same acronym as the 557th WW product — see note below on disambiguation)

The multi-model, multi-center design is intended to sample uncertainty more broadly than any single-model ensemble can, by combining members with different underlying physics, data assimilation, and model error characteristics.

This system is conceptually related to the **North American Ensemble Forecast System (NAEFS)**, which combines only the NCEP and CMC ensembles. 557th WW GEPS extends that approach by adding the Navy's ensemble as a third contributor.

---

## Naming note: GEPS disambiguation
The acronym "GEPS" is used for two unrelated ensemble systems in operational forecasting:

- **557th WW GEPS** (this entry): Global Ensemble Prediction **Suite** — a U.S. Air Force statistical multi-model ensemble
- **Canadian GEPS** ([separate entry](../canada/geps.md)): Global Ensemble Prediction **System** — Environment Canada's single-center global ensemble

These are entirely different systems. The Canadian GEPS is one of the inputs to 557th WW GEPS, but the two should not be confused with one another.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  https://nomads.ncep.noaa.gov/pub/data/nccf/com/557ww/prod/

---

## Notes
- 557th WW GEPS produces derived statistical products (probabilities, means, standard deviations) rather than individual raw ensemble members. Users needing individual member data should access the contributing single-center ensembles (GEFS, NAVGEM ensemble, Canadian GEPS) directly.
- The 557th Weather Wing is the lead military meteorology center of the U.S. Air Force. It reports environmental situational awareness worldwide to the Air Force, U.S. Army, joint warfighters, Unified Combatant Commands, the intelligence community, and the Secretary of Defense.
- Although operated by the U.S. military, the ensemble product is publicly distributed through NOAA's NOMADS system under standard open data terms.
- The underlying multi-center NUOPC framework has been operational since the mid-2010s and has demonstrated consistent skill improvements over any single contributing ensemble, particularly for surface variables.

---

## Official documentation
- 557th Weather Wing: https://www.557weatherwing.af.mil/
- NOMADS 557th WW product directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/557ww/prod/
