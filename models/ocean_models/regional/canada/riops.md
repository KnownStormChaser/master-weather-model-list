# RIOPS (Regional Ice-Ocean Prediction System)

## What this model is
The **Regional Ice-Ocean Prediction System (RIOPS)** is the operational regional ocean and sea ice forecast system run by **Environment and Climate Change Canada (ECCC)** at the **Canadian Centre for Meteorological and Environmental Prediction (CCMEP)**. RIOPS provides 84-hour ocean and sea ice forecasts four times daily at 1/12° resolution (3–8 km) covering the three Canadian oceans — parts of the North Atlantic, the Arctic, and parts of the North Pacific.

RIOPS is the regional companion to ECCC's [GIOPS (Global Ice-Ocean Prediction System)](./giops.md), providing higher-resolution forecasts within Canadian waters than GIOPS's global 1/4° grid can support. The two systems are operationally linked: RIOPS is **initialized using GIOPS analyses**, and uses GDPS atmospheric forcing — making the GIOPS/RIOPS/GDPS triplet a tightly integrated operational prediction suite at ECCC.

A distinctive feature of RIOPS is its **integrated ice analysis component (RIPS)**, which produces 5-km regional sea ice concentration analyses by assimilating SAR data from Canada's **RADARSAT Constellation Mission (RCM)** alongside passive microwave and scatterometer observations. This SAR-based ice analysis capability is operationally rare and reflects Canada's historical investment in spaceborne SAR for sea ice monitoring.

The current operational version is **RIOPS v2.5.0**, implemented April 14, 2026 alongside ECCC's migration to a new High Performance Computing infrastructure. The v2.5.0 upgrade is computational/infrastructure-only — the scientific formulation, data assimilation, model components, and outputs are unchanged from **v2.4.0** (June 11, 2024), which introduced significant scientific upgrades including CICE6 sea ice, the Delta-Eddington radiation scheme, GDPS atmospheric forcing, updated Mean Dynamic Topography, and assimilation of RCM-SAR ice data.

---

## Who runs it
- **Operating organization:** Environment and Climate Change Canada (ECCC)
- **Production unit:** Canadian Centre for Meteorological and Environmental Prediction (CCMEP), part of the Meteorological Service of Canada (MSC)
- **Country:** Canada
- **Programme:** MSC Open Data — operational forecast services for Canadians, government agencies, and partners
- **Operational since:** July 3, 2019 (RIOPS v2.0.0 — when the system transitioned from experimental to operational status)

---

## What area it covers
- **Coverage:** Pan-Canadian — covers the three Canadian oceans
  - **North Atlantic:** From 25.6°N northward
  - **Arctic Ocean:** Full Arctic basin including the Canadian Arctic Archipelago
  - **North Pacific:** From 43.8°N northward
- **Native grid:** ORCA12-derived regional configuration
- **Grid dimensions:** 1580 × 2198
- **Vertical levels:** 75 z-levels

The Canadian Arctic Archipelago — the labyrinth of straits, channels, and islands between the Canadian mainland and Arctic Ocean — is operationally important coverage that few global ocean systems represent at adequate resolution.

---

## Basic details
- **Model type:** Regional deterministic ocean physics forecast, with online sea ice and data assimilation
- **Core ocean model:** NEMO v3.6 / OPA (Madec et al., 1998; Madec and NEMO team, 2008)
- **Sea ice model:** CICE v6.2.0 (Hunke et al., 2021) — Community Ice CodE, updated from CICE v4 in v2.4.0
- **System name:** RIOPS (Regional Ice-Ocean Prediction System)
- **Horizontal resolution:** Variable from 8 km in the North Atlantic to 2 km in the Canadian Arctic Archipelago — nominally 1/12°
- **Numerical technique:** Primitive equations with finite differences on Arakawa C-grid
- **Time integration:** Explicit leapfrog with non-linear free surface (time-splitting of barotropic and baroclinic time-stepping); baroclinic time-step 300 seconds
- **Forecast length:** 84 hours (3.5 days)
- **Update frequency:** Four times daily at 00, 06, 12, 18 UTC
- **Cutoff time:** 4 hours (the system runs at T+4 to ensure observation availability)
- **Vertical mixing:** k-ε turbulent mixing scheme (Umlauf and Burchard, 2003)
- **Bathymetry:** ETOPO1-derived with smoothing to accommodate high tides

