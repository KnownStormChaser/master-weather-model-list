# GLO12 (Mercator Global Ocean Physical Analysis and Forecast)

## What this model is
The **Mercator Global Ocean Physical Analysis and Forecast** is the operational global ocean physics forecast system run by **Mercator Ocean International** for the **Copernicus Marine Service**. It provides 10-day forecasts of 3D ocean temperature, salinity, currents, sea level, mixed layer depth, and sea ice fields at 1/12° (~8 km) horizontal resolution with 50 vertical levels.

GLO12 is the European counterpart to NOAA's [Global RTOFS](../../../ocean_models/global/us/rtofs-global.md) — both are eddy-resolving global ocean physics systems at 1/12° resolution providing similar forecast variables. The two systems are the principal global operational ocean physics forecasts available worldwide. They differ in model core (NEMO for GLO12, HYCOM for RTOFS), sea ice model (LIM3 vs CICE), data assimilation approach (SAM2/SEEK Kernel vs 3DVar), and operating institution.

The system name "GLO12" refers to the operational system identifier; the current operational version is **GLO12v4**, distributed under the Copernicus Marine product identifier **GLOBAL_ANALYSISFORECAST_PHY_001_024**. The product is currently at issue 2.3 (March 2026) which introduced wave forcing from MFWAM — a recent addition that brings GLO12 closer to representing wave-mediated ocean processes.

GLO12 also produces a notable derived product, the **Surface and Merged Ocean Currents (SMOC)** dataset, which combines general circulation, tidal currents, and Stokes drift into a single hourly surface velocity field — the most complete representation of total surface ocean motion available from any operational global system.

---

## Who runs it
- **Production Unit:** Mercator Ocean International
- **Country:** France (headquartered in Toulouse)
- **Programme:** Copernicus Marine Service (implemented by Mercator Ocean International on behalf of the European Union)
- **Role in larger system:** Production Unit for the Global Monitoring and Forecasting Centre (Global MFC); GLO12 outputs are used as boundary conditions for several Copernicus Marine regional ocean physics products

---

## What area it covers
- **Coverage:** Global ocean
- **Domain bounds:** 180°W – 180°E, 89°S – 90°N
- **Native grid:** Arakawa C-grid at 1/12° with 50 vertical levels (the operational model grid)
- **Distributed grid:** Regular collocated 1/12° grid (4320 × 2041), bicubically interpolated from the native grid for distribution
- **Vertical levels:** 50 z-levels from surface to seafloor
- **Vertical coordinate:** z-level (fixed depth)

---

## Basic details
- **Model type:** Global deterministic ocean physics forecast, coupled to sea ice, with data assimilation
- **Core ocean model:** NEMO v3.6 (Madec et al., 2017)
- **Sea ice model:** LIM3 — multi-category sea ice model with Elastic-Viscous-Plastic (EVP) rheology and 11 sea ice categories
- **System name:** GLO12v4 (operational system); GLOBAL_ANALYSISFORECAST_PHY_001_024 (Copernicus Marine product ID)
- **Eddy-resolving:** Yes — the 1/12° resolution resolves mesoscale ocean eddies (typically ~50–100 km diameter at mid-latitudes)
- **Forecast length:** 10 days (240 hours) — longer than RTOFS's 8-day forecast
- **Update frequency:** Daily (forecast); weekly (analysis updates)
- **Cycle time:** 00 UTC base time
- **Target delivery time:** 12:00 UTC daily
- **Output frequencies:** Hourly (surface), 6-hourly instantaneous (3D), daily mean, monthly mean — see "What it provides" for the full breakdown
- **Bathymetry:** ETOPO1 for deep ocean, GEBCO8 in regions shallower than 200 m, with linear interpolation in the 200–300 m transition layer
- **Initial conditions:** GLO12v3 restart file from October 2016 (the model has been running operationally since then with continuous restart chains)

---

## Forcing

GLO12 uses three primary forcing sources:

