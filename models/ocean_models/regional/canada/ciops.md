# CIOPS (Coastal Ice-Ocean Prediction System)

## What this model is
The **Coastal Ice-Ocean Prediction System (CIOPS)** is the operational coastal ocean and sea ice forecast system run by **Environment and Climate Change Canada (ECCC)** at the **Canadian Centre for Meteorological and Environmental Prediction (CCMEP)**. CIOPS provides 48-hour ocean and sea ice forecasts four times daily at **1/36° resolution (~2–2.5 km)** over three coastal Canadian domains — **CIOPS-East** (North Atlantic / Gulf of St. Lawrence / Scotian Shelf), **CIOPS-West** (North Pacific coast of British Columbia and southern Alaska), and a nested **Salish Sea (SS500)** subdomain at 500 m resolution.

CIOPS is the coastal-resolution layer of ECCC's three-tier operational ocean prediction stack — sitting downstream of [GIOPS](./giops.md) (global, 1/4°) and [RIOPS](./riops.md) (regional, 1/12°) and resolving the narrow channels, fjords, estuaries, and shelf features that the coarser parents cannot. Each CIOPS domain runs in two coupled components: a **pseudo-analysis** that is forced at the ocean lateral boundaries by RIOPS forecasts and **spectrally nudged to the RIOPS solution in the deep ocean (>1500 m)**, and a 48-hour **forecast** initialized from the pseudo-analysis at 00 UTC (or from the previous cycle's 6-hour restart at 06, 12, 18 UTC).

A distinctive feature of CIOPS relative to [GIOPS](./giops.md) and [RIOPS](./riops.md) is that **it does not run its own ocean data assimilation** — the pseudo-analysis is essentially a downscaling of RIOPS, with assimilation skill inherited from the parent system. This makes CIOPS a "coastal dynamical downscaling" of RIOPS rather than an independent assimilation system, and the design choice keeps operational complexity manageable while delivering kilometric coastal resolution where it is operationally needed.

The current operational version of both CIOPS-East and CIOPS-West is **v2.4.0**, implemented April 14, 2026 alongside ECCC's migration to a new High Performance Computing infrastructure. The v2.4.0 upgrade is computational/infrastructure-only — the scientific formulation, model components, forcing, and outputs are unchanged from **v2.3.0** (June 11, 2024), which introduced significant scientific upgrades including CICE6 sea ice with the Delta-Eddington radiation scheme, updated St. Lawrence estuary mixing (East), and a 10 km uncoupled GDPS atmospheric forcing component (West).

---

## Who runs it
- **Operating organization:** Environment and Climate Change Canada (ECCC)
- **Production unit:** Canadian Centre for Meteorological and Environmental Prediction (CCMEP), part of the Meteorological Service of Canada (MSC)
- **Country:** Canada
- **Programme:** MSC Open Data — operational forecast services for Canadians, government agencies, and partners
- **Operational since:** December 1, 2021 (CIOPS v2.0.0 — when CIOPS-East and CIOPS-West both transitioned from experimental to operational status)

---

## What area it covers

CIOPS covers **three distinct coastal domains**, each with its own grid:

### CIOPS-East
- **Coverage:** North Atlantic coast of Canada — Gulf of St. Lawrence, Scotian Shelf, Gulf of Maine, southern Labrador Sea
- **Domain bounds:** 34.88°N to 54.46°N, coast to 323°E (37°W)
- **Native grid:** 1/36° regional configuration
- **Grid dimensions:** 945 × 1410
- **Vertical levels:** 100 z-levels with zstar (variable volume) coordinates
- **Minimum depth:** 7.5 m (to accommodate high tides in the Bay of Fundy and elsewhere)

### CIOPS-West (NEP — North East Pacific)
- **Coverage:** North Pacific coast of Canada — British Columbia coast and southern Alaska
- **Domain bounds:** 44.3°N to 59.6°N, coast to 142.3°W
- **Native grid:** 1/36° regional configuration
- **Grid dimensions:** 714 × 1020
- **Vertical levels:** 75 z-levels with zstar (variable volume) coordinates
- **Minimum depth:** 8 m