The variable resolution — 8 km in deeper Atlantic waters narrowing to 2 km in the Canadian Arctic Archipelago — is achieved through the ORCA12 grid's natural meridional convergence at high latitudes. This puts the highest spatial resolution exactly where it is most operationally needed for Canadian ice-and-narrow-strait forecasting.

---

## Forcing

RIOPS is forced by ECCC's own operational atmospheric and global ocean systems:

- **Atmospheric forcing:** GDPS-G0 v9.0.0 (the high-resolution component of the Global Deterministic Prediction System) at ~10 km
  - Forcing variables: winds, surface pressure, radiative fluxes, humidity
  - Surface flux scheme: GEM bulk formulae for turbulent sensible heat, latent heat, and momentum
  - **Switched to GDPS forecast** in v2.4.0 (previously used different atmospheric forcing source)
- **Lateral oceanic boundary conditions:** GDPS-G1 v9.0.0 (the larger-domain GDPS component used to provide oceanic OBC at the regional domain edges)
- **St. Lawrence River input:** Real-time data from the **CanHys database** (switched from IML feed in v2.4.0). The St. Lawrence is operationally important because it brings significant freshwater into the Gulf of St. Lawrence and shelf system, affecting salinity, currents, and stratification.
- **Initial conditions:**
  - **00 UTC cycle:** From the RIOPS data assimilation analysis (using GIOPS analyses as input — see DA section)
  - **06, 12, 18 UTC cycles:** From the previous forecast restart at lead time 6 hours
  - **Sea ice initial state:** From the regional RIPS_early ice analysis (with 4-hour cutoff, including RCM-SAR data)

### Tidal forcing
RIOPS includes tides via online tidal harmonic analysis (Smith et al., 2021). This is a distinctive feature — most regional ocean systems either include tides explicitly through forced tidal boundary conditions or omit them entirely. RIOPS's online harmonic analysis approach handles tides as a real-time component of the forecast.

---

## Coupling

RIOPS is **coupled to sea ice (CICE v6.2.0)** with updated ice physics introduced in v2.4.0:

### Sea ice physics highlights (v2.4.0 onward)
- **Coupling mode:** Online, every model timestep
- **Radiation scheme:** Delta-Eddington multiple scattering parameterization (Briegleb and Light, 2007) with parameters R_ice=0.0, R_pnd=0.0, R_snw=1.0
- **Conductivity scheme:** "Bubbly" thermal conductivity (Pringle et al., 2007)
- **Ice strength parameters:** P*=22.5 kN/m², C*=15
- **Ice-ocean roughness:** iceruf_ocn = 1.8 cm
- **Air-ice roughness:** iceruf = 0.54 mm

### Atmospheric coupling
RIOPS uses one-way GDPS forcing rather than two-way atmosphere-ocean coupling — this is a difference from GIOPS, which is two-way coupled to GDPS. The regional coupling design uses GDPS as the upstream system, with RIOPS receiving but not feeding back into the atmospheric model in the same forecast cycle.

---

## Data assimilation

