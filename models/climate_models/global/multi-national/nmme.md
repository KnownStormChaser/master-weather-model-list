# NMME (North American Multi-Model Ensemble)

## What this model is
NMME is a multi-model seasonal forecasting system that combines several independently run, fully coupled global models from North American modeling centres into a single, equally weighted ensemble. It has two faces: a real-time experimental-operational seasonal prediction component, run on the NCEP/CPC monthly schedule, and a research hindcast database. Forecasts extend to 12 months. The openly distributed products are gridded full-field and anomaly ensemble-mean forecasts, tercile probabilities, and a hindcast climatology — all NetCDF, global, at 1°.

Note that NMME is an *operational seasonal prediction* system rather than a climate-projection model, and that its membership evolves over time as models are added and retired.

---

## Who runs it
- **Organization:** Coordinated by NOAA/NCEP's Climate Prediction Center (CPC), which runs the real-time application; the hindcast archive is hosted by the International Research Institute for Climate and Society (IRI) at Columbia University. Development was led by NOAA/NCEP, NOAA/CTB, and NOAA/WPO.
- **Country / region:** United States (a multi-centre North American collaboration)
- **Coordinating body / programme:** NMME itself; project support from NOAA, NSF, NASA, and DOE. Currently contributing centres are NOAA/NCEP, Environment and Climate Change Canada (ECCC), NASA/GMAO, and NCAR/University of Miami; NOAA/GFDL has contributed historically.

---

## What area it covers
- **Coverage:** Global
- **Domain details:** Distributed on a 1° × 1° regular lat-lon grid

---

## Basic details
- **Model type:** Multi-model ensemble (umbrella) of fully coupled global seasonal models — full-field and anomaly ensemble-mean forecasts plus probabilistic products
- **Model system / core:** Varies by member (see Multi-model composition); the current real-time roster is CFSv2 (NCEP), CanESM5 and GEM5.2-NEMO (ECCC, the two CanSIPS components entered as separate streams), NASA GEOS5v2, and NCAR CCSM4 and CESM1
- **Range class:** Seasonal (monthly-to-seasonal; forecasts to 12 months)
- **Forecast length:** 12 months; distributed anomaly/forecast products cover the following ~7 monthly leads and 5 overlapping 3-month seasons
- **Initialization cadence:** Monthly — member output delivered to CPC by the 8th; run folders are date-stamped `YYYYMM0800` and posted around the 7th–9th
- **Ensemble generation:** Multi-model — six active models, each contributing an ensemble (commonly ~10 realizations), pooled into the multi-model ensemble
- **Ensemble size:** Six active models in the real-time channel; pooled member count depends on active membership *(confirm exact per-model realization counts against the live tree)*
- **Temporal output resolution:** Monthly means
- **Output aggregation levels:** Monthly and 3-month (seasonal) means; full forecast fields and anomalies; tercile probabilities

---

## Coupled configuration
Each contributing model is an independently developed, fully coupled atmosphere–ocean–land–sea-ice system; dynamical cores, resolutions, and component models differ by member and are documented in the individual member entries and the live IRIDL tree. There is no common model formulation — only a common delivered grid of 1° × 1°. (The CanESM5 and GEM5.2-NEMO members are described in the CanSIPS entry; CFSv2 in its own entry.)

---

## Initialization
Initialization varies by member: each centre initializes its own model from its own operational analyses and/or reanalyses. There is no shared NMME initialization. (See member entries.)

---

## Hindcasts (reforecasts)
- **Hindcast period:** 1982–2010 for all members (CFSv1 is 1982–2009).
- **Content:** Monthly means only, for three variables — sea-surface temperature, 2-m temperature, and precipitation rate — at 1°.
- **Reference climatology period:** The 1982–2010 hindcasts define each model's climatology for forming anomalies, and underpin the anomaly-correlation skill maps.
- **Host:** IRI Data Library.
- **Distributed alongside forecasts?** Yes — via IRIDL, separately from the CPC real-time FTP channels.

---

## Sources of predictability
ENSO is the flagship signal (the Niño-3.4 SST plume is a headline product). The multi-model design is intended to raise skill and characterize forecast uncertainty better than any single constituent model.

---

## Multi-model composition
- **Current real-time members (verified against the live CPC `realtime_anom` tree, which is authoritative as membership rotates):** CFSv2 (NCEP); CanESM5 and GEM5.2-NEMO (ECCC — the two CanSIPS component models, carried as separate streams); NASA GEOS5v2 (GEOS-S2S); NCAR CCSM4; NCAR CESM1. An `ENSMEAN` stream provides each model's ensemble mean plus the combined NMME multi-model mean.
- **Historical / retired members:** NCEP CFSv1, IRI ECHAM4p5, GFDL CM2.1 and CM2.5-FLOR (FLORa06/FLORb01), and ECCC CanCM3/CanCM4. (GFDL is not present in the current real-time anomaly channel.)
- **Combination method:** Equal weighting. For anomalies, each model's ensemble mean is computed first, then the model means are averaged into the multi-model mean. For probabilities, all members across all models are pooled with equal member weight (the probabilistic product is shown for the NMME only).
- **Common delivered grid:** 1° × 1°.
- **Per-contributor terms:** Individual centres' outputs may carry their own acknowledgement requirements (the NMME "Table XX" convention lists the models and institutions whose output was used).

---