### CIOPS Salish Sea (SS500) — nested subdomain inside CIOPS-West
- **Coverage:** Strait of Georgia, Strait of Juan de Fuca, Puget Sound approaches
- **Domain bounds:** 46.9°N to 51.1°N, coast to 126.4°W
- **Native grid:** 500 m regular resolution
- **Grid dimensions:** 398 × 898
- **Vertical levels:** 40 z-levels with zstar coordinates
- **Minimum depth:** 4 m (to accommodate tides in shallow areas)
- **Sea ice:** Treated via a surface flux limiting scheme (no full CICE6 component, unlike CIOPS-East and CIOPS-West)

The Salish Sea 500 m nest is one of the highest-resolution operational ocean physics products available globally for any coastal region. The Strait of Georgia, with its complex bathymetry, strong tidal currents, and major freshwater input from the Fraser River, is a region where 2 km resolution is genuinely insufficient and where 500 m delivers operationally meaningful skill improvement.

---

## Basic details

### CIOPS-East (v2.4.0)
- **Model type:** Coastal deterministic ocean physics forecast with online sea ice; pseudo-analysis + forecast architecture, no independent DA
- **Core ocean model:** NEMO v3.6 / OPA (Madec et al., 2017)
- **Sea ice model:** CICE v6.2.0 (Hunke et al., 2021)
- **Horizontal resolution:** 1/36° nominally (2 to 2.5 km)
- **Numerical technique:** Primitive equations on Arakawa C-grid; explicit leapfrog with non-linear free surface (time-splitting of barotropic and baroclinic stepping)
- **Baroclinic time-step:** 150 seconds
- **Horizontal diffusion:** Bi-Laplacian (Del-4) on momentum along geopotential surfaces, Laplacian (Del-2) on tracers along iso-neutral surfaces
- **Vertical mixing:** Generic Length Scale (GLS) scheme, with reduced enhanced-mixing region in the St. Lawrence upper estuary (refined in v2.3.0 to better fit observations)
- **Roughness length scheme:** Smith et al. (1992) and Rascle et al. (2008)
- **Surface scheme:** GEM bulk formulae for turbulent sensible and latent heat, and momentum
- **Advection:** Total Variance Dissipation (TVD) for vertical tracer advection; 3rd-order Upstream-Biased Scheme (UBS) in flux form for momentum
- **Bathymetry:** SRTM30 (Becker et al., 2009) blended with GoMSS (Katavouta et al., 2016) and additional Fisheries and Oceans Canada observations
- **Tidal forcing:** 13 constituents from WebTide (M2, N2, S2, K2, K1, O1, Q1, P1, M4, Mf, Mm, Mn4, Ms4)

### CIOPS-West (NEP, v2.4.0)
- **Model type:** Coastal deterministic ocean physics forecast with online sea ice; pseudo-analysis + forecast architecture, no independent DA
- **Core ocean model:** NEMO v3.6 / OPA
- **Sea ice model:** CICE v6.2.0
- **Horizontal resolution:** 1/36° nominally (2 to 2.5 km)
- **Baroclinic time-step:** 60 seconds
- **Horizontal diffusion:** Laplacian (Del-2) on momentum along geopotential surfaces, Laplacian (Del-2) on tracers along iso-neutral surfaces
- **Vertical mixing:** Generic Length Scale (GLS) scheme
- **Surface scheme:** GEM bulk formulae for turbulent sensible and latent heat, and momentum
- **Advection:** TVD scheme with sub-time stepping for vertical tracer and momentum advection
- **Bathymetry:** SRTM30_plus v11 (Becker et al., 2009) blended with the Cascadia DEM (Haugerud, 1999)
- **Tidal forcing:** 8 constituents from WebTide (M2, K1, N2, S2, K2, O1, P1, Q1)

