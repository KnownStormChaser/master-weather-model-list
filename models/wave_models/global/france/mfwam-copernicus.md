# MFWAM Global (Copernicus Marine)

## What this model is
This entry documents Météo-France's global MFWAM wave forecast as distributed through the **Copernicus Marine Service** (product identifier `GLOBAL_ANALYSISFORECAST_WAV_001_027`).

MFWAM is a third-generation spectral ocean wave model derived from the WAM model and operated by Météo-France. The Copernicus Marine global product runs MFWAM on a 1/12° grid (~8 km) with multi-satellite data assimilation of significant wave height and Sentinel-1 SAR wave spectra. It provides a 10-day forecast twice daily, with a 2-year rolling archive of analyses available through the Marine Data Store.

Météo-France also distributes a separate global MFWAM product (GLOB01 at 0.1°) through its national data portal. That product is a distinct operational product run with different forcing and output cadence — see the [MFWAM GLOB01 entry](./mfwam-global-france.md) for details.

The official Copernicus Marine product name is "Global Ocean Wave Analysis and Forecast." The system name in Météo-France's internal nomenclature is **GLO12v3**.

---

## Who runs it
- **Originating organization:** Météo-France (Global Monitoring and Forecasting Centre for the Copernicus Marine global wave product)
- **Country / region:** France (operator) / European Union (distribution)
- **Distribution:** Copernicus Marine Service (Mercator Ocean International)

---

## What area it covers
- **Coverage:** Global
- **Domain bounds:** 180°W – 180°E, 89°S – 90°N
- **Grid dimensions:** 4320 × 2041 (regular lat-lon)

---

## Basic details
- **Model type:** Global deterministic wave model (with data assimilation)
- **Core wave model:** MFWAM (ECWAM-IFS source code, cycle 41R1)
- **System name:** GLO12v3
- **Horizontal resolution:** 1/12° (~8 km)
- **Spectral resolution:** 24 directions × 30 frequencies
- **Forecast length:** 10 days (240 hours)
- **Update frequency:** 2× daily (00 and 12 UTC)
- **Temporal output resolution:** 3-hourly instantaneous (at 00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Archive:** 2-year rolling analysis archive on the Marine Data Store
- **Bathymetry:** ETOPO2 / NOAA

---

## Forcing
- **Atmospheric forcing:** ECMWF wind and ice fraction
  - 6-hourly analysis
  - 3-hourly forecasts
  - Native forcing resolution: 1/10°

Analyses are performed 4 times per day (00, 06, 12, 18 UTC) internally with updated ECMWF forcings; the product is delivered twice daily (00 and 12 UTC) to the Marine Data Store.

---

## Data assimilation
The Copernicus Marine global MFWAM uses **Optimal Interpolation** with a **daily assimilation cycle** and assimilates observations from multiple satellite missions:

### Significant wave height (SWH) from altimeters
- Jason-3
- SARAL
- CryoSat-2
- Sentinel-3 A and Sentinel-3 B
- CFOSAT (SWIM-nadir instrument)
- Sentinel-6

### Wave spectra
- Sentinel-1 SAR wave spectra

This multi-satellite assimilation is the principal operational differentiator between this product and Météo-France's GLOB01 distribution.

---

## What it provides
The Copernicus Marine product provides full integrated wave parameters and partitioned swell fields:

### Total wave spectrum parameters
- Spectral significant wave height (Hm0)
- Wave periods: spectral moments (-1,0), (0,2), peak period (Tp)
- Mean wave direction and principal direction at spectral peak
- Stokes drift (U and V components)
- Maximum crest-to-trough wave height (VCMX)
- Height of the highest crest (VMXL)

### Partitioned fields
- Wind wave: height, period, direction
- Primary swell: height, period, direction
- Secondary swell: height, period, direction

### Static file
- Bathymetry (separate static dataset)

---

## Data availability
- **Is the data free?** Yes (registration required)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.6 conventions)
- **Product identifier:** `GLOBAL_ANALYSISFORECAST_WAV_001_027`
- **Dataset identifiers:**
  - `cmems_mod_glo_wav_anfc_0.083deg_PT3H-i` (forecast and analysis fields)
  - `cmems_mod_wav_anfc_0.083deg_static` (bathymetry)
- **Official access:** https://data.marine.copernicus.eu/product/GLOBAL_ANALYSISFORECAST_WAV_001_027/description
- **Licence:** Copernicus Marine Service licence (free with registration)

---

## Version history

### November 2024
- New variable added: height of the highest crest (VMXL)

### November 2023
- Assessment of GLO12V4 vs GLO12V3 current data (upstream data change)
- New variable added: maximum crest-to-trough wave height (VCMX)

### November 2022
- New nomenclature and new template

### December 2021
- Upgrade to twice-daily delivery

### December 2020
- Forecast length extended to 10 days
- SWIM-nadir SWH from CFOSAT added to the data assimilation system

### January 2018
- Initial product release (`GLOBAL_ANALYSISFORECAST_WAV_001_027` V1.0)

---

## Relationship to other MFWAM products
Météo-France runs MFWAM in multiple operational configurations distributed through different channels:

- **[MFWAM GLOB01](./mfwam-global-france.md)** — global 0.1° product distributed via data.gouv.fr, with ARPEGE wind forcing and hourly short-range output
- **This entry (Copernicus)** — global 1/12° product distributed via Copernicus Marine, with ECMWF forcing and multi-satellite wave data assimilation
- **[MFWAM High-Resolution (FRANGP0025)](../../regional/france/mfwam-hr-france.md)** — European regional 0.025° product nested within the Météo-France MFWAM chain

Users primarily interested in global wave forecasting with observational wave-data assimilation should use this entry's Copernicus product. Users who need hourly short-range output without registration should use GLOB01. Users focused on European coastal and offshore conditions should look to FRANGP0025.

---

## Notes
- The Copernicus Marine product is part of the Global Monitoring and Forecasting Centre (GLO MFC) portfolio, alongside the coupled physics product `GLOBAL_ANALYSISFORECAST_PHY_001_027`. The wave product does not share initial conditions or state with the physics product directly, but they are designed as a coherent operational pair.
- The product is not coupled to an ocean physics model in the same way as BALWAM (which ingests time-varying currents, sea surface height, and ice concentration from a companion Baltic ocean system). The Copernicus Marine global MFWAM is forced only by ECMWF winds and ice fraction.
- Copernicus Marine is the same distribution channel used for BALWAM, Mediterranean, Arctic, Black Sea, and several other regional wave products, all distributed through the Marine Data Store infrastructure.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/GLOBAL_ANALYSISFORECAST_WAV_001_027/description
- Product User Manual (PUM): https://documentation.marine.copernicus.eu/PUM/CMEMS-GLO-PUM-001-027.pdf
- Quality Information Document (QUID): https://documentation.marine.copernicus.eu/QUID/CMEMS-GLO-QUID-001-027.pdf
- Copernicus Marine Service: https://marine.copernicus.eu/
- Météo-France: https://meteofrance.com/
