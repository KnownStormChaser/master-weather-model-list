# NAM (North American Mesoscale Forecast System)

> ⚠️ **Scheduled for retirement October 6, 2026 at 12 UTC.** The NAM (12 km parent and all five high-resolution nests, NAM-DNG, DGEX, and NAM MOS) is scheduled for retirement under [NWS Service Change Notice 26-47](https://www.weather.gov/media/notification/pdf_2026/scn26-47_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS.aaa.pdf) (termination notice; updated July 6, 2026), on the same cycle that brings [RRFS](./rrfs.md) into operations under companion [SCN 26-48](https://www.weather.gov/media/notification/pdf_2026/scn26-048_RRFS_and_REFS_Implementation.aab.pdf) (implementation notice; May 12, 2026, updated July 6, 2026). The retirement set was originally proposed under [NWS PNS 25-41](https://www.weather.gov/media/notification/pdf_2025/pns25-41_RRFS_legacy_model_cessation.pdf) (June 26, 2025). Implementation is subject to the standard CWD/ECE postponement contingency.

## What this model is
The North American Mesoscale Forecast System (NAM) is NOAA's regional deterministic numerical weather prediction system covering North America at 12 km horizontal resolution, with five embedded high-resolution nests run within the same forecast integration.

The NAM is structurally a **single integrated system** that produces multiple forecast products in one execution: the 12 km parent domain covering all of North America to 84 hours, plus the five nested high-resolution domains documented separately as the [NAM Nest](./nam-nest.md). Despite the common shorthand of treating these as separate models, they share the same dynamical core, run within the same NCEP production cycle, and are tightly integrated.

The NAM has been operationally **frozen since the v4.0 upgrade in March 2017**, when NCEP redirected its mesoscale modeling resources toward developing the FV3-based regional system that ultimately became the [RRFS](./rrfs.md). The NAM remains in operations primarily because no operational replacement has yet fully matured, and because it continues to provide essential downstream guidance for the National Digital Forecast Database (NDFD) and other operational products.

The current operational version is **NAMv4** (operational since 21 March 2017). No further upgrades are planned.

---

## Who runs it
- **Organization:** NOAA / National Weather Service (NCEP)
- **Country / region:** United States
- **Operating center:** NCEP / Environmental Modeling Center (EMC)

---

## What area it covers
- **Coverage:** North America
- **Domain:** Full North American 12 km parent grid; the integrated nests cover CONUS, Alaska, Hawaii, Puerto Rico, plus a moveable 1.5 km fire weather nest (see [NAM Nest](./nam-nest.md))

---

## Basic details
- **Model type:** Regional deterministic NWP
- **Model system / core:** NEMS NMMB — NOAA Environmental Modeling System version of the **Non-Hydrostatic Multi-scale Model on B-grid**
- **Dynamical formulation:** Non-hydrostatic, on the Arakawa B-grid horizontal staggering
- **Convection-allowing:** No (deep convection is parameterized via Betts-Miller-Janjic at 12 km resolution)
- **Native horizontal resolution:** 12 km (Lambert conformal projection over North America)
- **Vertical levels:** 60 (hybrid sigma-pressure; the NAMv4 development considered 70 levels but adopted a redistributed 60-level configuration for nearly equivalent skill at lower compute cost)
- **Forecast length:** 84 hours
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 3-hourly for the 12 km parent (the embedded high-resolution nests are output hourly; see [NAM Nest](./nam-nest.md))

---

## Data assimilation
The NAM 12 km parent domain is initialized using the **NCEP NAM Data Assimilation System (NDAS)** — a hybrid variational-ensemble analysis based on the GSI framework:

- **Method:** Hybrid 3DEnVar combining a static background-error covariance with flow-dependent covariances drawn from the GDAS-EnKF ensemble (the same 80-member ensemble that informs the [GFS](../../global/usa/gfs.md) and [RAP](./rap.md) hybrid analyses)
- **Cycle structure:** 6-hour DA cycle with **hourly analysis updates** within each cycle
- **Shared with cycled nests:** The CONUS and Alaska 3 km nests share the same NDAS analysis system; the Hawaii, Puerto Rico, and fire-weather nests are non-cycled and inherit a first guess from the 12 km parent (see [NAM Nest](./nam-nest.md) for nest-specific details)

### Assimilated observations
- Conventional surface, aircraft, and radiosonde observations
- Satellite radiances (AMSU-A, MHS, AIRS, IASI, ATMS, CrIS) and atmospheric motion vectors
- GPS radio occultation (GPS-RO) refractivity
- Wind profiler and VAD (Velocity-Azimuth Display) winds
- Surface mesonet observations
- GOES cloud-top and cloud-drift winds

---

## What it provides
Deterministic regional forecasts of:
- Temperature, wind, humidity, and pressure (surface and upper-air)
- Total precipitation, precipitation rate, and precipitation type
- Cloud cover, hydrometeor fields, and cloud-top properties
- Convective indices (CAPE, CIN, lifted index)
- Boundary-layer diagnostics and surface fluxes
- Aviation-relevant fields including ceilings, visibility, and turbulence-relevant parameters
- Soil moisture, soil temperature, and surface variables (Noah land surface model)

The NAM is also the source for several downstream operational products:
- **NAM Downscaled Numerical Guidance (NAM-DNG)** at 2.5 km (CONUS) and 3 km (Alaska), populating gridded forecasts for the National Digital Forecast Database (NDFD)
- **NAM Extension (DGEX)** — a longer-range product extending NAM-style guidance to 192 hours by blending with [GFS](../../global/usa/gfs.md) forecasts
- Lateral boundary conditions for the integrated [NAM Nest](./nam-nest.md) high-resolution domains

---

## Physics suite
The NAM uses physics broadly inherited from the Eta and WRF-NMM lineage that preceded it:

- **Microphysics:** Ferrier-Aligo bulk scheme (NCEP-developed; operational in NAM since August 2014)
- **Convection:** Betts-Miller-Janjic deep convective parameterization
- **Boundary layer:** MYJ (Mellor-Yamada-Janjic) scheme
- **Land surface:** Noah land surface model
- **Radiation:** RRTM longwave; GFDL shortwave
- **Gravity wave drag and mountain blocking:** Active in the 12 km parent (not used in the high-resolution nests)

---

## Relationship to other models
- **[NAM Nest](./nam-nest.md):** The five high-resolution nested domains run within the same parent NAM execution. Documented separately but operationally integrated.
- **[GFS](../../global/usa/gfs.md):** Provides lateral boundary conditions for the 12 km NAM, and is blended with NAM forecasts in the DGEX (NAM Extension) product.
- **[RAP](./rap.md):** Operational mesoscale counterpart at 13 km. RAP and NAM are similar in resolution but use different dynamical cores (NAM: NMMB B-grid; RAP: WRF-ARW), different DA systems (NAM: NDAS; RAP: hourly cycling GSI hybrid), and different update cadences (NAM: 4× daily; RAP: hourly). NAM produces longer 84-hour forecasts; RAP produces shorter forecasts more frequently.
- **[NBM](./nbm.md):** Uses NAM fields as one of many deterministic inputs.
- **SREF:** Multi-model regional ensemble that included NAM-NMMB members alongside other model formulations. SREF is being retired on the same day as the NAM (October 6, 2026) under SCN 26-47 and replaced by [REFS](../../ensemble_models/regional/usa/refs.md).
- **[RRFS](./rrfs.md):** Replacement. RRFSv1 implements October 6, 2026 at 12 UTC under SCN 26-48 (NAM termination under companion SCN 26-47) and is intended to replace both the 12 km NAM parent and the [NAM Nest](./nam-nest.md) high-resolution domains. Most NAM products will continue to be produced from RRFS output via post-processing during the transition.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **License:** Public domain (U.S. government work; CC0-equivalent)
- **Data formats:** GRIB2
- **Official download locations:**
  - **NOMADS:** https://nomads.ncep.noaa.gov/pub/data/nccf/com/nam/prod/ (real-time, ~10-day rolling)
  - **NCEI archive:** https://www.ncei.noaa.gov/products/weather-climate-models/north-american-mesoscale (long-term archive)
  - **AWS Open Data:** https://registry.opendata.aws/noaa-nam/ (real-time + archive)
  - **Microsoft Azure** and **Google Cloud Storage** mirrors are also available via the NOAA Big Data Program

NAM 12 km output is distributed in GRIB2 files separate from the NAM Nest output, all within the parent NAM directory structure. NAM-DNG and DGEX are distributed as separate products.

---

## Notes
- The NAM transitioned from the **WRF-NMM (E-grid) dynamical core to NMMB (B-grid)** in October 2011, replacing the long-running Eta/WRF-NMM lineage with the NCEP-developed NMMB. This transition required users with native-grid GRIB processing pipelines to update their software to handle B-grid staggering.
- The NAM has been **frozen since the v4.0 implementation in March 2017**, with no scientific changes since. EMC redirected its mesoscale development effort toward what became the FV3-based [RRFS](./rrfs.md) at that time.
- The NAM and [RAP](./rap.md) cover similar geographic areas at similar resolutions (12 km vs 13 km) but were developed for different operational purposes: the NAM provides longer-range mesoscale guidance with twice-daily-equivalent update frequency, while RAP provides hourly-updating short-range guidance optimized for aviation and rapid-evolution forecasting. Both are scheduled to be retired with the [RRFS](./rrfs.md) transition, though on different timelines (NAM with RRFSv1 on October 6, 2026; RAP and HRRR with RRFSv2, no formal SCN yet).
- The NMMB dynamical core was developed at NCEP/EMC by Zaviša Janjić and colleagues. It uses the Arakawa B-grid horizontal staggering (different from WRF-ARW's C-grid and the original Eta E-grid), with conservative finite-difference advection and forward-backward time-stepping for fast modes. NMMB is no longer an active operational dynamical core in any other major NCEP system as of 2026.

---

## Status and retirement timeline
- **Scheduled for full retirement October 6, 2026 at 12 UTC** under NWS Service Change Notice 26-47 (termination notice; updated July 6, 2026); RRFS/REFS implementation under companion SCN 26-48. The July 6, 2026 update moved the date from August 31, 2026 to October 6, 2026.
- Retirement covers the 12 km parent domain, all five nests, NAM-DNG, DGEX, and NAM MOS. SCN 26-47 lists the parent grid explicitly under the heading "Discontinued NAM North America (12 km) and Nests", covering all products and domains — CONUS, North America, Caribbean/Central America, Pacific, Alaska, Hawaii, and Puerto Rico. The 12 km parent is **not** deferred to the RRFSv2 wave alongside HRRR and RAP.
- Originally proposed under PNS 25-41 (June 26, 2025) for early 2026; the date slipped through pre-operational evaluation of RRFSv1 before being scheduled in SCN 26-47/26-48.
- Per the SCN, if October 6, 2026 is declared a Critical Weather Day, an Enhanced Caution Event, or other significant weather is occurring or anticipated, retirement moves to 12 UTC on the next eligible weekday — the same contingency provision that pushed NBM v5.0 from April 30 to May 5, 2026.
- Many NAM output grids will continue to be produced from [RRFS](./rrfs.md) output via post-processing; others will be fully discontinued. EMC has published lists:
  - NAM grids being retired: https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/nam_retiredgrids.txt
  - NAM products being discontinued: https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/nam_retirements.txt

---

## Recent version history

### NAM v4.0 — operational 21 March 2017 (current; final version)
The last NAM upgrade. Headline changes:
- **CONUS nest resolution increased from 4 km to 3 km**; **Alaska nest resolution increased from 6 km to 3 km** (see [NAM Nest](./nam-nest.md))
- Updated Ferrier-Aligo microphysics
- Model stability improvements (more frequent moist physics and radiation calls; mix-out of superadiabatic layers — partly motivated by NAM CONUS nest failures during Hurricane Joaquin in 2015)
- Land-surface and radiation changes targeting cool-season surface temperature/dewpoint biases
- 12 km parent domain configuration largely unchanged from v3 — the v4 upgrade was primarily a high-resolution nest improvement

### NAM v3 — operational 18 August 2014
Introduced the **Ferrier-Aligo microphysics scheme**, replacing the previous NAM microphysics. Targeted at improved deep-convection forecasts in the CONUS nest.

### NAM transition to NMMB — operational 18 October 2011 (TIN 11-16)
The most significant NAM architectural change since the original Eta retirement. Replaced the WRF-NMM (E-grid) dynamical core with the NEMS-based NMMB (B-grid). Native-grid GRIB filenames changed from "egrd*" to "bgrd*" and users with native-grid pipelines required code updates.

### Earlier history
- The NAM as a name dates to 2006, when NCEP transitioned the operational mesoscale system from the **Eta model** (operational from 1993) to the **WRF-NMM** dynamical core
- The Eta model itself was the long-running operational mesoscale system at NCEP from 1993 to 2006, after replacing the earlier Limited-Area Fine Mesh (LFM) system
- NAM nests were progressively added in subsequent versions: 4 km CONUS nest in 2012; Hawaii, Puerto Rico, and Alaska nests in subsequent NAM upgrades; the moveable fire weather nest at 1.5 km was added as part of the NAM Nest evolution

---

## Official documentation
- NAM at EMC: https://emc.ncep.noaa.gov/emc/pages/numerical_forecast_systems/nam.php
- NAM Vlab page: https://vlab.noaa.gov/web/emc/nam
- NAM Product Inventory at NCEP: https://www.nco.ncep.noaa.gov/pmb/products/nam/
- NWS SCN 26-47 (termination of NAM/SREF/HREF/HiresW/NAM MOS, effective October 6, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-47_Retirement_of_NAM_SREF_HREF_HiresW_NAM_MOS.aaa.pdf
- NWS SCN 26-48 (RRFS and REFS implementation, effective October 6, 2026): https://www.weather.gov/media/notification/pdf_2026/scn26-048_RRFS_and_REFS_Implementation.aab.pdf
- NAM grids being retired: https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/nam_retiredgrids.txt
- NAM products being discontinued: https://www.emc.ncep.noaa.gov/mmb/mpyle/rrfs_info/nam_retirements.txt
- NCEI long-term archive: https://www.ncei.noaa.gov/products/weather-climate-models/north-american-mesoscale
- AWS Open Data: https://registry.opendata.aws/noaa-nam/

### Key references
- Janjic, Z., and R. Gall (2012). *Scientific documentation of the NCEP Nonhydrostatic Multiscale Model on the B grid (NMMB), Part 1: Dynamics.* NCAR/TN-489+STR.
- Aligo, E. A., B. Ferrier, and J. R. Carley (2018). *Modified NAM Microphysics for Forecasts of Deep Convective Storms.* Mon. Wea. Rev., 146, 4115–4153. https://doi.org/10.1175/MWR-D-17-0277.1
