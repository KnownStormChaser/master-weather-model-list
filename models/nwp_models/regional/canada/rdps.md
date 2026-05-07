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
- **Temporal output resolution:** Typically hourly to 3-hourly

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
- **Official download locations:** RDPS GRIB2 output is published on two grids with different coverage and filename conventions:
  - **Polar stereographic grid** (covers North America and adjacent waters; 935 × 824 at 10 km resolution at 60°N): https://dd.weather.gc.ca/today/model_gem_regional/10km/grib2/
    - Available for all four runs (00, 06, 12, 18 UTC)
    - Legacy filename nomenclature: `CMC_reg_{Variable}_{LevelType}_{level}_ps10km_{YYYYMMDDHH}_P{hhh}.grib2`
  - **Rotated lat-lon grid** (covers a larger area also including the Caribbean, all of Mexico, and part of Northern Europe; 1102 × 1076 at 0.09°): https://dd.weather.gc.ca/today/model_rdps/10km/
    - Available only for the 00 and 12 UTC runs
    - ISO-8601 filename nomenclature: `{YYYYMMDD}T{HH}Z_MSC_RDPS_{VAR}_{LVLTYPE-LVL}_RLatLon0.09_PT{hhh}H.grib2`
- **Open data documentation:** https://eccc-msc.github.io/open-data/msc-data/nwp_rdps/readme_rdps-datamart_en/

---

## Notes
- RDPS shares the same modern GEM dynamical core, vertical structure, and 4DEnVar assimilation framework as GDPS. The 9.0.0 release tightened that integration further by replacing the standalone regional LAM continuous assimilation cycle with a single global-domain analysis driven by GDPS backgrounds.
- The shift from a limited-area continuous-cycle configuration to a global 10 km configuration simplifies system maintenance and improves consistency with global guidance, while the rotated LAM output grid preserves the regional product distribution users rely on.
- Although the model is run on the global Yin–Yang grid, RDPS remains the *regional* deterministic guidance product because forecasts are distributed on the rotated North America LAM grid and tuned for short-range (1–2 day) regional skill.
- RDPS plays a role similar to the U.S. **NAM**, but with tighter integration into a global modeling framework.
- Open data licensing is genuinely open — no registration required, direct file access via the Datamart. Same as GDPS, GIOPS, RIOPS, and CIOPS.

---

## Recent version history

### RDPS v9.0.0 — operational June 11, 2024 (current)
Implemented as part of Innovation Cycle 4 (IC-4), alongside GDPS 9.0.0. The biggest change is structural: the RDPS no longer runs a standalone regional LAM with its own continuous assimilation cycle.

Headline changes:
- **Removal of the regional LAM continuous assimilation cycle.** Replaced by a single 4DEnVar analysis every 6 hours on the **global Yin–Yang domain at 10 km**, using GDPS-9.0.0 background fields as the first guess. The (R2) final analysis and background step is also removed
- **Forecast still distributed on the rotated North America LAM grid** (1140 × 1045 at 0.09°), so end-user products are unchanged in geometry
- **GEM upgraded to version 5.2**
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

---

## Official documentation
- RDPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_rdps/readme_rdps-datamart_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_RDPS_e.pdf
- ECCC operational systems changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Caron, J.-F., et al. (2015). Implementation of Deterministic Weather Forecasting Systems Based on Ensemble–Variational Data Assimilation at Environment Canada. Part II: The Regional System. *Mon. Wea. Rev.*, 143, 2560–2580. https://doi.org/10.1175/MWR-D-14-00353.1
- Côté, J., et al. (1998). The Operational CMC-MRB Global Environmental Multiscale (GEM) Model: Part I — Design Considerations and Formulation. *Mon. Wea. Rev.*, 126, 1373–1395.
- Girard, C., et al. (2014). Staggered Vertical Discretization of the Canadian Environmental Multiscale (GEM) Model Using a Coordinate of the Log-Hydrostatic-Pressure Type. *Mon. Wea. Rev.*, 142, 1183–1196.
- McTaggart-Cowan, R., et al. (2019). Modernization of Atmospheric Physics Parameterization in Canadian NWP. *J. Adv. Model. Earth Syst.*, 11, 3593–3635.
- Shahabadi, M. B., and M. Buehner (2021). Towards All-sky Assimilation of Microwave Temperature Sounding Channels in Environment Canada's Global Deterministic Weather Prediction System. *Mon. Wea. Rev.*, 149, 3725–3738.
