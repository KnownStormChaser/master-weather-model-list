# RAQDPS (Regional Air Quality Deterministic Prediction System)

## What this model is
The Regional Air Quality Deterministic Prediction System (RAQDPS) is Environment and Climate Change Canada's operational regional air quality forecast system for North America.

It produces short-term (3-day) hourly forecasts of surface concentrations of ozone, nitrogen dioxide, particulate matter, and other atmospheric chemical species. These forecasts are used directly to calculate the Air Quality Health Index (AQHI) that ECCC disseminates to the Canadian public.

RAQDPS has been operated by ECCC since 2009. The current operational version is version 25.0.0, implemented on June 11, 2024 with the 12 UTC run.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Canada, the contiguous United States, northern Mexico, and adjacent oceans
- **Domain details:** Rotated limited-area latitude–longitude grid, 772 × 642 grid points

---

## Basic details
- **Model type:** Regional deterministic air quality / atmospheric composition
- **Model system / core:** GEM-MACH (Global Environmental Multiscale – Modelling Air quality and CHemistry), an online one-way-coupled chemical transport model embedded within the GEM atmospheric model
- **GEM version:** 5.2.1 (as of RAQDPS v25)
- **Horizontal resolution:** ~10 km (0.09° uniform)
- **Vertical levels:** 84 staggered hybrid levels
- **Model top:** 0.1 hPa
- **Meteorological time step:** 300 seconds
- **Chemistry time step:** 900 seconds (every third GEM step)
- **Forecast length:** 72 hours
- **Update frequency:** 2× daily (00, 12 UTC)
- **Temporal output resolution:** Hourly

---

## Meteorological driver
- **Driving NWP model:** GDPS-G0 9.0.0 (10 km component of the Global Deterministic Prediction System), as of RAQDPS v25
- **Coupling:** Online (GEM-MACH embedded within GEM)
- **Meteorological initial conditions:** From GDPS-G0, with 4D Incremental Analysis Update (4D-IAU) applied during a 6-hour window centred at T=0
- **Meteorological lateral boundary conditions:** Hourly from GDPS-G0 forecast at the same start time
- **Previous driver:** Prior to v25, RAQDPS was piloted by the Regional Deterministic Prediction System (RDPS)

---

## Chemistry and aerosols
- **Gas-phase chemical mechanism:** ADOM-2 tropospheric chemistry (41 species, 114 reactions); LINOZ scheme for stratospheric ozone
- **Gas-phase solver:** Rosenbrock method (RODAS3) via Kinetic Pre-Processor (KPP), as of RAQDPS v25
- **Prognostic variables:** 41 gas-phase species, 16 particle-phase species
- **Aerosol treatment:** Sectional, 2 size bins (0–2.5 μm and 2.5–10 μm Stokes diameter)
- **PM chemical components (9):** Sulphate, nitrate, ammonium, elemental carbon, primary organic matter, secondary organic matter, crustal material, sea salt (new in v25), particle-bound water
- **Aerosol mixing state:** Internally mixed
- **Aqueous-phase chemistry:** ADOM aqueous-phase mechanism (25 species, 25 reactions) operating in bulk cloud droplets
- **Inorganic heterogeneous chemistry:** HETV algorithm (based on ISORROPIA) for gas-particle partitioning of sulphate, nitrate, and ammonium systems
- **Secondary organic aerosol:** Instantaneous Aerosol Yield (IAY) scheme based on Jiang (2003, 2004)

---

## Emissions
- **Emissions processing system:** SMOKE (Sparse Matrix Operator Kernel Emissions) version 4.7
- **Emitted model species:** 30 (16 gas-phase, 14 particle-phase across two size bins)

### Anthropogenic emissions (SET5.0.0 as of RAQDPS v25)
- **Canada:** 2023 projected national Air Pollutant Emission Inventory (APEI), based on 2019 APEI
- **United States:** 2023 projected National Emissions Inventory (NEI), based on 2016 NEI v1
- **Mexico:** 2023 projected NEI, based on 2016 NEI v1
- **Inventory species:** SO2, NOx, CO, VOC, NH3, PM2.5, PM10
- **Speciation:** 331 VOC and 85 PM2.5 speciation profiles (SPECIATE 4.5)

### Biogenic emissions
- **Emission factor model:** BEIS 3.7 (updated from 3.09 in v25)
- **Vegetation database:** BELD 4 (updated from BELD 3 in v25)
- **Leaf area index:** Satellite-derived MODIS LAI used to constrain biomass factors (new in v25)
- Emissions are calculated online, modulated by hourly GEM-predicted temperature and solar insolation

