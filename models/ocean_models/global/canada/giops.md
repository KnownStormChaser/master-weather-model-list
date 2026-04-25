# GIOPS (Global Ice-Ocean Prediction System)

## What this model is
The **Global Ice-Ocean Prediction System (GIOPS)** is the operational global ocean and sea ice forecast system run by **Environment and Climate Change Canada (ECCC)** at the **Canadian Centre for Meteorological and Environmental Prediction (CCMEP)**. GIOPS provides daily 10-day forecasts of ocean temperature, salinity, currents, sea level, and sea ice fields globally, with particular emphasis on the Arctic and high-latitude regions where Canadian operational interests are strongest.

GIOPS is one of three major operational global ocean physics forecast systems alongside [Mercator's GLO12](../../france/glo12.md) (Copernicus Marine) and [NOAA's Global RTOFS](../../us/rtofs-global.md). All three share NEMO or HYCOM ocean cores and similar forecast horizons, but operate at different resolutions and serve different national operational priorities. GIOPS runs at 1/4° on the global ORCA025 tripolar grid (coarser than the 1/12° of GLO12 and RTOFS), but distinguishes itself through its strong sea ice modelling capability — a natural emphasis given Canada's Arctic operational responsibilities — and its **two-way coupling with the Global Deterministic Prediction System (GDPS)**, ECCC's operational atmospheric NWP model.

The current operational version is **GIOPS v3.6.0**, implemented April 14, 2026 alongside ECCC's migration to a new High Performance Computing infrastructure. The v3.6.0 upgrade is computational/infrastructure-only — the scientific formulation, data assimilation, model components, and outputs are unchanged from **v3.5.0** (June 11, 2024), which introduced significant scientific upgrades including CICE6 sea ice, the Delta-Eddington radiation scheme, and updated mean dynamic topography.

---

## Who runs it
- **Operating organization:** Environment and Climate Change Canada (ECCC)
- **Production unit:** Canadian Centre for Meteorological and Environmental Prediction (CCMEP), part of the Meteorological Service of Canada (MSC)
- **Country:** Canada
- **Programme:** MSC Open Data — operational forecast services for Canadians, government agencies, and partners

---

## What area it covers
- **Coverage:** Global ocean
- **Native grid:** ORCA025 — global tripolar grid at 1/4° resolution (a NEMO standard configuration)
- **Vertical levels:** 50 levels from surface (0.5 m) to 5500 m depth
- **Distributed grids (post-processed):**
  - **Global grid:** Regular 0.2° latitude-longitude grid, covering the global ocean north of 80°S
  - **Arctic grid:** North-polar stereographic projection at 5 km spacing at the standard parallel 60°N, covering the Arctic Ocean and adjacent sub-polar seas

The dual-grid distribution is a distinctive design choice reflecting GIOPS's dual mission — providing global ocean state forecasts while also offering high-resolution Arctic-focused output for Canadian operational stakeholders.

---

## Basic details
- **Model type:** Global deterministic ocean physics forecast, two-way coupled to atmosphere via GDPS, with online sea ice and data assimilation
- **Core ocean model:** NEMO v3.6 (Madec et al., 2008; Madec and NEMO team, 2008)
- **Sea ice model:** CICE v6.2.0 (Hunke et al., 2021) — Community Ice CodE, updated from CICE v4 in the v3.5.0 upgrade
- **System name:** GIOPS (Global Ice-Ocean Prediction System)
- **Horizontal resolution:** 1/4° (~25 km at mid-latitudes) on the ORCA025 tripolar grid
- **Forecast length:** 10 days, daily updates
- **Cycle time:** 00 UTC analysis, with daily forecast runs
- **Output frequencies:** Time-mean fields (daily means as the standard product)
- **Atmospheric coupling:** Two-way with GDPS (since GIOPS v2.3, November 2017)
- **Bathymetry:** ETOPO-derived bathymetry (NEMO/ORCA025 standard configuration)

---

## Forcing

GIOPS is forced by ECCC's own operational atmospheric model:

- **Atmospheric forcing source:** GDPS v9.0.0 (Global Deterministic Prediction System)
- **Forcing variables:** Temperature, winds, radiative fluxes, humidity
- **Forcing mode:** Atmospheric fields are taken from the **delayed-mode (G2) GDPS analysis** rather than from forecast fields — this is a methodological choice introduced in GIOPS v3.3.0 (December 2021) that gives the ocean model the best-quality atmospheric state for spin-up
- **River runoff:** Climatological river runoff (NEMO/ORCA025 standard configuration)
- **Tidal forcing:** Not represented in the operational global forecast (handled separately in regional Canadian systems like RIOPS)

### Two-way coupling with GDPS
A distinctive feature of GIOPS is that since v2.3 (November 2017), it is **two-way coupled** with GDPS, ECCC's operational global atmospheric model. This means:
- Sea surface temperature and sea ice fields from GIOPS feed into GDPS atmospheric forecasts
- Atmospheric fluxes from GDPS feed back into GIOPS ocean dynamics
- The coupling improves both atmospheric forecast skill (particularly for marine areas and the Arctic) and ocean forecast skill

This is operationally rare: most global ocean forecasting systems use one-way atmospheric forcing rather than two-way coupling. The two-way design reflects ECCC's integrated approach to environmental prediction.

---

## Coupling

GIOPS is **coupled to two components**:

### Sea ice (CICE v6.2.0)
- **Coupling mode:** Online, every model timestep
- **Sea ice physics highlights (v3.5.0 onward):**
  - **Radiation scheme:** Delta-Eddington multiple scattering parameterization (Briegleb and Light, 2007) — represents solar radiation interactions with ice, snow, and melt ponds with greater physical realism than older schemes (parameters: R_ice=2.0, R_pnd=2.0, R_snw=2.0)
  - **Conductivity scheme:** "Bubbly" thermal conductivity (Pringle et al., 2007) — represents thermal conductivity of brine-bubble-containing sea ice
  - **Dynamics:** Elastic-Viscous-Plastic (EVP) rheology (Hunke, 2001)
  - **Ice strength parameters:** P*=22.5 kN/m², C*=15
  - **Air-ice roughness:** iceruf = 0.54 mm

### Atmosphere (GDPS, two-way)
See "Two-way coupling with GDPS" above. This is the operationally distinctive feature that separates GIOPS from most other global ocean forecasting systems.

GIOPS is **not directly coupled** to wave models in operations. Wave coupling is handled in ECCC's regional systems (RIOPS, GDWPS) rather than the global ocean forecast.

---

## Data assimilation

