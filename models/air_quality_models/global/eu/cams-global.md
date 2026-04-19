# CAMS Global (Global Atmospheric Composition Forecasts)

## What this model is
The CAMS Global atmospheric composition forecast system is the Copernicus Atmosphere Monitoring Service's deterministic global forecast of aerosols, reactive trace gases, and greenhouse gases. It is operated by ECMWF on behalf of the European Union as part of the Copernicus programme.

The system produces twice-daily 5-day global forecasts of 56 reactive trace gases (including ozone, carbon monoxide, nitrogen dioxide, sulphur dioxide, formaldehyde, methane, and others) and seven types of aerosol: desert dust, sea salt, organic matter, black carbon, sulphate, nitrate, and ammonium. Aerosol optical depth products at many wavelengths, particulate matter concentrations (PM1, PM2.5, PM10), and total-column chemistry products are also provided.

CAMS Global uses the Integrated Forecasting System (IFS) — the same physical model that produces ECMWF's weather forecasts — with additional modules for aerosols, reactive gases, and greenhouse gases developed within CAMS and its precursor projects GEMS and MACC. The current operational cycle is IFS Cycle 49r1, implemented on 12 November 2024.

---

## Who runs it
- **Organization:** ECMWF (European Centre for Medium-Range Weather Forecasts), operating the Copernicus Atmosphere Monitoring Service (CAMS) on behalf of the European Union
- **Country / region:** European Union / international

---

## What area it covers
- **Coverage:** Global
- **Grid:** Reduced Gaussian grid (N256), archived either as spectral coefficients (triangular truncation T511, "linear grid" TL511) or on the native N256 grid
- **ADS distribution:** Pre-interpolated to a regular 0.4° × 0.4° latitude/longitude grid

---

## Basic details
- **Model type:** Global deterministic atmospheric composition
- **Model system / core:** ECMWF Integrated Forecasting System (IFS) with atmospheric composition modules
- **IFS cycle:** 49r1 (since 12 November 2024)
- **Horizontal resolution:** ~40 km (TL511 linear grid)
- **Vertical levels:** 137 hybrid levels
- **Model top:** ~0.01 hPa (upper stratosphere/lower mesosphere)
- **Forecast length:** 5 days (120 hours)
- **Update frequency:** 2× daily (00, 12 UTC)
- **Temporal output resolution:**
  - Surface fields: hourly
  - Model- and pressure-level fields: 3-hourly
  - Analyses: every 6 hours (00, 06, 12, 18 UTC)

---

## Meteorological driver
Unlike most air quality models, CAMS Global does not use an external NWP model as a driver. The atmospheric composition modules are fully coupled within the IFS itself, so meteorology and chemistry are computed together within a single forecast system.

- **IFS model documentation:** https://www.ecmwf.int/en/publications/ifs-documentation
- **Atmospheric composition component:** https://www.ecmwf.int/en/elibrary/81630-ifs-documentation-cy49r1-part-viii-atmospheric-composition

---

## Chemistry and aerosols
- **Tropospheric gas-phase chemistry:** CB05 mechanism
- **Stratospheric chemistry:** BASCOE
- **Reactive trace gases:** 56 species in the troposphere (including O3, CO, NO, NO2, SO2, HCHO, CH4, HNO3, PAN, isoprene, methanol, ethane, propane, and many others), plus stratospheric ozone
- **Aerosol types (7):** Desert dust, sea salt, organic matter, black carbon, sulphate, nitrate, ammonium (nitrate and ammonium added 9 July 2019)
- **Aerosol partitioning:** EQSAM4Clim module (activated in cycle 49r1)
- **Aerosol microphysics:** Full treatment including nucleation, condensation/evaporation, coagulation, hygroscopic growth, and dry deposition
- **Online pH calculation:** Aerosols, cloud water, and precipitation pH computed online (49r1) and used in aqueous chemistry and wet deposition
- **Stratospheric sulphate aerosol:** Coupled with BASCOE gas-phase chemistry (49r1)
- **Dust optical properties:** New hydrophilic and aspherical dust treatment (49r1)

---

## Emissions
All CAMS Global emission inventories are developed within the CAMS programme:
- **Anthropogenic:** CAMS_GLOB_ANT v6.1, with sectoral diurnal cycles and injection heights
- **Natural and biogenic:** CAMS_GLOB_BIO v3.1 climatologies
- **Soil emissions:** CAMS-GLOB-Soils v2.4
- **Volcanic emissions:** CAMS-GLOB-VOLC-CLIM (climatology based on 2005–2021 data)
- **Ocean DMS (dimethyl sulfide):** CAMS_GLOB_OCE v3.1 climatology
- **Aircraft emissions (CO2 and NOx):** CAMS_GLOB_AIR v3.1
- **Biomass burning (wildfires):** CAMS GFAS v1.4, inferred in near-real-time from satellite observations of fire radiative power, with IS4FIRES injection heights
- **Lightning NOx:** Lopez lightning emission parameterisation (new in 49r1)

---

## Data assimilation
CAMS Global uses a **four-dimensional variational data assimilation method (4D-Var)** — the same assimilation framework as ECMWF's operational weather forecasts, extended to assimilate atmospheric composition satellite retrievals alongside meteorological observations.

