# GDPS-EXP (Global Deterministic Prediction System — Experimental)

> ⚠️ **Experimental system.** GDPS-EXP is distributed as experimental data through the standard MSC datamart (not the alpha datamart). Per ECCC's own README at the data distribution endpoint, it is "expected to replace in 2026, the current Global Deterministic Prediction System (GDPS) data." This is a directional signal rather than a confirmed implementation date.

## What this model is
GDPS-EXP is the experimental successor candidate to ECCC's [operational Global Deterministic Prediction System (GDPS)](./gem-global.md). It is a coupled atmosphere-ocean-sea ice prediction system based on the Global Environmental Multiscale (GEM) model — the same dynamical core as the operational GDPS — but extends it with **hybrid AI-physics forecasting** via spectral nudging toward [GEML](./gdps-geml.md), ECCC's data-driven AI weather model.

The headline feature is the hybrid AI-physics integration: GEM's large-scale temperature and horizontal wind fields are spectrally nudged toward GEML's predictions during the forecast integration. But GDPS-EXP is more than just "operational GDPS plus AI nudging." Version 9.0.9 also brings substantial updates to the data assimilation system (4DEnVar with 256-member LETKF backgrounds from GEPS 8.0.0, RTTOV v13, all-sky ATMS humidity assimilation), updated CICE 6.2.0 sea ice physics, a new Mean Dynamic Topography for SLA assimilation, and various other refinements relative to the operational GDPS 9.0.0.

The current version is GDPS-EXP 9.0.9, implemented at CMC operations on March 5, 2025. Per ECCC's MSC datamart README, this experimental system is expected to replace the operational GDPS in 2026.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global
- **Atmospheric grid:** Yin-Yang horizontal grid, 2 × 2047 × 683 grid points
- **Atmospheric resolution:** Quasi-uniform 0.135° (~15 km)
- **Ice-ocean grid:** Global 0.25° with 50 z-levels

---

## Basic details

### Atmospheric component (hybrid GEM + GEML)
- **Model type:** Global deterministic NWP, hybrid physics-AI, coupled to ocean and sea ice
- **Core atmospheric model:** GEM (Global Environmental Multiscale) version 5.2
- **AI component:** [GEML 1.0](./gdps-geml.md) (graph neural network, GraphCast-derived)
- **Hybrid approach:** Spectral nudging of GEM's large-scale fields toward GEML predictions
- **Horizontal resolution:** ~15 km (0.135°)
- **Vertical levels:** 84 staggered hybrid levels with Charney-Phillips vertical grid
- **Model lid:** 0.1 hPa
- **Forecast length:** 10 days (240 hours)
- **Update frequency:** 2× daily (00 and 12 UTC)
- **Time step:** 450 seconds
- **Time integration:** Iterative-implicit, semi-Lagrangian (3D), 2 time-level

### Ice-ocean component
- **Ocean model:** NEMO 3.6
- **Sea ice model:** CICE 6.2.0 (upgraded from CICE 4 in operational GDPS)
- **Horizontal resolution:** 0.25°
- **Vertical sampling:** 50 z-levels
- **Time step:** 450 s (synchronized with atmosphere)
- **Coupling frequency:** Every ocean time step (450 s) via the GOSSIP coupler

---

## Hybrid AI-physics forecasting

The defining feature of GDPS-EXP is its hybrid integration of GEM physics with GEML AI predictions through **spectral nudging**:

### Nudged variables
- Large-scale temperature
- Large-scale horizontal wind components (u, v)

### Spatial constraints
- **Vertical profile:** Nudging applied between **250–850 hPa** (mid-troposphere). No nudging in the planetary boundary layer (>850 hPa) or stratosphere (<250 hPa).
- **Horizontal scales:** Discrete cosine transform (DCT) spectral filter applied to the Yin and Yang grids separately. Full nudging weight for scales **larger than 2750 km**, ramping to zero below 2250 km. Smaller scales remain governed by GEM physics.

### Temporal handling
- GEML output is available every 6 hours
- At each GEM time step (450 s), linear interpolation is performed between successive GEML forecasts
- Time-varying nudging weight reaches its maximum at the beginning and end of each 6-hour window (when GEML output is available) and decreases rapidly in between
- Nudging is suspended when the weight falls below a critical threshold
- **Nudging relaxation time:** 3.28125 hours

