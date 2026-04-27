# CAPS (Canadian Arctic Prediction System)

> ⚠️ **Experimental system.** CAPS is distributed as experimental data. While it is hosted on the standard MSC datamart (not the alpha datamart), ECCC explicitly designates it as experimental and the system is not subject to the same operational change-management process as GDPS, RDPS, HRDPS, or GIOPS/RIOPS.

## What this model is
The Canadian Arctic Prediction System (CAPS) is an experimental coupled atmosphere-ocean-sea ice prediction system operated by Environment and Climate Change Canada (ECCC). It provides 48-hour forecasts at ~3 km horizontal resolution over a large pan-Arctic domain spanning northern Canada, Alaska, Greenland, Iceland, Scandinavia, the Baltic countries, eastern Russia, and the surrounding Arctic Ocean and adjacent seas.

CAPS is structurally distinct from ECCC's other regional NWP systems in that it is a **fully coupled atmosphere-ocean-sea ice forecast** rather than an atmosphere-only model with prescribed surface conditions. The atmospheric component is based on the Global Environmental Multiscale (GEM) model (configuration inherited from the decommissioned HRDPS-North) and is two-way coupled to NEMO v3.6 ocean and CICE v6.2.0 sea ice components shared with [RIOPS](../../../ocean_models/regional/canada/riops.md). The system was developed to provide numerical weather and environmental forecasting guidance for several Storm Prediction Centres (SPCs) across Canada, as well as for the Department of National Defence (DND), Canadian Coast Guard (CCG), and Department of Fisheries and Oceans (DFO).

The current experimental version is CAPS 3.0.0, implemented June 18, 2025.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Pan-Arctic, with substantial extension into mid-latitudes
- **Atmospheric domain:** Rotated latitude-longitude grid covering the Canadian Arctic and surrounding regions, including:
  - Northern Canada and the Canadian Arctic Archipelago
  - Alaska
  - Greenland and Iceland
  - Scandinavia (Norway, Sweden, Finland)
  - The Baltic countries
  - Eastern Russia and adjacent Arctic seas
- **Ice-ocean domain:** Extends further south than the atmospheric domain, covering additional parts of the North Atlantic and North Pacific (consistent with the RIOPS model domain), with active atmosphere-ocean coupling occurring only over the atmospheric domain footprint

The unusually large pan-Arctic atmospheric coverage is one of CAPS's distinctive features — it provides high-resolution coupled guidance over a domain that would normally be split across multiple national operational systems.

---

## Basic details

### Atmospheric component
- **Model type:** Experimental regional deterministic NWP (limited-area, non-hydrostatic, convection-permitting), coupled to ocean and sea ice
- **Model system / core:** GEM (Global Environmental Multiscale) version 5.2.0
- **Configuration lineage:** Based on the decommissioned HRDPS-North version 2.1.0
- **Horizontal resolution:** ~3 km (0.02925° uniform)
- **Grid dimensions:** 2250 × 1850 rotated latitude-longitude grid
- **Vertical levels:** 62 staggered hybrid levels with SLEVE coordinate (Husain et al., 2020)
- **Model lid:** 10 hPa (with nesting over 4 levels)
- **Lowest model level:** 40 m
- **Diagnostic levels:** 10 m (winds), 1.5 m (temperature, specific humidity)
- **Time integration:** Implicit, semi-Lagrangian (3-D), 2 time-level, 100 second time step
- **Forecast length:** 48 hours
- **Update frequency:** 2× daily (00, 12 UTC)

### Ice-ocean component
- **Ocean model:** NEMO v3.6 / OPA
- **Sea ice model:** CICE v6.2.0
- **Domain:** Regional, from 25.6°N in the North Atlantic to 43.8°N in the Pacific (covers all three Canadian oceans — North Atlantic, Arctic, North Pacific)
- **Horizontal resolution:** Tripolar 1/12° grid (~8 km in North Atlantic, ~2 km in Canadian Arctic Archipelago)
- **Grid dimensions:** 1580 × 2198
- **Vertical sampling:** 75 z-levels
- **Baroclinic time step:** 300 seconds

---

## Coupling

CAPS is **two-way coupled** between its atmospheric (GEM LAM) and ice-ocean (NEMO-CICE) components, with coupling occurring every 5 minutes via the GOSSIP coupler.

### GEM LAM → NEMO-CICE
- Downward shortwave and longwave radiation fluxes
- Near-surface atmospheric temperature, humidity, and winds
- Surface and near-surface atmospheric pressure
- Precipitation

### NEMO-CICE → GEM LAM
- Upward longwave radiation flux
- Fractional coverage and thickness of marine ice
- Depth of snow on marine ice
- Surface roughness
- Surface and near-surface temperature, humidity, and winds
- Surface-level turbulent sensible and latent heat fluxes, and turbulent momentum flux

This makes CAPS one of the few publicly distributed regional NWP systems with online two-way atmosphere-ocean-ice coupling at convection-permitting resolution. The coupling is active over the atmospheric domain footprint; the ice-ocean model's southward extension into the North Atlantic and North Pacific is forced by GDPS atmospheric fields outside the coupled region.

