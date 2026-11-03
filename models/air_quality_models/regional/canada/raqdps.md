# RAQDPS (Regional Air Quality Deterministic Prediction System)

## What this model is
The Regional Air Quality Deterministic Prediction System (RAQDPS) is Environment and Climate Change Canada's operational regional air quality forecast system for North America.

It produces short-term (3-day) hourly forecasts of surface concentrations of ozone, nitrogen dioxide, particulate matter, and other atmospheric chemical species. These forecasts are used directly to calculate the Air Quality Health Index (AQHI) that ECCC disseminates to the Canadian public.

RAQDPS has been operated by ECCC since 2009. The current operational version is version 25.1.0, implemented on April 14, 2026. It is a port of version 25.0.0 (June 11, 2024, 12 UTC run) to ECCC's new high-performance computing infrastructure.

---

## Who runs it
- **Organization:** Canadian Meteorological Centre (CMC) / Environment and Climate Change Canada
- **Country / region:** Canada

---

## What area it covers
- **Coverage:** Canada, the contiguous United States, northern Mexico, and adjacent oceans
- **Domain details:** Rotated limited-area latitude–longitude grid (0.09° ≈ 10 km). The full computational grid is 772 × 642 points (per the v25 fact sheet and technical specifications); the publicly distributed GRIB2 files are on a cropped 729 × 599 grid — see *Data availability*.

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
- **License:** Environment and Climate Change Canada Data Servers End-use Licence (attribution required; commercial use permitted) — https://eccc-msc.github.io/open-data/licence/readme_en/
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (JPEG2000-compressed; `grid_jpeg` packing)
- **Distributed grid:** Rotated lat-lon, 729 × 599 points at 0.09° (~10 km), first grid point 32°S, 39.5°W. This is the cropped distribution grid — smaller than the 772 × 642 full computational grid (see *What area it covers*).
- **Official download location:**
  https://dd.weather.gc.ca/today/model_raqdps/10km/grib2
  - **Path template:** `https://dd.weather.gc.ca/today/model_raqdps/10km/grib2/{HH}/{hhh}/` — `{HH}` = run (`00`/`12`), `{hhh}` = 3-digit forecast hour (`000`–`072`). `today/` holds the most recent ~24 h; runs are also reachable under a dated path (`https://dd.weather.gc.ca/{YYYYMMDD}/WXO-DD/model_raqdps/10km/grib2/{HH}/`).
  - **Filename convention:** `{YYYYMMDD}T{HH}Z_MSC_RAQDPS_{VAR}_{LVL}_RLatLon0.09_PT{hhh}H.grib2` (e.g. `20260720T12Z_MSC_RAQDPS_O3_Sfc_RLatLon0.09_PT001H.grib2`)
  - `{VAR}` ∈ {PM2.5, PM2.5-WildfireSmokePlume, PM10, PM10-WildfireSmokePlume, NO2, NO, O3, SO2} (plus `PM2.5-WildfireSmokePlume-DAvg` / `-DMax` daily statistics); `{LVL}` ∈ {Sfc, AGL-2m, EAtm}

---

## Version history

The list runs from the start of operations (November 2009) to the present, newest first. ECCC's changelog itself begins in December 2011. Unless noted, upgrades start at the 12 UTC run. RAQDPS has changed its version numbering twice: from 1.x (to 2014), to three-digit numbers (013, 015, 016 in 2016), to plain integers (19 onward). Most upgrades follow a change to its meteorological driver: [RDPS](../../../nwp_models/regional/canada/rdps.md) until 2024, the GDPS 10 km G0 component since.