RIOPS uses **SAM2 (Système d'Assimilation Mercator)** for its ocean assimilation — the same algorithm family used by [GIOPS](./giops.md) and [GLO12](../../france/glo12.md). The RIOPS assimilation system runs in two modes (delayed and real-time) and is closely integrated with GIOPS.

### DA scheme details
- **Algorithm:** SAM2 with SEEK (Singular Evolutive Extended Kalman) filter formulation (Pham et al., 1998)
- **Mean Dynamic Topography:** Updated MDT field as of v2.4.0 (June 2024)
- **Background-error covariances:** Modelled 3D anomalies derived from a multi-year hindcast simulation
- **Analysis increments:** IAU method applied over the analysis period — 7 days for the weekly cycle, 1 day for the daily cycle
- **Analysis increment grid:** Reduced grid (one point every 4 in each direction)
- **Trial fields:** 168-hour forecast (RR and RD modes) and 24-hour forecast (RU mode)
- **3D-Var bias correction:** Temperature and salinity bias correction scheme runs alongside SAM2

### Frequency
- **Two weekly analyses** (delayed-mode and real-time) producing an analysis valid on Wednesday
- **Daily analysis update at 00 UTC**

### Assimilated observations

**Direct scalars (DS) — Sea surface temperature:**
- CCMEP gridded SST analysis at 0.1° resolution (the same product used by GIOPS), synthesizing:
  - AVHRR from MetOp-A, MetOp-B, MetOp-C
  - VIIRS from Suomi NPP and NOAA-20
  - AMSR2 from GCOM-W1
  - In-situ SST from buoys, drifters, and ships

**Indirect scalars (IS) — Sea level anomaly from altimetry (six altimeters, all activated in v2.4.0):**
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
- (Quality-controlled via the DFOQC system shared with GIOPS)

---

## RIPS — Regional Ice Analysis component

RIOPS uniquely includes an integrated **Regional Ice Prediction System (RIPS)** ice analysis component that runs alongside the ocean assimilation. RIPS produces operational sea ice concentration analyses at 5 km resolution and is one of RIOPS's most distinctive features.

### RIPS configuration
- **Algorithm:** 2DVar using ECCC's MIDAS data assimilation infrastructure
- **Variables:** Ice concentration plus ice concentration analysis error
- **Domain:** ORCA12 grid (corrected around the North Pole)
- **Resolution:** 0.045° (~5 km)
- **Assimilation window:** 6 hours, centered on the analysis time

### Assimilated ice observations
- **SAR from RADARSAT Constellation Mission (RCM)** — three-satellite SAR mission, providing high-resolution ice/water discrimination (Komarov et al., 2020). **This was added in v2.4.0** and represents Canada's distinctive operational use of SAR for sea ice analysis.
- CIS daily ice charts (Canadian Ice Service)
- CIS image analysis charts and lake bulletins
- Passive microwave: SSM/IS from DMSP-17 and DMSP-18
- Scatterometer: ASCAT from MetOp-B and MetOp-C
- Microwave: AMSR2

### RIPS analysis cycles
- **RIPS_late:** 4 analyses daily (00, 06, 12, 18 UTC), 7-hour cutoff — full data availability
- **RIPS_early:** 4 analyses daily (00, 06, 12, 18 UTC), 4-hour cutoff — uses RIPS_late as background; this is the analysis used to initialize RIOPS forecasts

The dual-cycle design (late for full quality, early for operational forecast initialization) is operationally elegant — it gets the best possible analysis into the next forecast cycle without sacrificing data quality for the archived analysis.

---

## What it provides

RIOPS distributes ocean and sea ice forecast fields at 1/12° resolution covering the Canadian three-ocean domain.

### Ocean fields (3D)
- Potential temperature
- Salinity
- Horizontal currents (eastward, northward)
- Turbulent kinetic energy (TKE)
- Sea surface height (2D)
- Vertical velocity (derived)

### Sea ice fields
- Sea ice concentration
- Sea ice thickness
- Sea ice velocity
- Sea ice surface temperature
- Snow thickness on sea ice

### File format
- **Format:** NetCDF following the Climate and Forecast (CF) Conventions
- **Vertical resolution:** Output at multiple of the 75 z-levels

---

## Data availability
- **Is the data free?** Yes (no registration required for MSC Open Data)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-compliant)
- **System name:** RIOPS (Regional Ice-Ocean Prediction System)
- **Distribution channels:**
  - **MSC Datamart:** Direct file access — primary download channel
  - **MSC GeoMet:** Geospatial web services (WMS, WCS, OGC API)