### Salish Sea 500m (SS500, v2.4.0)
- **Model type:** Coastal deterministic ocean physics forecast (no full sea ice — surface flux limiting scheme only)
- **Core ocean model:** NEMO v3.6 / OPA
- **Horizontal resolution:** 500 m
- **Baroclinic time-step:** 40 seconds
- **Surface scheme:** CORE bulk formulae for turbulent sensible and latent heat, and momentum
- **Advection:** TVD with sub-time stepping for vertical tracer and momentum advection
- **Bathymetry:** Canadian Hydrographic Service (CHS) measurements blended with the NOAA 3-arc-second dataset and the USGS Cascadia dataset, with smoothing and a 4 m minimum depth
- **Tidal forcing:** 8 constituents from WebTide, with additional tuning to best fit the Strait of Georgia

### Common operational details
- **Forecast length:** 48 hours
- **Update frequency:** Four times daily at 00, 06, 12, 18 UTC
- **Architecture:** Two-component design — pseudo-analysis for initial conditions, then 48-hour forecast
- **Independent variables:** x, y, z, time
- **Prognostic variables:** 3D horizontal currents, potential temperature, salinity, turbulent kinetic energy (TKE); 2D sea surface height (SSH)
- **Derived variables:** Vertical velocity

---

## Forcing

### Atmospheric forcing
- **CIOPS-East:** GDPS-G1 v9.0.0 (Global Deterministic Prediction System) blended with HRDPS v7.0.0 (High Resolution Deterministic Prediction System) where HRDPS coverage allows. Spatial and temporal interpolation between the two atmospheric sources.
- **CIOPS-West:** **GDPS-G0 v9.0.0** — an uncoupled 10 km component of the GDPS — blended with HRDPS v7.0.0 where HRDPS coverage allows. The switch to a 10 km uncoupled GDPS component was a notable v2.3.0 upgrade for CIOPS-West, providing higher-resolution atmospheric forcing where HRDPS coverage gaps exist.
- **Salish Sea 500m:** HRDPS forcing only (the Salish Sea sits entirely within HRDPS coverage, so no GDPS blending is required).

### Lateral oceanic boundary conditions
- **CIOPS-East and CIOPS-West:** From RIOPS-F v2.4.0 (the forecast component of RIOPS).
- **Salish Sea 500m:** From its parent CIOPS-West pseudo-analysis (no direct connection to RIOPS).

### River runoff
- **CIOPS-East:** R1D river model for the St. Lawrence; climatological values for other rivers (Dai and Trenberth, 2002; Saucier et al., 2003) with specific treatment at river mouths. **St. Lawrence flow is now drawn from the CanHys river discharge database** (multiple stations, using the SHOP-suite preprocessing) — switched from the IML feed in v2.3.0.
- **CIOPS-West:** Monthly climatological river runoff (Morrison et al., 2012; Dai, 2017; Dai et al., 2009).
- **Salish Sea 500m:** Real-time **CanHyS observed discharge for the Fraser River at Hope (station 08MF005)** — operationally important because the Fraser dominates Strait of Georgia freshwater input. Other rivers from Morrison (2011) climatology, subdivided into 150 input locations.

### Initial conditions (cycle dependence)
- **00 UTC cycle:** Initialized from the CIOPS pseudo-analysis valid at the same time. The pseudo-analysis itself is a free-running NEMO simulation forced at the boundaries by RIOPS and spectrally nudged to RIOPS over the deep ocean (>1500 m).
- **06, 12, 18 UTC cycles:** Initialized from the previous forecast's 6-hour restart file. This staggered initialization avoids running an expensive pseudo-analysis four times daily while still providing fresh atmospheric forcing in each cycle.

This pseudo-analysis + restart-chain design is the key operational efficiency of CIOPS — RIOPS provides assimilated ocean state at the boundaries and in the deep ocean, and the coastal coupled model is allowed to develop its own dynamics in the resolved coastal interior.

---

## Coupling

CIOPS-East and CIOPS-West are coupled to **sea ice (CICE v6.2.0)** with the same updated ice physics introduced in their parent RIOPS v2.4.0:

### Sea ice physics highlights (v2.3.0 onward)
- **Coupling mode:** Online, every model timestep
- **Radiation scheme:** Delta-Eddington multiple scattering parameterization (Briegleb and Light, 2007) — introduced in v2.3.0
- **Conductivity scheme:** "Bubbly" thermal conductivity (Pringle et al., 2007)
- **Dynamics:** Elastic-Viscous-Plastic (EVP) rheology (Hunke, 2001; Lipscomb et al., 2007)

