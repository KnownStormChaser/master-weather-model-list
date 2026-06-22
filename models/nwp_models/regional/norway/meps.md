# MEPS (MetCoOp Ensemble Prediction System)

## What this model is
MEPS (MetCoOp Ensemble Prediction System) is a **high-resolution, convection-permitting regional ensemble numerical weather prediction (NWP) system** based on HARMONIE-AROME.

It provides short-range probabilistic forecasts and quantifies forecast uncertainty over the Nordic region for high-impact weather such as heavy precipitation, strong winds, and convection.

---

## Who runs it
- **Organization:** MetCoOp (Meteorological Cooperation on Operational Numerical Weather Prediction)
  - Founding partnership: MET Norway and SMHI (2011)
  - FMI (Finland) joined in 2017, the Estonian Environment Agency (ESTEA) in 2020, and the Latvian Environmental, Geological and Meteorological Centre (LEGMC) in 2024
  - The Lithuanian Hydrometeorological Service (LHMS) holds observer status and is expected to join as a full member
- **Country / region:** Multi-national (Nordic and Baltic)

---

## What area it covers
- **Coverage:** Scandinavia and Finland (Nordic region, including surrounding marine areas)
- **Domain details:** ~2.5 km Lambert grid covering Norway, Sweden, Finland, and surrounding seas (North Sea, Norwegian Sea, Baltic Sea, parts of the Barents Sea). Domain expanded by ~20% in February 2020.
- **Grid dimensions:** 960 × 1080 grid points at 2.5 km uniform spacing

---

## Basic details
- **Model type:** Regional ensemble NWP (convection-permitting)
- **Model system / core:** HARMONIE-AROME (ALADIN-NH non-hydrostatic spectral dynamical core); cycle 43h2.2 operationally, with an upgrade to a newer cycle foreseen in 2026
- **Dynamical formulation:** Non-hydrostatic, spectral, with two-time-level semi-implicit semi-Lagrangian discretization; 75 s time step; single-precision forecast integration
- **Convection-allowing:** Yes (deep convection explicitly resolved at 2.5 km; shallow convection parameterized)
- **Ensemble size:** 30 perturbed members + 1 control (control is non-lagged; perturbed members are time-lagged up to 6 hours, with 5 new members generated every hour)
- **Horizontal resolution:** ~2.5 km
- **Vertical levels:** 65 (lowest level ~12 m above ground); a 90-level upgrade has been in preoperational testing since March 2024 with operational rollout planned for 2026
- **Model top:** 10 hPa (~30 km)
- **Forecast length:**
  - 61 hours for perturbed ensemble members (as archived and disseminated)
  - 66 hours for the control member
- **Update frequency / cycles:**
  - Ensemble: 5 new perturbed members initialized every hour (continuous cycling)
  - Control member: every 3 hours
- **Temporal output resolution:** 1 hour
- **Observation cutoff time:** 75 minutes after analysis time
- **Product dissemination:** ~2 hours after analysis time

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** Upper-air 3D-Var with large-scale mixing (the largest spatial scales of the background are blended with ECMWF HRES forecasts before analysis, per Dahlgren and Gustafsson 2012); surface analysis via CANARI optimal interpolation, with an additional once-daily analysis of snow water equivalent, snow density, and albedo that ingests SYNOP observations and satellite imagery.
- **Observations assimilated:** SYNOP, SHIP, drifting buoy, radiosonde, aircraft (conventional AMDAR and Mode-S EHS via EMADDC, including data from receivers at Copenhagen Kastrup, Oslo, and Stockholm Arlanda), GNSS zenith total delay, weather radar reflectivity and Doppler radial wind, polar-orbiting scatterometer winds, microwave sounder radiances (AMSU-A, MHS, ATMS, MWHS-2 from FengYun-3 C/D), infrared sounder radiances (IASI), and polar-orbiting satellite snow-coverage data. Background-error statistics are static and derived from samples of ensemble forecast differences.

---

## Initial and boundary conditions
- **Initial conditions:** HARMONIE-AROME analyses with ensemble perturbations
- **Lateral boundary conditions:** ECMWF IFS (HRES for the control member; 28 IFSENS members used for the perturbed members, with each of the 14 perturbed MEPS members running twice per 6 h on alternating IFSENS boundaries). If ECMWF dissemination fails, the production switches automatically to Météo-France ARPEGE.
- **Sea surface temperature and sea ice:** Interpolated from the ECMWF boundary fields in most of the domain, and from the Nemo-Nordic 2.0 ocean model in the largest Swedish lakes and the Baltic Sea. Fixed during the forecast integration.

---