GIOPS uses **SAM2 (Système d'Assimilation Mercator)**, a reduced-order Kalman filter algorithm originally developed by Mercator Ocean and adapted for ECCC operations. This makes GIOPS a methodological cousin to Mercator's [GLO12](../../france/glo12.md), which uses the same SAM2 family of algorithms.

### DA scheme details
- **Algorithm:** SAM2 with SEEK (Singular Evolutive Extended Kalman) filter formulation (Pham et al., 1998)
- **Mean Dynamic Topography:** Updated MDT field as of v3.5.0 (June 2024)
- **Background-error covariances:** Modelled 3D anomalies derived from a multi-year hindcast simulation (Lellouche et al., 2013)
- **Analysis increments:** Incremental Analysis Update (IAU) method applied over one day
- **Analysis cycle:** Two weekly analyses (delayed-mode and real-time), with an analysis valid on Wednesday and daily analysis updates at 00 UTC
- **In-situ quality control:** DFOQC quality control system (added in v3.4.1, November 2023)

### Assimilated observations

**Direct scalars (DS) — Sea surface temperature:**
- CCMEP gridded SST analysis at 0.1° resolution, which itself synthesizes:
  - AVHRR from MetOp-B and MetOp-C
  - VIIRS from Suomi NPP and NOAA-20
  - AMSR2 from GCOM-W1
  - In-situ SST from buoys, drifters, and ships

**Indirect scalars (IS) — Sea level anomaly from altimetry (six altimeters):**
- SARAL/AltiKa
- CryoSat-2N
- Jason-3N
- Sentinel-3A
- Sentinel-3B
- Sentinel-6A-HR

**Vertical profiles (VP) — Temperature and salinity:**
- Argo floats
- Drifters and bathymeters
- Gliders
- Ships of opportunity
- Buoys and moorings
- Sources: profiles obtained primarily from CMEMS data streams, with quality control via DFOQC

The use of CMEMS-distributed in-situ profiles is operationally interesting — GIOPS depends on the same Copernicus Marine in-situ TAC data streams that feed Mercator's own GLO12.

---

## What it provides

GIOPS distributes **time-mean ocean and sea ice forecast fields** on two distinct grids designed for different user communities.

### Ocean fields
- 3D potential temperature
- 3D salinity
- 3D ocean currents (eastward, northward, vertical)
- Sea surface height
- Mixed layer depth

### Sea ice fields
- Sea ice concentration
- Sea ice thickness
- Sea ice velocity
- Snow thickness on sea ice
- Sea ice surface temperature

### Distributed grids
- **Global grid:** 0.2° regular latitude-longitude, global coverage north of 80°S
- **Arctic grid:** North-polar stereographic projection at 5 km spacing (standard parallel 60°N), covering the Arctic Ocean and sub-polar seas

The Arctic grid is a meaningful product distinction — at high latitudes, regular lat-lon grids suffer severe distortion (cells become very narrow longitudinally near the pole), making polar stereographic the natural choice for Arctic users. Few other operational global ocean systems provide a dedicated polar projection product alongside the global grid.

### File format
- **Format:** NetCDF following the Climate and Forecast (CF) Conventions
- **Vertical levels:** Output available at 50 depths

---

## Data availability

- **Is the data free?** Yes (no registration required for MSC Open Data)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-compliant)
- **System name:** GIOPS (Global Ice-Ocean Prediction System)
- **Distribution channels:**
  - **MSC Datamart:** Direct file access — primary download channel
  - **MSC GeoMet:** Geospatial web services (WMS, WCS, OGC API) — for users wanting map services rather than raw files
  - **MSC AniMet:** Visualization service
- **Official access:** https://eccc-msc.github.io/open-data/msc-data/nwp_giops/readme_giops_en/
- **Datamart access:** https://eccc-msc.github.io/open-data/msc-data/nwp_giops/readme_giops-datamart_en/
- **Open Government metadata:** https://open.canada.ca/data/en/dataset/dc3a7022-95e8-45a7-bf63-3d45b6cda0dc
- **Licence:** ECCC Data Servers End-User Licence (open access)

ECCC's open data approach is genuinely open — no registration required, no API key, direct HTTP access to NetCDF files via the Datamart. This is the most permissive distribution model among the three major operational global ocean forecasts (GLO12 requires Copernicus Marine registration; RTOFS is similarly open via NOMADS but with less curated metadata).

---

## Version history

### April 14, 2026 — GIOPS v3.6.0 (current)
- **Migration to new High Performance Computing infrastructure** at ECCC
- Computational/infrastructure upgrade only — no scientific changes to model formulation, DA, or outputs
- Part of broader ECCC operational migration affecting multiple operational systems

### June 11, 2024 — GIOPS v3.5.0
- **Sea ice model upgraded from CICE v4 to CICE v6.2.0** (Hunke et al., 2021)
- Delta-Eddington radiation scheme introduced for sea ice (Briegleb and Light, 2007)
- "Bubbly" thermal conductivity scheme for sea ice (Pringle et al., 2007)
- New mean dynamic topography field
- Activation of new altimetry datasets (Jason-3N and Sentinel-6A-HR added)

### November 28, 2023 — GIOPS v3.4.1
- Added DFOQC quality control component for in-situ observations used in data assimilation

### June 28, 2022 — GIOPS v3.4.0
- Migration to new High Performance Computing infrastructure (computational only)

### December 1, 2021 — GIOPS v3.3.0
- Inclusion of diurnal cycle in atmospheric forcing
- Ice strength parameter updated
- Ice roughness adjusted to fit GDPS (atmospheric model) value
- Atmospheric forcing changed from forecast to assimilation run (delayed-mode G2)
- New monitoring system

### January 21, 2020 — GIOPS v3.2.1
- Migration to new High Performance Computing infrastructure (computational only)