### Design rationale
The spatial and temporal constraints are deliberately conservative. Nudging only the largest scales (>2750 km) in the mid-troposphere preserves GEM's physics-driven behavior at synoptic and mesoscale features, regional precipitation, boundary-layer dynamics, and stratospheric processes. The intent, per Husain et al. (2024), is to harness AI's demonstrated skill at large-scale prediction while leaving smaller-scale processes — where physics-based models retain advantages — to GEM's parameterizations and dynamics.

---

## What's new in v9.0.9 relative to operational GDPS

GDPS-EXP 9.0.9 differs from operational GDPS 9.0.0 in several material ways beyond the headline hybrid AI feature:

### Data assimilation
- **4DEnVar with 256-member LETKF backgrounds** from the new GEPS 8.0.0 (vs older background ensemble configurations)
- **Fully ensemble-derived B matrix** in 4DEnVar instead of NMC-method backgrounds
- **Scale-dependent (horizontal) localization** of 4D ensemble covariances
- **RTTOV v13** radiative transfer model
- **All-sky assimilation of ATMS temperature and AMSUB/MHS humidity channels**
- **Hessian matrix cycling removed**
- **Vertical level changes:** Analysis increments moved from L80/vCode 5002 to L84/vCode 5005 (matching GEPS)
- **3DEnVar with fully ensemble-derived B** for radiance bias correction reference state (replacing 3DVar)
- **Hyperspectral infrared QC** modifications for albedo changes
- **Additional ozone satellite data:** TROPOMI, OMPS-NM, OMPS-NP added

### Atmospheric physics
- New **convection scheme for low-CAPE environments** (mid-level mass-flux scheme; McTaggart-Cowan et al., 2020)
- Methane oxidation via climatological relaxation
- Revised orographic and non-orographic gravity wave drag (refactored sgo16 scheme)
- Adjustments to convection trigger parameters between 30°S and 30°N

### Ice-ocean component
- **CICE upgraded from version 4 to 6.2.0**
- **Delta-Eddington radiation scheme** for sea ice (Briegleb and Light, 2007)
- **'Bubbly' thermal conductivity** scheme (Pringle et al., 2007)
- **New Mean Dynamic Topography** for SLA assimilation
- Coupling moved to GIOPS 3.5.0 for initialization
- New altimetry sources added (Jason-3N, Sentinel-6A-HR)

### Surface
- **Increased momentum roughness length over sea ice** (from 0.16 m to 0.54 m)
- Updated minimum Obukhov length thresholds for surface-atmosphere decoupling in stable conditions

These changes mean GDPS-EXP is not just operational GDPS with AI nudging bolted on — it's a substantially evolved system that happens to also incorporate hybrid AI-physics forecasting. Users planning long-term backtests, validation, or climatology should treat operational GDPS 9.0.0 and GDPS-EXP 9.0.9 as separate model eras.

---

## Data assimilation
GDPS-EXP uses a **four-dimensional ensemble-variational (4DEnVar) approach** for atmospheric assimilation, with two-cycle structure:

### Cycle structure
- **Final analysis and background:** 4× daily at 00, 06, 12, 18 UTC, with a 7-hour cutoff time. Used as background for the next analysis cycle.
- **Early analysis and forecast:** 2× daily at 00 and 12 UTC, with a 3-hour cutoff time. Each early analysis is initialized with the preceding GDPS background from the final analysis run.

### Assimilated observations

**Microwave satellite radiances:**
- AMSU-A, MHS, ATMS, SSMIS, MWHS-2

**Infrared satellite radiances:**
- AIRS, IASI, CrIS

**Imager radiances:**
- Geostationary imagers (1 to 3 channels)

**Other satellite data:**
- GPS-RO refractivity
- Atmospheric Motion Vectors (AMVs)
- Scatterometer winds (ASCAT)
- ZTD from ground-based GPS

**Conventional data:**
- Radiosonde (TEMP, PILOT)
- Surface stations (SYNOP/SHIP, METAR, MSC partner networks, BUOY/DRIFTER)
- Aircraft data

**Ozone observations:**
- OMI, OMPS-NM, TROPOMI, MLS, SBUV/2, OMPS-NP

### Initialization
- **4D Incremental Analysis Update (4D-IAU)** applied during the 6-hour period from t-3 to t+3 hours
- Recycling of physics variables: total condensate, cloud fraction, TKE, turbulent regime, mixing length, friction velocity, PBL height, PBL cloud water/fraction, PBL flux enhancement factor

---

