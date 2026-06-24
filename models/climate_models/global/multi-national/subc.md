# SubC (The Subseasonal Consortium) — formerly SubX

## What this model is
SubC is a multi-model subseasonal prediction system that pools several independently run global models into a single multi-model ensemble (MME) at the weeks-1-to-4 range. It began in 2015 as The Subseasonal Experiment (SubX), a NOAA Climate Testbed project providing guidance to NOAA's week-3-4 temperature and precipitation outlooks, and was reorganized in 2023 into the Subseasonal Consortium. It maintains a research hindcast archive (1999–2015) and weekly real-time forecasts (since July 2017). The openly posted products are gridded temperature and precipitation anomalies by lead week, plus the full multi-model ensemble anomaly database and a fuller multivariable per-model archive — all NetCDF.

Note that SubC is an *operational-facing subseasonal prediction* experiment, not a climate-projection model. It sits at the sub-seasonal end of this category's range.

---

## Who runs it
- **Organization:** The Subseasonal Consortium — a group of academic, government, and non-profit partners, led from the University of Oklahoma (ESPLab / K. Pegion). The data archive is hosted at the IRI Data Library, Columbia University (migrating to Columbia CCSR — see Notes).
- **Country / region:** United States (a multi-centre, multi-national collaboration)
- **Coordinating body / programme:** Originated as a NOAA/Climate Testbed project within the NOAA/MAPP S2S Task Force; contributes guidance to NOAA/CPC week-3-4 outlooks.

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Predominantly 1° × 1°; some members at 0.5° × 0.5° (e.g. GMAO GEOS_V3, and the CPC observed climatologies). The IRIDL database carries pressure-level fields (between 10 hPa and 1000 hPa, varying by model) in addition to surface fields. The OU feed additionally provides regional clips (North America, Mexico, Venezuela, Central Asia, Iran) alongside the global fields.

---

## Basic details
- **Model type:** Multi-model ensemble (umbrella) of global subseasonal models — anomaly products, deterministic and ensemble-statistic
- **Model system / core:** Varies by member (see Multi-model composition)
- **Range class:** Sub-seasonal (weeks 1–4; underlying forecasts run to ~32–45 days, model-dependent)
- **Forecast length:** ~32–45 days; products delivered at weekly leads (Week 1, 2, 3, 4) plus a combined Weeks-3-4 product
- **Initialization cadence:** Weekly (real-time feed in date-stamped folders, e.g. `20260618`)
- **Ensemble generation:** Multi-model — each member model contributes its own ensemble, pooled into the SubC MME
- **Ensemble size:** Per-model ensemble sizes range from 1 to ~30 members (see Multi-model composition)
- **Temporal output resolution:** Daily underlying output, delivered as weekly means
- **Output aggregation levels:** Weekly means (weeks 1–4), combined weeks 3–4; anomalies; ensemble max/mean/min

---

## Coupled configuration
The member models are a mix of fully coupled and atmosphere-focused global systems; dynamical cores, resolutions, and component models differ by member and are documented in the SubX/SubC technical record and the live IRIDL tree. There is no common formulation — only a common (predominantly 1°) delivered grid.

---

## Initialization
Initialization varies by member: each centre initializes its own model from its own analyses and/or reanalyses. There is no shared SubC initialization.

---

## Hindcasts (reforecasts)
- **Hindcast period:** January 1999 – December 2015 (17 years) of retrospective forecasts.
- **Content:** Per-model reforecasts on the common grid; used for lead-dependent bias correction, model climatology, and skill estimation. CPC observed climatologies for 1999–2015 are provided as the verification reference.
- **Real-time forecasts:** Began July 2017 (weekly), continuing under SubC.
- **Host:** IRI Data Library (migrating to Columbia CCSR — see Notes).
- **Distributed alongside forecasts?** Yes — the database holds both reforecasts and real-time forecasts; per-model availability of forecast vs hindcast varies (see Multi-model composition).

---

## Sources of predictability
The MJO is the flagship subseasonal driver (skillful MJO prediction to ~4 weeks), with the NAO contributing at ~2 weeks and ENSO providing background signal. The multi-model mean is more skillful overall than any individual member.

---

## Multi-model composition
Current members in the IRIDL `.SubX` tree, grouped by contributing centre (resolution 1° unless noted; "F" = forecast, "H" = hindcast):
- **NCAR (CESM):** 30LCESM1 (H), 46LCESM1 (H) — members 0–9
- **ECCC:** GEM (F/H), GEPS5 (F/H), GEPS6 (F/H), GEPS7 (F + forecast_weekly / H), GEPS8 (F/H) — forecasts members 0–20, hindcasts ~4 members
- **EMC (NCEP):** GEFS (F/H), GEFSv12 (older issues; F/H), GEFSv12_CPC (current issues served by CPC; F/H) — forecasts members 0–20 to 0–30
- **ESRL (NOAA):** FIMr1p1 (forecast_weekly / H) — members 4
- **GMAO (NASA):** GEOS_V2p1 (F/H), GEOS_V2p1_5daily (F/H), GEOS_V3 (F only; 0.5°, 15 members)
- **NCEP:** CFSv2 (F/H)
- **NRL:** NESM (F/H) — 1 member
- **RSMAS (University of Miami):** CCSM4 (F/H) — forecast 9 members, hindcast 3
- **observed:** CPC observed climatologies, 1999–2015 (0.5°), as reference
- **Combination method:** Multi-model ensemble on the common grid; a SubC multi-model ensemble mean is the headline product.
- **Per-contributor terms:** Covered under the project-wide CC BY 4.0 license; the required acknowledgement names the contributing modeling groups.

