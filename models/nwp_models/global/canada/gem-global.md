# GDPS (Global Deterministic Prediction System)

## What this model is
The Global Deterministic Prediction System (GDPS) is Canada's primary global numerical weather prediction system, providing deterministic forecasts of atmospheric conditions worldwide.

GDPS is based on the Global Environmental Multiscale (GEM) atmospheric model and operates as a coupled atmosphere–ocean–sea ice prediction system, supplying large-scale guidance and boundary conditions for Canada's regional and high-resolution forecast systems.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Global
- **Atmospheric grid:** Yin–Yang horizontal grid, 2 × 2047 × 683 grid points
- **Ice-ocean grid:** Global 0.25° with 50 z-levels

---

## Basic details
- **Model type:** Deterministic global NWP, coupled to ocean and sea ice
- **Model system / core:** GEM (Global Environmental Multiscale) version 5.2
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

## Data availability
- **Is the data free?** Yes (no registration required for MSC Open Data)
- **License:** Environment and Climate Change Canada Data Servers End-use Licence (attribution required; commercial use permitted) — https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Distribution channels:**
  - **MSC Datamart:** Direct file access — primary download channel
  - **MSC GeoMet:** Geospatial web services (WMS, WCS, OGC API)
  - **MSC AniMet:** Visualization service
- **Official download location:** https://dd.weather.gc.ca/today/model_gem_global/15km/grib2/lat_lon/
- **Open data documentation:** https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps-datamart_en/

---

## Notes
- GDPS uses a global **Yin–Yang grid**, a distinctive feature of modern GEM configurations.
- Background-error covariances in 4DEnVar come entirely from the 256-member LETKF ensemble of GEPS 8.0.0 backgrounds (no NMC component), with scale-dependent horizontal localization.
- GDPS is part of a tightly integrated suite of Canadian operational prediction systems sharing the GEM core, the GEPS ensemble for DA covariances, and GIOPS for ice-ocean initialization.
- Open data licensing is genuinely open — no registration required, direct file access via the Datamart. Same as GIOPS, RIOPS, and CIOPS.

---

## Recent version history

### GDPS v9.0.0 — operational June 11, 2024 (current)
Implemented at the 12 UTC run on June 11, 2024. Major work was on the data assimilation component, with smaller changes to the atmospheric and ice-ocean forecast components.

Headline changes:
- **GEM upgraded to version 5.2**
- **4DEnVar with a fully ensemble-derived B matrix** built from 256-member LETKF backgrounds from the new **GEPS 8.0.0** (no NMC component); Hessian matrix cycling removed
- **Radiative transfer model upgraded to RTTOV v13**
- **All-sky assimilation extended to ATMS temperature and AMSUB/MHS humidity channels** (cloud-affected radiances), in addition to the existing all-sky AMSU-A
- **Analysis increment vertical levels** moved from L80/vCode 5002 to L84/vCode 5005, matching GEPS
- **3DEnVar reference state for radiance bias correction**, replacing 3DVar
- Modifications to hyperspectral infrared QC for albedo changes; reduced horizontal localization at the largest scales
- Additional ozone observations: OMPS-NM, TROPOMI, OMPS-NP added; piecewise weighted-averaging interpolation and weighted integration of total column ozone as a strong constraint
- **4D-IAU** initialization with recycling of an expanded set of physics variables (total condensate, cloud fraction, TKE, turbulent regime, mixing length, friction velocity, PBL height, PBL cloud water/fraction, PBL flux enhancement factor)
- **Iterative solver of the Euler equations** (Pacull and Aubert, 2014) and improved trapeze-cubic semi-Lagrangian trajectory calculations
- **Ice-ocean component:** initialization moved to GIOPS 3.5.0; **CICE upgraded to 6.2.0** with Delta-Eddington radiation and "bubbly" thermal conductivity; new Mean Dynamic Topography for SLA assimilation; sea-ice concentration analysis now done in MIDAS
- Increased momentum roughness length over sea ice (from 0.16 m to 0.54 m)

---

## Related: experimental successor

ECCC distributes an experimental successor to the operational GDPS at https://dd.weather.gc.ca/today/model_gdps/ which, per ECCC's own statement at that endpoint, is expected to replace the operational GDPS in 2026. The experimental system (GDPS Experimental Version 9.0.9, implemented March 5, 2025) features hybrid AI-physics forecasting via spectral nudging toward the [GEML](./gdps-geml.md) data-driven weather model, along with substantial updates to data assimilation, sea ice physics, and other components.

See [`gdps-exp.md`](./gdps-exp.md) for full documentation of the experimental system.

---

## Official documentation
- GDPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_gdps/readme_gdps-datamart_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_GDPS_e.pdf
- ECCC operational systems changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Buehner, M., et al. (2015). Implementation of Deterministic Weather Forecasting Systems Based on Ensemble–Variational Data Assimilation at Environment Canada. Part I: The Global System. *Mon. Wea. Rev.*, 144, 2532–2559. https://doi.org/10.1175/MWR-D-14-00354.1
- Côté, J., et al. (1998). The Operational CMC-MRB Global Environmental Multiscale (GEM) Model: Part I — Design Considerations and Formulation. *Mon. Wea. Rev.*, 126, 1373–1395.
- Girard, C., et al. (2014). Staggered Vertical Discretization of the Canadian Environmental Multiscale (GEM) Model Using a Coordinate of the Log-Hydrostatic-Pressure Type. *Mon. Wea. Rev.*, 142, 1183–1196.
- Qaddouri, A., and V. Lee (2011). The Canadian Global Environmental Multiscale model on the Yin–Yang grid system. *Q. J. R. Meteorol. Soc.*, 137, 1913–1926.
- Shahabadi, M. B., and M. Buehner (2024). Implementation of All-Sky Assimilation of Microwave Humidity Sounding Channels in Environment Canada's Global Deterministic Weather Prediction System. *Mon. Wea. Rev.*, 152, 1027–1038.
