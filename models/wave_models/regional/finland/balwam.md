# BALWAM (Baltic Sea Wave Analysis and Forecast)

## What this model is
BALWAM is an operational regional wave analysis and forecast system for the Baltic Sea, run by the Finnish Meteorological Institute (FMI) and distributed through the Copernicus Marine Service.

It is a WAM-based spectral wave model covering the entire Baltic Sea including the transition region toward the North Sea (Danish Belts, Kattegat, Skagerrak). Unlike many operational wave models, BALWAM is fully coupled to a companion ocean physics product, receiving time-varying surface currents, sea surface height, and ice concentration from the Baltic Sea Analysis and Forecast physics system.

The official Copernicus Marine product identifier is `BALTICSEA_ANALYSISFORECAST_WAV_003_010`. "BALWAM" is the short name used by FMI and appears in the product file naming convention (`BAL_WAM_WAV${date}.nc`).

---

## Who runs it
- **Organization:** Finnish Meteorological Institute (FMI), operating as part of the Baltic Sea Monitoring and Forecasting Centre (BAL MFC) under the Copernicus Marine Service
- **Country / region:** Finland (operator) / European Union (distribution)

---

## What area it covers
- **Coverage:** Entire Baltic Sea, including the Danish Belts, Kattegat, and Skagerrak
- **Domain bounds:** 9.01°E – 30.21°E, 53.01°N – 65.91°N

---

## Basic details
- **Model type:** Regional deterministic wave model (coupled to ocean physics)
- **Core wave model:** WAM cycle 4.7 (with ST4 source term package)
- **Horizontal resolution:** 1 nautical mile (approximately 1.85 km; 0.017° latitude × 0.028° longitude)
- **Forecast length:** 9 days (216 hours) for both daily cycles
- **Update frequency:** 2× daily
- **Production cycles:** 00 UTC and 12 UTC
- **Publication time:** 10:00 UTC and 22:00 UTC
- **Temporal output resolution:** 1-hourly instantaneous

---

## Forcing and coupling
BALWAM is forced by a hybrid atmospheric scheme and coupled to the Baltic Sea ocean physics product:

- **Atmospheric forcing:**
  - MetCoOp-HARMONIE for the first 66 hours of the forecast
  - ECMWF deterministic forecast thereafter
- **Surface current forcing:** Hourly fields from `BALTICSEA_ANALYSISFORECAST_PHY_003_006`
- **Sea surface height forcing:** 15-minute fields from `BALTICSEA_ANALYSISFORECAST_PHY_003_006`
- **Ice concentration:** Hourly fields from `BALTICSEA_ANALYSISFORECAST_PHY_003_006`
- **Lateral boundary conditions:** Wave spectra from ECMWF deterministic forecast
- **Bathymetry:** Based on EMODnet
- **Data assimilation:** None (wave observations not assimilated; initial conditions come from the previous hindcast)

---

## What it provides
Hourly instantaneous fields of integrated wave parameters including:

- Spectral significant wave height (Hm0) for total sea, wind wave, primary swell, and secondary swell
- Wave periods: spectral moments (-1,0), (0,2), (0,1), and spectral peak period
- Mean wave direction and principal direction at spectral peak
- Stokes drift (U and V components)
- Maximum crest height and maximum crest-to-trough wave height
- Partitioned wind wave and swell parameters (height, period, direction)

Static files provide the land-sea mask and bathymetry.

---

## Data availability
- **Is the data free?** Yes (registration required)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.0 conventions)
- **Product identifier:** `BALTICSEA_ANALYSISFORECAST_WAV_003_010`
- **Dataset identifier:** `cmems_mod_bal_wav_anfc_PT1H-i` (forecast), `cmems_mod_bal_wav_anfc_static` (static fields)
- **File naming:** `BAL_WAM_WAV${date}.nc`
- **Archive:** Rolling 2-year analysis archive available on the Copernicus Marine server
- **Official access:** https://data.marine.copernicus.eu/product/BALTICSEA_ANALYSISFORECAST_WAV_003_010/description
- **DOI:** https://doi.org/10.48670/moi-00011
- **Licence:** Copernicus Marine Service licence (free with registration)

---

## Version history

### November 2025
- Forecast length extended to 9 days for both 00Z and 12Z cycles

### November 2024
- Forecast length extended from 6 to 10 days for the 00Z cycle (12Z remained shorter)

### November 2023
- WAM upgraded from cycle 4.6.2 to cycle 4.7
- Source term package updated to ST4
- Bathymetry and grid-obstruction fields updated
- Two new parameters added: VMXL (maximum crest height) and VCMX (maximum crest-to-trough height)

### November 2022
- Coupling to NEMO ocean model extended to include time-varying sea surface height

### December 2020
- Upgraded coupling to NEMO ocean model with time-varying currents and ice concentration
- Model grid and bathymetry updated to match the Baltic Sea physics product

### March 2020
- Forecast length extended from 5 to 6 days

### July 2019
- Wave refraction due to surface currents added
- Wind-wave coupling parameter retuned

### April 2018
- Enhanced ice data treatment in the wave model

### January 2017
- Initial product release (BALTICSEA_ANALYSISFORECAST_WAV_003_010 V1.0)

---

## Relationship to other wave models in the Baltic
BALWAM and SWAN-EST (Estonian Meteorological and Hydrological Institute) are complementary rather than redundant:

- BALWAM is WAM-based and optimized for basin-wide wave propagation across the entire Baltic. It provides uniform 1 nautical mile coverage and is coupled to a full Baltic Sea ocean physics system.
- SWAN-EST is based on the SWAN nearshore wave model and is optimized for shallow-water processes and high-resolution coastal dynamics along the Estonian coastline.

Users needing basin-wide wave conditions with ocean-physics coupling should look to BALWAM; users needing high-fidelity nearshore wave dynamics in Estonian waters should look to SWAN-EST.

---

## Notes
- BALWAM is the Baltic regional counterpart to other Copernicus Marine wave products (Mediterranean, Black Sea, Arctic, Iberian-Biscay-Irish, Global) — all distributed through the same Marine Data Store infrastructure.
- The MetCoOp-HARMONIE atmospheric forcing for the short range is the same Nordic collaboration system that underlies the MEPS ensemble documented elsewhere in this repository.
- The Copernicus Marine product description page currently lists forecast length as "10 days (00Z) / 6 days (12Z)" and "2 × 2 km" resolution; the November 2025 Product User Manual supersedes both: 9 days for both cycles, 1 nautical mile resolution.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/BALTICSEA_ANALYSISFORECAST_WAV_003_010/description
- Product User Manual (PUM): https://documentation.marine.copernicus.eu/PUM/CMEMS-BAL-PUM-003-010.pdf
- Quality Information Document (QUID): https://documentation.marine.copernicus.eu/QUID/CMEMS-BAL-QUID-003-010.pdf
- Copernicus Marine Service: https://marine.copernicus.eu/
- FMI: https://en.ilmatieteenlaitos.fi/
