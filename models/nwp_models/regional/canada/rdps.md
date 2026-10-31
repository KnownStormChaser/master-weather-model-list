# RDPS (Regional Deterministic Prediction System)

## What this model is
The Regional Deterministic Prediction System (RDPS) provides higher-resolution short-range deterministic weather forecasts focused on North America. It supplies the main NWP guidance at the Meteorological Service of Canada for forecast days 1 and 2.

As of RDPS 9.0.0, RDPS is no longer produced by a standalone limited-area continuous-cycle model. Instead, it is generated from a global GEM run on the same Yin–Yang domain as GDPS, but at ~10 km horizontal resolution. Forecast products are extracted to a rotated regional grid covering North America. The continuous regional LAM assimilation cycle has been removed; the analysis is now a single 4DEnVar run per cycle that uses GDPS-9.0.0 background fields as the first guess.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** North America (regional output), produced from a global model run
- **Forecast (model) grid:** Global Yin–Yang grid, 3124 × 2084 grid points, uniform 0.09° (~10 km) resolution
- **User (output) grid:** Rotated latitude–longitude LAM grid, 1140 × 1045 grid points, uniform 0.09° (~10 km), covering North America and adjacent oceans. The LAM rotation is identical to the rotation of the Yin domain in GDPS

---

## Basic details
- **Model type:** Deterministic NWP (regional-domain output extracted from a global Yin–Yang run)
- **Model system / core:** GEM (Global Environmental Multiscale) version 5.2
- **Dynamical formulation:** Hydrostatic primitive equations
- **Convection-allowing:** No (~10 km horizontal resolution)
- **Horizontal resolution:** ~10 km (0.09°)
- **Vertical levels:** 84 staggered hybrid levels (Charney–Phillips vertical grid; identical to GDPS 9.0.0)
- **Model top:** 0.1 hPa
- **Forecast length:** Up to ~84 hours
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC) early analysis and forecast runs
- **Time step:** 300 seconds
- **Time integration:** Iterative-implicit, semi-Lagrangian (3D), 2 time-level
- **Temporal output resolution:** Hourly, out to +84 h (file-verified on the rotated `model_rdps` grid)

---

## Data assimilation
- **Data assimilation:** Yes
- **Method:** Four-dimensional ensemble–variational (4DEnVar) — a single analysis per cycle, replacing the previous continuous LAM assimilation cycle. The flow-dependent background-error covariances come from the same 256-member LETKF ensemble of GEPS 8.0.0 backgrounds used by GDPS, with scale-dependent horizontal localization
- **Cadence:** Four analyses per day at 00, 06, 12, and 18 UTC, each with a 2-hour cutoff. The (R2) final analysis and background step has been removed; each early analysis is initialized with the 15 km GDPS background from the previous 6-h GDPS final analysis
- **Background fields:** 3 to 9-hour forecasts with 15-minute outputs, provided by the 15 km GDPS-9.0.0 assimilation cycle
- **Analysis increment grid:** 0.22° × 0.22° global Gaussian grid (~25 km)
- **Initialization:** 4D Incremental Analysis Update (4D-IAU) applied over the 6-hour window from t-3 to t+3 hours, recycling physics variables (total condensate, cloud fraction, TKE, turbulent regime, mixing length, friction velocity, PBL height, PBL cloud water/fraction, PBL flux enhancement factor) from the GDPS background forecast
- **Radiative transfer model:** RTTOV v13, with slant-path radiative transfer
- **Notable observations assimilated:** Microwave radiances (AMSU-A, MHS, SSMIS, ATMS, MWHS-2, including all-sky AMSU-A, ATMS temperature and AMSU-B/MHS humidity channels); infrared radiances (IASI, CrIS — AIRS no longer available); geostationary imager radiances; GPS-RO refractivity; AMVs; ASCAT scatterometer winds; ground-based GPS ZTD; radiosondes; surface stations; aircraft; and ozone retrievals (OMI, OMPS-NM, TROPOMI, MLS, OMPS-NP)
- **Satellite radiance bias correction:** Coefficients computed by GDPS-9.0.0 from a separate 3DEnVar analysis without radiances (last 7 days, 2× daily), reused by RDPS

---

## What it provides
Deterministic short-range forecasts of:
- Three-dimensional winds, virtual temperature, geopotential, specific humidity, and surface pressure
- Quantitative precipitation forecast (QPF), precipitation rate and type
- Cloud condensate, cloud cover, boundary layer height
- Mean sea-level pressure, relative humidity, omega
- Prognostic ozone, total column ozone, and clear-sky / all-sky UV indices
- Aviation- and public-weather–relevant fields