### Wildfire emissions (integrated into operational RAQDPS as of v25)
- **Wildfire emissions model:** Canadian Forest Fire Emission Prediction System (CFFEPS) v4.1
- **Near-real-time fire hotspot data:** NRCan Canadian Wildland Fire Information System (CWFIS), using MODIS and VIIRS satellite data
- **Approach:** Bottom-up, process-based, with emissions per hotspot calculated from estimated burn area, fuel consumption (via NRCan Fire Behavior Prediction module), and literature-based emission factors
- **Plume rise:** Energy-balance-based injection height, calculated hourly using GEM-MACH's full vertical column

### Other natural emissions
- **Sea salt:** Online, wind-driven (Gong et al. 1997, 2003); included in PM2.5 mass as of v25
- **Lightning, volcanic, and pollen emissions:** Not considered

---

## Chemical initial and lateral boundary conditions
- **Chemical initial conditions:** From the 12-hour forecast of the previous RAQDPS cycle (perpetual forecast)
- **Chemical lateral boundary conditions:** Spatially-varying monthly climatology
  - Particles: From a 2009 annual simulation with the NCAR MOZART4 global chemical transport model
  - Gases: Monthly averages from NCAR CAM-Chem v2.2 using 8 years of data (2012–2019)

---

## Data assimilation
RAQDPS does not assimilate air quality observations directly. Meteorological initial conditions come from GDPS, which has its own data assimilation cycle. The 4D-IAU approach inherited from GDPS allows recycling of several physics variables during model spin-up. Chemical fields are initialized from the previous RAQDPS cycle's 12-hour forecast.

---

## What it provides
Deterministic hourly forecasts of:
- Primary predictands used for AQHI: ozone (O3), nitrogen dioxide (NO2), and PM2.5 total mass
- Additional gases: SO2, NO, CO, NH3, HNO3, PAN, formaldehyde, and others
- PM10 coarse particulate matter
- PM chemical component fields (sulphate, nitrate, ammonium, carbon species, sea salt, etc.)
- Dry and wet deposition fields
- Wildfire smoke contribution (new quantity as of v25)

Forecasts of NO2, O3, and PM2.5 are used to compute the **Air Quality Health Index (AQHI)**, ECCC's primary public air-quality communication tool.

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**
  https://eccc-msc.github.io/open-data/msc-data/nwp_raqdps/readme_raqdps_en/

---

## Version history

### RAQDPS v25.0.0 — operational June 11, 2024
Current operational version.
- GEM upgraded to version 5.2.1; piloted by GDPS-G0 9.0.0 (10 km) replacing RDPS as meteorological driver
- Wildfire emissions integrated into operational RAQDPS via CFFEPS v4.1 (previously handled by the separate FireWork system)
- Anthropogenic emissions updated to SET5.0.0 with projected 2023 inventories for Canada, U.S., and Mexico
- Biogenic emissions updated to BEIS 3.7 and BELD 4; MODIS satellite LAI used to constrain biomass factors
- Sea salt added to total PM2.5 mass
- Tropospheric gas chemistry solver upgraded to Rosenbrock method (RODAS3) via KPP
- Vehicle-induced turbulence (VIT) added as sub-grid process for enhanced mixing of on-road mobile emissions
- Updates to chemical boundary conditions, dry deposition of gaseous species, below-cloud particle scavenging

### RAQDPS v23 — operational December 2021 – June 2024
Previous operational version.
- Based on GEM version 5.1.0 and MACH version 3.1.0.0
- Piloted by RDPS version 8.0.0
- Described in detail by Moran et al. (2025), the first comprehensive peer-reviewed description of any RAQDPS version

---

## Notes
- RAQDPS is an online, one-way-coupled chemical transport model. Meteorology influences chemistry, but chemistry does not feed back into the meteorological forecast.
- Activating the MACH chemistry module increases model run time by a factor of approximately 4.4 compared to meteorology-only runs.
- RAQDPS is part of ECCC's broader operational air quality forecasting suite. The separate FireWork wildfire smoke system's core capability is now integrated into RAQDPS v25.

---

## Official documentation
- MSC Open Data RAQDPS readme: https://eccc-msc.github.io/open-data/msc-data/nwp_raqdps/readme_raqdps_en/
- RAQDPS v25 fact sheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_raqdps-v25_e.pdf
- RAQDPS v25 technical specifications: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_specifications/tech_specifications_RAQDPS_025_e.pdf
- RAQDPS v25 technical note: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_raqdps-v25_e.pdf
- Moran et al. (2025), comprehensive RAQDPS023 description: https://egusphere.copernicus.org/preprints/2025/egusphere-2025-4324/
