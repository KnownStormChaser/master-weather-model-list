# BSeas7 (Black Sea Waves Analysis and Forecast)

## What this model is
BSeas7 is an operational regional wave analysis and forecast system for the Black Sea and Sea of Azov, run by **Helmholtz-Zentrum Hereon** (Germany) and distributed through the **Copernicus Marine Service**.

It is a WAM Cycle 6 spectral wave model covering the full Black Sea basin plus (as of November 2024) the Sea of Azov, at 1/40° (~2.5 km) horizontal resolution. The system is forced by ECMWF winds and by sea surface height and surface currents from the companion Black Sea physics product, with sequential Optimal Interpolation assimilation of altimetric significant wave height and wind speed. Since the November 2024 upgrade, it runs twice daily (previously once daily), providing 12-hour analysis plus 10-day forecast per cycle.

The official Copernicus Marine product identifier is `BLKSEA_ANALYSISFORECAST_WAV_007_003`. The operational system name is **BSeas7**. The product is also known as "BLK-WAV" or "BS-Waves" in Copernicus Marine documentation.

---

## Who runs it
- **Production Unit:** Helmholtz-Zentrum Hereon (formerly HZG — Helmholtz-Zentrum Geesthacht)
- **Country:** Germany
- **Role in Copernicus Marine:** Production Unit for the Black Sea Monitoring and Forecasting Centre (BS MFC) wave product

---

## What area it covers
- **Coverage:** Black Sea and Sea of Azov
- **Domain bounds:** 27.25°E – 42.00°E, 40.50°N – 47.325°N
- **Grid dimensions:** 591 × 274 (regular lat-lon)
- **Active wave model grid points:** 81,531
- **Azov Sea:** Added in November 2024 (previously excluded)

---

## Basic details
- **Model type:** Regional deterministic wave model (ocean-forced, with data assimilation)
- **Core wave model:** WAM Cycle 6 (shallow-water version)
- **System name:** BSeas7
- **Horizontal resolution:** 1/40° (~2.5 km)
- **Spectral resolution:** 24 directions × 30 frequencies
- **Forecast length:** 10 days (240 hours) per cycle
- **Analysis window:** 12 hours per cycle
- **Update frequency:** 2× daily (since November 2024; previously 1× daily)
- **Production cycles:** 00 UTC and 12 UTC
- **Target delivery time:** 00:00 UTC and 12:00 UTC
- **Temporal output resolution:** 1-hourly instantaneous
- **Analysis archive:** Rolling 2-year analysis archive on the Marine Data Store
- **Bathymetry:** GEBCO 30 arc-second, interpolated onto the model grid
- **Physical processes:** Depth refraction and wave breaking represented

---

## Forcing
BSeas7 is forced by ECMWF atmospheric fields and by surface fields from the companion Black Sea physics product:

- **Atmospheric forcing:** ECMWF 10 m winds at 1/10° resolution
  - 6-hourly analysis winds
  - 1-hourly forecast winds for the first 3 days
  - 3-hourly forecast winds for days 4–6
  - 6-hourly forecast winds for days 7–10
- **Surface forcing:** Sea surface height and surface currents from `BLKSEA_ANALYSISFORECAST_PHY_007_001` (Black Sea physics product)
- **Lateral boundary conditions:** None (the Black Sea is a closed basin)
- **Initial conditions:** Initial 2D wave spectrum generated via fetch law from the local wind field

---

## Data assimilation
BSeas7 uses **sequential Optimal Interpolation** (the inherent DA scheme of WAM Cycle 6) during the 12-hour analysis phase of each cycle. Data assimilation was added in the November 2022 upgrade.

### Assimilated observations
- Significant wave height and wind speed from satellite altimetry
- Source: Copernicus Marine WAVE TAC product `WAVE_GLO_PHY_SWH_L3_NRT_014_001`

The specific altimetry missions contributing to this Level-3 product vary with the TAC's operational configuration; users needing the precise sensor list at a given date should consult the WAVE TAC documentation.

---

## What it provides
Hourly instantaneous fields of integrated wave parameters, partitioned swell fields, and maximum crest/trough variables:

### Total wave spectrum parameters
- Spectral significant wave height (Hm0)
- Wave periods: spectral moments (-1,0), (0,2), peak period (Tp)
- Mean wave direction and principal direction at spectral peak
- Stokes drift (U and V components)
- Height of the highest crest (VMXL)
- Maximum crest-to-trough wave height (VCMX)

### Partitioned fields
- Wind wave: height, period, direction
- Primary swell: height, period, direction
- Secondary swell: height, period, direction

### Static fields
- Cell dimensions (e1t, e2t)
- Land-sea mask
- Bathymetry

---

## Data availability
- **Is the data free?** Yes (registration required)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.6 conventions)
- **Product identifier:** `BLKSEA_ANALYSISFORECAST_WAV_007_003`
- **Dataset identifiers:**
  - `cmems_mod_blk_wav_anfc_2.5km_PT1H-i` (hourly forecast and analysis)
  - `cmems_mod_blk_wav_anfc_2.5km_static` (coordinates, mask, bathymetry)