RDPS provides the primary deterministic NWP guidance for forecast days 1 and 2 in Canada and is operationally part of a tightly integrated suite alongside [GDPS](../../global/canada/gem-global.md), [HRDPS](./hrdps.md), and [REPS](../../../ensemble_models/regional/canada/reps.md).

---

## Surface and physics
- **Surface scheme:** Mosaic approach with 4 surface types (land, water, sea ice, glacier)
- **Land surface:** ISBA scheme (Noilhan & Planton, 1989; Bélair et al., 2003a, 2003b)
- **Sea-surface temperature:** SST analysis at 0.1°
- **Sea ice:** Sea ice concentration, snow depth over sea ice, and sea ice thickness sourced from GIOPS
- **Radiation:** Correlated-k distribution (Li & Barker, 2005), 3D trace gas and ozone climatologies
- **Boundary-layer mixing:** TKE-based with statistical subgrid-scale clouds; Richardson number hysteresis; regime-specific mixing length (Blackadar in laminar flow, Bougeault–Lacarrère in turbulent flow)
- **Deep convection:** Updated Kain–Fritsch with vertical momentum transfer, Lagrangian initiation, and object-based clouds (McTaggart-Cowan et al., 2019b)
- **Shallow convection:** Bechtold (2001) mass-flux scheme with convective momentum transport
- **Mid-level convection:** Mass-flux scheme for low-CAPE environments (McTaggart-Cowan et al., 2019a)
- **Gravity wave drag:** Orographic (McFarlane "sgo16"), non-orographic (Hines), and low-level blocking (Lott–Miller / Zadra et al.)

---

## Data availability
- **Is the data free?** Yes (no registration required for MSC Open Data)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence (attribution required; commercial use permitted) — https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Distribution channels:**
  - **MSC Datamart:** Direct file access (HTTPS, with optional AMQP push notifications) — primary download channel
  - **MSC GeoMet:** Geospatial web services (WMS, WCS, OGC API)
  - **MSC AniMet:** Visualization service
- **Official download location:** RDPS GRIB2 output is distributed on a single **rotated latitude–longitude grid** (1140 × 1045 at 0.09°), covering North America and adjacent oceans and extending to the Caribbean, all of Mexico, and part of northern Europe:
  https://dd.weather.gc.ca/today/model_rdps/10km/
  - Available for **all four runs (00, 06, 12, 18 UTC)**, each out to **+84 h** (file-verified 2026-07-13)
  - ISO-8601 filename nomenclature: `{YYYYMMDD}T{HH}Z_MSC_RDPS_{VAR}_{LVLTYPE-LVL}_RLatLon0.09_PT{hhh}H.grib2`
- **Open data documentation:** https://eccc-msc.github.io/open-data/msc-data/nwp_rdps/readme_rdps-datamart_en/

---

## Notes
- RDPS shares the same modern GEM dynamical core, vertical structure, and 4DEnVar assimilation framework as GDPS. The 9.0.0 release tightened that integration further by replacing the standalone regional LAM continuous assimilation cycle with a single global-domain analysis driven by GDPS backgrounds.
- The shift from a limited-area continuous-cycle configuration to a global 10 km configuration simplifies system maintenance and improves consistency with global guidance, while the rotated LAM output grid preserves the regional product distribution users rely on.
- Although the model is run on the global Yin–Yang grid, RDPS remains the *regional* deterministic guidance product because forecasts are distributed on the rotated North America LAM grid and tuned for short-range (1–2 day) regional skill.
- RDPS plays a role similar to the U.S. **NAM**, but with tighter integration into a global modeling framework.
- **Version-history gaps and discrepancies (ECCC sources):**
  - (1) The current version, **RDPS 10.0.0**, has no changelog entry of its own, and there is no 9.1.0 entry either. 10.0.0 is confirmed only by the July 22, 2026 PROGNOS entry. ECCC's "current" technical specifications link still serves the 9.0.0 document.
  - (2) RDPS 4.2.0 (March 2016) and 5.1.0 (2018) appear only in ECCC's multi-system changelog. 5.1.0 has no documented implementation date.
  - (3) The RDPS changelog dates the run-start change to "Wednesday October 19 2012". October 19, 2012 was a Friday.
  - (4) The v7.0.0 entry's "uniform 7h cut-off for all daily runs" matches the GDPS v7.0.0 wording word for word. It may have been copied from GDPS, since RDPS early runs use a ~2-hour cut-off.
