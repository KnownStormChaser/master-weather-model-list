# RAP (Rapid Refresh)

## What this model is
The Rapid Refresh (RAP) is NOAA's hourly-updating mesoscale numerical weather prediction model covering North America, providing short-range deterministic guidance for rapidly-evolving weather such as fronts, wind shifts, low-level temperature changes, and aviation-relevant atmospheric conditions.

RAP serves three operationally important roles:
1. **Standalone forecast system** — providing 13 km mesoscale forecasts updated every hour, used widely for aviation forecasting, mesoscale analysis, and short-range decision support
2. **Parent model for HRRR** — supplying lateral boundary conditions and first-guess fields to the convection-allowing [HRRR](./hrrr.md) at 3 km
3. **Smoke forecast component for NAQFC** — RAP-Smoke output is the operational smoke component of NOAA's National Air Quality Forecast Capability since June 2022

The current operational version is **RAPv5** (operational since 2 December 2020), which is the **final version** of RAP. No further upgrades are planned; the system has been frozen since the RAPv5/HRRRv4 implementation alongside the broader transition to the Unified Forecast System (UFS) framework. RAP is expected to be retired with the eventual transition to RRFSv2 (see [Future outlook](#future-outlook)).

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP)
- **Country / region:** United States
- **Development:** NOAA Global Systems Laboratory (GSL); operated at NCEP

---

## What area it covers
- **Coverage:** North America
- **Domain details:** 759 × 568 grid points on a Lambert conformal projection covering the contiguous United States, Canada, Mexico, and surrounding waters

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** WRF-ARW (Advanced Research WRF, version 3.x)
- **Dynamical formulation:** Non-hydrostatic, fully compressible, on a mass-based (sigma-pressure hybrid) vertical coordinate
- **Convection-allowing:** No (deep convection is parameterized via Grell-Freitas at 13 km resolution)
- **Native horizontal resolution:** 13 km (Lambert conformal)
- **Vertical levels:** 50
- **Vertical coordinate:** Hybrid sigma-pressure
- **Model top:** 10 hPa
- **Forecast length:**
  - 51 hours for the 03, 09, 15, 21 UTC cycles (the four "extended" cycles)
  - 21 hours for the other 20 cycles
- **Update frequency:** Hourly (24× daily)
- **Temporal output resolution:** Hourly

---

## Data assimilation
RAP runs an **hourly cycling Gridpoint Statistical Interpolation (GSI) hybrid ensemble-3DVar** data assimilation system, with flow-dependent background-error covariances drawn from the GDAS-EnKF ensemble that informs the deterministic [GFS](../../global/usa/gfs.md):

- **Method:** Hybrid 3DVar combining a static background-error covariance with flow-dependent covariances from the 80-member GDAS-EnKF ensemble
- **Cycle structure:** RAP runs a continuous hourly DA cycle, with each cycle's analysis serving as the first guess for the next hour
- **Partial cycling:** Twice daily (typically 09 and 21 UTC), RAP partially cycles back to GDAS-derived initial conditions to absorb global-scale information from the deterministic GFS analysis. This refreshes the regional analysis with global atmospheric structure that may not have been captured during continuous hourly cycling.

### Assimilated observations
- Conventional surface, aircraft, and radiosonde data
- Satellite radiances (AMSU-A, ATMS, IASI, AIRS, CrIS) and atmospheric motion vectors
- GPS-RO refractivity profiles
- Mesonet surface observations (uniquely valuable at RAP's resolution)
- **Cloud and hydrometeor analysis** during the 1-hour pre-forecast spin-up — a distinctive RAP/HRRR feature that initializes ongoing precipitation systems from observed reflectivity, lightning, and satellite cloud retrievals
- Radar reflectivity (used for digital filter initialization, distinct from HRRR's full radar DA)

The cloud and hydrometeor analysis significantly improves short-range cloud and precipitation forecasts compared to a clean cold-start from upstream model fields.

---

## Initial and boundary conditions
- **Initial conditions:** Hourly RAP cycling analysis (continuous), with twice-daily partial cycling from GFS-derived backgrounds
- **Lateral boundary conditions:** GFS, with boundaries updated each cycle from the most recent available GFS forecast

---

## What it provides
Deterministic short-range forecasts of:
- Temperature, wind, humidity, and pressure (surface and upper-air)
- Cloud cover, ceilings, and visibility (with strong skill for low ceilings due to the hourly cloud analysis)
- Precipitation, precipitation type, and accumulation
- Aviation-relevant fields including icing potential, turbulence, and tropopause diagnostics
- Convective indices (CAPE, CIN, lifted index)
- Wildfire smoke fields (near-surface smoke concentration and vertically integrated smoke), with smoke aerosols feeding back on shortwave radiation and the atmospheric forecast
- Boundary-layer diagnostics, surface fluxes, and short-range mesoscale guidance

RAP analyses are also used as initial conditions for HRRR's hybrid data assimilation system (where they serve as the first guess into HRRR's own DA cycle, rather than as direct ICs).

---

## Relationship to other models
- **[HRRR](./hrrr.md):** RAP supplies the lateral boundary conditions and first-guess fields for HRRR. HRRR runs its own hourly data assimilation (using the 36-member HRRRDAS ensemble in HRRRv4) but inherits global-scale information from RAP through this background coupling. The two systems share much of the same physics suite and DA infrastructure.
- **[GFS](../../global/usa/gfs.md):** Provides the lateral boundary conditions for RAP and the partial-cycling source for RAP's twice-daily DA refresh.
- **[NBM](./nbm.md):** RAP is one of the deterministic inputs blended into the National Blend of Models for short-range guidance.
- **[RRFS](./rrfs.md):** Future replacement for RAP and HRRR. RRFSv1 (proposed for early 2026) will not retire RAP — RAP retirement is tied to the future RRFSv2 (MPAS-based) timeline.

### NAQFC integration
RAP-Smoke (the wildfire smoke component integrated into operational RAPv5 since December 2020) became the **operational smoke component of NOAA's National Air Quality Forecast Capability (NAQFC)** on 28 June 2022, replacing HYSPLIT. HYSPLIT now provides only dust forecasts within NAQFC. This makes RAP doubly important operationally: not just as a weather forecast system, but as the underlying smoke-prediction infrastructure for U.S. air quality forecasting.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data formats:** GRIB2
- **Official download locations:**
  - **NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/rap/prod/ (real-time, ~10-day rolling)
  - **AWS Open Data:** https://registry.opendata.aws/noaa-rap/ (real-time + archive)
  - **Microsoft Azure** and **Google Cloud Storage** mirrors are also available via the NOAA Big Data Program

The smoke fields are distributed alongside the standard meteorological fields in the operational RAP output. NOAA's RAP graphics portal also presents smoke fields under a separate "RAP-Smoke" menu page, but these are sourced from the same operational model run.

---

## Notes
- RAP and [HRRR](./hrrr.md) share much of the same modeling and assimilation infrastructure, including a similar physics suite, the GSI hybrid DA framework, and the cloud/hydrometeor initialization scheme. They are best understood as a paired mesoscale + storm-scale system rather than independent models.
- Unlike HRRR, RAP uses a cumulus parameterization (Grell-Freitas scheme with sub-grid downdrafts and stochastic convective triggering). The 13 km resolution does not resolve individual convective storms.
- RAP's hourly cycling, mesonet observation use, and cloud analysis make it especially well-suited for aviation forecasting, low-ceiling/visibility prediction, and short-range mesoscale analysis. It is one of the most heavily-used operational mesoscale models in U.S. aviation operations.
- **RAPv5 is the final version of RAP.** No further upgrades are planned. The system has been operationally frozen since 2 December 2020 and is in maintenance-only status pending eventual retirement.
- RAP-Smoke is no longer a separate experimental run — wildfire smoke prediction has been integrated into operational RAPv5 since December 2020, with smoke aerosols affecting radiation and feeding back into the meteorological forecast. This same RAPv5/HRRRv4 upgrade integrated smoke into HRRR as well.

---

## Future outlook

RAP is expected to be retired alongside [HRRR](./hrrr.md) as part of the eventual transition to **RRFSv2** (the MPAS-based version of the [Rapid Refresh Forecast System](./rrfs.md)). Unlike the NAM, NAM Nest, HiresW (non-Guam), HREF, and SREF — which are scheduled for retirement on October 6, 2026 under [NWS Service Change Notice 26-47](https://www.weather.gov/media/notification/pdf_2026/scn26-47_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS.aaa.pdf) (termination notice, updated July 6, 2026; RRFS/REFS implementation under companion [SCN 26-48](https://www.weather.gov/media/notification/pdf_2026/scn26-048_RRFS_and_REFS_Implementation.aab.pdf); originally proposed under PNS 25-41) — **RAP and HRRR are explicitly not part of the RRFSv1 retirement wave**. Both are expected to continue operating in parallel with RRFSv1 until the RRFSv2 transition.

No formal Service Change Notice for RAP retirement has been issued as of May 2026. The RRFSv2 timeline itself has not been publicly committed; it depends on resolution of issues identified in RRFSv1 evaluation (notably tropical cyclone tracking and convective precipitation overprediction with the FV3 core, which are intended to be addressed by the MPAS dynamical core in v2).

---

## Recent version history

### RAPv5 — operational 2 December 2020 (current; final version)
The last RAP upgrade. Joint implementation with HRRRv4. Headline changes:
- **Wildfire smoke prediction** integrated into operational RAP (previously experimental as RAP-Smoke) — smoke aerosols feed back on shortwave radiation and the atmospheric forecast
- Improved cloud representation for boundary-top clouds, especially shallow cold-air layers
- Better representation of cloud bands (snow squalls, hurricane bands, lake-effect bands)
- Updated physics suite consistent with HRRRv4
- Improved data assimilation including better surface observation handling
- Subsequent maintenance updates have been bug fixes only — no scientific changes

### RAPv4 — operational August 2016
Earlier major upgrade introducing significant DA enhancements including extended mesonet observation use and improved 2 m temperature handling. Documented in Benjamin et al. (2016).

### Earlier history
RAP became operational at NCEP in May 2012, replacing the long-running **Rapid Update Cycle (RUC)** model that had been operational since 1994. RUC ran at successively finer resolutions (60 km in 1994, 40 km in 1998, 20 km in 2002, 13 km in 2008) before transitioning to the WRF-ARW-based RAP. RAP-Smoke was first introduced experimentally in 2016 before becoming part of operational RAPv5 in 2020.

---

## Official documentation
- RAP home page (NOAA GSL): https://rapidrefresh.noaa.gov/
- RAP at EMC: https://emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/rap.php
- RAP FAQ: https://rapidrefresh.noaa.gov/RR.faq.html
- NWS Service Change Notice 20-46 (RAPv5/HRRRv4 implementation): https://www.weather.gov/media/notification/pdf2/scn20-46rap_v5_hrrr_v4_aab.pdf
- NWS PNS 25-41 (RRFS legacy model cessation, NOT including RAP): https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf
- AWS Open Data: https://registry.opendata.aws/noaa-rap/

### Key references
- Benjamin, S. G., et al. (2016). *A North American Hourly Assimilation and Model Forecast Cycle: The Rapid Refresh.* Mon. Wea. Rev., 144, 1669–1694. https://doi.org/10.1175/MWR-D-15-0242.1
- James, E. P., et al. (2019). *Rapidly-updating high-resolution predictions of smoke, visibility, and smoke-weather interactions using satellite fire products within the Rapid Refresh and High-Resolution Rapid Refresh coupled with smoke (RAP/HRRR-smoke).* 35th Conf. on Environmental Information Processing Technologies, Phoenix, AZ.