### RAQDPS v25.1.0 — operational April 14, 2026 (current)
- Port of v25.0.0 to ECCC's new high-performance computing infrastructure. No science or configuration changes listed.
- *(TBD — whether the May 26, 2026 GDPS v10.0.0 upgrade changed RAQDPS's GDPS-G0 driver. The RAQDPS changelog has no entry for it.)*

### RAQDPS v25.0.0 — operational June 11, 2024
- GEM upgraded to version 5.2.1; piloted by GDPS-G0 9.0.0 (10 km) replacing RDPS as meteorological driver
- Wildfire emissions integrated into operational RAQDPS via CFFEPS v4.1 (previously handled by the separate FireWork system)
- Anthropogenic emissions updated to SET5.0.0 with projected 2023 inventories for Canada, U.S., and Mexico
- Biogenic emissions updated to BEIS 3.7 and BELD 4; MODIS satellite LAI used to constrain biomass factors
- Sea salt added to total PM2.5 mass
- Tropospheric gas chemistry solver upgraded to Rosenbrock method (RODAS3) via KPP
- Vehicle-induced turbulence (VIT) added as sub-grid process for enhanced mixing of on-road mobile emissions
- Updates to chemical boundary conditions, dry deposition of gaseous species, below-cloud particle scavenging

### RAQDPS v24 — operational June 28, 2022
- Port of v23 to ECCC's then-new HPC infrastructure. No science changes listed.

### RAQDPS v23 — operational December 1, 2021
- **Piloted by RDPS v8.0.0** (previously v7.1.0)
- **GEM upgraded from 5.0.2 to 5.1.0; GEM-MACH chemistry from 3.0.0.2 to 3.1.0.0**
- New anthropogenic emissions:
  - Canada: projected 2020 APEI (previously 2013 APEI)
  - U.S.: projected 2023 NEI (previously projected 2017 NEI)
  - Mexico: projected 2023 NEI (previously 2008 NEI)
- Improved representation of particle sedimentation
- Described in detail by Moran et al. (2025), the first comprehensive peer-reviewed description of any RAQDPS version

### RAQDPS v22.0.0 — operational January 21, 2020
- Port of v21 to ECCC's then-new HPC infrastructure. No science changes listed.

### RAQDPS v21 — operational July 3, 2019
- Upgraded together with its companion wildfire-smoke system FireWork
- Piloted by RDPS v7.0.0 (previously v6.0.0)
- GEM upgraded from v4.8-LTS.16 to v5.0.0
- Improvements to chemistry processing

### RAQDPS v20 — operational September 18, 2018
- Upgraded together with FireWork
- Meteorological analyses and pilot linked to RDPS v6.0.0 (previously v5.1.0), adopting its configuration, including recycling of key physics variables and IAU-based meteorological initialization
- New RAQDPS model code (v2.3.1) for faster processing and better robustness
- Chemistry improvements: gas-phase dry deposition over snow-covered surfaces and sea ice
- New emissions files based on more recent national inventories

### RAQDPS v016 — operational September 7, 2016
- GEM upgraded from a modified v4.6.2 to v4.8.0
- Meteorological analyses and pilot linked to RDPS 5.0.0 (previously 4.2.0)
- **Grid rotated to match the new RDPS 5.0.0 grid**
- Important change to the use of the Monin–Obukhov parameter; minor bug fixes

### RAQDPS v015 — operational April 7, 2016
- Replaced version 013, which ECCC notes was previously numbered 1.5.4
- Slight change to grid size (10 km spacing and orientation unchanged)
- **GEM upgraded from 3.3.8.2 to 4.6.2**
- New chemical lateral boundary conditions
- Chemistry module updates, including two important bug fixes

### Emissions update — June 11, 2015
- New emissions based on the 2010 Canadian and 2011 U.S. inventories (previously 2006 Canadian and 2005 U.S.). ECCC's changelog gives no version number for this update.

### RAQDPS v1.5.3 — operational November 18, 2014
- Driven by RDPS 4.0.0
- GEM upgraded from 3.3.8.1 to 3.3.8.2

### RAQDPS v1.5.1 — operational February 26, 2013
- Three chemistry bug fixes, present since the 2009 implementation, affecting wet removal by convective precipitation and vertical mixing in stable conditions
- Testing showed better scores than 1.5.0 for ozone, PM2.5 and NO2, with the largest gain for winter PM2.5 peaks. UMOS-AQ statistical post-processing performed about the same at roughly 90% of stations and better at most of the rest
- No changes to product availability

### RAQDPS v1.5.0 — operational October 3, 2012
- **Horizontal resolution increased from 15 km to 10 km** (GEM-MACH15 → GEM-MACH10); vertical grid spacing modified
- Updated GEM version and improved emissions files
- Replaced v1.4.6 (GEM-MACH15, in service since November 2009). Products could arrive a few minutes later due to the longer run time

### RAQDPS v1.4.5 — December 22, 2011
- Corrected GEM-MACH15 binary that better handles humidity and cloud information in the upper atmosphere

### GEM-MACH15 — operational November 2009
- First operational GEM-MACH air quality system at 15 km. Recorded in ECCC's changelog only through the v1.5.0 entry; there is no dated entry for the 2009 implementation.

---

## Notes
- RAQDPS is an online, one-way-coupled chemical transport model. Meteorology influences chemistry, but chemistry does not feed back into the meteorological forecast.
- Activating the MACH chemistry module increases model run time by a factor of approximately 4.4 compared to meteorology-only runs.
- RAQDPS is part of ECCC's broader operational air quality forecasting suite. The separate FireWork wildfire smoke system's core capability is now integrated into RAQDPS v25.
- **Version-history gaps (ECCC sources):** Several versions have no changelog entry: 1.4.6 (in service before October 2012), 1.5.2, 1.5.4/013, 014, 017 and 018. The June 2015 emissions update and the 2009 implementation have no version number. The changelog also uses three numbering schemes over time (see Version history).
- **The public data is a subset of the full model output.** The operational model carries dozens of chemical fields, but the MSC Datamart GRIB2 feed distributes only eight species/levels — near-surface (2 m AGL) NO2, NO, O3, SO2; surface and column-integral (EAtm) PM2.5 and PM10; and the matching PM2.5/PM10 WildfireSmokePlume fields — plus daily-average (DAvg) and daily-maximum (DMax) near-surface PM2.5 smoke-plume fields at hours 024/048/072 (days 1–3). Species such as NH3, HNO3, PAN, individual PM chemical components, and deposition fields are not in the public feed.

---

## Official documentation
- RAQDPS changelog: https://eccc-msc.github.io/open-data/msc-data/nwp_raqdps/changelog_raqdps_en/
- MSC Open Data RAQDPS readme: https://eccc-msc.github.io/open-data/msc-data/nwp_raqdps/readme_raqdps_en/
- RAQDPS v25 fact sheet: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/fact_sheets/factsheet_raqdps-v25_e.pdf
- RAQDPS v25 technical specifications: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_specifications/tech_specifications_RAQDPS_025_e.pdf
- RAQDPS v25 technical note: https://collaboration.cmc.ec.gc.ca/cmc/cmoi/product_guide/docs/tech_notes/technote_raqdps-v25_e.pdf
- Moran et al. (2025), comprehensive RAQDPS023 description: https://egusphere.copernicus.org/preprints/2025/egusphere-2025-4324/
- RAQDPS023 Part 1: system description (*Geosci. Model Dev.*, 19, 4137, 2026): https://gmd.copernicus.org/articles/19/4137/2026/
