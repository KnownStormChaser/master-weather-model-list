# CAMS Global Greenhouse Gas Forecasts

## What this model is
The **CAMS Global Greenhouse Gas Forecasts** are an operational global atmospheric composition forecast specifically for the long-lived greenhouse gases **carbon dioxide (CO₂)** and **methane (CH₄)**, produced daily by the European Centre for Medium-Range Weather Forecasts (ECMWF) on behalf of the **Copernicus Atmosphere Monitoring Service (CAMS)**.

The system is built on ECMWF's Integrated Forecasting System (IFS) — the same model that produces ECMWF's operational weather forecasts — with additional modules for greenhouse gas tracking, surface flux representation, and 4D-Var assimilation of satellite column retrievals. It is a separate operational production from the main [CAMS Global](../eu/cams-global.md) product (which handles reactive gases and aerosols), though both run on the IFS and share architectural lineage.

The GHG forecast provides daily 5-day forecasts at approximately 9 km resolution (high-resolution forecast) with a separate 4D-Var analysis at approximately 25 km resolution. Because the analysis depends on satellite retrievals that arrive with a 2–4 day latency, the analysis itself is not distributed in near-real-time; instead, the high-resolution forecast is initialized from a 4-day forecast of the analysis experiment, allowing the forecast product to be delivered with minimal delay.

---

## Who runs it
- **Production Unit:** European Centre for Medium-Range Weather Forecasts (ECMWF)
- **Programme:** Copernicus Atmosphere Monitoring Service (CAMS), implemented by ECMWF on behalf of the European Union
- **Country:** Intergovernmental (ECMWF headquartered in Reading, UK, with supercomputing in Bologna, Italy)
- **Distribution:** Atmosphere Data Store (ADS)

---

## What area it covers
- **Coverage:** Global
- **Native grid:** Reduced Gaussian grid O1280 (forecast) / Tco399 (O400) (analysis)
- **Distributed grid:** Regular latitude-longitude at ~0.10° (forecast) and ~0.25° (analysis)
- **Vertical levels:** 137 hybrid sigma-pressure levels from surface to 0.01 hPa (IFS operational configuration)

---

## Basic details
- **Model type:** Global deterministic atmospheric composition forecast (long-lived greenhouse gases)
- **Core model:** IFS (Integrated Forecasting System) with greenhouse gas modules developed within CAMS and its precursor projects GEMS and MACC
- **Current IFS cycle:** CY49R1 (as of time of writing — ECMWF upgrades the IFS cycle approximately once per year)
- **Tracers:** CO₂ and CH₄
- **Forecast resolution:** ~9 km (approximately 0.10°) — high-resolution forecast
- **Analysis resolution:** ~25 km (approximately 0.25°) — 4D-Var analysis
- **Forecast length:** 5 days
- **Update frequency:** Daily
- **Data assimilation:** 4D-Var with satellite column retrievals
- **Coupling:** Online-coupled GHG tracers within IFS (not externally coupled)
- **Distribution delay:** Forecast runs a few hours behind real time; some meteorological fields delayed 5 days due to licensing
- **Documentation:** IFS Documentation Part VIII: Atmospheric Composition

---

## How it relates to other CAMS global products

CAMS runs global atmospheric composition production in **two operational modes**, which are separate production systems:

1. **Real-time chemistry and aerosol** — the main [CAMS Global](../eu/cams-global.md) product, handling reactive gases (O₃, CO, NO₂, SO₂), aerosols (AOD, dust, sea salt, black carbon, organic matter, sulfate), and other short-lived species.
2. **Real-time long-lived greenhouse gases** — this product, handling CO₂ and CH₄.

The two systems share the IFS model base but assimilate different observations, use different analysis configurations, and are distributed as separate products with different grids and delivery schedules. They are not different variables of the same forecast — they are distinct operational runs.

---

## Data assimilation
The GHG forecast uses a **4D-Var data assimilation scheme** to constrain the initial atmospheric state from satellite column retrievals. Because these retrievals arrive with a 2–4 day latency, the analysis is run 4 days behind real time.

### Assimilated observations
- **GOSAT/TANSO:**
  - Column-averaged CO₂ (XCO₂) — provided by University of Bremen (UB)
  - Column-averaged CH₄ (XCH₄) — provided by SRON (Netherlands Institute for Space Research)
