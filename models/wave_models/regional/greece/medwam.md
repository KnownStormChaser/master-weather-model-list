# MEDWAM (Mediterranean Sea Waves Analysis and Forecast)

## What this model is
MEDWAM is an operational regional wave analysis and forecast system for the Mediterranean Sea, run by the **Hellenic Centre for Marine Research (HCMR)** in Greece and distributed through the **Copernicus Marine Service**.

It is a WAM-based spectral wave model covering the entire Mediterranean Sea and extending west to 18.125°W into the Atlantic Ocean to capture Atlantic swell propagation. The system is a **fully coupled wave component of the Mediterranean Forecasting System (MFS)** — it ingests time-varying surface currents and sea level from the Mediterranean Sea physics product and receives lateral boundary conditions from the Copernicus Global MFWAM wave product.

The official Copernicus Marine product identifier is `MEDSEA_ANALYSISFORECAST_WAV_006_017`. The modelling system is named **Med-WAV**, and the current operational configuration is **MEDWAM4** (the naming progression follows MEDWAM3 → MEDWAM4 across version upgrades).

---

## Who runs it
- **Organization:** Hellenic Centre for Marine Research (HCMR), operating as the Production Unit for the Mediterranean Monitoring and Forecasting Centre (Med MFC) under the Copernicus Marine Service
- **Country / region:** Greece (operator) / European Union (distribution)

---

## What area it covers
- **Coverage:** Entire Mediterranean Sea, extending west to 18.125°W into the Atlantic Ocean
- **Domain bounds:** 18.125°W – 36.2917°E, 30.1875°N – 45.9792°N
- **Grid dimensions:** 1307 × 380 (regular lat-lon grid)

---

## Basic details
- **Model type:** Regional deterministic wave model (coupled, with data assimilation)
- **Core wave model:** WAM Cycle 6
- **System name:** Med-WAV (MEDWAM4 configuration)
- **Horizontal resolution:** 1/24° (~4.6 km)
- **Spectral resolution:** 24 directional × 32 logarithmically distributed frequency bins
- **Forecast length:** 10 days (240 hours)
- **Analysis window:** 12 hours of hindcast per cycle
- **Update frequency:** 2× daily
- **Production cycles:** 00 UTC and 12 UTC
- **Publication time:** 06:00 UTC and 20:00 UTC
- **Temporal output resolution:** 1-hourly instantaneous
- **Analysis archive:** 2-year rolling archive on the Marine Data Store
- **Bathymetry:** GEBCO 30 arc-second interpolated to model grid

---

## Forcing and coupling
MEDWAM is both forced and coupled within the broader Mediterranean Forecasting System infrastructure:

- **Atmospheric forcing:**
  - ECMWF 10 m winds at 1/10° resolution
  - 6-hourly analysis winds for the analysis phase
  - Forecast winds at hourly intervals (days 1–3), 3-hourly intervals (days 4–6), 6-hourly intervals (days 7–10)
  - Free convective velocity over the oceans from ECMWF
- **Surface forcing (ocean coupling):** Hourly surface currents and sea level from `MEDSEA_ANALYSISFORECAST_PHY_006_013` (Mediterranean Sea physics product, Med-PHY)
- **Lateral boundary conditions:** Wave spectra from `GLOBAL_ANALYSISFORECAST_WAV_001_027` (Copernicus Global MFWAM)
- **Initial conditions:** Previous version restart file, with a 5-day spin-up period with data assimilation at 3-hourly intervals

This makes MEDWAM a genuinely coupled wave-ocean product, not a wave model forced only by atmospheric fields.

---

## Data assimilation
MEDWAM uses the inherent **Optimal Interpolation (OI)** data assimilation scheme of WAM Cycle 6, applied at 3-hourly intervals during the 12-hour analysis phase. Along-track satellite observations are assimilated:

### Significant wave height (SWH)
- Jason-3
- Sentinel-3 A and Sentinel-3 B
- Sentinel-6A
- SARAL / AltiKa
- CryoSat-2
- CFOSAT
- HAIYANG-2B

### 10 m wind speed (U10)
As of EIS Q4/2025, altimeter wind speed measurements are also assimilated. Equivalent U10 observations are optimally interpolated in the same manner as SWH observations, updating the ECMWF analysis winds in support of the corrections introduced by the SWH data assimilation.

Assimilated data come from the Copernicus Marine WAVE TAC product `WAVE_GLO_WAV_L3_SWH_NRT_OBSERVATIONS_014_001`.

---

## What it provides
Hourly instantaneous fields of integrated wave parameters and partitioned swell fields:

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
- Coordinates (e1t, e2t cell dimensions)
- Land-sea mask
- Bathymetry

---

