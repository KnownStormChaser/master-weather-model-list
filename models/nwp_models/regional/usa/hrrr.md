# HRRR (High-Resolution Rapid Refresh)

## What this model is
The High-Resolution Rapid Refresh (HRRR) is NOAA's hourly-updating convection-allowing numerical weather prediction system, designed for very short-range forecasting of rapidly-evolving high-impact weather such as thunderstorms, fog, low clouds, wind shifts, and precipitation systems.

Operating at 3 km horizontal resolution over the contiguous United States and Alaska, HRRR explicitly resolves convective storms rather than parameterizing them, making it the primary operational guidance source for severe weather forecasting, aviation operations, fire weather, wind energy, and short-term decision support across the U.S.

HRRR is foundational infrastructure for several downstream NOAA products:
- **National Blend of Models ([NBM](./nbm.md))** — HRRR is one of the highest-weighted inputs for short-range CONUS guidance
- **Localized Aviation Model Output Statistics (LAMP)** — relies on HRRR cloud fields
- **Real-Time Mesoscale Analysis (RTMA)** — uses HRRR near-surface fields as background
- **National Water Model** — uses HRRR precipitation forecasts as forcing

The current operational version is **HRRRv4** (operational since 2 December 2020), which is the **final version** of HRRR. No further upgrades are planned; the system has been frozen since the joint RAPv5/HRRRv4 implementation. HRRR is expected to be retired alongside [RAP](./rap.md) with the eventual transition to RRFSv2 (see [Future outlook](#future-outlook)).

Since HRRRv4 (December 2020), HRRR also produces operational wildfire smoke forecasts as integrated output fields, replacing the earlier experimental HRRR-Smoke parallel run. Smoke aerosols feed back on radiation and the atmospheric forecast, making HRRR one of the first operational weather models in the U.S. to include direct smoke-meteorology coupling.

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP)
- **Country / region:** United States
- **Development:** NOAA Global Systems Laboratory (GSL); operated at NCEP

---

## What area it covers
- **Operational coverage:** Contiguous United States (CONUS) and Alaska
- **CONUS domain:** 1799 × 1059 grid points on a Lambert conformal projection
- **Alaska domain:** Separate operational domain at the same 3 km resolution
- **Experimental sub-domains** (not operational): Hawaii, Caribbean/Puerto Rico, San Francisco 1 km nest, IMPACTS 1 km nest

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-allowing)
- **Model system / core:** WRF-ARW (Advanced Research WRF, version 3.x)
- **Dynamical formulation:** Non-hydrostatic, fully compressible, on a mass-based (sigma-pressure hybrid) vertical coordinate
- **Convection-allowing:** Yes — deep convection is explicitly resolved at 3 km grid spacing; no cumulus parameterization is used
- **Native horizontal resolution:** 3 km (Lambert conformal projection)
- **Vertical levels:** 50
- **Vertical coordinate:** Hybrid sigma-pressure
- **Model top:** 20 hPa
- **Forecast length (CONUS):**
  - 18 hours hourly (every cycle, 24× daily)
  - 48 hours at 00, 06, 12, 18 UTC (extended cycles)
- **Forecast length (Alaska):**
  - Updated every 3 hours (8× daily): 00, 03, 06, 09, 12, 15, 18, 21 UTC
  - 18 hours at 03, 09, 15, 21 UTC
  - 48 hours at 00, 06, 12, 18 UTC
- **Update frequency:** Hourly for CONUS (24× daily); every 3 hours for Alaska (8× daily)
- **Temporal output resolution:**
  - **Subhourly (15-minute) output** for selected fields during the first 18 forecast hours
  - **Hourly output** from forecast hours 18 to 48 (extended cycles only)

---

## Data assimilation
HRRR runs an **hourly cycling hybrid ensemble-variational** data assimilation system at 3 km, using the Gridpoint Statistical Interpolation (GSI) framework. The CONUS and Alaska domains use different DA architectures:

### CONUS — HRRRDAS (HRRR Data Assimilation System, since v4)
- **36-member 3 km ensemble** running with explicit convective storms
- Each member integrated for 1 hour during the DA cycle
- **Ensemble Kalman Filter** assimilation including direct radar reflectivity assimilation
- The ensemble mean provides the initial conditions for the deterministic HRRR spin-up and analysis process
- Provides flow-dependent background-error covariances for the deterministic HRRR's hybrid analysis
- Partially cycled from RAP analyses to incorporate global-scale information from GFS via the RAP→HRRRDAS chain

### Alaska — RAP-driven initialization (no separate ensemble DA)
- Initial conditions derived from the latest [RAP](./rap.md) analysis (HRRRDAS is CONUS-only)
- Hourly 3 km radar reflectivity assimilation during the 1-hour spin-up period
- Cloud and hydrometeor analysis to initialize stratiform cloud layers

### Common across both domains
- **Radar reflectivity assimilation every 15 minutes during the 1-hour pre-forecast spin-up**, using digital filter initialization to ingest precipitation system structure
- Cloud and hydrometeor analysis to initialize stratiform cloud layers from METAR, satellite, and other observations
- Conventional surface, aircraft, radiosonde, mesonet, and satellite observation assimilation

The combination of dense radar DA, cloud analysis, and (for CONUS) the 36-member ensemble background makes HRRR's initialization one of the most observation-rich in operational mesoscale NWP.

---

## Initial and boundary conditions
- **CONUS initial conditions:** HRRRDAS ensemble mean analysis (HRRRv4); previously RAP analysis (HRRRv1-v3)
- **Alaska initial conditions:** RAP analysis (no HRRRDAS for Alaska)
- **Lateral boundary conditions:** [RAP](./rap.md), updated each cycle from the most recent available RAP forecast

---

## What it provides
Deterministic short-range forecasts of:
- Near-surface temperature, humidity, wind, and pressure
- Convection and severe storm evolution (explicitly resolved)
- Precipitation type, intensity, and accumulation
- Low clouds, ceilings, visibility, and fog
- Fire-weather-relevant fields including near-surface wind and humidity
- Wildfire smoke fields (near-surface smoke concentration and vertically integrated smoke), with smoke aerosols feeding back on radiation and the atmospheric forecast
- Convective indices including CAPE, CIN, helicity, and updraft helicity
- Aviation-relevant fields including icing, turbulence, and ceilings
- Subhourly (15-minute) updates of selected fields for nowcasting use

HRRR is primarily intended for **surface, boundary-layer, and storm-scale analysis** rather than synoptic-scale upper-air diagnostics. NOAA explicitly notes that "full 3-dimensional upper level fields are not provided for the HRRR, as there is little relevant synoptic-scale information gained from the resolution difference between the HRRR and its parent, the RAP."

---

## Relationship to other models
- **[RAP](./rap.md):** Parent model. RAP supplies lateral boundary conditions and (for Alaska HRRR) initial conditions. RAP also provides background fields and global-scale information that flow into HRRRDAS through the partial cycling chain. The two systems share much of the same physics suite and DA infrastructure.
- **[NBM](./nbm.md):** HRRR is one of the most heavily-weighted deterministic inputs for short-range CONUS guidance.
- **[RRFS](./rrfs.md) and [REFS](../../ensemble_models/regional/usa/refs.md):** Future replacement for HRRR and RAP. RRFSv1 implements August 31, 2026 at 12 UTC under SCN 26-48 but does **not** retire HRRR — HRRR retirement is tied to the future RRFSv2 (MPAS-based) timeline. Notably, **HRRR contributes two members (current and 6 h old cycles) to the CONUS and Alaska REFS domains**, so HRRR is an explicit operational input to REFS from August 31, 2026 onward until HRRR itself is retired.