### Sea ice in the Salish Sea 500m subdomain
The Salish Sea nest does **not** run a full CICE6 sea ice component — sea ice is handled through a surface flux limiting scheme. This is appropriate for the Strait of Georgia where significant ice cover is rare; running full CICE6 on the 500 m grid would impose a large computational cost for limited operational return.

CIOPS does **not** include atmospheric two-way coupling, in contrast to the GIOPS↔GDPS two-way coupling used in the global system.

---

## Data assimilation

**CIOPS does not perform its own ocean data assimilation.** This is a deliberate design choice that distinguishes CIOPS from [GIOPS](./giops.md) and [RIOPS](./riops.md) in the ECCC operational stack.

Instead, CIOPS inherits assimilation skill through two mechanisms:

1. **Lateral boundary conditions from RIOPS-F v2.4.0** — RIOPS itself uses SAM2 with assimilation of altimetry (six altimeters), gridded SST analyses, in-situ T/S profiles, and SAR-based ice concentration analyses. All of this assimilated information enters CIOPS through its boundaries.
2. **Spectral nudging to RIOPS over the deep ocean (>1500 m)** — within the CIOPS domain interior, the pseudo-analysis solution is constrained towards RIOPS in deep water, while shallower coastal water is allowed to develop free dynamics resolved by the 1/36° grid.

The net result is that CIOPS is a **"coastal dynamical downscaling"** of RIOPS — it inherits assimilation quality from its parent and adds value through resolved coastal physics (narrow channels, fjords, estuarine fronts, tidal mixing) that the 1/12° parent cannot represent.

This architecture keeps operational complexity manageable while delivering kilometric coastal resolution. The trade-off is that CIOPS skill at any given coastal location is bounded by the quality of RIOPS at the nearest assimilated information — there is no independent local correction from coastal observations within CIOPS itself.

---

## What it provides

CIOPS distributes ocean and sea ice forecast fields at 1/36° resolution (and 500 m for the Salish Sea nest).

### Ocean fields (3D)
- Potential temperature
- Salinity
- Horizontal currents (eastward, northward)
- Turbulent kinetic energy (TKE)
- Sea surface height (2D)
- Vertical velocity (derived)

### Sea ice fields (CIOPS-East and CIOPS-West only — not Salish Sea 500m)
- Sea ice concentration
- Sea ice thickness
- Sea ice velocity
- Sea ice surface temperature
- Snow thickness on sea ice

### File format
- **Format:** NetCDF following the Climate and Forecast (CF) Conventions

---

## Data availability
- **Is the data free?** Yes (no registration required for MSC Open Data)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-compliant)
- **System name:** CIOPS (Coastal Ice-Ocean Prediction System) — three domains: CIOPS-East, CIOPS-West, CIOPS-SalishSea
- **Distribution channels:**
  - **MSC Datamart:** Direct file access — primary download channel
  - **MSC GeoMet:** Geospatial web services (WMS, WCS, OGC API)
  - **MSC AniMet:** Visualization service for the Salish Sea product
  - **www.meteocentre.com/plus** (under CMC experimental tab) — visualization service for CIOPS-West products
- **Official access:** https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/readme_ciops_en/
- **Datamart access:** https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/readme_ciops-datamart_en/
- **Open Government metadata:**
  - CIOPS-East: https://open.canada.ca/data/en/dataset/bfe44cce-a9c4-467f-9172-c8800b32e4ec
  - CIOPS-West: https://open.canada.ca/data/en/dataset/390abee6-4ba0-4d6e-ae79-25753d1c43f3
  - CIOPS-SalishSea: https://open.canada.ca/data/en/dataset/cccb0064-5ab3-416a-a4f0-566b54f466f3
- **Licence:** ECCC Data Servers End-User Licence (open access)

---

## Version history

### April 14, 2026 — CIOPS-East v2.4.0 / CIOPS-West v2.4.0 (current)
- **Migration to new High Performance Computing infrastructure** at ECCC
- Computational/infrastructure upgrade only — no scientific changes to model formulation, forcing, or outputs
- Part of broader ECCC operational migration affecting GIOPS, RIOPS, and other operational systems