- **Official access:** https://eccc-msc.github.io/open-data/msc-data/nwp_riops/readme_riops_en/
- **Datamart access:** https://eccc-msc.github.io/open-data/msc-data/nwp_riops/readme_riops-datamart_en/
- **Open Government metadata:** https://open.canada.ca/data/en/dataset/66caa8cc-0e9c-4fdb-ae40-fab9c255b811
- **Viewing service:** Available via WMS at https://www.meteocentre.com/plus (under CMC experimental tab)
- **Licence:** ECCC Data Servers End-User Licence (open access)

---

## Version history

### April 14, 2026 — RIOPS v2.5.0 (current)
- **Migration to new High Performance Computing infrastructure** at ECCC
- Computational/infrastructure upgrade only — no scientific changes to model formulation, DA, or outputs
- Part of broader ECCC operational migration affecting multiple operational systems

### June 11, 2024 — RIOPS v2.4.0
- **Sea ice model upgraded from CICE v4 to CICE v6.2.0**
- Delta-Eddington radiation scheme introduced for sea ice
- "Bubbly" thermal conductivity scheme for sea ice
- New Mean Dynamic Topography reference field
- Activation of three new altimetry datasets (Jason-3N, Sentinel-6A-HR, additions activated)
- **Switched atmospheric forcing to GDPS-G0** v9.0.0
- **Switched St. Lawrence River input to CanHys database** (from IML feed)
- **Added RCM-SAR (RADARSAT Constellation Mission)** ice data to RIPS analysis

### November 28, 2023 — RIOPS v2.3.1
- DFO-QC quality control component for in-situ observations (shared with GIOPS v3.4.1)
- No model or DA changes; only the in-situ data quality pipeline updated

### June 28, 2022 — RIOPS v2.3.0
- Migration to new High Performance Computing infrastructure (computational only)

### December 1, 2021 — RIOPS v2.2.0
- New schema for vertical blending
- 3D-Var bias correction scheme for temperature and salinity profiles
- Reduction of turbulent kinetic energy injection due to wave breaking (Rascle et al., 2008)
- Increased ice roughness
- New diurnal cycle handling
- New monitoring package

### January 21, 2020 — RIOPS v2.1.0
- Migration to new High Performance Computing infrastructure (computational only)

### July 3, 2019 — RIOPS v2.0.0 (declared operational)
- **System transitioned from experimental to operational status**
- Added independent oceanic data assimilation component (replacing previous spectral nudging)
- **Domain extended to North Pacific** (previously did not include Pacific coverage)

### June 21, 2016 — RIOPS v1.1 (experimental)
- Initial implementation as experimental system
- Added 3D oceanic component (NEMO-CICE) with tides
- Spectral nudging of GIOPS analyses during pseudo-analysis
- Forecast initialization from pseudo-analysis with ice concentration insertion
- Added AVHRR data to ice concentration analysis

---

## Relationship to other ocean products

### Companion ECCC systems (operational triplet)
- **[GIOPS (Global Ice-Ocean Prediction System)](./giops.md):** RIOPS's global parent system. RIOPS uses GIOPS analyses as initialization input. Both systems share the same SAM2 DA algorithm and CICE6 sea ice model — RIOPS is essentially "GIOPS with regional resolution refinement." Operational since 2015 (GIOPS) and 2019 (RIOPS as operational).
- **GDPS (Global Deterministic Prediction System):** ECCC's operational global atmospheric NWP model. RIOPS receives GDPS-G0 and GDPS-G1 as atmospheric forcing and lateral oceanic boundary conditions. GDPS itself is two-way coupled with GIOPS — meaning information flows from RIOPS upstream through GIOPS into GDPS, then back into RIOPS in the next cycle.