- **File naming:** `{YYYYMMDD}{00|12}_h-Hereon--WAVE-BSeas7-BLK-b{YYYYMMDD}_{an|fc}{00|12}-sv10.00.nc`
- **File size:** ~47 MB per hourly file; ~2 MB for static file
- **Official access:** https://data.marine.copernicus.eu/product/BLKSEA_ANALYSISFORECAST_WAV_007_003/description
- **DOI:** https://doi.org/10.25423/CMCC/BLKSEA_ANALYSISFORECAST_WAV_007_003_EAS5
- **Licence:** Copernicus Marine Service licence (free with registration)

---

## Version history

### November 2024 — Issue 4.0 (current)
- Second forecast cycle added (system moved from 1× to 2× daily production)
- Domain extended to include the **Sea of Azov**

### November 2023 — Issue 3.2
- LATEMAR maximum variables added (VMXL — height of the highest crest, VCMX — maximum crest-to-trough height)

### November 2022 — Issue 3.1
- **Data assimilation added:** sequential Optimal Interpolation of altimetric SWH and wind speed

### December 2021 — Issue 3.0
- New product release

### September 2020 — Issue 2.0
- New product name including new dataset (part of a broader Copernicus Marine product identifier refresh)

### April 2020 — Issue 1.2
- Target delivery time changed from 00:00 UTC to 12:00 UTC
- New template and updated time-series temporal coverage

### January 2019 — Issue 1.1
- Addition of static files description

### November 2016 — Issue 1.0
- Initial release

---

## Relationship to other wave products

### Copernicus Marine regional wave portfolio
BSeas7 is part of the Copernicus Marine set of coupled regional wave forecasting systems, each run by a different national institute:

- **[BALWAM](../finland/balwam.md)** — Baltic Sea (FMI, Finland; WAM Cycle 4.7)
- **[MEDWAM](../greece/medwam.md)** — Mediterranean Sea (HCMR, Greece; WAM Cycle 6)
- **[IBIWAM](../spain/ibiwam.md)** — Iberian-Biscay-Irish waters (Nologin/CESGA, Spain; MFWAM)
- **[ARCWAM](../norway/arcwam.md)** — Arctic Ocean (MET Norway; WAM Cycle 4.7)
- **[AMM15-WW3](../uk/amm15-ww3-uk.md)** — North-West European Shelf (UK Met Office; WAVEWATCH III v7.1)
- **This entry** — Black Sea and Sea of Azov (Hereon, Germany; WAM Cycle 6)

Together, these six systems form the full set of Copernicus Marine regional wave forecasting products for European regional seas and adjacent waters. BSeas7 shares its WAM Cycle 6 core with MEDWAM; all the WAM-family products share architectural lineage, though each is tuned and configured for its own basin.

### Companion physics product
BSeas7 is paired with `BLKSEA_ANALYSISFORECAST_PHY_007_001` (Black Sea physics analysis and forecast), which provides the sea surface height and surface currents forcing. The two products are designed as a coherent operational pair within the Black Sea MFC portfolio.

---

## Notes
- BSeas7 is notable for being one of the few operational wave products covering the Black Sea with open distribution. The basin is semi-enclosed with no significant lateral wave boundaries, so the system's skill is primarily determined by local wind forcing quality and the data assimilation of altimetric observations.
- The November 2024 addition of the Sea of Azov is operationally meaningful: the Azov is very shallow (mean depth ~7 m) with complex wave-wind dynamics, and its inclusion makes the product more useful for users operating in the northern Black Sea coastal zone.
- The DOI includes "EAS5" in its identifier, which refers to the operational system version lineage (the EAS5 system tag is inherited from the Black Sea MFC's broader naming convention). This appears in the dataset citation but is not part of the product ID used for data access.
- The production chain figure in the PUM (not reproduced here) shows how each 12-hour analysis is combined with the subsequent 10-day forecast into the aggregated hourly time series delivered to the MDS. Users pulling data near the current date will see a mix of analysis (for hours before T) and forecast (for hours after T), with the analysis fields being the authoritative best estimate.
- Coupling is listed as N/A in the PUM — unlike IBIWAM or AMM15-WW3, there is no two-way feedback from the wave model back into the Black Sea physics system. The physics supplies SSH and surface currents as one-way forcing.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/BLKSEA_ANALYSISFORECAST_WAV_007_003/description
- Product User Manual (PUM): https://catalogue.marine.copernicus.eu/documents/PUM/CMEMS-BLK-PUM-007-003.pdf
- Quality Information Document (QUID): https://catalogue.marine.copernicus.eu/documents/QUID/CMEMS-BLK-QUID-007_003.pdf
- DOI: https://doi.org/10.25423/CMCC/BLKSEA_ANALYSISFORECAST_WAV_007_003_EAS5
- Helmholtz-Zentrum Hereon: https://www.hereon.de/
- Copernicus Marine Service: https://marine.copernicus.eu/

### Citation
Staneva, J., Ricker, M., & Behrens, A. (2022). *Black Sea Waves Analysis and Forecast (CMEMS BS-Waves, EAS5 system)* (Version 1) [Data set]. Copernicus Monitoring Environment Marine Service (CMEMS). https://doi.org/10.25423/CMCC/BLKSEA_ANALYSISFORECAST_WAV_007_003_EAS5