## What it provides
Openly distributed, all as gridded NetCDF, for the core variables precipitation rate (`prate`), 2-m temperature (`tmp2m`), and surface temperature (`tmpsfc`):
- **Per-model member anomalies** — each member model's full set of ensemble realizations, anomaly only, in one file per variable (e.g. `realtime_anom/CFSv2/{run}/CFSv2.prate.{YYYYMM}.anom.nc`). File size scales with each model's ensemble count (CFSv2 ~72M; CanESM5 and GEM5.2-NEMO ~45M; NASA GEOS5v2, NCAR CCSM4, NCAR CESM1 ~22M).
- **Per-model ensemble-mean forecasts and anomalies** — full field (`.fcst`) and anomaly (`.anom`) for each model, in the `ENSMEAN` folder.
- **Multi-model (NMME) ensemble-mean anomalies** — an extended variable set: `prate`, `tmp2m`, `tmpsfc`, plus max/min 2-m temperature (`tmax`, `tmin`) and 200-hPa geopotential height (`z200`), in the `ENSMEAN` folder.
- **Tercile probability forecasts** — precipitation rate and 2-m temperature, monthly and seasonal, in raw and skill-adjusted variants (CPC `prob/netcdf`).
- **Hindcast monthly means** — SST, 2-m temperature, and precipitation rate, 1982–2010, at 1° (IRIDL).

Viewer-only siblings (out of scope): the CPC anomaly/probability maps, skill maps, and Niño-3.4 plumes, plus the displayed IMME/EuroSIP — the latter is not part of NMME and its raw data is not available.

---

## Data availability
- **Is the data free?** Yes
- **License:** Free and open for public and research use; no formal license identifier. Use requires citing Kirtman et al. (2014) and including the project's specified acknowledgement — that NMME and its data dissemination are supported by NOAA, NSF, NASA, and DOE, with thanks to NCEP, IRI, and NCAR personnel who create, update, and maintain the archive. Individual member outputs may carry additional modeling-centre acknowledgement conditions.
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF
- **Official download location:**  
  - Real-time forecasts and anomalies: https://ftp.cpc.ncep.noaa.gov/NMME/realtime_anom/ — organized as `{Model|ENSMEAN}/{YYYYMM0800}/`. Per-model folders hold member-level anomalies as `{Model}.{var}.{YYYYMM}.anom.nc` (variables `prate`, `tmp2m`, `tmpsfc`); the `ENSMEAN` folder holds per-model means and the NMME multi-model mean as `{Model|NMME}.{var}.{YYYYMM}.ENSMEAN.{anom|fcst}.nc`
  - Real-time probability forecasts: https://ftp.cpc.ncep.noaa.gov/NMME/prob/netcdf/ — files named `{prate|tmp2m}.{YYYYMM}.prob[.adj].{mon|seas}.nc`, a continuous monthly archive from 2019-01
  - Hindcasts (1982–2010 monthly means): https://iridl.ldeo.columbia.edu/SOURCES/.Models/.NMME/
- **Access route notes:** The CPC channels are plain HTTPS directory listings. IRIDL is a faceted data-library tree (model → forecast type → frequency → variable; append `/dods` for an OpenDAP endpoint, with server-side X/Y/T subsetting and a built-in viewer). The fuller multivariable per-member database (~13 variables, daily, ~10 realizations) is **not** reliably openly hosted — the NCEI mirror is partial and stale (latest holdings lag by years).

---

## Notes
- **Umbrella entry.** NMME's NCEP member is documented separately as [CFSv2](../../usa/cfsv2.md); the ECCC contribution is documented as [CanSIPS](../../canada/cansips.md), though within NMME it appears as its two component models (CanESM5 and GEM5.2-NEMO) as distinct streams. This entry cross-references rather than re-documenting. *(Confirm relative paths once placement is set.)*
- **Stale public roster.** The CPC user's-guide model-roster page still lists CanCM3/4, GEOS5, and CCSM3; treat the live `realtime_anom` and IRIDL trees as authoritative for current membership.
- **Out of scope, but worth a wiki note.** The CPC graphical maps (viewer-only) and IMME/EuroSIP (not part of NMME; raw data unavailable) are both candidates for the *Systems Not in the Catalog* page.
- **Subseasonal sibling.** SubX is the sub-seasonal-range counterpart (separate entry candidate), hosted at `iridl.ldeo.columbia.edu/SOURCES/.Models/.SubX/`.
- **Scope basis.** Included because the distributed products are gridded numerical NetCDF — including raw per-member anomaly fields and full forecast fields (`.fcst`), not only ensemble-mean or categorized-probability products.

---

## Recent version history
NMME has no single versioned release; its membership evolves continuously. The current real-time set is CFSv2, CanESM5, GEM5.2-NEMO, NASA GEOS5v2, NCAR CCSM4, and NCAR CESM1. CFSv1, IRI-ECHAM4p5, the GFDL CM2.x/FLOR members, and the older CanCM3/CanCM4 have been retired. The live IRIDL and CPC `realtime_anom` trees reflect the active set at any time.

---

## Official documentation
- NMME home: https://www.cpc.ncep.noaa.gov/products/NMME/
- User's guide: https://www.cpc.ncep.noaa.gov/products/NMME/users_guide.html
- Data page: https://www.cpc.ncep.noaa.gov/products/NMME/data.html
- IRIDL NMME root: https://iridl.ldeo.columbia.edu/SOURCES/.Models/.NMME/
- Kirtman et al. (2014), *The North American Multimodel Ensemble*, BAMS (project description / required citation): https://journals.ametsoc.org/view/journals/bams/95/4/bams-d-12-00050.1.xml
- Becker et al. (2022), *A Decade of the North American Multimodel Ensemble (NMME)*, BAMS: https://journals.ametsoc.org/view/journals/bams/103/3/BAMS-D-20-0327.1.xml