## Ensemble perturbations
- **Initial conditions:** Perturbations added to assimilated observations, plus the scaled difference between each IFSENS member and the IFSENS control (Frogner et al. 2019)
- **Surface:** Perturbations to roughness, albedo, sea surface temperature, soil temperature, and soil water (via the soil wetness index, accounting for wilting point and field capacity)
- **Model physics:** Stochastically perturbed parameterization (SPP) scheme perturbing five parameters affecting cloud and rain formation, shallow convection, and vertical mixing length scales (Frogner et al. 2022; Tsiringakis et al. 2024)

---

## What it provides
Probabilistic and deterministic forecasts of:
- temperature (2 m, model levels, pressure levels)
- wind (10 m, model levels, pressure levels)
- precipitation (including precipitation type: drizzle, rain, sleet, snow, freezing drizzle, freezing rain, graupel, hail)
- mean sea level and surface pressure
- humidity
- cloud cover and hydrometeor fields
- surface fluxes and radiation
- vertical profiles and cross-sections (separate `vc` files)

Ensemble products derived from the 30+1 members include ensemble means and spread, percentiles, and probability-of-exceedance fields.

---

## Data availability
- **Is the data free?** Yes
- **License:** Norwegian Licence for Open Government Data (NLOD) and Creative Commons Attribution 4.0 International (CC BY 4.0). Attribution required: "MET Norway" or equivalent (e.g. "Data from The Norwegian Meteorological Institute" / "Based on data from MET Norway").
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF and NCML (NCML files are aggregations pointing to underlying NetCDF files; accessible via OPeNDAP)
- **Official download location:**
  - MEPS latest catalogue: https://thredds.met.no/thredds/catalog/mepslatest/catalog.html
  - MEPS archive: https://thredds.met.no/thredds/catalog/meps25epsarchive/catalog.html
- **Alternative distributions:** The same MetCoOp MEPS production is also redistributed by FMI in a different access scheme and subset (see [HARMONIE (FMI)](../finland/harmonie-fmi.md)).

---

## Notes
- **Repository location:** MEPS is an ensemble system, but is currently filed under `nwp_models/regional/norway/` rather than `ensemble_models/regional/norway/`. Consider relocating to align with the repository's convention of separating deterministic and ensemble systems (and using `ensemble-model.template.md` for ensemble-specific fields like perturbation strategy).
- **Geographically distributed production.** The MEPS workload is split across three HPC platforms hosted by the partner institutes: Cirrus and Stratus (jointly hosted by SMHI and MET Norway) and Vinha (FMI). Each HPC runs a subset of the 31 members; the geo-redundancy makes the production robust against scheduled maintenance and technical issues at any single site.
- **Companion suites — MNWC and MECaS.** MetCoOp also runs two operational products built on the same modelling grid as MEPS:
  - **MNWC (MetCoOp Nowcasting System):** A deterministic 12-hour forecast on the 960 × 1080 / 2.5 km grid, refreshed hourly with a 25-minute observation cutoff and 15-minute output time resolution. MNWC immediately follows its main analysis with an additional cloud-initialization step ingesting geostationary satellite imagery (Gregow et al. 2020). Two MNWC instances run in parallel on Stratus and Vinha. Operational since 2022.
  - **MECaS (MetCoOp Ensemble Calibration System):** A statistically calibrated ensemble forecast of near-surface temperature, wind speed, and maximum wind gust speed, produced from raw MEPS using a Bernstein quantile network (Bremnes 2020) and ensemble copula coupling. Distributed on both the 2.5 km and a 1 km grid (the latter exploiting a 30 m DEM and ECOCLIMAP surface-type data). MECaS forecasts become available about 20 minutes after the raw MEPS forecast. Operational since late 2024.
  If documented separately, MNWC belongs under `nwp_models/regional/` and MECaS under a postprocessing index.