## What it provides
GDPS-EXP produces global deterministic forecasts of:
- Three-dimensional winds, virtual temperature, vertical motion
- Geopotential, specific humidity, surface pressure
- Cloud condensate mixing ratio, turbulent kinetic energy
- Prognostic ozone (LINOZ scheme), used for online total column ozone and UV index forecasts
- Mean sea level pressure, relative humidity
- Quantitative precipitation forecast (QPF), precipitation rate, precipitation type
- Cloud cover, boundary layer height
- Total column ozone, clear-sky and all-sky UV indices

The ice-ocean component additionally provides:
- 3D ocean potential temperature, salinity, and currents
- Sea surface height
- Sea ice concentration, thickness, and snow on sea ice (dynamic, from coupling)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://dd.weather.gc.ca/today/model_gdps/
- **README at data location:**  
  https://dd.weather.gc.ca/today/model_gdps/README.txt
- **Documentation:**  
  https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps_en/#data-of-the-experimental-global-deterministic-prediction-system
- **Viewing service:** AniMet (under "GDPS [experimental]")

ECCC distributes GDPS-EXP through the standard 24/7-supported HTTPS MSC datamart (rather than the alpha datamart used for some other experimental systems like HRDPS-West), reflecting the system's pre-operational status and the explicit intent for users to test it as a candidate replacement for operational GDPS.

---

## Path to operationalization

ECCC has provided several public signals indicating that GDPS-EXP is on a trajectory toward replacing the operational GDPS:

1. **Direct ECCC statement:** The MSC datamart README at the GDPS-EXP distribution endpoint states that the data "is expected to replace in 2026, the current Global Deterministic Prediction System (GDPS) data."
2. **Distribution infrastructure:** Unlike alpha-datamart experimental systems, GDPS-EXP is distributed via the standard 24/7-supported MSC datamart, signaling operational-grade infrastructure expectations.
3. **HPC enabling infrastructure:** ECCC's April 2026 supercomputer upgrade to H100 GPU infrastructure provides the GPU acceleration required to run GEML inference inside the operational forecast cycle. AI-hybrid NWP at the scale GDPS-EXP requires was not feasible within ECCC's pre-2026 operational compute envelope.
4. **Pre-operational scientific evaluation:** The fact sheet describes the system as "now in pre-operational scientific evaluation."

The 2026 replacement signal is directional rather than a fixed implementation date. As of writing, no formal Service Change Notice has been issued, and the timeline could slip — the ECCC pattern of "experimental → operational" transitions has historically taken longer than initial signals suggested.

---

## Status
- GDPS-EXP is distributed as **experimental data** but on the standard 24/7-supported MSC datamart.
- It is in **pre-operational scientific evaluation** as of 2025.
- Per ECCC's own statement at the data distribution endpoint, it is **expected to replace operational GDPS in 2026**.
- As an experimental system, configuration and outputs may change before formal operationalization.
- It is one of several experimental ECCC systems alongside [GEML](./gdps-geml.md), [CAPS](../../regional/canada/caps.md), and [HRDPS-West](../../regional/canada/hrdps-west.md) — see the repository's [`STATUS.md`](../../../STATUS.md) for the current list.

---

## Relationship to other models

### Companion ECCC systems
- **[Operational GDPS](./gem-global.md):** The current operational system that GDPS-EXP is expected to replace in 2026. Both share the GEM dynamical core.
- **[GEML](./gdps-geml.md):** The AI weather model that provides spectral nudging targets for GDPS-EXP. GEML is also distributed as a standalone forecast product.
- **[GEPS](../../../ensemble_models/global/canada/geps.md):** Provides the 256-member ensemble backgrounds (LETKF) used in GDPS-EXP's 4DEnVar data assimilation.
- **[GIOPS](../../../ocean_models/global/canada/giops.md):** Provides initial conditions for the ice-ocean component (GIOPS 3.5.0).

### Hybrid physics-AI peers
GDPS-EXP is part of an emerging class of operational hybrid physics-AI weather forecasting systems. Notable peers include:
- **[HGEFS](../../../ensemble_models/global/usa/hgefs.md)** (NOAA, operational since December 2025) — combines physics-based GEFS members with AI-based AIGEFS members in a "grand ensemble" architecture. Different hybrid strategy than GDPS-EXP: combines ensemble members rather than nudging within a single forecast.

GDPS-EXP and HGEFS represent two different theories of how AI should enter operational NWP:
- **GDPS-EXP:** AI predictions guide a physics forecast through online spectral nudging. Single-model output.
- **HGEFS:** AI and physics ensembles run independently and are combined post-forecast for probabilistic guidance. Multi-model ensemble.

