# HRDPS (High Resolution Deterministic Prediction System)

## What this model is
The High Resolution Deterministic Prediction System (HRDPS) is Canada's convection-permitting numerical weather prediction system, designed to forecast small-scale and rapidly evolving weather phenomena such as thunderstorms, localized wind patterns, and heavy precipitation.

HRDPS is a limited-area (LAM) configuration of the Global Environmental Multiscale (GEM) model running at ~2.5 km horizontal resolution over a pan-Canadian domain. It is piloted by the GDPS 10 km uncoupled component (GDPS-9.0.0 G0) for lateral and upper boundary conditions, takes its surface initial conditions from a coupled 2.5 km configuration of the Canadian Land Data Assimilation System (CaLDAS), and runs its own 4DEnVar atmospheric assimilation in a continuous cycle. Together with [GDPS](../../global/canada/gem-global.md) and [RDPS](./rdps.md), HRDPS is one of the main sources of NWP guidance at the Meteorological Service of Canada for forecast days 1 and 2.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Pan-Canadian — Canada (excluding the Canadian Archipelago), northern United States, and adjacent oceans
- **Grid:** Rotated latitude–longitude LAM at uniform 0.0225° (~2.5 km) resolution
  - **Computational (model) grid:** 2582 × 1332 grid points — the full pan-Canadian forecast domain (HRDPS 7.0.0 technical specifications)
  - **Distributed (output) grid:** 2540 × 1290 grid points, rotated south pole at 36.089°S / 245.305°E — the grid in the public GRIB2 (v7.0.0 fact sheet; file-verified)

---

## Basic details
- **Model type:** Deterministic NWP (convection-permitting / convection-allowing)
- **Model system / core:** GEM (Global Environmental Multiscale) version 5.2.0
- **Dynamical formulation:** Non-hydrostatic primitive equations
- **Convection-allowing:** Yes (~2.5 km horizontal resolution)
- **Horizontal resolution:** ~2.5 km (0.0225°)
- **Vertical levels:** 62 staggered hybrid levels with SLEVE coordinate (forecast step). The continuous assimilation cycle background runs on 80 staggered hybrid levels with model lid at 0.1 hPa
- **Model top:** 1 hPa (forecast step), with nesting over the top 3 levels
- **Forecast length:** Up to ~48 hours
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC). Both the continuous-cycle final analysis and the early analysis/forecast step run at all four cycles
- **Time step:** 60 seconds
- **Time integration:** Implicit, semi-Lagrangian (3D), 2 time-level
- **Temporal output resolution:** Typically hourly

---

## Data assimilation
- **Data assimilation:** Yes — HRDPS produces its own atmospheric analysis (since v6.0.0)
- **Method:** Four-dimensional ensemble–variational (4DEnVar) with flow-dependent background-error covariances from the same 256-member LETKF ensemble of GEPS 8.0.0 backgrounds at 25 km used by GDPS and RDPS, with scale-dependent horizontal localization. Analysis increments are computed on the 0.22° (~25 km) global Gaussian grid, then clipped to the LAM domain
- **Cadence:** A continuous final analysis/background cycle every 6 hours (00, 06, 12, 18 UTC) with a 7-hour observation cutoff supplies the next background; an early analysis at the same four cycles with a 2-hour cutoff initializes the 48-hour forecast. Both cycles use a 6-hour assimilation window centered on the synoptic hour
- **Initialization:** 4D Incremental Analysis Update (4D-IAU) applied over the 6-hour window (t-3 to t+3 hours), identical scheme to GDPS
- **Radiative transfer model:** RTTOV v13, with slant-path radiative transfer
- **Radar assimilation:** Inferred precipitation rates from radar reflectivities are assimilated every 6 minutes via Latent Heat Nudging (Jacques et al., 2018) — this is a feature unique to HRDPS in the ECCC operational suite
- **Notable observations assimilated:** Microwave radiances (AMSU-A, MHS, SSMIS, ATMS, MWHS-2); infrared radiances (AIRS, IASI, CrIS); geostationary imager radiances; GPS-RO refractivity; AMVs; scatterometer winds; ground-based GPS ZTD; radar reflectivities (via LHN); radiosondes; surface stations; aircraft. *(Note: AIRS is still listed in the HRDPS 7.0.0 spec, in contrast to RDPS 9.0.0 where AIRS is no longer assimilated.)*
- **Satellite radiance bias correction:** Coefficients computed by GDPS-9.0.0 from a separate 3DEnVar analysis without radiances and reused by HRDPS