---

## Initial and boundary conditions

### Atmospheric component
- **Initial atmospheric and surface conditions:** GDPS-9.0.0 G0 component (10 km)
- **Lateral boundary conditions:** GDPS-9.0.0 G0 forecast, refreshed hourly
- **Hydrometeor initial fields:** Recycled from the 12-hour forecast of the previous CAPS integration
- **Land surface:** ISBA scheme, initialized from GDPS analyses

### Ice-ocean component
- **Sea ice and ocean initial conditions:**
  - 00 UTC cycle: From RIOPS v2.4.0 analysis (which incorporates RIPS_early ice analysis with RCM-SAR data assimilated, 4-hour cutoff)
  - 12 UTC cycle: From the previous CAPS forecast restart at lead time 12 hours
- **Ocean lateral boundary conditions:** GDPS-G1 v9.0.0 (in the North Atlantic and North Pacific)
- **St. Lawrence River discharge:** Real-time data from the CanHys database
- **Tidal forcing:** 13 constituents (M2, N2, S2, K2, K1, O1, Q1, P1, M4, Mf, Mm, Mn4, Ms4) from OSU

---

## Atmospheric physics

The CAPS atmospheric component uses the same physics suite as related GEM-based ECCC systems:

- **Radiation:** Li-Barker correlated k-distribution radiative transfer scheme (called every 15 minutes)
- **Surface scheme:** Mosaic approach with 4 types (land, water, sea ice, glacier); ISBA scheme for land surface (Noilhan and Planton, 1989; Bélair et al., 2003a, 2003b)
- **Boundary-layer turbulent mixing:** TKE-based scheme (Benoit et al., 1989; Delage, 1988a, 1988b) with statistical representation of subgrid-scale clouds (Mailhot and Bélair, 2002; Bélair et al., 2005); Blackadar mixing length; Richardson number hysteresis (McTaggart-Cowan and Zadra, 2015)
- **Shallow convection:** Kuo Transient scheme (Bélair et al., 2005)
- **Stable precipitation:** P3 bulk two-moment microphysics (Morrison and Milbrandt, 2015; Milbrandt and Morrison, 2016) with Bourgouin (2000) precipitation type diagnostics
- **Deep convection:** Kain-Fritsch scheme (Kain and Fritsch, 1990, 1993)
- **Surface roughness over continental waters:** Charnock formulation for momentum, Deacu formulation for Z0T (Deacu et al., 2012)

Notably, CAPS does not include orographic gravity wave drag, non-orographic gravity wave drag, low-level blocking, or an urban scheme — reflecting its convection-permitting resolution and high-latitude focus.

### Ice physics (CICE 6.2.0)
- Delta-Eddington radiation scheme (Briegleb and Light, 2007)
- "Bubbly" thermal conductivity (Pringle et al., 2007)
- Viscous-plastic rheology with 10 ice categories
- Ice strength parameters: P* = 22.5 kN/m², C* = 15
- Ice-ocean roughness: iceruf_ocn = 1.8 cm
- Air-ice roughness: iceruf = 0.54 mm

---

## What it provides

Forecasts of atmospheric, ocean, and sea ice fields including:

### Atmospheric
- Near-surface temperature, wind, and humidity
- Surface and mean sea level pressure
- Precipitation (with rain/snow phase partitioning via Bourgouin)
- Cloud cover and hydrometeor fields
- Boundary-layer height and other derived parameters

### Ocean
- 3D potential temperature, salinity, and currents
- Sea surface height
- Turbulent kinetic energy

### Sea ice
- Sea ice concentration, thickness, and velocity
- Snow thickness on sea ice
- Sea ice surface temperature

CAPS is intended for high-latitude marine and atmospheric forecasting where atmosphere-ocean-ice coupling matters — applications that include Arctic shipping guidance, sea ice forecasting, polar low development, and cold-air outbreak prediction in regions with significant ice-edge dynamics.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://dd.weather.gc.ca/today/model_caps/3km/
- **Documentation:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_caps/readme_caps_en/
- **Changelog:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_caps/changelog_caps_en/
- **Viewing service:** AniMet (deployment expected by Fall 2025 per the v3.0.0 specifications document)

---

## Version history

### CAPS 3.0.0 — implemented June 18, 2025 (current)
- Atmospheric component: GEM v5.2.0, configuration inherited from the (now decommissioned) HRDPS-North v2.1.0
- Ice-ocean components: NEMO v3.6 with CICE v6.2.0, coupled to RIOPS v2.4.0 forecast component for initial and boundary conditions
- Atmospheric forcing from GDPS-9.0.0 G0 (10 km)
- Two-way GEM-NEMO-CICE coupling at 5-minute exchange frequency via GOSSIP

---

