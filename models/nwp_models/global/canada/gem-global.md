# GDPS (Global Deterministic Prediction System)

## What this model is
The Global Deterministic Prediction System (GDPS) is Canada's primary global numerical weather prediction system, providing deterministic forecasts of atmospheric conditions worldwide.

GDPS is based on the Global Environmental Multiscale (GEM) atmospheric model and operates as a coupled atmosphere–ocean–sea ice prediction system, supplying large-scale guidance and boundary conditions for Canada's regional and high-resolution forecast systems.

As of version 10.0.0 (operational May 26, 2026), GDPS is a **hybrid physics–AI system**: GEM's large-scale temperature and horizontal wind fields are spectrally nudged toward the [GEML](./gdps-geml.md) data-driven AI weather model during the forecast integration. This hybrid configuration — previously distributed as the experimental GDPS — is now the operational system.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global
- **Atmospheric grid (native):** Yin–Yang horizontal grid, 2 × 2047 × 683 grid points
- **Distributed grid (GRIB2 output):** Global regular latitude–longitude, 0.15° × 0.15°, 2400 × 1201 grid points (−90°→90° latitude, 0°→359.85° longitude) — the GEM forecast is interpolated from the native Yin–Yang grid onto this uniform lat-lon grid for distribution
- **Ice-ocean grid:** Global 0.25° with 50 z-levels

---

## Basic details
- **Model type:** Deterministic global NWP, hybrid physics–AI, coupled to ocean and sea ice
- **Model system / core:** GEM (Global Environmental Multiscale) version 5.2, with large-scale spectral nudging toward the [GEML](./gdps-geml.md) AI weather model (version 1.1)
- **Dynamical formulation:** Hydrostatic primitive equations
- **Convection-allowing:** No (~15 km horizontal resolution)
- **Horizontal resolution:** ~15 km (quasi-uniform 0.135° Yin–Yang grid)
- **Vertical levels:** 84 staggered hybrid levels (Charney–Phillips vertical grid)
- **Model top:** 0.1 hPa
- **Forecast length:** Up to ~10 days (240 h)
- **Update frequency / cycles:** 2× daily early analysis runs (00, 12 UTC); 4× daily final analysis cycle (00, 06, 12, 18 UTC) feeds the next early run
- **Time step:** 450 seconds
- **Time integration:** Iterative-implicit, semi-Lagrangian (3D), 2 time-level
- **Temporal output resolution:** Typically 3-hourly

---

## Data assimilation
- **Data assimilation:** Yes
- **Method:** Four-dimensional ensemble–variational (4DEnVar), with a fully ensemble-derived background-error covariance built from 256-member LETKF backgrounds drawn from the 25 km GEPS 8.0.0 ensemble
- **Cadence:** Final analyses 4× daily (00, 06, 12, 18 UTC) with a 7-hour cutoff; early analyses 2× daily (00, 12 UTC) with a 3-hour cutoff. Each 6-hour assimilation window is centred on the synoptic hour, with observations binned at 15-minute intervals
- **Initialization:** 4D Incremental Analysis Update (4D-IAU) applied over the 6-hour window from t-3 to t+3 hours
- **Radiative transfer model:** RTTOV v13, with slant-path radiative transfer following the radiance line of sight
- **Notable observations assimilated:** Microwave radiances (AMSU-A, MHS, ATMS, SSMIS, MWHS-2, including all-sky AMSU-A, ATMS temperature and AMSUB/MHS humidity channels), infrared radiances (AIRS, IASI, CrIS), geostationary imager radiances, GPS-RO refractivity, AMVs, ASCAT scatterometer winds, ground-based GPS ZTD, radiosondes, surface stations, aircraft, and ozone retrievals (OMI, OMPS-NM, TROPOMI, MLS, SBUV/2, OMPS-NP)

---