- Open data licensing is genuinely open — no registration required, direct file access via the Datamart. Same as GDPS, GIOPS, RIOPS, and CIOPS.

---

## Version history

The list runs from the oldest entry in ECCC's RDPS changelog (v2.0.0 improvements, November 2011) to the present, newest first. Small data-assimilation-only updates are grouped under the version that was running. Before v9.0.0, RDPS was a standalone limited-area model. Since v9.0.0 it has been a 10 km component of the GDPS, and its version history is closely tied to GDPS's (see [GDPS](../../global/canada/gem-global.md)).

### Distribution change — 2026
The legacy polar-stereographic distribution (`model_gem_regional`, `CMC_reg_*_ps10km_*` filenames, 935 × 824) has been retired from the MSC Datamart, along with its documentation. RDPS GRIB2 is now published only on the rotated lat-lon grid (`model_rdps`), which carries all four daily runs (00/06/12/18) out to +84 h.

### RDPS v10.0.0 — current (implementation date TBD)
- ECCC's RDPS changelog has no entry for this upgrade. The version is confirmed only by the July 22, 2026 PROGNOS entry, which refers to "RDPS 10.0.0", and by ECCC's NWP-wide changelog. The RDPS changelog also has no 9.1.0 entry.
- *(TBD — implementation date and changes.)* Because RDPS is a GDPS component, it most likely moved to 10.0.0 with the GDPS v10.0.0 hybrid physics–AI upgrade on May 26, 2026, and through a 9.1.0 HPC port with GDPS on April 14, 2026. Neither is documented for RDPS. It is also unconfirmed whether the 10 km RDPS run uses the same GEML spectral nudging as GDPS.
- ECCC's "current version" technical specifications link (`tech_specifications_RDPS_e.pdf`) still serves the **RDPS 9.0.0** document (© 2024), checked October 31, 2026. No 10.0.0 specifications have been published, so the Basic details and Data assimilation sections above reflect 9.0.0.
- Jul 22, 2026 (12 UTC run) — PROGNOS wind-speed post-processing switched from a LASSO model to a LightGBM machine-learning model, improving moderate-to-strong wind forecasts. A downstream product change, not a model change

### RDPS v9.0.0 — operational June 11, 2024
Implemented as part of Innovation Cycle 4 (IC-4), alongside GDPS 9.0.0. The biggest change is structural: the RDPS no longer runs a standalone regional LAM with its own continuous assimilation cycle. ECCC's NWP-wide changelog lists it as an "RDPS replacement" rather than a normal upgrade.

Headline changes:
- **Removal of the regional LAM continuous assimilation cycle.** Replaced by a single 4DEnVar analysis every 6 hours on the **global Yin–Yang domain at 10 km**, using GDPS-9.0.0 background fields as the first guess. The (R2) final analysis and background step is also removed
- **Forecast still distributed on the rotated North America LAM grid** (1140 × 1045 at 0.09°), so end-user products are unchanged in geometry
- **GEM upgraded to version 5.2** (5.2.1 per the GDPS 9.0.0 changelog)
- **4DEnVar with a fully ensemble-derived B** built from 256-member LETKF backgrounds from **GEPS 8.0.0**; Hessian matrix cycling removed; small reduction of horizontal localization at the largest scales
- **Radiative transfer model upgraded to RTTOV v13**
- **All-sky assimilation extended to ATMS temperature and AMSU-B/MHS humidity channels** (cloud-affected radiances), in addition to all-sky AMSU-A
- **Analysis increment vertical levels** moved from L80/vCode 5002 to L84/vCode 5005, matching GEPS and GDPS
- **3DEnVar reference state for radiance bias correction** (computed by GDPS), replacing 3DVar
- AIRS no longer assimilated (data no longer available)
- Additional ozone observations: OMPS-NM, TROPOMI, OMPS-NP added; piecewise weighted-averaging interpolation and weighted integration of total column ozone as a strong constraint
- **4D-IAU** initialization with recycling of an expanded set of physics variables from the GDPS background forecast
- **Iterative solver of the Euler equations** (Pacull and Aubert, 2014) and improved trapeze-cubic semi-Lagrangian trajectory calculations
- Sea ice concentration, snow depth over sea ice, and sea ice thickness now sourced from GIOPS
- Cutoff time for the early analysis: 2 hours
- The specifications above were checked against the RDPS 9.0.0 technical specifications document: grids, levels, 300 s time step, 2-hour cut-off and GDPS-9.0.0 backgrounds all match. That document describes its changes relative to RDPS-8.1.0, not 8.1.1. The 8.1.1 update was only a snow-analysis QC fix.
- Jul 23, 2025 — PROGNOS v1.0.0 statistical post-processing (successor to UMOS) introduced on RDPS output. A downstream product change, not a model change