---

## What it provides
Openly posted, all NetCDF:
- **OU weekly feed** (`forecasts/data/{YYYYMMDD}/`):
  - 2-m temperature anomalies, global and regional (North America, Mexico, Venezuela, Central Asia, Iran), at weekly leads (`Week1`–`Week4`) and combined `Weeks34`
  - Surface precipitation anomalies as ensemble maximum, mean, and minimum (`fcst_{date}.anom.pr_sfc.e{max|mean|min}.nc`)
  - The full multi-model ensemble anomalies, weeks 1–4 (`subx_mme_anoms_wk_1-4_{date}.nc`, ~4.3 GB)
  - Per-run `manifest.json` and `validation_manifest.json`
- **IRIDL `.SubX` database:** the fuller multivariable per-model and MME hindcast + real-time archive, including pressure-level fields (10–1000 hPa, model-dependent), predominantly at 1°

Viewer-only siblings (out of scope): the SubC static and interactive forecast maps.

---

## Data availability
- **Is the data free?** Yes — publicly accessible without registration.
- **License:** CC BY 4.0. The SubC documentation states the SubC datasets are licensed under CC BY 4.0. Attribution: refer to the data as "the SubC data," cite DOI 10.1175/BAMS-D-18-0270.1 (Pegion et al. 2019), and include the project acknowledgement naming the contributing modeling groups (Environment Canada, NASA, NOAA/NCEP, NRL, University of Miami) and coordinating support (NOAA/MAPP, ONR, NASA, NOAA/NWS).
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF
- **Official download location:**  
  - OU weekly real-time feed: https://weather.ou.edu/~kpegion/subc/forecasts/data/
  - IRIDL SubX database (hindcasts + forecasts): https://iridl.ldeo.columbia.edu/SOURCES/.Models/.SubX/ — **migrating to Columbia CCSR (forecast.ccsr); see Notes**
- **Access route notes:** The OU feed is plain HTTPS in weekly date-stamped folders. IRIDL is a faceted data-library tree (append `/dods` for OpenDAP, with server-side subsetting). Processing tools are published at the consortium's GitHub (`ou-esplab/SubX`).

---

## Notes
- **IRIDL shutdown / CCSR migration (time-sensitive).** The IRI Data Library is being sunset — full shutdown expected by ~end of October 2026, possibly earlier — and is being replaced by a new service (forecast.ccsr) at Columbia Climate School's Center for Climate Systems Research, which will host NMME, SubC, and S2S. SubC migration follows the NMME migration that is already under way, so the IRIDL `.SubX` URLs here should be revisited and updated to the CCSR endpoints once published. **This same migration affects the NMME entry's IRIDL hindcast links.**
- **Name change.** SubX → SubC in 2023; the IRIDL data tree is still rooted at `.SubX`, and `subx_*` naming persists in the feed.
- **Academic hosting.** Like NMME, the operational feed is hosted by academic institutions (OU + Columbia) rather than a national met service — included on the same basis.
- **Distinct from the S2S Prediction Project database.** The WWRP/WCRP S2S database (also on IRIDL, and also slated for CCSR) is a *separate* system with research-use terms and a real-time embargo — a separate, likely out-of-scope candidate that should not be conflated with SubC.
- **Member overlap.** NCEP CFSv2 (own entry) and ECCC GEM/GEPS appear as SubC members; this entry cross-references rather than re-documenting.

---

## Recent version history
- **2015:** Launched as The Subseasonal Experiment (SubX), a NOAA Climate Testbed project.
- **2023:** Reorganized into the Subseasonal Consortium (SubC). Membership continues to evolve; the live IRIDL tree reflects the active set.

---

## Official documentation
- SubC home: https://weather.ou.edu/~kpegion/subc/
- IRIDL SubX database (with overview and license docs): https://iridl.ldeo.columbia.edu/SOURCES/.Models/.SubX/
- Code / tools: https://github.com/ou-esplab/SubX
- IRIDL sunset / CCSR migration notice: https://iri.columbia.edu/resources/data-library/sunset/
- Pegion et al. (2019), *The Subseasonal Experiment (SubX)*, BAMS: https://doi.org/10.1175/BAMS-D-18-0270.1
