# SubC (The Subseasonal Consortium) — formerly SubX

## What this model is
SubC is a multi-model subseasonal prediction system that pools several independently run global models into a single multi-model ensemble (MME) at the weeks-1-to-4 range. It began in 2015 as The Subseasonal Experiment (SubX), a NOAA Climate Testbed project providing guidance to NOAA's week-3-4 temperature and precipitation outlooks, and was reorganized in 2023 into the Subseasonal Consortium. It maintains a research hindcast archive (1999–2015) and weekly real-time forecasts (since July 2017). The openly posted products are gridded temperature and precipitation anomalies by lead week, plus the full multi-model ensemble anomaly database — all NetCDF.

Note that SubC is an *operational-facing subseasonal prediction* experiment, not a climate-projection model. It sits at the sub-seasonal end of this category's range.

---

## Who runs it
- **Organization:** The Subseasonal Consortium — a group of academic, government, and non-profit partners, led from the University of Oklahoma (ESPLab / K. Pegion). The data archive is hosted at the International Research Institute for Climate and Society (IRI) Data Library, Columbia University.
- **Country / region:** United States (a multi-centre, multi-national collaboration)
- **Coordinating body / programme:** Originated as a NOAA/Climate Testbed project within the NOAA/MAPP S2S Task Force; contributes guidance to NOAA/CPC week-3-4 outlooks.

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Standard SubX grid of 1° × 1°. The OU feed additionally provides regional clips (North America, Mexico, Venezuela, Central Asia, Iran) alongside the global fields.

---

## Basic details
- **Model type:** Multi-model ensemble (umbrella) of global subseasonal models — anomaly products, deterministic and ensemble-statistic
- **Model system / core:** Varies by member (see Multi-model composition)
- **Range class:** Sub-seasonal (weeks 1–4; underlying forecasts run to ~32–45 days, model-dependent)
- **Forecast length:** ~32–45 days; products delivered at weekly leads (Week 1, 2, 3, 4) plus a combined Weeks-3-4 product
- **Initialization cadence:** Weekly (real-time feed in date-stamped folders, e.g. `20260618`)
- **Ensemble generation:** Multi-model — each member model contributes its own ensemble, pooled into the SubC MME
- **Ensemble size:** Originally seven global models; per-model ensemble sizes vary *(confirm current roster and member counts against the live IRIDL tree)*
- **Temporal output resolution:** Daily underlying output, delivered as weekly means
- **Output aggregation levels:** Weekly means (weeks 1–4), combined weeks 3–4; anomalies; ensemble max/mean/min

---

## Coupled configuration
The member models are a mix of fully coupled and atmosphere-focused global systems; dynamical cores, resolutions, and component models differ by member and are documented in the SubX/SubC technical record and the live IRIDL tree. There is no common formulation — only a common delivered grid of 1° × 1°.

---

## Initialization
Initialization varies by member: each centre initializes its own model from its own analyses and/or reanalyses. There is no shared SubC initialization.

---

## Hindcasts (reforecasts)
- **Hindcast period:** January 1999 – December 2015 (17 years) of retrospective forecasts.
- **Content:** Per-model reforecasts on the common 1° grid; used for lead-dependent bias correction, model climatology, and skill estimation.
- **Real-time forecasts:** Began July 2017 (weekly), continuing under SubC.
- **Host:** IRI Data Library.
- **Distributed alongside forecasts?** Yes — the IRIDL database holds both reforecasts and real-time forecasts.

---

## Sources of predictability
The MJO is the flagship subseasonal driver (skillful MJO prediction to ~4 weeks), with the NAO contributing at ~2 weeks and ENSO providing background signal. The multi-model mean is more skillful overall than any individual member.

---