- **Atmospheric forcing:**
  - **Source:** ECMWF IFS HRES (the same atmospheric model that powers ECMWF's operational weather forecasts)
  - **Frequency:** 1-hourly, 3-hourly, and 6-hourly fields
  - **Resolution:** 1/10° (~10 km)
  - **Processing:** All atmospheric fields are interpolated to 1-hourly for the ocean model
- **Wave forcing (added in v2.3, March 2026):**
  - **Source:** MFWAM (Mercator's operational global wave model)
  - **Frequency:** 3-hourly
  - **Resolution:** 1/10° (~10 km)
  - **Forecast handling:** Temporal extrapolation applied for the last 12 hours of each forecast cycle (where wave model forecast is shorter than ocean forecast)
- **River runoff:**
  - 100 major rivers from Dai et al. (2009)
  - Runoff fluxes from Greenland and Antarctica ice sheet meltwater

**Tidal forcing:** Tidal constituents are **not** explicitly resolved in the primary GLO12 forecast. However, tidal currents are included separately in the SMOC derived product (see "What it provides" below).

---

## Coupling

GLO12 is **coupled to sea ice** via the LIM3 multi-category sea ice model:
- **Ocean → ice:** Surface ocean state passed to LIM3
- **Ice → ocean:** Sea ice modifies surface momentum, heat, and freshwater fluxes back to the ocean
- **Coupling mode:** Online, every model timestep
- **LIM3 configuration:** 11 sea ice thickness categories with Elastic-Viscous-Plastic rheology

GLO12 is **not directly coupled** to wave or atmospheric models in the operational run. The PUM lists "Coupling: N/A" for the system overall — wave fields from MFWAM are applied as one-way forcing rather than two-way coupling, and atmospheric fields from ECMWF IFS are similarly one-way.

This is a notable contrast with some Copernicus Marine regional systems (e.g., [IBIWAM](../../../wave_models/regional/spain/ibiwam.md) and [AMM15-WW3](../../../wave_models/regional/uk/amm15-ww3-uk.md)) which do have two-way wave-ocean coupling. The trade-off is computational: full two-way coupling at global 1/12° scale is significantly more expensive than one-way wave forcing.

---

## Data assimilation

GLO12 uses **SAM2 (SEEK Kernel)** as its core data assimilation algorithm — a reduced-order Kalman filter derivative developed by Mercator Ocean. The system is augmented with several additional components:

### DA scheme components
- **SAM2 (SEEK Kernel) 4D:** Core assimilation algorithm — a multivariate, reduced-rank Kalman filter implemented in 4D (using observations distributed in time)
- **Incremental Analysis Update (IAU):** Increments are applied gradually rather than as a single shock to preserve dynamical balance
- **3D-Var bias correction:** Slowly-varying bias correction with a 1-month time window
- **Quality control on T/S vertical profiles:** Pre-processing of in-situ temperature and salinity observations
- **3D observation error files:** Spatially-varying observation error specifications for in-situ profile assimilation
- **Super-observation method:** Used to reduce representativeness error by combining nearby observations
- **Global SSH constraint:** Global mean increment of total sea surface height is set to zero (preventing artificial drift in global mean sea level)

### Assimilated observations

**Sea Surface Temperature:**
- L3S SST from ODYSSEA (Copernicus Marine SST TAC product)

**Sea Ice Concentration:**
- OSI SAF (EUMETSAT Ocean and Sea Ice Satellite Application Facility)

**Sea Level Anomaly (altimetry):**
- AVISO multi-mission altimeter data (synthesizing observations from operating altimeter missions including Jason-3, Sentinel-3A/B, Sentinel-6A, CryoSat-2, SARAL/AltiKa, and SWOT as available)

**Temperature and Salinity profiles:**
- CORIOLIS database (consolidating Argo floats, XBT, CTD, gliders, and other in-situ profiling instruments)

**Below 2000 m:**
- WOA 2013 climatology (temperature and salinity) used with non-Gaussian error specification at depth

**Mean Dynamic Topography reference:**
- CNES-CLS18 (Mulet et al., 2021) used as the MDT reference for absolute dynamic topography assimilation

---

## Analysis tier structure

A distinctive feature of GLO12 is its **two-tier analysis structure**, which is unusual among operational ocean systems and worth understanding:

- **NRT-Analysis (Near Real Time):** Analysis run with data assimilation for a period very close to the present. Quality is constrained by limited observation availability — fewer observations have arrived through the data pipeline, and some observations are less accurate (e.g., not yet quality-controlled).
- **Best-Analysis:** Analysis run with data assimilation for a period further from present. Has the maximum number of observations and the most accurate observations available.

Catalogue entries for any given date are **progressively upgraded** through this hierarchy:
1. First appears as a 10-day forecast (~10 days before validity date)
2. Forecast is replaced by hindcast simulation (no DA) when the validity date arrives
3. Simulation is replaced by NRT-analysis approximately 1 week after validity
4. NRT-analysis is replaced by best-analysis approximately 2-3 weeks after validity
5. Best-analysis is the final state and is not overwritten further

This means a given point in the catalogue archive may be either a forecast, simulation, NRT-analysis, or best-analysis depending on how recent it is — and a value that was retrieved 3 weeks ago may have been updated since to a more accurate best-analysis value. Users doing time-series analysis should be aware of this distinction; the QUID document provides validation statistics separately for NRT-analysis and best-analysis.

This contrasts with RTOFS, which produces a single analysis that is not subsequently revised.

---

## What it provides

GLO12 distributes **sixteen separate datasets** organized by temporal frequency, dimensionality, and variable group. This is the most extensive variable distribution of any operational global ocean system.

### 3D fields (every 6 hours, instantaneous)
- Potential temperature
- Salinity
- Horizontal currents (eastward + northward components)

### 3D fields (daily mean)
- Potential temperature
- Salinity
- Horizontal currents
- Vertical currents (upward velocity)

### 3D fields (monthly mean)
- All of the above (potential temperature, salinity, horizontal currents, vertical currents)

### 2D surface and seafloor fields (daily and monthly mean)
- Sea surface height (above geoid)
- Mixed layer depth (defined by σ-θ density criterion)
- Sea floor pressure
- Sea floor potential temperature
- Sea floor salinity
- Sea ice concentration
- Sea ice thickness
- Snow thickness on sea ice
- Sea ice albedo
- Sea ice age
- Sea ice surface temperature
- Sea ice speed and velocity components

### Hourly surface fields (mean)
- Potential temperature
- Sea surface salinity
- Eastward and northward current
- Sea surface height

### SMOC — Surface and Merged Ocean Currents (hourly instantaneous)
A distinctive Copernicus Marine derived product combining three physical components on the 1/12° global grid:
- **General circulation** (uo, vo) from GLO12 itself
- **Tidal currents** (utide, vtide) from a tidal model
- **Stokes drift** (ustokes, vstokes) from MFWAM
- **Total** (utotal, vtotal) — the linear sum of all three components

The SMOC product is uniquely useful for applications requiring total surface ocean motion: drift trajectory modelling (search and rescue, marine pollution, debris tracking), Lagrangian particle tracking, and surface biophysical applications.

### MOL — Merged Ocean Levels (hourly instantaneous)
High-frequency total water level decomposed into:
- Total elevation = ocean tide + inverse barometer + sea surface height + global mean steric variation + global mean mass volume variation
- Each component distributed separately for users needing physical decomposition

### SST anomaly fields (daily and monthly)
- Sea surface temperature anomaly relative to climatology

### Climatology uncertainty (monthly)
- Monthly RMSD, mean difference, and observation count for SST relative to climatology

### Static fields
- Bathymetry, land-sea mask, mean dynamic topography, cell dimensions, model level number at sea floor

---

## Data availability

- **Is the data free?** Yes (registration required for Copernicus Marine MDS access)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4, CF-1.4 conventions
- **Product identifier:** `GLOBAL_ANALYSISFORECAST_PHY_001_024`
- **Number of distributed datasets:** 16 (plus static fields)
- **File naming examples:**
  - `glo12_rg_1d-m_{date1}-{date1}_2D_{mode}_R{date2}.nc` (daily mean)
  - `glo12_rg_6h-i_{date1}-{HH}h_3D-uovo_{mode}_R{date2}.nc` (6-hourly instantaneous)
  - `glo12_rg_1m-m_{YYYYMM}-{YYYYMM}_3D-thetao_{mode}.nc` (monthly mean)
  - `MOL_{date1}_R{date2}.nc` (merged ocean levels)
- **File size range:** ~34 MB (SST anomaly) to ~2.5 GB (hourly 2D fields)
- **Archive:** Rolling 2-year archive on the Marine Data Store
- **Pre-2024 data:** Available upon request from Mercator Ocean
- **Official access:** https://data.marine.copernicus.eu/product/GLOBAL_ANALYSISFORECAST_PHY_001_024/description
- **Delivery mechanism:** Copernicus Marine Toolbox
- **Licence:** Copernicus Marine Service licence (free with registration)
- **Missing value:** 10.E36
- **Land masking:** Land grid points use `_FillValue` attribute

---

## Version history

### March 2026 — Issue 2.3 (current)
- **Wave forcing added:** MFWAM wave fields now provide forcing to GLO12 (3-hourly, 1/10°). This is a methodologically significant addition that brings wave-mediated processes into the ocean physics forecast for the first time.
- Temporal extrapolation applied for the last 12 hours of each forecast cycle to handle the shorter MFWAM forecast horizon

### October 2025 — Issue 2.2
- Information added on tidal constituents (representation in the SMOC derived product)

### May 2024 — Issue 2.1
- Additional datasets added to the product portfolio
- Continued refinement of forecast quality

### June 2023 — Issue 2.0
- New 6-hourly instantaneous 3D dataset added
- Extended forecast lead time for some products

### September 2022 — Issue 1.9
- Updated archive availability information

### October 2021 — Issue 1.8
- SMOC dataset standard names corrected

### May 2021 — Issue 1.7
- 10-day forecast extended to SMOC product

### July 2020 — Issue 1.6
- Nomenclature description and FTP download behaviour documented

### November 2019 — Issue 1.5
- Instantaneous datasets added (separate from the existing time-mean fields)

### January 2019 — Issue 1.4
- New SMOC dataset introduced — merged general circulation, tides, and waves into single surface velocity product

### April 2018 — Issue 1.3
- Sea surface height (SSH) information clarified in documentation

### September 2017 — Issue 1.2
- Static fields and monthly mean datasets added; new template

### October 2016 — Initial GLO12 implementation
- The current GLO12 system has been running operationally since 5 October 2016 with continuous restart chains
- Earlier Mercator global ocean systems existed at lower resolutions before this

---

## Relationship to other ocean products

### Peer global ocean physics systems
- **[Global RTOFS](../../../ocean_models/global/us/rtofs-global.md)** — NOAA's operational global ocean physics forecast. The two systems are direct peers: same 1/12° resolution, similar forecast variables, similar 8–10 day forecast range. Different model core (NEMO vs HYCOM), different sea ice model (LIM3 vs CICE v4), different DA approach (SAM2/SEEK vs 3DVar). Together GLO12 and RTOFS represent the two dominant operational global ocean forecasts.
- **Navy Global HYCOM (FNMOC)** — U.S. Navy operational HYCOM run; shares the HYCOM model core with RTOFS, operated independently by FNMOC.
- **BLUElink OceanMAPS** — Australian Bureau of Meteorology operational global ocean forecast (~1/10° global).

### Companion Copernicus Marine products
- **MFWAM (Global, Copernicus Marine)** — Mercator's operational global wave model distributed through Copernicus Marine, now providing wave forcing to GLO12 (since March 2026). [See MFWAM Copernicus entry](../../../wave_models/global/france/mfwam-copernicus.md).
- **GLORYS12** — Mercator's global ocean reanalysis at 1/12°. Same model lineage and resolution as GLO12 but covers the historical period 1993–present with consistent reprocessing. **Not in this repository** as the repo focuses on operational forecasts rather than reanalyses.
- **Copernicus Marine global biogeochemistry product** (`GLOBAL_ANALYSISFORECAST_BGC_001_028`) — companion product providing nutrients, carbon, phytoplankton, and oxygen forecasts. Not currently in this repository.

### Regional ocean physics products nested inside GLO12
GLO12 provides boundary conditions for several Copernicus Marine regional ocean physics products operated by other Production Units:
- North-West European Shelf physics (UK Met Office)
- Iberian-Biscay-Irish physics (Nologin/CESGA)
- Mediterranean physics (CMCC)
- Black Sea physics (companion to BSeas7)
- Baltic physics (BAL MFC)
- Arctic physics (MET Norway)

Each regional system provides higher resolution within its domain while inheriting GLO12's outputs at the lateral boundaries.

### AI-based counterparts
**GLONET** is Mercator Ocean's neural-network global ocean forecast system, trained on GLO12 reanalysis data (GLORYS12) and benchmarked against GLO12. As of GLO12 v2.3, GLONET is a research/development product (accessible via the EDITO platform) rather than a fully operational replacement. The relationship between GLO12 and GLONET is analogous to that between IFS and AIFS at ECMWF — GLO12 is the established physics-based system, GLONET is its neural counterpart trained on the corresponding reanalysis.

---

## Notes
- The system has run continuously since October 2016 — the GLO12v4 system inherits a long-running model state through restart files dating back to that initial implementation. This long continuous integration is one factor contributing to GLO12's quality, since the model has had time to spin up internal dynamics that match observed climatology.
- The hybrid analysis structure (NRT-analysis → best-analysis) is genuinely unusual and worth understanding for anyone using the catalogue archive. A retrieval done today for last week's date may be NRT-analysis; the same retrieval done in 3 weeks may be best-analysis. For most operational and research uses this is fine, but reproducibility analyses should record the catalogue retrieval date alongside the data.
- The SMOC product is one of GLO12's most distinctive features. Combining general circulation, tides, and Stokes drift into a single surface velocity field is operationally rare — most systems require users to combine these components themselves from separate sources. SMOC is particularly valuable for drift modelling applications.
- The bathymetry approach (ETOPO1 deep + GEBCO8 shallow with linear interpolation) is a pragmatic blend chosen because GEBCO has higher resolution in shallow coastal regions where ETOPO1 has known limitations.
- LIM3's 11 sea ice categories provide considerably more detailed sea ice physics than CICE v4's typical configurations — particularly for representing ridging, melt ponds, and ice age distributions. Polar applications may benefit from this extra detail.
- The vertical grid uses 50 fixed z-levels, which is standard for NEMO. This contrasts with HYCOM's hybrid coordinate (used in RTOFS) which adapts its vertical grid to flow conditions. Both approaches have advantages: z-levels are simpler to work with for users; hybrid coordinates better resolve density structure in stratified regions.
- The 10-day forecast horizon is longer than RTOFS's 8 days. Whether this is meaningful depends on application — forecast skill at extended ranges degrades for both systems, and the Lagrangian decorrelation timescale of mesoscale ocean eddies is roughly 10–15 days.
- Wave forcing in v2.3 (March 2026) is genuinely new and worth flagging for users with long time series. Pre-2026 GLO12 output had no wave influence in its surface ocean dynamics; post-March 2026 output does. This may show up as a small but systematic shift in surface mixed layer dynamics.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/GLOBAL_ANALYSISFORECAST_PHY_001_024/description
- Product User Manual (PUM): https://documentation.marine.copernicus.eu/PUM/CMEMS-GLO-PUM-001-024.pdf
- Quality Information Document (QUID): https://documentation.marine.copernicus.eu/QUID/CMEMS-GLO-QUID-001-024.pdf
- Mercator Ocean International: https://www.mercator-ocean.eu/
- NEMO ocean model: https://www.nemo-ocean.eu/
- Copernicus Marine Service: https://marine.copernicus.eu/

### Key references
- Madec, G. and the NEMO team (2017). NEMO ocean engine. *Note du Pôle de modélisation*, Institut Pierre-Simon Laplace.
- Dai, A., Qian, T., Trenberth, K.E., Milliman, J.D. (2009). Changes in continental freshwater discharge from 1948 to 2004. *Journal of Climate*, 22, 2773-2792.
- Mulet, S., Rio, M.-H., Etienne, H., Dibarboure, G., Picot, N. (2021). The new CNES-CLS18 mean dynamic topography of the global ocean from altimetry, gravity and in-situ data. *Ocean Science*, 17, 789-808.
- Garric, G. and Parent, L. (various years). Mercator-Ocean global ocean reanalysis papers.
- Boucher, O. et al. (2018). Mercator Ocean's analysis and forecast system overview.