Satellite observations assimilated include retrievals of:
- Aerosol optical depth (AOD) — including VIIRS AOD as of Cycle 47r3
- Ozone (O3)
- Carbon monoxide (CO)
- Nitrogen dioxide (NO2)
- Sulphur dioxide (SO2)
- IASI SO2 with altitude information (volcanic SO2 tracer, new in 49r1)
- GEMS O3 and NO2 (passive monitoring, new in 49r1)

---

## What it provides
Very extensive list of products. Key operational products include:
- **Particulate matter:** PM1, PM2.5, PM10 mass concentrations at the surface
- **Aerosol optical depth:** Total AOD and species-specific AODs (dust, sea salt, organic matter, black carbon, sulphate, nitrate, ammonium) at many wavelengths from 340 nm to 2130 nm
- **Trace gas total columns:** CO, NO2, SO2, O3, formaldehyde, methane, nitric acid, ethane, isoprene, and many others
- **Surface meteorology:** 10 m winds, 2 m temperature, surface pressure, etc. (inherited from IFS)
- **Reactive stratospheric chemistry:** Many trace gases relevant to ozone (chlorine species, bromine species, CFCs, HFCs) added in Cycle 48r1
- **Dry and wet deposition:** Time-integrated fluxes for many species
- **UV index products:** UV biologically effective dose (clear-sky and all-sky)

---

## Data availability
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:**
  - Model level fields: GRIB2 (with important packing and GRIB tables changes introduced in Cycle 48r1)
  - Other fields: GRIB1
  - NetCDF: available (experimental) on the ADS
- **Primary access:** Atmosphere Data Store (ADS)
  - Web interface: https://ads.atmosphere.copernicus.eu/datasets/cams-global-atmospheric-composition-forecasts?tab=form
  - Programmatic access via CDS API: https://ads.atmosphere.copernicus.eu/how-to-api
- **Near-real-time subset (last 3 days, time-critical applications):** ECMWF Data Portal via SFTP/FTP/HTTPS — https://confluence.ecmwf.int/display/CKB/SFTP-FTP-HTTPS+data+access+to+CAMS+global+data
- **Data availability guarantees:**
  - 00 UTC forecast data: by 10:00 UTC
  - 12 UTC forecast data: by 22:00 UTC

Registration is required for the ADS; data use is subject to the Copernicus licence agreement.

---

## Version history

### Cycle 49r1 — operational 12 November 2024 (current)
- EQSAM4Clim module activated for gas-phase aerosol partitioning
- Online computation of aerosol, cloud, and precipitation pH
- Coupled stratospheric sulphate aerosol with BASCOE gas-phase chemistry
- New hydrophilic and aspherical dust optical properties
- Lopez lightning NOx emission parameterisation
- IASI SO2 altitude information assimilated into volcanic SO2 tracer
- Passive monitoring of GEMS O3 and NO2
- Updates to photolysis quantum yields and cross sections for selected tropospheric trace gases
- Addition of total column volcanic sulphur dioxide as a new product

### Cycle 48r1 — operational 27 June 2023
- Major update to atmospheric composition and meteorology
- Significant changes to GRIB encoding for model-level fields (packing and GRIB tables)
- Many new trace gas products added (chlorine species, bromine species, halocarbons)

### Cycle 47r3 — operational from 12 October 2021
- Multiple incremental updates through early 2023
- VIIRS AOD assimilation added (February 2023)

### Earlier cycles
- **Cycle 46r1** (9 July 2019): Nitrate and ammonium aerosols added; vertical grid expanded from 60 to 137 levels
- **Cycle 41r1** (21 June 2016): Horizontal resolution refined from 80 km to 40 km; twice-daily production introduced (previously once daily)

---

## Notes
- CAMS Global is the operational near-real-time product. A separate **CAMS Reanalysis (EAC4)** provides a consistent multi-decadal reconstruction using the same data assimilation framework; see https://confluence.ecmwf.int/display/CKB/CAMS%3A+Reanalysis+data+documentation.
- Access to CAMS global air quality forecast and analysis data through the legacy ECMWF public Web API service ended on 30 June 2021. The ADS is now the primary access path.
- The NRT ADS archive is a rolling archive; older data is migrated to reanalysis products over time.
- **Upcoming: Cycle 50r1** is scheduled for 12 May 2026 (see main ECMWF IFS entry for details on ocean/sea-ice coupling changes). Atmospheric composition changes accompanying 50r1 are documented at https://confluence.ecmwf.int/display/CKB/Implementation+of+IFS+cycle+50R1+for+CAMS.

---

## Official documentation
- CAMS Global forecast data documentation: https://confluence.ecmwf.int/display/CKB/CAMS%3A+Global+atmospheric+composition+forecast+data+documentation
- IFS Cycle 49r1 documentation — atmospheric composition: https://www.ecmwf.int/en/elibrary/81630-ifs-documentation-cy49r1-part-viii-atmospheric-composition
- CAMS Cycle 49r1 implementation page: https://confluence.ecmwf.int/display/COPSRV/Implementation+of+IFS+cycle+49r1+for+CAMS
- Evaluation reports (quarterly): https://atmosphere.copernicus.eu/eqa-reports-global-services