## Multi-model composition
- **Contributing centres / models (original SubX suite — confirm the current SubC roster against the live IRIDL tree, as membership has evolved):** NASA/GMAO (GEOS); RSMAS/University of Miami (CCSM4); NOAA/ESRL (FIM); Environment and Climate Change Canada (GEM/GEPS); U.S. Naval Research Laboratory (NESM); NCEP/EMC (GEFS); and NCEP (CFSv2).
- **Combination method:** Multi-model ensemble on a common 1° grid; a SubC multi-model ensemble mean is the headline product.
- **Common delivered grid:** 1° × 1°.
- **Per-contributor terms:** Individual member outputs may carry their own modeling-centre acknowledgement conditions.

---

## What it provides
Openly posted, all NetCDF:
- **OU weekly feed** (`forecasts/data/{YYYYMMDD}/`):
  - 2-m temperature anomalies, global and regional (North America, Mexico, Venezuela, Central Asia, Iran), at weekly leads (`Week1`–`Week4`) and combined `Weeks34`
  - Surface precipitation anomalies as ensemble maximum, mean, and minimum (`fcst_{date}.anom.pr_sfc.e{max|mean|min}.nc`)
  - The full multi-model ensemble anomalies, weeks 1–4 (`subx_mme_anoms_wk_1-4_{date}.nc`, ~4.3 GB)
  - Per-run `manifest.json` and `validation_manifest.json`
- **IRIDL SubX database:** the fuller multivariable per-model and MME hindcast + real-time archive at 1°, daily

Viewer-only siblings (out of scope): the SubC static and interactive forecast maps.

---

## Data availability
- **Is the data free?** Yes — publicly accessible without registration.
- **License:** **Not formally specified.** No explicit license identifier is published on either the OU feed or the IRIDL archive (the OU documentation pages and user-guide PDF currently 404). The project requests citation of Kirtman et al. (2017; IRIDL dataset, doi:10.7916/D8PG249H) and Pegion et al. (2019). Member-model outputs may carry their own centre terms. *(Flag: confirm formal redistribution terms before treating as fully open — open access here does not by itself establish open licensing.)*
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF
- **Official download location:**  
  - OU weekly real-time feed: https://weather.ou.edu/~kpegion/subc/forecasts/data/
  - IRIDL SubX database (hindcasts + forecasts): https://iridl.ldeo.columbia.edu/SOURCES/.Models/.SubX/
- **Access route notes:** The OU feed is plain HTTPS in weekly date-stamped folders. IRIDL is a faceted data-library tree (append `/dods` for OpenDAP, with server-side subsetting). Processing tools are published at the consortium's GitHub (`ou-esplab/SubX`).

---

## Notes
- **Name change.** SubX → SubC in 2023; the IRIDL data tree is still rooted at `.SubX`, and `subx_*` naming persists in the feed.
- **Academic hosting.** Like NMME, the operational feed is hosted by academic institutions (OU + Columbia IRIDL) rather than a national met service — included on the same basis.
- **Distinct from the S2S Prediction Project database.** The WWRP/WCRP S2S database (also on IRIDL) is a *separate* system with research-use terms and a real-time embargo — a separate, likely out-of-scope candidate that should not be conflated with SubC.
- **Member overlap.** NCEP CFSv2 (own entry) and ECCC GEM/GEPS appear as SubC members; this entry cross-references rather than re-documenting.
- **License unresolved** (see Data availability) — the single most important item to confirm before this is treated as settled.

---

## Recent version history
- **2015:** Launched as The Subseasonal Experiment (SubX), a NOAA Climate Testbed project.
- **2023:** Reorganized into the Subseasonal Consortium (SubC). Membership continues to evolve; the live IRIDL tree reflects the active set.

---

## Official documentation
- SubC home: https://weather.ou.edu/~kpegion/subc/
- IRIDL SubX database: https://iridl.ldeo.columbia.edu/SOURCES/.Models/.SubX/
- Code / tools: https://github.com/ou-esplab/SubX
- Pegion et al. (2019), *The Subseasonal Experiment (SubX): A Multimodel Subseasonal Prediction Experiment*, BAMS: https://doi.org/10.1175/BAMS-D-18-0270.1
- Kirtman et al. (2017), *The Subseasonal Experiment (SubX)*, IRI Data Library dataset: https://doi.org/10.7916/D8PG249H