### July 3, 2019 — GIOPS v3.0.0
- Major DA upgrade: Updated to SAM2 (Système d'Assimilation Mercator)
- New SST and sea-ice analyses at 0.1° resolution (improved from 0.2° resolution)

### November 1, 2017 — GIOPS v2.3
- **Two-way coupling with GDPS introduced** — major operational upgrade enabling bidirectional atmosphere-ocean exchange

### June 21, 2016 — GIOPS v2.1
- New assimilation source code based on Mercator-Océan's 2015 SAM
- Introduction of 4D Incremental Analysis Update (4D-IAU) approach
- Improved Mean Dynamic Topography
- Use of bogus observations under sea ice
- Refinements in CMC SST and sea ice analyses

### August 20, 2015 — GIOPS v1.1.1 (initial operational implementation)
- ECCC declared GIOPS operational

---

## Relationship to other ocean products

### Peer global ocean physics systems
- **[GLO12 (Copernicus Marine Global Ocean Physics Analysis and Forecast)](../../france/glo12.md)** — Mercator Ocean's operational global ocean forecast. Methodological cousin to GIOPS (both use SAM2-family DA), but at higher resolution (1/12° vs 1/4°), with LIM3 sea ice instead of CICE6, and without two-way atmospheric coupling.
- **[Global RTOFS](../../us/rtofs-global.md)** — NOAA's operational global ocean physics forecast. Different model core (HYCOM vs NEMO), different DA approach (3DVar vs SAM2), but at higher resolution (1/12° vs GIOPS 1/4°). Similar 8–10 day forecast horizons.
- **Navy Global HYCOM (FNMOC)** — U.S. Navy operational HYCOM run.
- **BLUElink OceanMAPS** — Australian Bureau of Meteorology operational global ocean forecast (~1/10° global).

GIOPS occupies a distinctive position among these peers: lower resolution but stronger sea ice physics and the only system with operational two-way atmospheric coupling.

### Companion ECCC systems
- **GDPS (Global Deterministic Prediction System):** ECCC's operational global atmospheric NWP model, two-way coupled to GIOPS. The two systems share atmospheric and oceanic state at every coupling cycle.
- **RIOPS (Regional Ice-Ocean Prediction System):** ECCC's regional ocean physics forecast covering Canadian waters at higher resolution. RIOPS uses tidal harmonic analysis online and provides higher resolution for Canadian coastal applications. RIOPS is GIOPS's natural Canadian regional companion (Smith et al., 2021).
- **GDWPS (Global Deterministic Wave Prediction System):** ECCC's operational global wave forecast, fed by GDPS atmospheric forcing.
- **GEPS (Global Ensemble Prediction System):** ECCC's atmospheric ensemble forecast.

### Methodological lineage
GIOPS shares its core data assimilation algorithm (SAM2) with Mercator Ocean's GLO12. This reflects the long-standing operational collaboration between ECCC and Mercator Ocean — GIOPS adapted Mercator's SAM system for Canadian operations starting in v2.1 (2016) and has tracked the algorithm's evolution since.

### AI-based counterparts
ECCC's documentation explicitly notes that the new April 2026 HPC infrastructure will "facilitate further technology transfers from research and development to operations, such as the implementation of innovative approaches based on artificial intelligence." This signals that AI-based ocean forecasting is on ECCC's roadmap, though no specific operational AI ocean system has been announced as of GIOPS v3.6.0.

---

## Notes
- The 1/4° resolution is coarser than GLO12 and RTOFS, but the trade-off is meaningful: GIOPS dedicates more compute budget to its strong sea ice physics (CICE v6.2.0 with the Delta-Eddington radiation scheme is among the most advanced sea ice physics in any operational global ocean system) and to its two-way atmospheric coupling. For applications dominated by mesoscale ocean eddies, users may prefer the higher-resolution GLO12 or RTOFS; for high-latitude and ice-influenced applications, GIOPS's design choices are strengths.
- The two-way coupling with GDPS (since 2017) is genuinely operationally distinctive. Most global ocean forecasting systems use one-way atmospheric forcing — the ocean model receives winds, fluxes, and pressure but does not feed back to update the atmosphere. The two-way design means GIOPS and GDPS are operationally inseparable: improvements in one system are reflected in the other.
- The dual-grid distribution (global 0.2° lat-lon plus Arctic 5 km polar stereographic) is unusual and reflects ECCC's operational mission. Few operational ocean systems offer a dedicated polar projection product; GIOPS does because Canadian operational interests in the Arctic are central to its purpose.
- The methodological link to Mercator (SAM2 DA) makes GIOPS something of a "Canadian cousin" to GLO12. The two systems make different design trade-offs (GIOPS lower resolution but with two-way atmospheric coupling and advanced sea ice; GLO12 higher resolution but without atmospheric coupling) but share core DA infrastructure.
- The Wednesday delayed-mode analysis cycle is a notable scheduling feature: GIOPS produces both a real-time daily analysis and a delayed-mode weekly analysis valid on Wednesday. Users seeking the most accurate analysis for retrospective work should use the delayed-mode product.
- The "G2" (delayed-mode) GDPS analysis input choice (since v3.3.0, 2021) is a quality optimization: the delayed-mode atmospheric analysis has had time to incorporate observations that arrived after the real-time GDPS cycle, giving GIOPS a higher-quality atmospheric state to spin up against.
- Open data licensing is genuinely open — no registration required, direct file access via Datamart. This contrasts with GLO12 (Copernicus Marine registration required) but matches RTOFS (NOMADS open access).

---

## Official documentation
- GIOPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_giops/readme_giops_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_GIOPS_e.pdf
- v3.5.0 Technical Note: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_giops-350_e.pdf
- v3.5.0 Factsheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_giops-350_e.pdf
- GIOPS Changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_giops/changelog_giops_en/
- ECCC operational systems changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- MSC Datamart access: https://eccc-msc.github.io/open-data/msc-data/nwp_giops/readme_giops-datamart_en/
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_GIOPS_en.svg

### Key references
- Smith, G.C., Roy, F., Reszka, M., Surcel Colan, D., He, Z., Deacu, D., Belanger, J.M., Skachko, S., Liu, Y., Dupont, F., Lemieux, J.-F. (2016). Sea ice forecast verification in the Canadian global ice ocean prediction system. *Q. J. Roy. Meteor. Soc.*, 142(695), 659–671. https://doi.org/10.1002/qj.2555
- Smith, G.C., Bélanger, J.M., Roy, F., Pellerin, P., Ritchie, H., Onu, K., Roch, M., Zadra, A., Surcel Colan, D., Winter, B., Fontecilla, J.S. (2018). Impact of coupling with an ice-ocean model on global medium-range NWP forecast skill. *Mon. Wea. Rev.*, 146, 1157–1180. https://doi.org/10.1175/MWR-D-17-0157.1
- Smith, G.C., Liu, Y., Benkiran, M., Chikhar, K., Surcel Colan, D., Gauthier, A.A., Testut, C.E., Dupont, F., Lei, J., Roy, F., Lemieux, J.-F. (2021). The Regional Ice Ocean Prediction System v2: a pan-Canadian ocean analysis system using an online tidal harmonic analysis. *Geoscientific Model Development*, 14(3), 1445–1467.
- Pham, D.T., Verron, J., Roubaud, M.C. (1998). A singular evolutive extended Kalman filter for data assimilation in oceanography. *J. Mar. Syst.*, 16, 323–340.
- Madec, G. and the NEMO team (2008). NEMO ocean engine. *Note du Pôle de modélisation*, Institut Pierre-Simon Laplace.
- Hunke, E.C., Allard, R., et al. (2021). CICE-Consortium/CICE: CICE Version 6.2.0. Zenodo. https://doi.org/10.5281/zenodo.4671172
- Briegleb, B.P., Light, B. (2007). A Delta-Eddington multiple scattering parameterization for solar radiation in the sea ice component of the Community Climate System Model. NCAR Technical Note NCAR/TN-472+STR.
- Pringle, D.J., Eicken, H., Trodahl, H.J., Backstrom, L.G.E. (2007). Thermal conductivity of landfast Antarctic and Arctic sea ice. *J. Geophys. Res. Oceans*. https://doi.org/10.1029/2006JC003641
- Lellouche, J.M., Le Galloudec, O., Drévillon, M., et al. (2013). Evaluation of global monitoring and forecasting systems at Mercator Océan. *Ocean Science*, 9(1), 57.