### RDPS v8.1.1 — operational March 8, 2023 (12 UTC run)
- Corrected quality control of snow-depth observations from Canadian automatic stations used in the snow analysis

Data-assimilation-only updates while at v8.1.1:
- Jul 5, 2023 — Commercial GNSS-RO from Spire (LEMUR constellation)
- Sep 14, 2023 — Commercial GNSS-RO from PlanetIQ (GNOMES constellation)
- Nov 2, 2023 — GOES-18 clear-sky radiances (CSR)

### RDPS v8.1.0 — operational June 28, 2022
- Port of v8.0.0 to ECCC's then-new HPC infrastructure. No science changes listed.

Data-assimilation-only updates while at v8.1.0:
- Nov 3, 2022 — Ship surface observations in BUFR format (TAC ship reports were already assimilated)
- Jan 4, 2023 — GOES-17 AMVs replaced by GOES-18
- Feb 15, 2023 — GPS-RO from Sentinel-6A

### RDPS v8.0.0 — operational December 1, 2021 (12 UTC run)
- **GEM upgraded to v5.1.0** (regional configuration). Implemented together with GDPS 8.0.0, HRDPS 6.0.0 and RAQDPS023 in ECCC's December 2021 suite upgrade. RDPS 8.0.0 was the pilot model for the uncoupled HRDPS-North 1.0.0 introduced the same day (see [CAPS](./caps.md)).
- ECCC's changelog gives no summary of changes for this version, only links to the factsheet, technical note and specifications (not retrievable for this entry). *(TBD — detailed v8.0.0 changes)*

Data-assimilation-only updates while at v8.0.0:
- Feb 16, 2022 — GPS-RO from GRACE-C and GRACE-D

### RDPS v7.1.0 — operational January 21, 2020
- Port of v7.0.0 to ECCC's then-new HPC infrastructure. No science changes listed.

Data-assimilation-only updates while at v7.1.0:
- Apr 28, 2020 — AMVs from MetOp-3/C
- Apr 29, 2020 — GPS-RO from PAZ and KOMPSAT-5
- Jun 26, 2020 — GPS-RO from COSMIC-2
- Dec 8, 2020 — AMSU-A and MHS (MetOp-3/C); CrIS full spectral resolution (Suomi NPP); GOES-17 CSR
- Jan 26, 2021 — MWHS-2 (FY-3C); IASI moved from MetOp-2/A to MetOp-3/C