## Coupled ice-ocean component
- **Ocean model:** NEMO 3.6
- **Sea ice model:** CICE 6.2.0 (with Delta-Eddington radiation and "bubbly" thermal conductivity scheme)
- **Horizontal resolution:** 0.25°
- **Vertical sampling:** 50 z-levels
- **Time step:** 450 s (synchronized with the atmosphere)
- **Coupling:** Every ocean time step (450 s) via the in-house GOSSIP coupler; air–ice and air–ocean fluxes computed in NEMO and passed to GEM
- **Ice-ocean initial conditions:** From [GIOPS](../../../ocean_models/global/canada/giops.md) version 3.5.0

---

## What it provides
Deterministic global forecasts of:
- Atmospheric temperature, wind (3D), humidity, geopotential, and surface pressure
- Quantitative precipitation forecast (QPF), precipitation rate and type
- Cloud condensate, cloud cover, boundary layer height
- Prognostic ozone (LINOZ), total column ozone, and clear-sky / all-sky UV indices
- Sea surface conditions (SST, sea ice concentration and thickness) from the coupled ice-ocean component
- Initial and boundary conditions for downstream Canadian systems

GDPS provides the large-scale deterministic guidance that underpins Canada's regional (RDPS) and high-resolution (HRDPS) deterministic prediction systems, and serves as the meteorological driver for systems such as [RAQDPS](../../../air_quality_models/regional/canada/raqdps.md).

---

## Hybrid AI-physics forecasting
Since v10.0.0, GEM's large-scale fields are spectrally nudged toward forecasts from [GEML](./gdps-geml.md) v1.1, ECCC's GraphCast-derived graph neural network AI weather model. The design is deliberately conservative — AI guidance is confined to the large scales and mid-troposphere where data-driven models have demonstrated skill, while GEM physics governs smaller scales, the boundary layer, and the stratosphere:
- **Nudged fields:** Large-scale temperature and horizontal wind components (u, v).
- **Vertical profile:** Applied between ~250–850 hPa; no nudging in the planetary boundary layer (>850 hPa) or stratosphere (<250 hPa).
- **Horizontal scales:** A discrete cosine transform (DCT) spectral filter is applied to the Yin and Yang grids separately — full nudging weight for scales >2750 km, ramping to zero below 2250 km.
- **Temporal handling:** GEML output is available every 6 hours; at each 450 s GEM time step the target is linearly interpolated between successive GEML forecasts, with a time-varying nudging weight that peaks when GEML output is available and decays in between (relaxation time 3.28125 h).

See [GEML](./gdps-geml.md) for details of the AI model itself. (Husain et al., 2024)

---

## Data availability
- **Is the data free?** Yes (no registration required for MSC Open Data)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence (attribution required; commercial use permitted) — https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Distribution channels:**
  - **MSC Datamart:** Direct file access — primary download channel
  - **MSC GeoMet:** Geospatial web services (WMS, WCS, OGC API)
  - **MSC AniMet:** Visualization service
- **Official download location:** https://dd.weather.gc.ca/today/model_gdps/15km/
  - **Path template:** `https://dd.weather.gc.ca/today/model_gdps/15km/{HH}/{hhh}/` — `{HH}` = run (`00`/`12`), `{hhh}` = 3-digit lead time (`000`–`240`)
  - **Filename convention:** `{YYYYMMDD}T{HH}Z_MSC_GDPS_{Var}_{LevelType}-{Level}_LatLon0.15_PT{hhh}H.grib2` (e.g. `20260714T00Z_MSC_GDPS_AirTemp_IsbL-0500_LatLon0.15_PT000H.grib2`)
- **Open data documentation:** https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps-datamart_en/

---