### Downstream NOAA products that depend on HRRR
- **Localized Aviation Model Output Statistics (LAMP)** — uses HRRR cloud fields
- **Real-Time Mesoscale Analysis (RTMA)** — uses HRRR near-surface fields as background
- **National Water Model** — uses HRRR precipitation forecasts as atmospheric forcing
- **National Blend of Models ([NBM](./nbm.md))** — uses all HRRR fields as input

### HRRR-Cast — experimental AI sibling
NOAA OAR/GSL released **HRRR-Cast** in July 2025, an AI-based experimental sibling that emulates HRRR forecasts using a neural network trained on HRRR analysis data. It is not a replacement for the operational HRRR — it runs experimentally at NWS EMC for evaluation, with HRRR-Cast v2 (September 2025) adding ensemble capabilities, increased vertical resolution (12 → 20 levels), and additional surface variables. A future transition to NWS operations is being evaluated, with v3 in development for late 2025 / early 2026 and trained on a larger ~9-year HRRR archive.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data formats:** GRIB2 (primary), with **Zarr** archives also available via the University of Utah / NOAA Open Data Program collaboration
- **Official download locations:**
  - **NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/hrrr/prod/ (real-time, ~10-day rolling)
  - **AWS Open Data (GRIB2):** https://registry.opendata.aws/noaa-hrrr-pds/ (real-time + archive since 2014)
  - **AWS Open Data (Zarr):** `s3://hrrrzarr/` — University of Utah-managed Zarr archive optimized for cloud-native analysis
  - **Microsoft Azure** and **Google Cloud Storage** mirrors are also available via the NOAA Big Data Program
  - **University of Utah HRRR archive:** https://home.chpc.utah.edu/~u0553130/Brian_Blaylock/hrrr.html (historical archive predating cloud Open Data)

The smoke fields are distributed alongside the standard meteorological fields in the operational HRRR output. NOAA's HRRR graphics portal also presents smoke fields under separate "HRRR CONUS Smoke Fields" and "HRRR Alaska Smoke Fields" menu pages, but these are sourced from the same operational model run.

---

## Notes
- HRRR is convection-allowing at 3 km and does not use a cumulus parameterization. Deep convection is explicitly resolved.
- HRRR and [RAP](./rap.md) share much of the same modeling and assimilation infrastructure, including a similar physics suite (MYNN-EDMF PBL, Thompson microphysics, RRTMG radiation, RUC-Smirnova land surface) and the GSI hybrid DA framework. They are best understood as a paired mesoscale (RAP) + storm-scale (HRRR) system.
- **HRRR-Smoke is no longer a separate experimental run** — wildfire smoke prediction has been integrated into operational HRRRv4 since December 2020, with smoke aerosols affecting radiation and feeding back into the meteorological forecast.
- **HRRRv4 is the final version of HRRR.** No further upgrades are planned. The system has been operationally frozen since 2 December 2020 and is in maintenance-only status pending eventual retirement.
- The **Alaska HRRR continues to use RAP-derived initial conditions** rather than a separate HRRRDAS ensemble. HRRRDAS is CONUS-only.
- The University of Utah's MesoWest group maintains a **Zarr-format archive** of HRRR data dating back to 2014, popular for cloud-native analysis workflows. Most operational users continue to use GRIB2.
- HRRR's hourly cycling, dense radar assimilation, and explicit convection make it especially well-suited for severe weather forecasting, fire weather, and aviation. It is among the most heavily-used operational mesoscale models in U.S. severe weather operations.

---

## Future outlook

