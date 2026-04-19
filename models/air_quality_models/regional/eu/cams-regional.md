# CAMS Regional (European Air Quality Ensemble)

## What this model is
The CAMS Regional production is the Copernicus Atmosphere Monitoring Service's operational European air quality forecasting system. It is a **multi-model ensemble** based on 11 independent, state-of-the-art air quality models developed by 11 different European research institutions.

Each of the 11 models runs independently, producing its own daily forecast and analysis. The ENSEMBLE product is the **median** of the individual model outputs at each grid point and time step; it currently provides the best consensus estimate from the system and is the most widely used operational product.

Both the ENSEMBLE output and all 11 individual model outputs are publicly available through the Copernicus Atmosphere Data Store. CAMS Regional forecasts and analyses cover the main atmospheric pollutants, pollen species, and selected aerosol components at near-surface levels across Europe.

---

## Who runs it
The system is coordinated by ECMWF on behalf of the European Union (Copernicus Atmosphere Monitoring Service), with 11 participating models operated by:

| Model | Institution | Country |
| --- | --- | --- |
| CHIMERE | INERIS | France |
| DEHM | Aarhus University | Denmark |
| EMEP | MET Norway | Norway |
| EURAD-IM | Jülich IEK | Germany |
| GEM-AQ | IEP-NRI | Poland |
| LOTOS-EUROS | KNMI and TNO | Netherlands |
| MATCH | SMHI | Sweden |
| MINNI | ENEA | Italy |
| MOCAGE | METEO-FRANCE | France |
| MONARCH | BSC | Spain |
| SILAM | FMI | Finland |

- **Overall coordination:** ECMWF / Copernicus Atmosphere Monitoring Service
- **Region:** European Union / Europe

---

## What area it covers
- **Coverage:** European domain — 25° W to 45° E, 30° N to 72° N (since 12 June 2019; previously 25° W to 45° E, 30° N to 70° N)
- **ENSEMBLE grid:** 0.1° × 0.1° regular latitude-longitude

---

## Basic details
- **Model type:** Regional multi-model air quality ensemble
- **Ensemble approach:** Median of 11 independent air quality models (not a perturbation-based ensemble)
- **Horizontal resolution:** ~10 km on the common ENSEMBLE grid (0.1°)
- **Individual model resolutions:** Vary by model (approximately 10 to 20 km native; all interpolated to the common ENSEMBLE grid)
- **Vertical levels (output):** Surface, 50 m, 100 m, 250 m, 500 m, 750 m, 1000 m, 2000 m, 3000 m, 5000 m (AGL)
- **Forecast length:** 96 hours (4 days)
- **Update frequency:** 1× daily (00 UTC base time)
- **Temporal output resolution:** Hourly (for forecasts)
- **Analyses:** Daily, combining 1-day-old surface observations with each model's forecast

---

## Meteorological driver
All 11 models use meteorology from the **00:00 UTC operational ECMWF IFS forecast** from the previous day. Chemical lateral boundary conditions come from the CAMS Global IFS production. Anthropogenic emissions and biomass burning emissions are supplied by the CAMS emission inventories.

---

## Chemistry and aerosols
Each of the 11 models uses its own chemistry and aerosol schemes; these vary significantly across the ensemble. Some key common features of the combined output:

- **Primary pollutants provided:** O3, NO2, NO, SO2, CO, PM2.5, PM10, NH3, formaldehyde, glyoxal, peroxyacyl nitrates (PANs), non-methane VOCs
- **Aerosol components and fractions:** Dust (as PM10 fraction), sea salt dry (as PM10 fraction), PM10 from wildfires only, secondary inorganic aerosol (SIA, as PM2.5 fraction), total organic matter (as PM2.5 fraction), nitrate / ammonium / sulfate (as PM2.5 fractions, added December 2024)
- **Elemental carbon:** Total anthropogenic EC and residential-sector EC (as PM2.5 fractions)
- **Pollen species:** Alder, birch, grass, mugwort, olive, ragweed — now forecast year-round (previously species-specific seasonal windows)

### Species considered experimental
The following are considered experimental and should be used with caution: SIA, CHOCHO, HCHO, NO, PANs, NMVOC, PM10_WF, PM25_ECtot, PM25_ECres, NH3, DUST, Sea Salt Dry, Total Organic Matter, NO3, NH4, SO4.

---

## Emissions
- **Anthropogenic:** CAMS_REG (current version CAMS_REG v7 for reference year 2023, implemented in November/December 2024; CAMSREG8 for reference year 2024 implemented in November 2025)
- **Biomass burning:** CAMS GFAS (hourly emissions integrated since the initial U0 upgrade in February 2019)
- **Biogenic:** Handled by each individual model; inventories vary across the ensemble

---

## Data assimilation
Each of the 11 models performs its own daily surface-observation data assimilation independently. There is no shared ensemble-level assimilation step.

- **Observation source:** European Environment Agency (EEA) near-real-time surface air quality measurements
- **Pollutants assimilated:** O3, NO2, PM2.5, PM10, CO, SO2
- **Station selection:** AI-based station selection pipeline is run annually to identify background-representative stations and remove outliers. Selection is pollutant-specific.
- **Two station sets:** Stations are split into an assimilation set (used by models in NRT analysis) and an independent evaluation set (used for quarterly verification reports). This two-set structure now applies to all six assimilated pollutants.

