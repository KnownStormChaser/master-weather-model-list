# NAEFS (North American Ensemble Forecast System)

## What this model is
The North American Ensemble Forecast System (NAEFS) is a **global, multi-center bias-corrected ensemble** produced operationally as a joint project between three national meteorological services. It combines the U.S. NWS's [GEFS](./gefs.md) and Environment and Climate Change Canada's [Canadian GEPS](../canada/geps.md) into a single bias-corrected "grand ensemble" that provides probabilistic medium-range guidance seamless across the Canada–U.S.–Mexico borders.

NAEFS is not a forecast model in the usual sense — it does not run its own atmospheric integrations. Instead, it ingests ensemble members produced by the contributing centers, applies statistical bias correction against observations, and combines them into unified probabilistic products. The operational rationale is that combining two independent, well-calibrated single-center ensembles produces probabilistic forecasts that are skill-superior to either parent ensemble alone, particularly in the medium range (days 1–14).

NAEFS was officially launched in November 2004 and is the longest-running operational multi-center ensemble in international NWP. The current operational version is **v7.0**, implemented December 5, 2023, which expanded the GEFS contribution from a 21-member subset to all 31 GEFS bias-corrected members.

---

## Who runs it
- **Joint project between three organizations:**
  - **U.S. National Weather Service (NWS)** — operational production and primary data distribution via NCEP Central Operations (NCO)
  - **Meteorological Service of Canada (MSC)** / Environment and Climate Change Canada — provides the Canadian GEPS contribution and produces derived chart products
  - **National Meteorological Service of Mexico (NMSM / SMN)** — joint partner since the 2004 launch
- **Country / region:** Multinational (Canada, United States, Mexico)
- **Operational distribution lead:** NCEP / NOAA (via NOMADS)
- **Operational since:** November 2004

---

## What area it covers
- **Coverage:** Global
- **Primary use regions:** CONUS, Alaska, Canada, Mexico, with seamless cross-border probabilistic products across North America

Although NAEFS is technically global (because both GEFS and the Canadian GEPS are global ensembles), its design intent and primary operational use focus on North America, where the harmonization of probabilistic guidance across the Canada–U.S.–Mexico boundaries is the central operational benefit.

---

## Basic details
- **Model type:** Multi-center bias-corrected global ensemble (statistical combination, not a forecast model)
- **Contributing systems:**
  - **NCEP [GEFS](./gefs.md):** 31 bias-corrected members (control + 30 perturbed) — full ensemble used as of v7.0
  - **MSC [Canadian GEPS](../canada/geps.md):** 21 bias-corrected members (control + 20 perturbed)
- **Total members:** 52 bias-corrected ensemble members
- **Horizontal resolution:** 0.5° (the harmonized grid for bias-corrected products)
- **Forecast length:** 384 hours (16 days)
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:**
  - 3-hourly from initial time out to +192 hours
  - 6-hourly from +192 hours out to +384 hours

---

## Methodology

### Bias correction
Each contributing ensemble's members are statistically bias-corrected against analyses before being combined. The bias correction is computed and applied independently for GEFS and Canadian GEPS members against their respective verification data, removing systematic model biases that would otherwise propagate into the combined product.

### Combination
After bias correction, the GEFS and GEPS members are pooled into a single 52-member ensemble. Probabilistic products (probabilities of exceedance, percentiles, ensemble mean, ensemble spread) are then derived from this combined membership.

### Why combine two ensembles?
The two parent ensembles use different model cores (GEFS uses GFS / FV3-based physics; the Canadian GEPS uses GEM with Yin–Yang grid), different data assimilation systems, and different perturbation strategies. Their forecast errors are therefore partially independent, and combining them samples a broader range of model uncertainty than either could alone. The bias-corrected combination has demonstrated skill improvements over either parent throughout the 1–14 day range, with the largest gains in the medium range (days 5–10) where model uncertainty dominates.

---

## What it provides

### Bias-corrected gridded fields (via NOMADS)
- Bias-corrected GEFS ensemble members (all 31 members, 0.5°)
- Bias-corrected GEFS analysis fields
- Bias-corrected ensemble forecasts of standard atmospheric variables: temperature, wind, geopotential height, humidity, pressure, precipitation
- Calibrated probabilistic precipitation products, including 6-hour and 24-hour accumulation forecasts on the NDGD 2.5 km CONUS grid
- Forecasts from the Canadian GEPS contribution and the FNMOC ensemble are also distributed alongside NAEFS data on NOMADS

### Derived probabilistic products (via MSC)
- Day 8–14 temperature anomaly outlooks
- EPSgrams combining ensemble and deterministic forecasts
- Standard deviation / mean charts
- Probability maps of weather events over time intervals

### Typical applications
- Medium-range probabilistic temperature, wind, and precipitation forecasts
- Calibrated precipitation guidance for hydrological forecasting
- Cross-border seamless probabilistic guidance for North American operational forecasting
- Long-range (week 2) outlook generation

---

## Relationship to other models

### Parent ensembles
NAEFS is built directly on two single-center ensembles, both documented separately in this repository:
- **U.S. contribution:** [GEFS](./gefs.md) (NCEP) — provides 31 members
- **Canadian contribution:** [Canadian GEPS](../canada/geps.md) (MSC / ECCC) — provides 21 members