---

## Initial and boundary conditions
- **Lateral and upper boundary conditions:** Provided by an early forecast from **GDPS-9.0.0 G0** — the 10 km uncoupled component of GDPS — updated hourly
- **Atmospheric upper-air initial conditions:** From HRDPS's own continuous 4DEnVar assimilation cycle
- **Hydrometeor initial conditions:** Recycled from the 6-hour background of the HRDPS continuous assimilation cycle
- **Surface initial conditions:** Provided by a coupled 2.5 km configuration of the **Canadian Land Data Assimilation System (CaLDAS)**, which is coupled to the HRDPS continuous assimilation cycle
- **Sea-surface temperature and sea-ice coverage:** From the GDPS-9.0.0 G6 global surface analysis; sea-ice cover and lead fraction specifically from GIOPS-3.5.0
- **Sea ice thickness:** Free evolution within the HRDPS continuous assimilation cycle

---

## What it provides
Deterministic high-resolution short-range forecasts of:
- Convective precipitation and thunderstorms
- Near-surface wind, temperature, and humidity
- Localized hazardous weather
- Boundary-layer and terrain-driven effects
- Cloud, rain, rime ice, and deposition ice mass and number mixing ratios (P3 two-moment microphysics)
- Mean sea-level pressure, relative humidity, QPF, precipitation rate, omega, cloud amount, boundary layer height, and many others

HRDPS is Canada's highest-resolution operational deterministic forecast system and is used for short-range, local-scale forecasting.

---

## Surface and physics
- **Surface scheme:** Mosaic approach with 5 surface types — land, water, sea ice, glacier, **and urban**. ISBA (Bélair et al., 2003a, 2003b) for the land component; **TEB (Masson, 2000) for the urban component** — distinguishing HRDPS from GDPS/RDPS, which do not use TEB
- **Subgrid-scale orography:** Turbulent Orographic Form Drag (TOFD; Beljaars et al., 2004) with alpha = 50. **No orographic gravity wave drag, non-orographic gravity wave drag, or low-level blocking schemes** — appropriate for the convection-permitting scale where these effects are largely resolved
- **Surface roughness over water:** Charnock formulation for momentum; Deacu formulation for thermal roughness
- **Radiation:** Li–Barker correlated-k distribution (called every 15 minutes)
- **Boundary-layer mixing:** TKE-based with statistical subgrid-scale clouds; Blackadar mixing length; Richardson number hysteresis (McTaggart-Cowan & Zadra, 2015)
- **Stable precipitation:** **Predicted Particle Properties (P3) bulk two-moment microphysics** (Morrison & Milbrandt, 2015; Milbrandt & Morrison, 2016) — a more sophisticated microphysics scheme than the Sundqvist scheme used by GDPS/RDPS
- **Shallow convection:** Kuo Transient scheme (Bélair et al., 2005)
- **Deep convection:** Kain–Fritsch scheme (Kain & Fritsch, 1990, 1993)
- **Topography:** Derived from the GMTED75 database at 250 m horizontal resolution; RC5 low-pass filter applied to the topography field

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
- **Official download location:** https://dd.weather.gc.ca/today/model_hrdps/continental/2.5km/
- **Open data documentation:** https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/readme_hrdps-datamart_en/

---

## Notes
- HRDPS operates in the convection-permitting regime and **does not use a deep convection parameterization** in the gray-zone sense the way GDPS/RDPS do — though a Kain-Fritsch scheme is still active. The combination of P3 two-moment microphysics, TEB urban surface, and 60-second time step at 2.5 km is the operational signature of an HRDPS forecast.
- HRDPS retains the **continuous assimilation cycle** that RDPS gave up in version 9.0.0. Each HRDPS analysis serves as the initial condition for a short-term background forecast that becomes the first guess for the next analysis 6 hours later. This is a meaningfully different DA architecture from the current RDPS, which now runs a single global-domain analysis driven by GDPS backgrounds.
- The forecast step runs on **62 levels with a SLEVE vertical coordinate**, while the assimilation-cycle background runs on **80 levels** without SLEVE. This split — different vertical configurations for the two stages — is a distinctive design choice. SLEVE attenuates fine-scale orography contributions to upper vertical coordinate surfaces (Husain et al., 2020), which matters at 2.5 km over complex terrain.
- HRDPS is piloted specifically by **GDPS-9.0.0 G0**, the 10 km uncoupled GDPS component, not the standard 15 km GDPS. The same G0 component pilots HRDPS-West and provides atmospheric forcing to CIOPS-West where HRDPS coverage is incomplete.
- HRDPS plays a role broadly comparable to the U.S. **HRRR**, though with different update frequency, domain strategy, and DA architecture (HRDPS uses 4DEnVar with radar LHN; HRRR uses 3DEnVar with radar reflectivity DA via GSI).
- Open data licensing is genuinely open — no registration required, direct file access via the Datamart. Same as GDPS, RDPS, GIOPS, RIOPS, and CIOPS.