### Peer regional ocean systems globally

RIOPS is one of relatively few **basin-scale regional ocean physics forecasts** operating internationally. Most "regional" ocean systems are smaller-domain coastal or estuarine models. RIOPS-equivalent peer systems include:

- **Regional NCOM (US Navy / NOAA NOMADS distribution):** US Navy Naval Oceanographic Office (NAVOCEANO) operates regional ocean physics systems at 1/36° (~3 km) for several US-relevant regions — US East Coast, Southern California, Hawaii, Gulf of America/Caribbean. Uses NCOM (Navy Coastal Ocean Model) rather than NEMO. Operationally distributed by NOAA NOMADS but not run by NOAA. Not currently in this repository.
- **Copernicus Marine regional ocean physics products:** Six regional Copernicus Marine ocean physics systems (NWS Shelf, IBI, Mediterranean, Black Sea, Baltic, Arctic) — each operated by a different European institute, providing regional-resolution physics within their domains. These are companion products to the corresponding wave models already documented in this repository.

### What the US does *not* have
NOAA itself does not operate a basin-scale regional ocean physics forecast system equivalent to RIOPS. NOAA's strategy uses Global RTOFS at 1/12° for regional applications and relies on the National Ocean Service's smaller-domain Operational Forecast Systems (OFS) — port, harbor, and estuary-scale ROMS/FVCOM models — for coastal applications. The discontinued **RTOFS-Atlantic** (retired 2017) was once a basin-scale regional system but no current NOAA equivalent exists.

### Methodological lineage
RIOPS, [GIOPS](./giops.md), and Mercator's [GLO12](../../france/glo12.md) all use the SAM2 DA algorithm originally developed by Mercator Ocean. This shared methodology reflects the long-standing operational collaboration between ECCC and Mercator. RIOPS's regional implementation tracks the same algorithm evolution as GIOPS.

### AI-based counterparts
ECCC's documentation explicitly notes that the new April 2026 HPC infrastructure will "facilitate further technology transfers from research and development to operations, such as the implementation of innovative approaches based on artificial intelligence." This suggests AI-based regional ocean forecasting may be on ECCC's roadmap, though no specific operational AI ocean system has been announced.

---

## Notes
- The 2-km resolution in the Canadian Arctic Archipelago is one of the highest-resolution operational ocean physics products available globally for the Canadian Arctic. Most other operational systems (including GIOPS itself) cannot resolve the narrow channels and complex coastline of the Archipelago at this detail. This is the resolution where RIOPS provides genuinely unique operational capability.
- The integrated RIPS ice analysis with RCM-SAR assimilation is operationally distinctive. Few operational sea ice analysis systems use SAR data — most rely primarily on passive microwave (which has lower resolution) or ice charts (which have lower update frequency). The combination of SAR + microwave + chart-based observations gives RIPS particularly strong performance in the marginal ice zone and complex ice geometry of Canadian waters.
- The 84-hour (3.5-day) forecast length is shorter than many regional ocean systems offer (Copernicus regional products typically provide 7–10 days). This trade-off prioritizes ensemble of high-quality cycles (4 per day) over forecast length — appropriate for RIOPS's operational focus on shipping, marine safety, and ice navigation in Canadian waters where short-range high-quality guidance is more valuable than extended forecasts.
- The St. Lawrence River input via CanHys database is operationally important — the St. Lawrence is one of the world's largest river systems and brings significant freshwater into the Gulf of St. Lawrence, affecting salinity stratification, current patterns, and ice formation in winter. The 2024 switch to CanHys reflects ECCC's modernization of its real-time hydrology data infrastructure.
- The two-cycle RIPS analysis design (late for quality, early for operational use) is methodologically elegant — it ensures the highest-quality ice analysis is preserved for archival use while still feeding the most timely available analysis into the next forecast cycle.
- The 4-hour cutoff for forecast cycles is short, reflecting the operational priority on rapid update cycles. The cutoff matches RIPS_early's 4-hour cutoff, ensuring ice initial conditions are aligned with ocean initial conditions.
- Open data licensing is genuinely open — no registration required, direct file access via Datamart. Same as GIOPS.