## Notes
- GDPS uses a global **Yin–Yang grid**, a distinctive feature of modern GEM configurations.
- Background-error covariances in 4DEnVar come entirely from the 256-member LETKF ensemble of GEPS 8.0.0 backgrounds (no NMC component), with scale-dependent horizontal localization.
- GDPS is part of a tightly integrated suite of Canadian operational prediction systems sharing the GEM core, the GEPS ensemble for DA covariances, and GIOPS for ice-ocean initialization.
- Open data licensing is genuinely open — no registration required, direct file access via the Datamart. Same as GIOPS, RIOPS, and CIOPS.
- **Version-history gaps and discrepancies (ECCC sources):**
  - (1) The v3.0.0 technical note describes the upgrade as "from version 2.2.2 to 3.0.0". ECCC's changelog has no 2.2.1 or 2.2.2 entries.
  - (2) The v9.0.0 changelog says GEM went "from 5.1.2" to 5.2.1, but v8.0.0 shipped GEM 5.1.0. The 5.1.0 → 5.1.2 step isn't documented; it may have come with the v8.1.0 HPC port.
  - (3) The multi-system changelog dates v5.1.0 to "Thursday, March 16, 2016". March 16, 2016 was a Wednesday.
  - (4) Releases 5.1.0 (2016) and the 2018 v6.0.0 observation swaps appear only in the multi-system changelog, not in the GDPS changelog.
- **Datamart path and naming migration (2026):** GDPS GRIB2 moved from `model_gem_global/15km/grib2/lat_lon/{HH}/{hhh}/` to `model_gdps/15km/{HH}/{hhh}/` (the `grib2/lat_lon/` segment was dropped and the folder renamed), and the filename scheme was overhauled at the same time — old `CMC_glb_{VAR}_{LVL}_latlon.15x.15_{YYYYMMDDHH}_P{hhh}.grib2` (GRIB abbreviations like `TMP`, `UGRD`) is replaced by `{YYYYMMDD}T{HH}Z_MSC_GDPS_{Var}_{LevelType}-{Level}_LatLon0.15_PT{hhh}H.grib2` (descriptive CamelCase tokens like `AirTemp`, `WindU`). The old `model_gem_global` path now returns HTTP 404 — a hard cutover, so scripts targeting the old paths or filenames will break.

---

## Version history

The list runs from the oldest version in ECCC's GDPS changelog (v2.2.0, November 2011) to the present, newest first. Major upgrades get a short summary. Small data-assimilation-only updates (new satellites, swapped instruments) are grouped under the version that was running. These updates did not change the version number, except where shown.

### GDPS v10.0.0 — operational May 26, 2026 (current)
Implemented at the 12 UTC run on May 26, 2026, replacing v9.1.0. The headline change is the introduction of **hybrid AI-physics forecasting**: the configuration previously distributed as the experimental GDPS is now operational.

Headline changes:
- **Hybrid AI-physics forecasting via spectral nudging.** GEM's large-scale temperature and horizontal wind (u, v) fields between 250–850 hPa are spectrally nudged toward forecasts from **[GEML](./gdps-geml.md) v1.1**, ECCC's graph neural network AI weather model. A DCT-based spectral filter fully constrains scales >2750 km (ramping to zero below 2250 km); the GEML target is refreshed every 6 hours and interpolated in time at each model step (relaxation time 3.28125 h). See *Hybrid AI-physics forecasting* above. (Husain et al., 2024)
- **Reported impact (ECCC):** Accuracy gains over v9.1.0, mainly in the mid and upper troposphere. Gains are small in the first 2–3 days and largest from day 3 to day 10. ECCC also reports better tropical cyclone tracks and about half a day of added predictability at day 7.
- All other components remain aligned with the v9.1.0 / v9.0.0 operational framework (4DEnVar data assimilation, GEM 5.2 dynamics, NEMO 3.6 / CICE 6.2.0 ice-ocean coupling).
- **Datamart:** The hybrid GDPS became the source of the derived products (PROGNOS post-processing, SCRIBE matrices, WEonG, global ice analysis). Distribution moved from `model_gem_global/` to `model_gdps/` (see Notes). ECCC paused the experimental standalone GEML feed from April 28, 2026, ahead of the switch.

### GDPS v9.1.0 — operational April 14, 2026
- Port of v9.0.0 to ECCC's new High Performance Computing infrastructure. No science or configuration changes to the forecast or assimilation systems.