- **METOP-C/IASI:**
  - Mid-tropospheric CH₄ (MT-CH₄) — provided by Laboratoire de Météorologie Dynamique (LMD)
  - Mid-tropospheric CO₂ (MT-CO₂) — provided by LMD, using combined IASI/METOP and AMSU measurements

The surface fluxes that drive the tracer concentrations are not optimized by the DA system itself; flux optimization is handled separately by the CAMS flux inversion system (a different product) and used as boundary conditions.

### Forecast initialization strategy
Because the 4D-Var analysis is not available in near-real-time (due to the 2–4 day satellite retrieval latency), the high-resolution forecast cannot be initialized from the analysis directly. Instead, the forecast is initialized from a **4-day forecast of the analysis experiment**, which brings the initial condition up to approximately real time. This is an unusual architectural choice driven by the observation latency constraint, and users should be aware that the initial conditions for the GHG forecast are not analysis-quality in the same sense as for a standard NWP forecast.

---

## What it provides
The forecast provides gridded CO₂ and CH₄ concentration fields and related meteorological and surface flux variables:

### Greenhouse gas fields
- CO₂ volume mixing ratio (surface and multiple pressure levels)
- CH₄ volume mixing ratio (surface and multiple pressure levels)
- CO₂ and CH₄ column-integrated concentrations (total column)
- CO₂ biogenic fluxes (vegetation uptake and soil release)

### Meteorological fields
- Standard IFS atmospheric fields (subject to a 5-day delay for certain fields due to licensing constraints on operational meteorological fields outside the general CAMS data licence)

### Surface fluxes (prescribed boundary conditions)
- Biogenic CO₂ fluxes from the CHTESSEL module (Boussetta et al., 2013), coupled online to radiation, precipitation, temperature, humidity, and soil moisture via the ECLand surface model
- Biogenic flux adjustment via the Biogenic Flux Adjustment System (BFAS; Agustí-Panareda et al., 2016)
- Anthropogenic emissions from CAMS emission inventories
- Biomass burning emissions from CAMS
- Ocean fluxes (climatological)
- CH₄ soil sink, wetland fluxes, termite and wild animal emissions

---

## Data availability
- **Is the data free?** Yes (registration required for ADS access)
- **Is the data downloadable?** Yes
- **Data format:** GRIB and NetCDF
- **Distribution channel:** Copernicus Atmosphere Data Store (ADS)
- **Product name on ADS:** CAMS global greenhouse gas forecasts
- **Dataset identifier:** `cams-global-greenhouse-gas-forecasts`
- **Official access:** https://ads.atmosphere.copernicus.eu/datasets/cams-global-greenhouse-gas-forecasts
- **Documentation:** https://confluence.ecmwf.int/pages/viewpage.action?pageId=394237962
- **Licence:** CAMS data licence (free with registration)
- **Delivery timing:** Forecast available a few hours behind real time; some meteorological fields held back 5 days due to licensing