## Status
- CAPS is distributed as **experimental data** via the standard MSC datamart (not the alpha datamart used for HRDPS-West), but is explicitly labelled as experimental in ECCC documentation.
- It is not operationally supported in the same sense as HRDPS, RDPS, GDPS, GIOPS, or RIOPS.
- CAPS is one of several experimental ECCC systems alongside [GDPS-SN](../../global/canada/gem-global.md#experimental-ai-hybrid-configuration-gdps-sn) and [HRDPS-West](./hrdps-west.md) — see the repository's [`STATUS.md`](../../../STATUS.md) for the current list.
- The system provides forecast guidance to Canadian Storm Prediction Centres, the Department of National Defence, the Canadian Coast Guard, and the Department of Fisheries and Oceans.

---

## Relationship to other ECCC systems

CAPS occupies a distinctive position in ECCC's operational and experimental forecast suite:

- **[HRDPS](./hrdps.md):** Pan-Canadian operational atmosphere-only convection-permitting model at 2.5 km. CAPS is structurally different — coupled atmosphere-ocean-ice — and covers a different domain (Arctic-focused vs pan-Canadian).
- **HRDPS-North (decommissioned):** The atmospheric configuration of CAPS is inherited directly from this retired system.
- **[HRDPS-West](./hrdps-west.md):** Experimental kilometric-scale system over Southern BC; atmosphere-only.
- **[RIOPS](../../../ocean_models/regional/canada/riops.md):** Provides initial and boundary conditions for the CAPS ice-ocean components. CAPS shares NEMO and CICE versions with RIOPS.
- **[GDPS](../../global/canada/gem-global.md):** Provides atmospheric initial and boundary conditions (G0 component) and ice-ocean lateral boundary conditions (G1 component).

CAPS is the only ECCC system in this repository that provides **coupled atmosphere-ocean-sea ice forecasts** at convection-permitting atmospheric resolution. This makes it methodologically more similar to research configurations like NOAA's HAFS (which couples FV3 atmosphere with HYCOM ocean and WAVEWATCH III) than to standalone atmospheric or ocean operational systems.

---

## Notes
- CAPS is convection-permitting at ~3 km resolution and uses the Kain-Fritsch scheme for deep convection. The atmospheric configuration inherits from HRDPS-North rather than from the operational HRDPS, which is reflected in the physics suite choices.
- The pan-Arctic atmospheric coverage extending into Scandinavia, the Baltic countries, Iceland, and eastern Russia means CAPS produces guidance over regions that fall outside Canadian forecast responsibility but are dynamically connected to Arctic processes affecting Canada. Users in those regions are not the intended audience but can access the data freely.
- The 5-minute coupling frequency between GEM and NEMO-CICE is unusually frequent for a regional forecast system — most coupled operational systems use hourly or longer coupling intervals. The tight coupling reflects the importance of high-frequency atmosphere-ice flux exchanges in polar boundary layer dynamics.
- The ice-ocean domain extends well south of the atmospheric domain (down to 25.6°N in the Atlantic and 43.8°N in the Pacific) to maintain consistency with RIOPS. Outside the coupled region, the ice-ocean model is forced by GDPS-G0 atmospheric fields rather than by CAPS's own atmospheric component.
- The technical specifications document for v3.0.0 contains a minor internal inconsistency, labelling some sections as "version 3.3.0." The implementation date (June 18, 2025), the official MSC documentation, and the version-3.0.0 references in the title and changelog all indicate that 3.0.0 is the correct designation.
- As an experimental system, configuration, domain, resolution, coupling specifications, and product availability may change without the formal change-management process used for operational ECCC systems.

---

## Official documentation
- CAPS open data page (English):  
  https://eccc-msc.github.io/open-data/msc-data/nwp_caps/readme_caps_en/
- CAPS changelog:  
  https://eccc-msc.github.io/open-data/msc-data/nwp_caps/changelog_caps_en/
- CAPS v3.0.0 technical specifications (Environment and Climate Change Canada, June 2025)
- CAPS v3.0.0 technical note (in preparation as of the v3.0.0 specifications document)

### Key references
- Côté, J., et al. (1998a, 1998b). The operational CMC-MRB Global Environmental Multiscale (GEM) model. *Mon. Wea. Rev.*, 126.
- Husain, S.Z., et al. (2020). On the Progressive Attenuation of Finescale Orography Contributions to the Vertical Coordinate Surfaces within a Terrain-Following Coordinate System. *Mon. Wea. Rev.*, 148, 4143–4158.
- Madec, G. (2015). NEMO ocean engine. *Note du Pôle de Modélisation*, Institut Pierre-Simon Laplace.
- Hunke, E.C., et al. (2021). CICE-Consortium/CICE: CICE Version 6.2.0. Zenodo. https://doi.org/10.5281/zenodo.4671172
- Morrison, H., and Milbrandt, J.A. (2015). Parameterization of cloud microphysics based on the prediction of bulk ice particle properties. Part I. *J. Atmos. Sci.*, 72, 287–311.
- Milbrandt, J.A., and Morrison, H. (2016). Parameterization of cloud microphysics based on the prediction of bulk ice particle properties. Part III. *J. Atmos. Sci.*, 73, 975–995.