### June 11, 2024 — CIOPS-East v2.3.0 / CIOPS-West v2.3.0

**CIOPS-East v2.3.0:**
- New region of upper-estuary enhanced mixing (refined to better fit observations in the St. Lawrence)
- Updated St. Lawrence River temperature climatology
- Sea ice model upgraded from CICE v4 to **CICE v6.2.0**
- Delta-Eddington radiation scheme introduced for sea ice
- St. Lawrence river input switched to **CanHys** discharge database (multiple stations) from IML feed
- Lateral boundary conditions from RIOPS-F v2.4.0
- Atmospheric forcing now from GDPS-G1 v9.0.0 + HRDPS v7.0.0

**CIOPS-West v2.3.0:**
- **Atmospheric forcing now from a 10 km uncoupled GDPS component (GDPS-G0 v9.0.0)** blended with HRDPS v7.0.0 where coverage allows
- Sea ice model upgraded from CICE v4 to **CICE v6.2.0**
- Delta-Eddington radiation scheme introduced for sea ice
- Lateral boundary conditions from RIOPS-F v2.4.0

### June 28, 2022 — CIOPS v2.1.0
- Migration to new High Performance Computing infrastructure (computational only)

### December 1, 2021 — CIOPS-East v2.0.0 / CIOPS-West v2.0.0 (declared operational)
- **Both CIOPS-East and CIOPS-West transitioned from experimental to operational status**

**CIOPS-East:**
- Reduced bottom roughness
- Increased vertical resolution in the upper water column (75 → 100 levels)
- Reduced wave roughness parameter
- New wave roughness formulation for wind stress
- Increased diffusivity in the upper St. Lawrence estuary west of Tadoussac

**CIOPS-West:**
- **Salish Sea 500 m subdomain (SS500) added** — introduced a nested 500 m configuration covering the Strait of Georgia and Strait of Juan de Fuca

---

## Relationship to other ocean products

### Companion ECCC systems (operational ocean stack)
- **[GIOPS (Global Ice-Ocean Prediction System)](./giops.md):** The 1/4° global parent of the ECCC ocean stack. Provides global state and feeds RIOPS with assimilated information.
- **[RIOPS (Regional Ice-Ocean Prediction System)](./riops.md):** The 1/12° regional parent that directly feeds CIOPS. RIOPS provides lateral boundary conditions for both CIOPS-East and CIOPS-West, and supplies the deep-ocean state that the CIOPS pseudo-analysis is spectrally nudged toward. The CIOPS↔RIOPS relationship is the most important operational dependency in CIOPS.
- **GDPS (Global Deterministic Prediction System):** ECCC's operational global atmospheric NWP model. The full GDPS provides atmospheric forcing to CIOPS-East; an uncoupled 10 km GDPS component (GDPS-G0) provides forcing to CIOPS-West where HRDPS coverage is incomplete.
- **HRDPS (High Resolution Deterministic Prediction System):** ECCC's operational kilometric-scale atmospheric NWP model. Provides primary atmospheric forcing for all three CIOPS domains where HRDPS coverage exists; the Salish Sea 500 m nest is forced exclusively by HRDPS.

The full operational chain reads: **HRDPS+GDPS atmospheric forcing → CIOPS pseudo-analysis ← RIOPS boundaries / nudging ← GIOPS analyses ← assimilated observations.**

### Peer coastal ocean physics systems globally

CIOPS is one of relatively few operational coastal ocean physics systems running at kilometric or sub-kilometric resolution over basin-scale coastal domains. Closer peer systems include:

- **NOAA NOS Operational Forecast Systems (OFS):** A family of port-, harbor-, and estuary-scale ROMS and FVCOM forecasts run by NOAA's National Ocean Service for US coastal waters. These are smaller-domain than CIOPS but at comparable or higher resolution.
- **Copernicus Marine regional ocean physics products** (NWS Shelf, IBI, Mediterranean, Black Sea, Baltic, Arctic): Each is a basin-scale regional product. None of these matches CIOPS's 1/36° native resolution over a comparable coastal domain.
- **Australian Bureau of Meteorology eReefs and OceanMAPS regional products:** Similar nested-resolution architecture for Australian coastal waters.