- **File naming convention:** Files follow `<model>_<form>_<resolution>_<DTG>.<type>`, where `<form>` is `det` for the control member, or `lagged_Xh_subset`/`lagged_Xh_latest` for time-lagged ensemble output. Most files in the latest catalogue are NCML-only and must be accessed via OPeNDAP rather than downloaded directly. Some files (`*_latest.nc`, `meps_lagged_6_h_latest_*`, `meps_det_vc_*`) are also available as NetCDF.
- **Time-lagging:** Since the February 2020 reorganization, MEPS runs 5 ensemble members every hour rather than larger batches at fixed cycle times. Ensemble files therefore contain 30 members at varying lag (up to 6 hours), while the control member is non-lagged. Only a subset of parameters from all members is archived (in 6-hourly subset files); model-level information is archived only for the control member.
- **Verification.** MEPS consistently outperforms the global ECMWF IFS over the MetCoOp domain for near-surface temperature, wind speed, and maximum wind gust speed (against ground-station observations); for precipitation it adds value mainly in the first 12–18 hours of the forecast. The MECaS postprocessing adds further skill on top of raw MEPS.
- **MET Nordic post-processed product:** A separate higher-resolution (1 km) post-processed product, [MET Nordic](https://github.com/metno/NWPdocs/wiki/MET-Nordic-dataset), is built on top of MEPS and distributed via the same THREDDS server. MET Nordic is run by MET Norway and is distinct from the MetCoOp-wide MECaS calibrated ensemble. Neither is the MEPS model itself but a downstream nowcasting / blending product.
- **Relationship to siblings:**
  - [AROME-Arctic](../norway/arome-arctic.md) — MET Norway's deterministic Arctic-domain HARMONIE-AROME model. AROME-Arctic generally tracks MEPS upgrades after some delay, with several configuration differences (no radar assimilation, different surface scheme settings, no MWHS-2/FengYun-3C assimilation).
  - [HARMONIE (FMI)](../finland/harmonie-fmi.md) — FMI's redistribution of the surface-level MEPS subset via a different access scheme (WFS + binary download service).
  - [HARMONIE-AROME Ireland](../ireland/harmonie-arome-ireland.md), [HARMONIE (DMI)](../denmark/harmonie-dmi.md), [AROME Hungary](../hungary/arome-hungary.md), and other ACCORD-consortium HARMONIE-AROME and AROME deployments share the same core model.
- **United Weather Centres (UWC).** MetCoOp is one of two regional UWC clusters; the other is **UWC-West** (KNMI, DMI, Met Éireann, Veðurstofa Íslands), which runs a separate joint operational suite. The HIRLAM consortium that previously provided the HARMONIE-AROME reference architecture dissolved at the end of 2025, with the new UWC consortium taking over that role from 2026 onward. The two regional clusters target a common UWC NWP production by ~2027. **DMI is in UWC-West, not MetCoOp.**
- **Geostationary radiance assimilation (status unconfirmed).** Schönach et al. (2025) demonstrated assimilation of SEVIRI water-vapour radiances (WV062, WV073) at satellite zenith angles up to 84° in HARMONIE-AROME, expanding geostationary coverage over the MetCoOp domain with positive short-range impact. Distinct from the polar-orbiting radiances in BAMS Table 3 and from the MNWC cloud-ingest step. Whether this is in the operational MEPS assimilation is not yet confirmed.

---

## Recent version history

### 2024 late — MECaS launched
A statistically calibrated ensemble forecast of near-surface parameters (Bernstein quantile network postprocessing of MEPS, with output on both 2.5 km and 1 km grids) entered operations.

### 2024-12-10 — MEPS archive cleaning
Introduction of a cleaning policy for the MEPS archive. See [MET Norway's announcement](https://github.com/metno/NWPdocs/wiki/Updates-to-MEPS-and-AROME%E2%80%90Arctic-archive-and-cleaning-2024).

### 2024 — Latvia joins MetCoOp
LEGMC (Latvian Environmental, Geological and Meteorological Centre) joined as a full member.

### 2023-10-01 — New MEPS archive regime
Archive structure changed. `meps_det_*.nc` and `meps_subset_*.nc` NetCDF files are no longer archived as monolithic files. Instead, one file is produced per member, level type, and time step, with NCML files aggregating these. See [New MEPS Archive 2023](https://github.com/metno/NWPdocs/wiki/New-MEPS-Archive-2023).

### 2022-05-03 — Stockholm Arlanda Mode-S EHS receiver and expanded ABO sources
Mode-S EHS data from the Stockholm Arlanda local receiver, together with data from the Maastricht Upper Area Control Centre and the Norwegian Defence Research Establishment (FFI), were introduced into the operational MEPS data assimilation, substantially improving aircraft-based observation coverage over Sweden, Finland, the Baltic, and parts of the Norwegian Sea (Lindskog et al. 2023). MEPS originally began assimilating Mode-S EHS in 2019 (initially Copenhagen Kastrup and Oslo receivers; temperature observations added 23 March 2021 alongside the cy43 upgrade).

### 2022 — MNWC launched
The MetCoOp Nowcasting System (a deterministic 12-hour forecast on the same grid, refreshed hourly with 15-minute output) entered operations.

### 2021-03-23 — Upgrade to HARMONIE cycle 43 (cy43)
Minor changes to forecast quality. See the [cy43 upgrade note](https://github.com/metno/NWPdocs/blob/master/documents/MEPS_update_cy43.pdf).

### 2020-02-04 — Continuous cycling and domain expansion
Major reorganization of the production schedule. Previously MEPS ran 10 members at fixed 00/06/12/18 UTC cycles; from this date it runs 5 new members every hour with 66-hour forecast length, producing 30 time-lagged ensemble members + 1 non-lagged control. The domain was expanded by ~20%. NetCDF outputs in the latest catalogue were largely replaced by NCML aggregations. SURFEX-derived parameters were prefixed with `SFX_` in variable names. Post-processed (`pp`) files were discontinued in favour of the separate MET post-processed (yr) product chain.

### 2020 — Estonia joins MetCoOp
ESTEA (Estonian Environment Agency) joined as a full member.

### 2017 — Finland joins MetCoOp
FMI joined as a full member from January 2017 and became an integral part of the production chain (running one EPS member) from September 2017, at which point the MEPS control member replaced FMI's own HARMONIE-AROME runs.

### 2016-11-01 — MEPS ensemble operational (SMHI + MET Norway)
The suite was extended from the deterministic AROME-MetCoOp configuration to a 10-member ensemble (HARMONIE-AROME cy40h1.1; SLAF-based initial and boundary perturbations), and the primary suite became the MetCoOp Ensemble Prediction System (MEPS). Ran in e-suite from June 2016, operational 1 November 2016.

### 2014-03-18 — Operational start
The deterministic AROME-MetCoOp configuration entered operations (Müller et al. 2017b). Experimental runs had been conducted since December 2013; the predecessor AROME-Norway had appeared on yr.no from 1 October 2013.

---

## Planned developments
- **Vertical resolution upgrade.** A 90-level grid (replacing the current 65 levels) has been in preoperational testing since March 2024, with operational rollout planned for 2026. The upgrade adds resolution mainly in the lower troposphere.
- **Horizontal resolution upgrade.** MNWC is targeted for a reduction to ~1.5 km grid spacing within 1–2 years; MEPS to ~1.5 km within 2–3 years, contingent on new HPC capacity.
- **Advanced data assimilation.** Move from 3D-Var with static background-error statistics to 3DEnVar with flow-dependent ensemble-derived covariances is under research.
- **Multilayer surface scheme.** Adoption of a multilayer-based surface scheme (similar to the cy46 upgrade already in AROME-Arctic) is under research.

---

## Official documentation
- MEPS dataset documentation: https://github.com/metno/NWPdocs/wiki/MEPS-dataset
- MetCoOp project overview: https://www.met.no/en/projects/metcoop
- Licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- Eresmaa et al. (2026), *The Operational Forecast Process at MetCoOp*, Bull. Amer. Meteor. Soc., 107, E946–E963. https://doi.org/10.1175/BAMS-D-25-0225.1
- Müller et al. (2017), *AROME-MetCoOp: A Nordic Convective-Scale Operational Weather Prediction Model*, Wea. Forecasting, 32, 609–627. https://doi.org/10.1175/WAF-D-16-0099.1
- Bengtsson et al. (2017), *The HARMONIE-AROME model configuration in the ALADIN-HIRLAM NWP system*, Mon. Wea. Rev., 145, 1919–1935. https://doi.org/10.1175/MWR-D-16-0417.1
- Frogner et al. (2019), *HarmonEPS — The HARMONIE Ensemble Prediction System*, Wea. Forecasting, 34, 1909–1937. https://doi.org/10.1175/WAF-D-19-0030.1
- Frogner et al. (2022), *Model uncertainty representation in a convection-permitting ensemble — SPP and SPPT in HarmonEPS*, Mon. Wea. Rev., 150, 775–795. https://doi.org/10.1175/MWR-D-21-0099.1
- Tsiringakis et al. (2024), *An update to the stochastically perturbed parameterization scheme of HarmonEPS*, Mon. Wea. Rev., 152, 1923–1943. https://doi.org/10.1175/MWR-D-23-0212.1
- Lindskog et al. (2023), *Impact of Mode-S Enhanced Surveillance Weather Observations on Weather Forecasts over the MetCoOp Northern European Model Domain*, J. Appl. Meteor. Climatol., 62, 985–1003. https://doi.org/10.1175/JAMC-D-23-0009.1
- Ridal and Dahlbom (2017), *Assimilation of multinational radar reflectivity data in a mesoscale model: A proof of concept*, J. Appl. Meteor. Climatol., 56, 1739–1751. https://doi.org/10.1175/JAMC-D-16-0247.1
- Ridal et al. (2023), *Optimal use of radar radial winds in the HARMONIE numerical weather prediction system*, J. Appl. Meteor. Climatol., 62, 1745–1759. https://doi.org/10.1175/JAMC-D-23-0013.1
- Bremnes (2020), *Ensemble postprocessing using quantile function regression based on neural networks and Bernstein polynomials*, Mon. Wea. Rev., 148, 403–414. https://doi.org/10.1175/MWR-D-19-0227.1
- Gleeson et al. (2024), *The Cycle 46 configuration of the HARMONIE-AROME forecast model*, Meteorology, 3, 354–390. https://doi.org/10.3390/meteorology3040018