---

## Recent version history

### HRDPS v7.0.0 — operational June 11, 2024 (current)
Implemented at the 12Z system run on June 11, 2024 as part of Innovation Cycle 4 (IC-4), alongside GDPS 9.0.0, RDPS 9.0.0, and GEPS 8.0.0.

Headline changes:
- **GEM upgraded to version 5.2.0**
- **Pilot upgraded to GDPS-9.0.0 G0** (the 10 km uncoupled GDPS component) for lateral and upper boundary conditions
- **4DEnVar atmospheric analysis** with a fully ensemble-derived background-error covariance built from 256-member LETKF backgrounds from **GEPS 8.0.0** at 25 km; Hessian matrix cycling removed
- **Radiative transfer model upgraded to RTTOV v13**
- Analysis increments now computed directly on the staggered vertical levels
- Background check made using standard deviations defined on the vCode=5005 vertical levels
- Modifications of hyperspectral infrared QC for albedo changes; correction to water saturation functions to adhere to LV18 standard
- **3DEnVar reference state for radiance bias correction** (computed by GDPS), replacing 3DVar
- **Forecast step uses 62 levels with SLEVE coordinate** (Husain et al., 2020); model lid at 1 hPa with nesting over 3 levels
- Sea ice cover and lead fraction now sourced from **GIOPS-3.5.0**
- TOFD alpha parameter set to 50
- **Latent Heat Nudging from radar reflectivities** every 6 minutes (Jacques et al., 2018) retained
- Cutoff times: 7 hours for the continuous-cycle final analysis; 2 hours for the early analysis/forecast step

---

## Official documentation
- HRDPS open data page: https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/readme_hrdps_en/
- HRDPS Datamart documentation: https://eccc-msc.github.io/open-data/msc-data/nwp_hrdps/readme_hrdps-datamart_en/
- Technical specifications (current): https://collaboration.cmc.ec.gc.ca/cmc/CMOI/product_guide/docs/tech_specifications/tech_specifications_HRDPS_e.pdf
- ECCC operational systems changelog: https://eccc-msc.github.io/open-data/msc-data/changelog_multisystems_en/
- Licence: https://eccc-msc.github.io/open-data/licence/readme_en/

### Key references
- Buehner, M., et al. (2015). Implementation of Deterministic Weather Forecasting Systems Based on Ensemble–Variational Data Assimilation at Environment Canada. Part I: The Global System. *Mon. Wea. Rev.*, 144, 2532–2559.
- Caron, J.-F., et al. (2015). Implementation of Deterministic Weather Forecasting Systems Based on Ensemble–Variational Data Assimilation at Environment Canada. Part II: The Regional System. *Mon. Wea. Rev.*, 143, 2560–2580.
- Carrera, M., S. Bélair, and B. Bilodeau (2015). The Canadian Land Data Assimilation System (CaLDAS): Description and Synthetic Evaluation Study. *J. Hydrometeor.*, 16, 1293–1314.
- Husain, S. Z., C. Girard, L. Separovic, A. Plante, and S. Corvec (2020). On the Progressive Attenuation of Finescale Orography Contributions to the Vertical Coordinate Surfaces within a Terrain-Following Coordinate System. *Mon. Wea. Rev.*, 148, 4143–4158.
- Jacques, D., D. Michelson, J.-F. Caron, and L. Fillion (2018). Latent Heat Nudging in the Canadian Regional Deterministic Prediction System. *Mon. Wea. Rev.*, 146, 3995–4014.
- Masson, V. (2000). A Physically-Based Scheme for the Urban Energy Budget in Atmospheric Models. *Bound.-Layer Meteor.*, 94, 357–397.
- Morrison, H. and J. A. Milbrandt (2015). Parameterization of Cloud Microphysics Based on the Prediction of Bulk Ice Particle Properties. Part I: Scheme Description and Idealized Tests. *J. Atmos. Sci.*, 72, 287–311.