The Salish Sea 500 m nest (CIOPS-SS500) sits in a particularly thin field — basin-scale 500 m operational ocean physics is genuinely rare worldwide.

### Methodological lineage
CIOPS uses NEMO v3.6 / OPA shared with [GIOPS](./giops.md) and [RIOPS](./riops.md), and CICE v6.2.0 shared with both. The pseudo-analysis + spectral nudging architecture is locally distinctive to CIOPS — neither GIOPS nor RIOPS uses this design. The choice not to run independent DA distinguishes CIOPS from its parents and makes it methodologically a "downscaling" rather than an "assimilation" system.

### AI-based counterparts
ECCC's broader documentation has flagged AI-based ocean forecasting as a development priority enabled by the new April 2026 HPC infrastructure. No specific operational AI counterpart to CIOPS has been announced, but coastal AI forecasting at this scale would be a natural near-term research direction.

---

## Notes
- The pseudo-analysis + spectral-nudging design is operationally elegant for a coastal downscaling system. Running independent DA at 1/36° over basin-scale coastal domains four times daily would be computationally prohibitive and would require dense coastal observation networks that don't exist for most of the CIOPS domains. Inheriting RIOPS's assimilation skill instead is the right operational trade-off.
- The 06/12/18 UTC restart-chain initialization (rather than a fresh pseudo-analysis at every cycle) is the other key operational efficiency. It means the pseudo-analysis only needs to run once per day at 00 UTC, while the forecast component still benefits from fresh atmospheric forcing four times daily.
- The 10 km uncoupled GDPS atmospheric forcing component for CIOPS-West (introduced in v2.3.0) is a meaningful design detail. The standard coupled GDPS would feed back ocean state from GIOPS into the atmospheric forecast, but for a regional downscaling that is itself initialized from a different parent (RIOPS), an uncoupled GDPS component is more appropriate — it avoids inconsistencies between the atmospheric forcing's ocean assumptions and the ocean state CIOPS-West is actually running.
- The Salish Sea 500 m nest is a genuinely high-resolution operational product. The Strait of Georgia is operationally important — major shipping routes (Vancouver, Seattle, the BC Ferries network), strong tidal currents, complex bathymetry, and significant Fraser River freshwater input. The 500 m grid resolves features that 2 km would smooth out.
- The CIOPS-East upper-estuary mixing refinement (v2.3.0) is a domain-specific tuning rather than a generic improvement. The St. Lawrence upper estuary west of Tadoussac is dynamically distinctive — strong tidal mixing, complex stratification, and a critical region for ice formation in winter. Iterative tuning of the mixing region against observations is a typical pattern for operational coastal systems.
- The 13 tidal constituents in CIOPS-East (vs 8 in CIOPS-West) reflect the Bay of Fundy / Gulf of Maine tidal regime — among the strongest tidal ranges in the world, with significant overtides (M4, MN4, MS4) and long-period constituents (Mf, Mm) that are operationally important for Atlantic coastal forecasting and that don't matter as much for the Pacific coast.
- The Salish Sea nest's WebTide tuning specifically for the Strait of Georgia is another example of domain-specific calibration — generic global tidal databases don't always represent strongly resonant inland seas accurately, and local tuning improves operational skill.
- Open data licensing is genuinely open — no registration required, direct file access via Datamart. Same as GIOPS and RIOPS.

---

## Official documentation
- CIOPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/readme_ciops_en/
- CIOPS Changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/changelog_ciops_en/
- ECCC operational systems changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- MSC Datamart access: https://eccc-msc.github.io/open-data/msc-data/nwp_ciops/readme_ciops-datamart_en/

### CIOPS-East
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_CIOPS-EAST_e.pdf
- Technical note: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_notes/technote_ciops-east_e.pdf
- v2.3.0 Factsheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_ciops-east-230_e.pdf
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_CIOPS-E_en.svg