HRRR is expected to be retired alongside [RAP](./rap.md) as part of the eventual transition to **RRFSv2** (the MPAS-based version of the [Rapid Refresh Forecast System](./rrfs.md)). Unlike the NAM, NAM Nest, HiresW (non-Guam), HREF, and SREF — which are scheduled for retirement on August 31, 2026 under [NWS Service Change Notice 26-48](https://www.weather.gov/media/notification/pdf_2026/scn26-48_RRFS_and_REFS_Implementation.pdf) (May 12, 2026, originally proposed under PNS 25-41) — **HRRR and RAP are explicitly not part of the RRFSv1 retirement wave**. Both are expected to continue operating in parallel with RRFSv1 until the RRFSv2 transition. SCN 26-48 also assigns HRRR an active role in the new REFS, contributing two members (current and 6 h old cycles) to the CONUS and Alaska domains — formalising HRRR as a continuing operational input rather than just a legacy system in maintenance.

No formal Service Change Notice for HRRR retirement has been issued as of May 2026.

---

## Recent version history

### HRRRv4 — operational 2 December 2020 (current; final version)
The last HRRR upgrade. Joint implementation with RAPv5. Headline changes:
- **HRRRDAS** introduced — 36-member 3 km ensemble for storm-scale data assimilation, providing flow-dependent covariances and replacing RAP-derived initial conditions for CONUS
- **Wildfire smoke prediction** integrated into operational HRRR (previously experimental as HRRR-Smoke) — smoke aerosols feed back on shortwave radiation and the atmospheric forecast
- Improved cloud representation for boundary-top clouds, especially shallow cold-air layers with cold-air retention
- Better representation of cloud bands (snow squalls, hurricane bands, lake-effect bands)
- Forecast extension to 48 hours every 6 hours (previously 36 hours in HRRRv3)
- Updated physics suite with improved MYNN-EDMF planetary boundary layer scheme

### HRRRv3 — operational July 2018
- **Alaska domain added**, expanding HRRR beyond CONUS for the first time
- Forecast extension to 36 hours every 6 hours
- Improved physics including PBL and microphysics enhancements

### HRRRv2 — operational 23 August 2016
First major upgrade after operational launch. Added subgrid-scale cloud handling, improved radar DA, and other enhancements documented in Benjamin et al. (2016).

### HRRRv1 — operational 30 September 2014
Initial operational implementation at NCEP. CONUS-only, hourly forecasts to 15 hours, 3 km grid spacing. Notable as the first hourly-updating convection-allowing model in U.S. operations and one of the earliest such systems globally.

---

## Official documentation
- HRRR home page (NOAA GSL): https://rapidrefresh.noaa.gov/hrrr/
- HRRR at EMC: https://emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/hrrr.php
- HRRR Product Inventory at NCEP: https://www.nco.ncep.noaa.gov/pmb/products/hrrr/
- NWS Service Change Notice 20-46 (RAPv5/HRRRv4 implementation): https://www.weather.gov/media/notification/pdf2/scn20-46rap_v5_hrrr_v4_aab.pdf
- NWS PNS 25-41 (RRFS legacy model cessation, NOT including HRRR): https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- AWS Open Data (GRIB2): https://registry.opendata.aws/noaa-hrrr-pds/
- HRRR-Cast (NOAA GSL): https://gsl.noaa.gov/news/new-upgrades-to-hrrr-cast-noaas-experimental-ai-powered-regional-model

### Key references
- Dowell, D. C., et al. (2022). *The High-Resolution Rapid Refresh (HRRR): An Hourly Updating Convection-Allowing Forecast Model. Part I: Motivation and System Description.* Wea. Forecasting, 37, 1371–1395. https://doi.org/10.1175/WAF-D-21-0151.1
- James, E. P., et al. (2022). *The High-Resolution Rapid Refresh (HRRR): An Hourly Updating Convection-Allowing Forecast Model. Part II: Forecast Performance.* Wea. Forecasting, 37, 1397–1417.
- Benjamin, S. G., et al. (2016). *A North American Hourly Assimilation and Model Forecast Cycle: The Rapid Refresh.* Mon. Wea. Rev., 144, 1669–1694. https://doi.org/10.1175/MWR-D-15-0242.1