## Data availability
- **Is the data free?** Yes (registration required)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (CF-1.6 conventions)
- **Product identifier:** `MEDSEA_ANALYSISFORECAST_WAV_006_017`
- **Dataset identifiers:**
  - `cmems_mod_med_wav_anfc_4.2km_PT1H-i` (hourly forecast and analysis fields)
  - `cmems_mod_med_wav_anfc_4.2km_static` (coordinates, mask, bathymetry)
- **Official access:** https://data.marine.copernicus.eu/product/MEDSEA_ANALYSISFORECAST_WAV_006_017/description
- **Licence:** Copernicus Marine Service licence (free with registration)

---

## Version history

### May 2025
- Data assimilation upgrade: altimeter 10 m wind speed (U10) observations added alongside SWH in the OI assimilation

### November 2023
- Additional output variables added
- Quality improvement and upstream data change
- Datasets renamed

### November 2022
- Quality improvement, file renaming, general revision

### September 2021
- Upstream data changes:
  - Nested within the Copernicus Global Wave system (`GLOBAL_ANALYSISFORECAST_WAV_001_027`)
  - ECMWF winds updated to higher spatial (1/10°) and temporal resolution

### January 2021
- Upgrade to twice-daily forecast delivery (00 UTC and 12 UTC cycles)
- Product and dataset naming changes
- Each file now contains 12 hours of data
- Model grid updated for consistency with the Mediterranean Sea physics product

### December 2019
- Forecast cycle moved from 12:00 UTC to 00:00 UTC
- Product dataset name changed

### January 2019
- New hourly datasets introduced

### January 2018
- Initial product release (V1.0, MEDWAM3 configuration)

---

## Relationship to other Mediterranean wave products
Several Mediterranean wave products are documented in this repository:

- **MEDWAM (this entry)** — WAM Cycle 6 at 1/24° (~4.6 km), data-assimilating, ocean-coupled, 10-day forecast, distributed via Copernicus Marine. Operated by HCMR (Greece).
- **[WW3-MARO](../france/ww3-maro-france.md)** — WAVEWATCH III at ~0.10°, forced by AROME, 3-day forecast, distributed via data.gouv.fr. Operated by Météo-France.

These are independent operational wave systems from different institutes using different model cores, different atmospheric forcing, different assimilation status, and different distribution channels. MEDWAM is the higher-resolution, longer-range, data-assimilating, ocean-coupled option; WW3-MARO is the shorter-range product with AROME-based forcing for targeted Mediterranean forecasting. Users comparing the two should note the fundamentally different model families (WAM vs WAVEWATCH III).

MEDWAM is the regional Mediterranean companion to:
- **[BALWAM](../finland/balwam.md)** — the analogous basin-wide Copernicus Marine wave product for the Baltic Sea (also WAM-based, FMI-operated)
- **[MFWAM Copernicus](../../global/france/mfwam-copernicus.md)** — the global MFWAM wave product that supplies MEDWAM's lateral boundary conditions

---

## Notes
- MEDWAM is part of the Med MFC (Mediterranean Monitoring and Forecasting Centre) portfolio, alongside the Mediterranean physics product (Med-PHY) that provides its surface current and sea level forcing. The two are designed as a coherent operational pair.
- The system's coupling to Med-PHY (hourly currents, hourly sea level) and nesting inside the global Copernicus wave product (`GLOBAL_ANALYSISFORECAST_WAV_001_027`) makes MEDWAM one of the more structurally integrated regional wave systems in operational distribution.
- The domain extends west of Gibraltar into the Atlantic to capture swell propagation entering the Mediterranean — this is visible in the 18.125°W western bound, which is substantially beyond the Mediterranean proper.
- The "MEDWAM" acronym in file naming reflects a system name-plus-version convention (MEDWAM3, MEDWAM4), not a distinct product identifier. Users referencing the product in code should use the Copernicus product ID (`MEDSEA_ANALYSISFORECAST_WAV_006_017`) for stability across version upgrades.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/MEDSEA_ANALYSISFORECAST_WAV_006_017/description
- Product User Manual (PUM): https://documentation.marine.copernicus.eu/PUM/CMEMS-MED-PUM-006-017.pdf
- Quality Information Document (QUID): https://documentation.marine.copernicus.eu/QUID/CMEMS-MED-QUID-006-017.pdf
- DOI (MEDWAM4): https://doi.org/10.25423/CMCC/MEDSEA_ANALYSISFORECAST_WAV_006_017_MEDWAM4
- DOI (MEDWAM3): https://doi.org/10.25423/CMCC/MEDSEA_ANALYSISFORECAST_WAV_006_017_MEDWAM3
- HCMR: https://www.hcmr.gr/
- Copernicus Marine Service: https://marine.copernicus.eu/