### GDPS v9.0.0 — operational June 11, 2024
Implemented at the 12 UTC run on June 11, 2024. Major work was on the data assimilation component, with smaller changes to the atmospheric and ice-ocean forecast components.

Headline changes:
- **GEM upgraded from 5.1.2 to 5.2.1**
- **New 10 km uncoupled component (G0)**, run mainly to supply boundary conditions to regional systems (e.g. REPS, HRDPS, CAPS)
- **4DEnVar with a fully ensemble-derived B matrix** built from 256-member LETKF backgrounds from the new 25 km **GEPS 8.0.0** (no NMC component); Hessian matrix cycling removed
- **Radiative transfer model upgraded to RTTOV v13**
- **All-sky assimilation extended to ATMS temperature and AMSUB/MHS humidity channels** (cloud-affected radiances), in addition to the existing all-sky AMSU-A
- **Analysis increment vertical levels** moved from L80/vCode 5002 to L84/vCode 5005, matching GEPS
- **3DEnVar reference state for radiance bias correction**, replacing 3DVar
- Modifications to hyperspectral infrared QC for albedo changes; reduced horizontal localization at the largest scales
- Additional ozone observations: OMPS-NM, TROPOMI, OMPS-NP added; piecewise weighted-averaging interpolation and weighted integration of total column ozone as a strong constraint
- **4D-IAU** initialization with recycling of an expanded set of physics variables (total condensate, cloud fraction, TKE, turbulent regime, mixing length, friction velocity, PBL height, PBL cloud water/fraction, PBL flux enhancement factor)
- **Iterative solver of the Euler equations** (Pacull and Aubert, 2014) and improved trapeze-cubic semi-Lagrangian trajectory calculations
- **Ice-ocean component:** initialization moved to GIOPS 3.5.0; **CICE upgraded from v4 to 6.2.0** with Delta-Eddington radiation and "bubbly" thermal conductivity; snow and ice analysis from GIOPS added to the assimilation cycle; new Mean Dynamic Topography for SLA assimilation; sea-ice concentration analysis now done in MIDAS
- Increased momentum roughness length over sea ice (from 0.16 m to 0.54 m)

Data-assimilation-only updates while at v9.0.0:
- Oct 23, 2024 — HSCAT sea-surface winds (HY-2B, HY-2C); OMPS total-column ozone (NOAA-21)
- Mar 3, 2025 — Increased TROPOMI total-column ozone thinning
- Apr 2, 2025 — New SSMIS thinning scheme (more data kept); azimuth-angle fix in slant-path computation for AMSU-A and MHS
- May 26, 2025 — ATMS and CrIS (NOAA-21); MWHS-2 via RARS (FY-3D, FY-3E)
- Jun 17, 2025 — Automated dynamic allowlists, rebuilt every 6 h, for radiosonde, aircraft and ground-GNSS observations, replacing monthly manual lists
- Jun 26, 2025 — AMVs from NOAA-21 and GOES-19
- Jul 23, 2025 — PROGNOS v1.0.0 statistical post-processing (successor to UMOS) introduced on GDPS output. A downstream product change, not a model change

### GDPS v8.1.1 — operational March 8, 2023
- Corrected quality control of snow-depth observations from Canadian automatic stations used in the snow analysis

Data-assimilation-only updates while at v8.1.1:
- Jul 5, 2023 — Commercial GNSS-RO from Spire (LEMUR constellation)
- Sep 14, 2023 — Commercial GNSS-RO from PlanetIQ (GNOMES constellation)
- Nov 2, 2023 — GOES-18 clear-sky radiances (CSR)

### GDPS v8.1.0 — operational June 28, 2022
- Port of v8.0.0 to ECCC's then-new HPC infrastructure. No science changes listed.

Data-assimilation-only updates while at v8.1.0:
- Nov 3, 2022 — Ship surface observations in BUFR format (TAC ship reports were already assimilated)
- Jan 4, 2023 — GOES-17 AMVs replaced by GOES-18
- Feb 15, 2023 — GPS-RO from Sentinel-6A