### RDPS v7.0.0 — operational July 3, 2019 (12 UTC run)
- **Pilot model changed from GDPS v6.1.0 to GDPS v7.0.0**
- **GEM upgraded from v4.8-LTS.16 to v5.0.0**
- **Vertical levels increased from 80 to 84**
- ISBA land-surface improvements
- Uniform 7-hour observation cut-off for all daily runs (as worded in ECCC's changelog)
- SST and sea-ice analyses refined from 0.2° to 0.1°
- Jul 23, 2019 (06 UTC run) — More observations added within seven families already in use, mainly for robustness. ECCC's changelog gives no version number for this update.

### RDPS v6.0.0 — operational September 18, 2018 (12 UTC run)
- No forecast-model changes
- **Continuous assimilation cycling replaced intermittent cycling**
- Analysis increment resolution increased from 50 km to 39 km, now applied through an Incremental Analysis Update (IAU)
- **RTTOV upgraded from v10.2 to v12**
- More weight given to GEPS trial fields in the background-error covariances
- Improved processing of radiosonde, AMV, ground-GPS and aircraft observations
- Near-surface observations from METARs and partner networks added; extra water-vapour CSR channels

### RDPS v5.1.0 — date not documented
- ECCC's RDPS changelog has no v5.1.0 entry. The version shows up only in the multi-system changelog's data-assimilation updates of March 1 and June 7, 2018. *(TBD — implementation date; possibly November 1, 2017 alongside GDPS v6.0.0)*

Data-assimilation-only updates while at v5.1.0 (listed in ECCC's multi-system changelog):
- Mar 1, 2018 — Meteosat-10 AMVs replaced by Meteosat-11
- Jun 7, 2018 — GOES-13 AMVs replaced by GOES-16; Meteosat-10 CSR replaced by Meteosat-11

### RDPS v5.0.0 — operational September 7, 2016 (12 UTC run)
- **GEM upgraded from v3.3.8.2-isba (unstaggered levels) to v4.8.2 (staggered levels)**
- **GDPS became the driving model** through a new 120-minute-cut-off GDPS run (G0)
- **New grid based on the GDPS Yin grid**, expanding the domain to cover all of North America
- Changed representation of surface outputs; Maestro replaced OCM as the job sequencer
- No assimilation or observation changes

### RDPS v4.2.0 — operational March 16, 2016 (12 UTC run)
- Data assimilation only (listed in ECCC's multi-system changelog, not the RDPS changelog): MTSAT-2 AMV and CSR replaced by Himawari-8; VIIRS AMVs (Suomi NPP) and RapidScat winds (ISS) added; geostationary CSR thinning modified

### RDPS v4.1.0 — operational December 15, 2015 (12 UTC run)
- Driving-model grid refined from 33 km to 25 km (following GDPS v5.0.0); lateral boundary conditions every 30 minutes instead of 60
- New observations: 17 ATMS channels, 103 more CrIS channels, 600+ more ground-GPS sites (mostly Europe); inter-channel error correlations for infrared and microwave radiances

### RDPS v4.0.0 — operational November 18, 2014 (12 UTC run)
- **4D-EnVar replaces 4D-Var**, using background-error covariances from an EnKF ensemble
- Improved radiosonde and aircraft processing; ground-GPS assimilation; improved satellite-radiance bias correction; more AIRS and IASI channels
- New sea-ice concentration analysis

### RDPS v3.2.1 — operational May 6, 2014 (12 UTC run)
- IASI radiances from METOP-B added

### RDPS v3.2.0 — operational November 7, 2013
- CSR from Meteosat-10, ATOVS radiances and AVHRR polar winds from Metop-B added
- Updated RTTOV coefficients (neutral impact)
- AIRS/IASI background check switched from snow cover to snow depth, and albedo interpolation corrected. Together these raised AIRS and IASI observation counts by up to 50% and 30%.

### RDPS v3.1.0 — operational February 13, 2013 (12 UTC run)
- Adjustments to harmonize RDPS with GDPS v3.0.0, implemented the same day
- Jul 3, 2013 — RDPS binary updated to support production of variable PX. ECCC's changelog gives no version number for this update.

### RDPS v3.0.0 — operational October 3, 2012 (12 UTC run)
- **Horizontal resolution increased from 15 km to 10 km**
- **4D-Var replaces 3D-Var**
- Important changes to the GEM physics
- Observation cut-off reduced by 10 minutes, to T+1:55
- Oct 19, 2012 (18 UTC run) — All four daily runs now start at T+2:00, giving 5 more minutes to collect observations

### RDPS v2.0.0 improvements — November 16, 2011 (06 UTC run)
- 62 more IASI channels, 7 SSMIS channels (DMSP F16), and a water-vapour channel from several geostationary satellites added
- Polar restriction on AIRS channels removed; satellite thinning reduced from 250 km to 150 km
- AMDAR aircraft moisture observations added
- Radiance bias correction moved to a 7-day period (from 15); RTTOV modified
- New 0.20° SST analysis

---

## Official documentation
- RDPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_rdps/readme_rdps-datamart_en/
- RDPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_rdps/changelog_rdps_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_RDPS_e.pdf (still the 9.0.0 document as of October 31, 2026)
- ECCC operational systems changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Caron, J.-F., et al. (2015). Implementation of Deterministic Weather Forecasting Systems Based on Ensemble–Variational Data Assimilation at Environment Canada. Part II: The Regional System. *Mon. Wea. Rev.*, 143, 2560–2580. https://doi.org/10.1175/MWR-D-14-00353.1
- Côté, J., et al. (1998). The Operational CMC-MRB Global Environmental Multiscale (GEM) Model: Part I — Design Considerations and Formulation. *Mon. Wea. Rev.*, 126, 1373–1395.
- Girard, C., et al. (2014). Staggered Vertical Discretization of the Canadian Environmental Multiscale (GEM) Model Using a Coordinate of the Log-Hydrostatic-Pressure Type. *Mon. Wea. Rev.*, 142, 1183–1196.
- McTaggart-Cowan, R., et al. (2019). Modernization of Atmospheric Physics Parameterization in Canadian NWP. *J. Adv. Model. Earth Syst.*, 11, 3593–3635.
- Shahabadi, M. B., and M. Buehner (2021). Towards All-sky Assimilation of Microwave Temperature Sounding Channels in Environment Canada's Global Deterministic Weather Prediction System. *Mon. Wea. Rev.*, 149, 3725–3738.