---

## What it provides

### ENSEMBLE products (median of 11 models)
Hourly 96-hour forecasts and daily analyses of:
- Gases: O3, NO2, NO, SO2, CO, NH3, HCHO (formaldehyde), CHOCHO (glyoxal), PANs, non-methane VOCs
- Particulate matter: PM2.5, PM10, and the above-listed PM fraction components
- Pollen: 6 species (alder, birch, grass, mugwort, olive, ragweed) — surface only

### Individual model outputs
All 11 models publish their own forecasts and analyses separately on the ADS, for users who want a specific model rather than the ensemble median. Individual model outputs are useful for understanding inter-model spread and uncertainty.

---

## Data availability
- **Is the data free?** Yes (registration and Copernicus licence acceptance required)
- **Is the data downloadable?** Yes
- **Data formats:**
  - ENSEMBLE output: NetCDF and GRIB2
  - Individual model outputs: NetCDF
- **Primary access:** CAMS Atmosphere Data Store (ADS)
  - Web interface: https://ads.atmosphere.copernicus.eu/datasets/cams-europe-air-quality-forecasts?tab=download
  - Programmatic access via CDS API: https://ads.atmosphere.copernicus.eu/how-to-api
- **Near-real-time subset (ENSEMBLE only, last 3 days, time-critical applications):** ECMWF Data Portal SFTP/HTTPS — https://confluence.ecmwf.int/display/CKB/SFTP+and+HTTPS+access+to+CAMS+European+air+quality+forecast+data
- **Web Map Service (WMS):** https://confluence.ecmwf.int/display/CKB/WMS+for+CAMS+Global+and+European+air+quality+products
- **Archive:** The NRT product is maintained as a 3-year rolling archive on the ADS; older data is superseded by reanalysis products.

### Data availability guarantees
- Ensemble 00–24h and 25–48h forecasts: by 08:00 UTC
- Ensemble 49–72h and 73–96h forecasts: by 10:00 UTC
- Ensemble analyses: by 12:00 UTC

---

## Version history

### U10 — November 2025
- New anthropogenic emissions CAMSREG8 for reference year 2024
- Improvements in individual models

### U9 — November/December 2024
- New anthropogenic emissions CAMS_REG v7 for reference year 2023
- Adaptation to IFS and CAMS Cycle 49r1 (meteorological and chemical driver update)
- New PM2.5 component fractions added: nitrate, sulfate, ammonium (data from 3 December 2024)

### U8 — November 2023 / U8b — June 2024
- New anthropogenic emissions CAMS-REG v6.1 for reference year 2022
- New products added: Total Organic Matter (PM2.5 fraction), Sea Salt Dry (PM10 fraction)
- Improvements in individual models

### U7 — November 2022 / U7b — June 2023
- Formaldehyde (HCHO) and glyoxal (CHOCHO) added as output species
- Adaptation to CAMS Cycle 48r1

### U6 — June 2022
- Ensemble expanded from 9 to **11 members** with addition of MONARCH (BSC, Spain) and MINNI (ENEA, Italy)

### U5 — March 2022
- New CAMS-REG v5.1 REF2 emissions for 2018
- Two new vertical output levels (100 m and 750 m)

### U3 and U4 — February and June 2020
- Pollen species added: alder, mugwort
- Elemental carbon source-decomposed species added (total, residential)

### U1.2 — October 2019
- Ensemble expanded from 7 to 9 members with addition of DEHM (Aarhus) and GEMAQ (IEP-NRI, Poland)

### U1.1 — June 2019
- Domain extended 2° north (northern boundary from 70° N to 72° N)
- IFS driver migrated to 137 vertical levels
- First PM aerosol fractions added (dust in PM10, secondary inorganic aerosols in PM2.5)

### U0 — February 2019
- GFAS hourly biomass-burning emissions integrated into the system

---

## Notes
- CAMS Regional is a **multi-model ensemble**, structurally different from perturbation-based ensembles like GEFS or GEPS. The 11 members are independently developed operational air quality models from different institutions, and the "spread" represents structural uncertainty across modelling approaches rather than initial-condition sensitivity.
- Model outputs are intended for background air pollution at regional scales — they are **not suitable for clinical trials or for fine-scale local (sub-grid) forecasting** near specific sources.
- A separate **CAMS Regional European air quality reanalysis** product is also available for scientific and long-term studies.
- Individual model fact sheets (2025) for each of the 11 models are linked from the main documentation page.

---

## Official documentation
- CAMS Regional forecast and analysis documentation: https://confluence.ecmwf.int/display/CKB/CAMS+Regional%3A+European+air+quality+analysis+and+forecast+data+documentation
- CAMS Regional reanalysis documentation: https://confluence.ecmwf.int/display/CKB/CAMS+Regional%3A+European+air+quality+reanalyses+data+documentation
- Individual model evaluation platform: https://regional.atmosphere.copernicus.eu/evaluation.php
- Regional production technical documentation (2021): https://confluence.ecmwf.int/download/attachments/202173092/CAMS50_2018SC2_D2.0.2-U3_Models_documentation_202101_v1.docx
- MACC-II ensemble paper (Marécal et al., 2015): https://www.geosci-model-dev.net/8/2777/2015/gmd-8-2777-2015.html