Neither approach has been declared the consensus path forward, and the operational NWP community continues to explore both.

### AI architectural lineage
The GEML component is part of the GraphCast architectural family (Lam et al., 2023), which also includes:
- **[GraphCastGFS](../usa/graphcastgfs.md)** (NOAA, experimental) — productionization with GDAS+ERA5 fine-tuning
- **[AIGFS](../usa/aigfs.md)** (NOAA, operational since December 2025) — operational descendant of GraphCastGFS

ECCC's choice to use a GraphCast-derived AI model rather than developing an independent architecture reflects the broader convergence around graph neural networks for global AI weather prediction.

---

## Notes
- The hybrid AI-physics design is genuinely conservative: nudging is restricted to large scales (>2750 km), only in the mid-troposphere (250–850 hPa), with intermittent time-varying weights. This deliberately limits AI's influence to where it has demonstrated skill (large-scale circulation) while preserving physics-based behavior at smaller scales, in the boundary layer, and in the stratosphere. Users may find the hybrid forecast looks broadly similar to operational GDPS at synoptic and smaller scales, with differences emerging primarily in the planetary-scale flow and large-scale teleconnection patterns.
- The 6-hourly nudging cadence (synchronized with GEML output) means that hybrid corrections are applied intermittently rather than continuously. The time-varying weighting that ramps down between GEML output times reflects the practical constraint that GEML runs on a 6-hour autoregressive timestep.
- GDPS-EXP runs on ECCC's standard global forecast schedule (00 and 12 UTC) but the 2× daily cadence is shorter than the operational GDPS's 4× daily early/final analysis structure for some products. This is a tradeoff for the experimental phase and may change before operationalization.
- Users planning operational dependencies on GDPS-EXP should be aware that the experimental designation means change is possible. ECCC has indicated the 2026 replacement timeline but has not committed via formal Service Change Notice.
- For users running long-term backtests or validation, the operational GDPS 9.0.0 / GDPS-EXP 9.0.9 boundary is a meaningful one. The systems differ not just in the AI component but in their data assimilation configurations, sea ice physics, and several atmospheric physics schemes. Pre- and post-replacement skill comparisons should be treated as separate model eras.
- The publicly distributed GEML weights (via HuggingFace, `ECCC-ASTD-MRD/geml`) are unusual among operational AI weather systems and reflect ECCC's relatively open distribution practices. Users wanting to understand the AI nudging input behavior in detail can run GEML inference themselves.

---

## Official documentation
- ECCC MSC datamart README at distribution endpoint:  
  https://dd.weather.gc.ca/today/model_gdps/README.txt
- Experimental GDPS open data documentation:  
  https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps_en/#data-of-the-experimental-global-deterministic-prediction-system
- GDPS Experimental Version 9.0.9 technical specifications (Environment and Climate Change Canada, June 2025)
- GDPS Experimental Version 9.0.9 fact sheet (Environment and Climate Change Canada, June 2025)
- GDPS Experimental Version 9.0.9 technical note (forthcoming as of v9.0.9 specifications publication)

### Key references
- Husain, S.Z., et al. (2024). Leveraging data-driven weather models for improving numerical weather prediction skill through large-scale spectral nudging. *arXiv*, arXiv:2407.06100. https://doi.org/10.48550/arXiv.2407.06100
- Lam, R., et al. (2023). Learning skillful medium-range global weather forecasting. *Science*, 382(6677), 1416–1421. https://doi.org/10.1126/science.adi2336
- McTaggart-Cowan, R., et al. (2020). A Convection Parameterization for Low-CAPE Environments. *Mon. Wea. Rev.*, 148, 4917–4941. https://doi.org/10.1175/MWR-D-20-0020.1
- Buehner, M., et al. (2015). Implementation of Deterministic Weather Forecasting Systems Based on Ensemble–Variational Data Assimilation at Environment Canada. Part I: The Global System. *Mon. Wea. Rev.*, 144, 2532–2559. https://doi.org/10.1175/MWR-D-14-00354.1
- Briegleb, B.P., and Light, B. (2007). A Delta-Eddington multiple scattering parameterization for solar radiation in the sea ice component of the Community Climate System Model. *NCAR Technical Note* NCAR/TN-472+STR.
- Pringle, D.J., et al. (2007). Thermal conductivity of landfast Antarctic and Arctic sea ice. *J. Geophys. Res. Oceans*. https://doi.org/10.1029/2006JC003641