### CIOPS-West (and Salish Sea)
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_CIOPS-WEST_e.pdf
- Technical note: https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_notes/technote_ciops-west_e.pdf
- v2.3.0 Factsheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_ciops-west-230_e.pdf
- Diagram of system dependencies: https://collaboration.cmc.ec.gc.ca/cmc/cmos/public_doc/msc-data/nwep-dependency-diagrams/system_CIOPS-W_en.svg

### Key references
- Madec, G. and the NEMO team (2017). NEMO ocean engine. *Notes du Pôle de modélisation de l'Institut Pierre-Simon Laplace (IPSL)*, v3.6, Number 27. Zenodo. https://doi.org/10.5281/zenodo.1472492
- Hunke, E.C., Allard, R., et al. (2021). CICE-Consortium/CICE: CICE Version 6.2.0. Zenodo. https://doi.org/10.5281/zenodo.4671172
- Hunke, E.C. (2001). Viscous-plastic sea ice dynamics with the EVP model: linearization issues. *J. Comput. Phys.*, 170, 18–38.
- Lipscomb, W.H., Hunke, E.C., Maslowski, W., Jakacki, J. (2007). Ridging, strength, and stability in high-resolution sea ice models. *J. Geophys. Res.*, 112, C03S91. https://doi.org/10.1029/2005JC003355
- Briegleb, B.P., Light, B. (2007). A Delta-Eddington multiple scattering parameterization for solar radiation in the sea ice component of the Community Climate System Model. NCAR Technical Note NCAR/TN-472+STR.
- Pringle, D.J., Eicken, H., Trodahl, H.J., Backstrom, L.G.E. (2007). Thermal conductivity of landfast Antarctic and Arctic sea ice. *J. Geophys. Res. Oceans*. https://doi.org/10.1029/2006JC003641
- Becker, J.J., Sandwell, D.T., Smith, W.H.F., et al. (2009). Global Bathymetry and Elevation Data at 30 Arc Seconds Resolution: SRTM30_PLUS. *Marine Geodesy*, 32(4), 355–371. https://doi.org/10.1080/01490410903297766
- Katavouta, A., Thompson, K.R., Lu, Y., Loder, J.W. (2016). Interaction between the tidal and seasonal variability of the Gulf of Maine and Scotian Shelf region. *Journal of Physical Oceanography*, 46(11), 3279–3298.
- Haugerud, R.A. (1999). Digital elevation model (DEM) of Cascadia, latitude 39N–53N, longitude 116W–133W. *USGS Open-File Report 99-369*. https://pubs.usgs.gov/of/1999/0369/
- Morrison, J., Foreman, M.G.G., Masson, D. (2012). A Method for Estimating Monthly Freshwater Discharge Affecting British Columbia Coastal Waters. *Atmosphere-Ocean*, 50(1), 1–8. https://doi.org/10.1080/07055900.2011.637667
- Saucier, F.J., Roy, F., Gilbert, D., Pellerin, P., Ritchie, H. (2003). Modeling the formation and circulation processes of water masses and sea ice in the Gulf of St. Lawrence, Canada. *J. Geophys. Res.*, 108, 3269. https://doi.org/10.1029/2000JC000686
- Smith, S.D., Anderson, R.J., Oost, W.A., et al. (1992). Sea surface wind stress and drag coefficients: The HEXOS results. *Boundary-Layer Meteorology*, 60, 109–142. https://doi.org/10.1007/BF00122064
- Rascle, N., Ardhuin, F., Queffeulou, P., Croizé-Fillon, D. (2008). A global wave parameter database for geophysical applications. Part 1. *Ocean Modelling*, 25, 154–171. https://doi.org/10.1016/j.ocemod.2008.07.006
- Dai, A., Trenberth, K.E. (2002). Estimates of freshwater discharge from continents: Latitudinal and seasonal variations. *Journal of Hydrometeorology*, 3, 660–687.
- Dai, A., Qian, T., Trenberth, K.E., Milliman, J.D. (2009). Changes in continental freshwater discharge from 1948–2004. *J. Climate*, 22, 2773–2791.
- Smith, G.C., Liu, Y., Benkiran, M., et al. (2021). The Regional Ice Ocean Prediction System v2: a pan-Canadian ocean analysis system using an online tidal harmonic analysis. *Geoscientific Model Development*, 14(3), 1445–1467.