### GDPS v8.0.0 — operational December 1, 2021 (12 UTC run)
- **GEM upgraded to v5.1.0**, still on the Yin–Yang grid. Implemented together with RDPS 8.0.0, GEPS 7.0.0 and RAQDPS023 in ECCC's December 2021 suite upgrade
- ECCC's changelog gives no summary of changes for this version, only links to the innovations factsheet, technical note and specifications (not retrievable for this entry). *(TBD — detailed v8.0.0 changes)*

Data-assimilation-only updates while at v8.0.0:
- Feb 16, 2022 — GPS-RO from GRACE-C and GRACE-D

### GDPS v7.1.0 — operational January 21, 2020
- Port of v7.0.0 to ECCC's then-new HPC infrastructure. No science changes listed.

Data-assimilation-only updates while at v7.1.0:
- Apr 28, 2020 — AMVs from MetOp-3/C
- Apr 29, 2020 — GPS-RO from PAZ and KOMPSAT-5
- Jun 26, 2020 — GPS-RO from COSMIC-2
- Dec 8, 2020 — AMSU-A and MHS (MetOp-3/C); CrIS full spectral resolution (Suomi NPP); GOES-17 CSR
- Jan 26, 2021 — MWHS-2 (FY-3C); IASI moved from MetOp-2/A to MetOp-3/C

### GDPS v7.0.0 — operational July 3, 2019
- **GEM upgraded from v4.8-LTS.16 to v5.0.0**
- **Horizontal resolution increased from 25 km to 15 km**
- **Vertical levels increased from 80 to 84**
- ISBA land-surface improvements
- **Ocean model upgraded from NEMO v3.1 to v3.6**
- Uniform 7-hour observation cut-off for all daily final-analysis runs
- Ice-ocean assimilation: GIOPS upgraded from v2.3.0 to v3.0.0 (SAM2 update); SST and sea-ice analyses refined from 0.2° to 0.1°
- Jul 23, 2019 — More observations added within seven families already in use, mainly for robustness. ECCC's changelog gives no version number for this update.

### GDPS v6.1.0 — operational September 18, 2018
- No forecast-model changes
- Improved processing of radiosonde, AMV, ground-based GPS and aircraft observations
- About 1.7% more observations assimilated, mostly CSR water-vapour channels
- **RTTOV upgraded from v10 to v12.1**
- 4D-EnVar analysis increment resolution increased from 50 km to 37 km

### GDPS v6.0.0 — operational November 1, 2017
- **Two-way coupling with an ice-ocean model** (via GIOPS), so sea-surface and ice conditions evolve during the forecast instead of staying fixed. This was the start of GDPS as a coupled atmosphere–ocean–ice system.
- GEM upgraded to v4.8.2, mainly surface-interface changes
- No assimilation or observation changes