### Related multi-center systems
- **[557th WW GEPS](./557wg-geps.md)** (U.S. Air Force Global Ensemble Prediction **Suite**): A separate multi-center statistical ensemble that extends NAEFS's approach by adding a third member set from FNMOC's NAVGEM-based ensemble. Conceptually a successor to NAEFS in the multi-center direction, although both run operationally side-by-side. Note that 557th WW GEPS uses the same "GEPS" acronym as the Canadian GEPS — see the disambiguation note in either entry.
- **NUOPC ensemble framework:** The broader multi-center ensemble framework (National Unified Operational Prediction Capability) under which 557th WW GEPS operates. NAEFS predates the NUOPC framework and operates independently of it.

### Distinction from contributing parents
NAEFS does not replace its parents. GEFS and Canadian GEPS continue to run independently and are distributed separately through their respective national channels. NAEFS is the bias-corrected combined product on top, distributed alongside the parent data.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (gridded forecasts), XML (MSC derived products)
- **Primary download location (NCEP NOMADS):**
  - https://nomads.ncep.noaa.gov/pub/data/nccf/com/naefs/prod/
  - https://www.ftp.ncep.noaa.gov/data/nccf/com/naefs/prod/
  - ftp://ftp.ncep.noaa.gov/pub/data/nccf/com/naefs/prod/
- **MSC Datamart (XML derived products):** https://eccc-msc.github.io/open-data/msc-data/nwp_naefs/readme_naefs-datamartxml_en/
- **GRIB filter (bias-corrected GEFS, includes NAEFS members 21–30):** https://nomads.ncep.noaa.gov/gribfilter.php?ds=gensbc
- **OPeNDAP (bias-corrected GEFS):** https://nomads.ncep.noaa.gov/dods/gens_bc
- **Parallel feed (used during version transitions):** https://nomads.ncep.noaa.gov/pub/data/nccf/com/naefs/para/

The bulk of operational NAEFS data is distributed through NOAA NOMADS rather than through MSC, which is why this entry is filed under `usa/` despite the multinational nature of the project. MSC distributes derived chart products and XML summaries but not the underlying gridded ensemble.

---

## Version history

### December 5, 2023 — NAEFS v7.0 (current)
- **Expanded GEFS contribution from 21 to all 31 bias-corrected members** — previously v6.1 used only a 21-member GEFS subset
- Probabilistic forecast skill extended by approximately 3–4 forecast hours
- Overall improvement in calibrated precipitation forecasts
- New bias-corrected files added for GEFS members 21–30: `gefs.YYYYMMDD/CC/pgrb2ap5_bc/gepMM.tCCz.pgrb2a.0p50_bcfFFF` and `pgrb2ap5_an/gepMM.tCCz.pgrb2a.0p50_anfFFF` (members 21–30, forecast steps 000–384)
- GEFS members 21–30 added to existing bias-corrected precipitation files (`prcp_bc_gb2`) and NDGD 2.5 km CONUS precipitation files (`ndgd_prcp_gb2`)
- The GRIB filter dataset `gensbc` and the OPeNDAP `gens_bc` product both expanded to include the additional members

### Earlier versions
- **v6.1 and earlier:** Used a 21-member GEFS subset alongside the Canadian GEPS contribution. NAEFS has gone through multiple incremental upgrades since the 2004 launch, generally tracking upgrades to the contributing parent ensembles (each major GEFS or Canadian GEPS upgrade typically prompts a NAEFS revision to incorporate the parent improvements into the bias correction and combination).
- **November 2004:** NAEFS officially launched as a joint MSC/NWS/NMSM project.

---

## Notes
- **NAEFS is a post-processing system, not a forecast model.** Compute cost is minimal compared to the parent ensembles — the operational expense is dominated by GEFS and the Canadian GEPS, with NAEFS itself adding only the bias correction and combination steps.
- **Why this is filed under `usa/` despite being trinational:** NCEP NCO operates the bias correction and combination, and NOMADS is the primary distribution channel for the gridded ensemble. ECCC and SMN are full project partners (the project is genuinely jointly governed and the costs are shared), but the operational data flow runs through U.S. infrastructure. The MSC Datamart distributes only derived XML and HTML chart products, not the underlying GRIB2 fields.
- **The 2004 launch is a notable milestone in operational NWP** — NAEFS was the first major operational multi-center ensemble combining independently-developed national systems, predating both the NUOPC framework and the 557th WW GEPS by roughly a decade. Its longevity demonstrates the durability of the multi-center ensemble approach.
- **The 31 + 21 = 52 member count is asymmetric** because the two parent ensembles have different sizes. v7.0's expansion to use all 31 GEFS members reflects GEFS's growth to its current 31-member configuration; the Canadian GEPS contribution remains at 21 members (control + 20 perturbed) matching the operational Canadian GEPS size.
- **As with all ensemble systems**, NAEFS output should be interpreted probabilistically rather than as a deterministic forecast. The product's value is specifically in its calibrated probabilities and ensemble spread, not in any single-member view.
- **Mexico's role (NMSM / SMN):** Mexico is a full project partner contributing to research, development, and operational direction, but does not currently operate its own contributing global ensemble. The Mexican meteorological service uses NAEFS products for cross-border probabilistic forecasting and contributes to the joint governance of the system.

---

## Official documentation
- NCEP/EMC NAEFS v7.0 page: https://www.emc.ncep.noaa.gov/users/meg/naefsv7/
- NWS Service Change Notice 23-104 (NAEFS v7.0): https://www.weather.gov/media/notification/pdf_2023_24/scn23-104_naefs_v7.0.pdf
- ECCC NAEFS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_naefs/readme_naefs_en/
- NOMADS NAEFS product directory: https://nomads.ncep.noaa.gov/pub/data/nccf/com/naefs/prod/
- COMET MetEd Introduction to NAEFS (account required): https://learn.meted.ucar.edu/#/online-courses/d336c069-8e91-425a-9d10-5b8a89009609