### Data availability notes
Some meteorological fields included in the forecast fall outside the general CAMS data licence because they are operational ECMWF NWP outputs. These fields are only made available with a 5-day delay. Users needing real-time meteorological fields should obtain them from a separate source (e.g., ECMWF's open data, IFS AIFS, or GFS) rather than relying on the CAMS GHG forecast.

Because the ADS is not a 24/7 time-critical operational service in the same sense as, for example, NOAA's operational product servers, delivery times may occasionally vary. ECMWF does not guarantee specific delivery times for ADS products.

---

## Version history
The CAMS GHG forecast has been continuously operational for roughly a decade, with periodic IFS cycle upgrades (approximately annual) that refresh both the underlying meteorological model and the atmospheric composition modules. Notable references for the model development:

- Agustí-Panareda et al. (2014) — initial development of CO₂ forecasting in IFS
- Massart et al. (2014, 2016) — 4D-Var data assimilation of CO₂ column retrievals
- Agustí-Panareda et al. (2016) — Biogenic Flux Adjustment System (BFAS) development
- Agustí-Panareda et al. (2017, 2022) — ongoing system assessment
- Boussetta et al. (2021) — ECLand surface model update

Each IFS cycle upgrade is documented in the ECMWF model change log: https://www.ecmwf.int/en/forecasts/documentation-and-support/changes-ecmwf-model/ifs-documentation

---

## Relationship to other Copernicus atmospheric products

### Companion CAMS global products
- **[CAMS Global](../eu/cams-global.md)** — sibling real-time product handling reactive gases and aerosols (O₃, CO, NO₂, SO₂, AOD, dust, sea salt, black carbon, organic matter, sulfate). Same IFS model base, different DA configuration and assimilated observations. Delivered on the ADS alongside the GHG forecast.
- **CAMS Greenhouse Gas Reanalysis (EGG4)** — reanalysis counterpart covering 2003–2020 (and extending). Uses the same IFS-with-GHG architecture as the forecast but with a longer and more homogeneous assimilation window. **Not listed in this repository** as this repository focuses on operational near-real-time forecasts rather than reanalyses.
- **CAMS Global Inversion-Optimised Greenhouse Gas Fluxes** — inverse modelling product producing net surface fluxes of CO₂, CH₄, and N₂O from observed concentrations. Architecturally different (flux inversion rather than forward forecast) and not an NWP-family product.

### Satellite observations used
The forecast assimilates products from third-party providers that are themselves built on ESA Sentinel and NASA/JAXA satellite missions:
- **GOSAT/TANSO** (JAXA, launched 2009) — column retrievals from UB and SRON
- **METOP-C/IASI** (EUMETSAT/CNES) — mid-tropospheric retrievals from LMD

### Relationship to N₂O
Unlike the inverse modelling and reanalysis products, the operational CAMS GHG forecast **does not include nitrous oxide (N₂O)** as an actively forecast tracer. N₂O forecasts are handled through the separate CAMS global inversion-optimised greenhouse gas fluxes product.

---

## Notes
- The forecast is of particular operational interest for applications that need near-real-time CO₂ and CH₄ fields, such as atmospheric transport modelling, plume dispersion studies, emission verification research, and coupling with regional air quality models that benefit from realistic GHG boundary conditions.
- Users looking for **long-term consistent time series** for trend analysis should consider the CAMS greenhouse gas reanalysis (EGG4, 2003–onward) or the CAMS global inversion-optimised fluxes product instead — these provide homogeneous datasets over decades, whereas the operational forecast changes with each IFS cycle upgrade.
- The system's separation of forecast and analysis configurations — with different resolutions (~9 km vs ~25 km) and the 4-day analysis latency — is unusual for an operational NWP-family product. It reflects the reality that greenhouse gas satellite retrievals are not available in the same real-time observational stream as conventional NWP observations. This is a good example of how atmospheric composition forecasting inherits different operational constraints than standard weather forecasting despite sharing the same model base.
- The forecast is one-way: surface flux estimates flow into the forecast as boundary conditions, but the forecast does not feed back to update the flux estimates. Surface flux optimization is handled separately in the CAMS inversion product.

---

## Official documentation
- Product page: https://ads.atmosphere.copernicus.eu/datasets/cams-global-greenhouse-gas-forecasts
- Technical documentation: https://confluence.ecmwf.int/pages/viewpage.action?pageId=394237962
- CAMS programme: https://atmosphere.copernicus.eu/
- ECMWF IFS documentation: https://www.ecmwf.int/en/forecasts/documentation-and-support/changes-ecmwf-model/ifs-documentation
- IFS CY49R1 Part VIII (Atmospheric Composition): see ECMWF documentation link above

### Key references
- Agustí-Panareda, A., et al. (2014). Forecasting global atmospheric CO₂.
- Agustí-Panareda, A., et al. (2016). A biogenic CO₂ flux adjustment scheme for the mitigation of large-scale biases in global atmospheric CO₂ analyses and forecasts. *Atmos. Chem. Phys.*, 16, 10399–10418.
- Agustí-Panareda, A., et al. (2017, 2022). Modelling CO₂ weather — why horizontal resolution matters.
- Boussetta, S., et al. (2013, 2021). Natural carbon dioxide exchanges in the ECMWF Integrated Forecasting System.
- Fleming, Z. L., et al. (2015). CAMS reactive gas and aerosol product development.
- Massart, S., et al. (2014, 2016). 4D-Var assimilation of CO₂ in IFS.