Data-assimilation-only updates while at v6.0.0 (listed in ECCC's multi-system changelog):
- Mar 1, 2018 — Meteosat-10 AMVs replaced by Meteosat-11
- Jun 7, 2018 — GOES-13 AMVs replaced by GOES-16; Meteosat-10 CSR replaced by Meteosat-11

### GDPS v5.1.0 — operational March 16, 2016 (12 UTC run)
- Data assimilation only (listed in ECCC's multi-system changelog, not the GDPS changelog): MTSAT-2 AMV and CSR replaced by Himawari-8; VIIRS AMVs (Suomi NPP) and RapidScat winds (ISS) added; geostationary CSR thinning modified

### GDPS v5.0.0 — operational December 15, 2015
- **Yin–Yang grid introduced:** Two overlapping limited-area grids replace the uniform global lat-lon grid. Resolution stays at 25 km.
- Trapezoidal semi-Lagrangian trajectory method with cubic interpolation
- One extra momentum level above 10 hPa; modified flattening rate of terrain-following levels
- Diagnostic levels at 1.5 m and 10 m
- Assimilation moved to a staggered vertical coordinate matching the forecast model
- New observations: 17 ATMS channels, 103 more CrIS channels, 600+ more ground-GPS sites (mostly Europe); inter-channel error correlations for infrared and microwave radiances

### GDPS v4.0.0 — operational November 18, 2014
- **4D-EnVar replaces 4D-Var**, using background-error covariances from an EnKF ensemble
- 4D Incremental Analysis Update (IAU) initialization, with recycling of key prognostic variables
- Improved radiosonde and aircraft processing; ground-GPS assimilation; improved satellite-radiance bias correction; more AIRS and IASI channels
- New sea-ice concentration analysis

### GDPS v3.1.1 — operational May 6, 2014
- IASI radiances from METOP-B added (from the April 30, 2014 06 UTC trial-field run), roughly doubling the IASI data used

### GDPS v3.1.0 — operational November 7, 2013
- CSR from Meteosat-10, ATOVS radiances and AVHRR polar winds from Metop-B added
- Updated RTTOV coefficient files for AMSU-B and MHS (neutral impact)

### GDPS v3.0.0 — operational February 13, 2013
- New GEM version, with changes to 4D-Var and the physics
- **Horizontal resolution increased to 25 km**
- **Staggered hybrid vertical coordinate introduced**
- Observation cut-off for the 00 and 12 UTC runs shortened to T+3:05
- Better objective scores, especially over North America in winter
- RDPS harmonized with it and upgraded to v3.1.0 at the same time
- Aug 12, 2013 — Production adjusted so global matrices arrive about 30 minutes earlier. ECCC's changelog gives no version number for this update.

### GDPS v2.2.0 — operational November 16, 2011
- 62 more IASI channels, 7 SSMIS channels (DMSP F16), and water-vapour channels from GOES-W and Meteosat added
- Polar restriction on AIRS channels removed; satellite thinning reduced from 250 km to 150 km
- AMDAR aircraft moisture observations added
- Radiance bias-correction period shortened from 15 to 7 days; RTTOV modified
- New 0.20° SST analysis
- Daily assimilated data increased from about 1.9 million to 4.2 million observations

---

## Related: AI component

The hybrid forecasting introduced in v10.0.0 relies on [GEML](./gdps-geml.md), ECCC's data-driven AI weather model, as its spectral nudging target. GEML is also distributed as a standalone 10-day forecast product on the MSC datamart. See [`gdps-geml.md`](./gdps-geml.md) for full documentation of the AI model.

---

## Official documentation
- GDPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps-datamart_en/
- GDPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/changelog_gdps_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_GDPS_e.pdf
- ECCC operational systems changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Buehner, M., et al. (2015). Implementation of Deterministic Weather Forecasting Systems Based on Ensemble–Variational Data Assimilation at Environment Canada. Part I: The Global System. *Mon. Wea. Rev.*, 144, 2532–2559. https://doi.org/10.1175/MWR-D-14-00354.1
- Côté, J., et al. (1998). The Operational CMC-MRB Global Environmental Multiscale (GEM) Model: Part I — Design Considerations and Formulation. *Mon. Wea. Rev.*, 126, 1373–1395.
- Girard, C., et al. (2014). Staggered Vertical Discretization of the Canadian Environmental Multiscale (GEM) Model Using a Coordinate of the Log-Hydrostatic-Pressure Type. *Mon. Wea. Rev.*, 142, 1183–1196.
- Husain, S. Z., et al. (2024). Leveraging data-driven weather models for improving numerical weather prediction skill through large-scale spectral nudging. *arXiv*, arXiv:2407.06100. https://doi.org/10.48550/arXiv.2407.06100
- Qaddouri, A., and V. Lee (2011). The Canadian Global Environmental Multiscale model on the Yin–Yang grid system. *Q. J. R. Meteorol. Soc.*, 137, 1913–1926.
- Shahabadi, M. B., and M. Buehner (2024). Implementation of All-Sky Assimilation of Microwave Humidity Sounding Channels in Environment Canada's Global Deterministic Weather Prediction System. *Mon. Wea. Rev.*, 152, 1027–1038.