---

## Official documentation
- RIOPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_riops/readme_riops_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_RIOPS_e.pdf
- v2.4.0 Technical Note: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_notes/technote_riops_e.pdf
- v2.4.0 Factsheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_riops-240_e.pdf
- RIOPS Changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_riops/changelog_riops_en/
- ECCC operational systems changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- MSC Datamart access: https://eccc-msc.github.io/open-data/msc-data/nwp_riops/readme_riops-datamart_en/
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_RIOPS_en.svg

### Key references
- Smith, G.C., Liu, Y., Benkiran, M., Chikhar, K., Surcel Colan, D., Gauthier, A.A., Testut, C.E., Dupont, F., Lei, J., Roy, F., Lemieux, J.-F. (2021). The Regional Ice Ocean Prediction System v2: a pan-Canadian ocean analysis system using an online tidal harmonic analysis. *Geoscientific Model Development*, 14(3), 1445–1467.
- Komarov, A.S., Caya, A., Buehner, M., Pogson, L. (2020). Assimilation of SAR Ice and Open Water Retrievals in Environment and Climate Change Canada Regional Ice-Ocean Prediction System. *IEEE Transactions on Geoscience and Remote Sensing*, 58(6), 4290–4303. https://doi.org/10.1109/TGRS.2019.2962656
- Pham, D.T., Verron, J., Roubaud, M.C. (1998). A singular evolutive extended Kalman filter for data assimilation in oceanography. *J. Mar. Syst.*, 16, 323–340.
- Madec, G. and the NEMO team (2008). NEMO ocean engine. *Note du Pôle de modélisation*, Institut Pierre-Simon Laplace.
- Madec, G., Delecluse, P., Imbard, M., Lévy, C. (1998). OPA 8.1 Ocean General Circulation Model reference manual. Note du Pôle de Modélisation, Institut Pierre-Simon Laplace, 11.
- Hunke, E.C., Allard, R., et al. (2021). CICE-Consortium/CICE: CICE Version 6.2.0. Zenodo. https://doi.org/10.5281/zenodo.4671172
- Hunke, E.C. (2001). Viscous-plastic sea ice dynamics with the EVP model: linearization issues. *J. Comput. Phys.*, 170, 18–38.
- Lipscomb, W.H., Hunke, E.C., Maslowski, W., Jakacki, J. (2007). Ridging, strength, and stability in high-resolution sea ice models. *J. Geophys. Res.*, 112, C03S91. https://doi.org/10.1029/2005JC003355
- Briegleb, B.P., Light, B. (2007). A Delta-Eddington multiple scattering parameterization for solar radiation in the sea ice component of the Community Climate System Model. NCAR Technical Note NCAR/TN-472+STR.
- Pringle, D.J., Eicken, H., Trodahl, H.J., Backstrom, L.G.E. (2007). Thermal conductivity of landfast Antarctic and Arctic sea ice. *J. Geophys. Res. Oceans*. https://doi.org/10.1029/2006JC003641
- Umlauf, L., Burchard, H. (2003). A generic length-scale equation for geophysical turbulence models. *J. Mar. Res.*, 61, 235–265.
- Rascle, N., Ardhuin, F., Queffeulou, P., Croizé-Fillon, D. (2008). A global wave parameter database for geophysical applications. Part 1: Wave–current–turbulence interaction parameters for the open ocean based on traditional parameterizations. *Ocean Modelling*, 25, 154–171. https://doi.org/10.1016/j.ocemod.2008.07.006
