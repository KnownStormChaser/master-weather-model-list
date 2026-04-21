# IBIWAM (Iberian-Biscay-Irish Waves Analysis and Forecast)

## What this model is
IBIWAM is a high-resolution regional wave analysis and forecast system for the **Iberian-Biscay-Irish (IBI) domain**, covering the Atlantic waters from the Iberian Peninsula north through the Bay of Biscay and into Irish coastal waters. It is operated by **Nologin** with supercomputing support from **CESGA (Centro de Supercomputación de Galicia)** in Spain, and distributed through the **Copernicus Marine Service**.

The system is built on the **MFWAM model** (the same wave model core used in Météo-France's global wave product), but with a substantially higher resolution (1/36° ≈ 2.7 km), tight two-way integration with the IBI ocean physics system for currents-wave coupling, and an advanced multi-sensor data assimilation scheme including altimeter SWH and directional wave spectra from CFOSAT and Sentinel-1A.

The official Copernicus Marine product identifier is `IBI_ANALYSISFORECAST_WAV_005_005`. The system is currently on version **IBI-V8** (operational since November 2024).

---

## Who runs it
- **Production Unit:** Nologin (operational suite) with CESGA supercomputing resources
- **Country:** Spain
- **Role in Copernicus Marine:** Production Unit for the IBI Monitoring and Forecasting Centre (IBI MFC) wave product
- **Upstream collaboration:** Météo-France (provides the MFWAM model code and CFOSAT wave spectral data)

---

## What area it covers
- **Coverage:** Iberian-Biscay-Irish waters (IBI domain)
- **Domain bounds:** 19°W – 5°E, 26°N – 56°N
- **Grid dimensions:** 1081 × 865 (regular lat-lon grid)
- **Native model grid:** 20°W – 17°E, 25°N – 64.6°N (product is post-processed from a larger native grid to the delivered IBI domain)

The domain includes:
- The full western and northern Iberian Peninsula coast
- The Bay of Biscay
- The French Atlantic coast
- The UK and Irish Atlantic coasts
- The western Mediterranean approach (Strait of Gibraltar and Alboran Sea fringe)
- Western North African Atlantic coast (Morocco to Mauritania boundary)

---

## Basic details
- **Model type:** Regional deterministic wave model (coupled, with data assimilation)
- **Core wave model:** MFWAM (based on ECWAM-IFS-47R1 code, with ST4 dissipation source terms from Ardhuin et al. 2010)
- **System name:** IBI-V8 (current operational version)
- **Horizontal resolution:** 1/36° (~2.7 km in mid-latitudes)
- **Spectral resolution:** 24 directions × 30 frequencies (range: 0.035 Hz – 0.56 Hz)
- **Forecast length:**
  - IBI-WAV-00H cycle: 1-day analysis + 5-day forecast
  - IBI-WAV-12H cycle: 10-day forecast
- **Update frequency:** 2× daily
- **Publication time:**
  - 00H cycle: 13:30 UTC
  - 12H cycle: 02:00 UTC (next day)
- **Temporal output resolution:** 1-hourly instantaneous
- **Analysis archive:** 2-year rolling archive
- **Bathymetry:** ETOPO1 (1 arc-minute, smoothed)

---

## Forcing and coupling
IBIWAM is coupled within the broader IBI MFC operational infrastructure:

- **Atmospheric forcing:** Hourly ECMWF 10 m winds
- **Surface currents forcing (ocean coupling):** Surface currents from the IBI ocean physics product `IBI_ANALYSISFORECAST_PHY_005_001`
- **Lateral boundary conditions:** Wave spectra from the Copernicus Global MFWAM product `GLOBAL_ANALYSISFORECAST_WAV_001_027`

Notably, the IBI wave system is **coupled bidirectionally** with the IBI ocean physics system — it consumes surface currents from the ocean model, and in turn generates wave-derived parameters (surface stress, wave-breaking induced turbulence in the mixed layer) that are used internally as forcing inputs by the IBI ocean circulation model's subsequent runs. This makes it a genuinely two-way coupled regional wave-ocean system, not merely a wave model forced by external fields.

---

## Data assimilation
IBIWAM uses **Optimal Interpolation** (OI, based on Lionello et al. 1992) for both significant wave height and directional wave spectra, applied as part of the analysis cycle.

### Significant wave height (SWH) assimilation
Along-track altimeter observations from Copernicus Marine Level-3 products:
- Jason-3
- SARAL
- CryoSat-2
- Sentinel-3 A and Sentinel-3 B
- Sentinel-6A
- **SWOT-NADIR** (added November 2024)
- **HY2B** (HaiYang-2B, added November 2024)

### Directional wave spectra assimilation
Added November 2022; applied to mean wavenumber components of each wave partition describing a dominant wave train:
- **CFOSAT** (supplied by Météo-France)
- **Sentinel-1A**

### OI configuration parameters
- Correlation length: 170 km
- Distance of influence: 650 km
- Background and observation error ratio: constant over the IBI domain

The assimilation scheme has been tuned specifically for the ST4 physics used in IBIWAM and induces corrections on the wave spectra primarily in the wind-sea portion of the frequency scale (Aouf and Lefevre 2015).

---

## What it provides
Hourly instantaneous fields of integrated wave parameters, partitioned swell fields, and maximum crest/trough variables:

### Total wave spectrum parameters
- Spectral significant wave height (Hm0)
- Wave periods: spectral moments (-1,0), (0,2), peak period (Tp)
- Mean wave direction and principal direction at spectral peak
- Maximum crest-to-trough wave height (VCMX)
- Height of the highest crest (VMXL)
- Stokes drift (U and V components)

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
- **Data formats:** NetCDF-4 (CF-1.8 conventions)
- **Product identifier:** `IBI_ANALYSISFORECAST_WAV_005_005`
- **Dataset identifiers:**
  - `cmems_mod_ibi_wav_anfc_0.027deg_PT1H-i` (hourly forecast and analysis fields)
  - `cmems_mod_ibi_wav_anfc_0.027deg_static` (coordinates, mask, bathymetry)
- **Official access:** https://data.marine.copernicus.eu/product/IBI_ANALYSISFORECAST_WAV_005_005/description
- **Licence:** Copernicus Marine Service licence (free with registration)

---

## Version history

### November 2024 — IBI-V8 (current)
- Model resolution increased from 1/20° (~5 km) to 1/36° (~2.7 km)
- Data assimilation upgrade: SWOT-NADIR and HY2B altimetric data added

### November 2023 — IBI-V7
- Model system update: same code and set-up as the IBI multiyear wave reanalysis product (`IBI_MULTIYEAR_WAV_005_006`), but maintaining 1/20° resolution
- New output variables: maximum crest height (VMXL) and maximum crest-to-trough height (VCMX)
- Static files updated, product and datasets renamed

### November 2022 — IBI-V6
- Data assimilation system upgraded to include directional wave spectra from CFOSAT and Sentinel-1A (in addition to multi-sensor SWH)
- Adapted to new Copernicus Marine template

### December 2020 — IBI-V6
- Forecast extension to 10 days for the second daily cycle (12H)

### July 2020 — IBI-V6
- Major upgrade: SWH altimeter data assimilation implemented (Optimal Interpolation)
- Surface currents forcing from IBI ocean circulation model introduced
- Upgraded MFWAM model code with improvements from the FP7 MyWave project
- Internal wave-to-ocean coupling parameters (surface stress, mixed-layer turbulence) added for use in the IBI ocean model run

### December 2019 — IBI-V5
- ECMWF wind forcing temporal resolution enhanced from 3-hourly to hourly

### July 2019 — IBI-V5
- Major spatial resolution increase from 0.10° to 0.05°
- New dataset for static files

### March 2018 — IBI-V4
- Minor model update improving surface stress characterization (new wave dissipation, sheltering parameter, Phillips spectrum tail for high-frequency part)

### April 2017 — IBI-V3
- Initial release of the IBI MFC Wave operational system

---

## Relationship to other wave products
IBIWAM occupies a distinct niche in the repository's wave model coverage:

### Shared model core, different systems
IBIWAM, [MFWAM Copernicus (global)](../../global/france/mfwam-copernicus.md), and [MFWAM GLOB01](../../global/france/mfwam-global-france.md) all use the MFWAM core but are run as separate operational systems with different resolutions, different forcings, different assimilation schemes, and different operators. Users should choose based on coverage and resolution needs:
- **IBIWAM** — highest resolution (1/36°) for IBI domain, with currents-wave coupling and directional spectral DA
- **MFWAM Copernicus (global)** — 1/12° globally, multi-satellite SWH + SAR spectral DA, no ocean coupling
- **MFWAM GLOB01** — 0.1° globally, hourly short-range output, no DA, no registration required

### Overlapping regional coverage
- **[WW3-WARP](../france/ww3-warp-france.md)** — Météo-France's WAVEWATCH III Atlantic regional product, distributed via data.gouv.fr. Uses a different model family (WW3 vs WAM/MFWAM), different operator (Météo-France vs Nologin/CESGA), different distribution, no documented data assimilation, and no documented currents-wave coupling. The two products overlap geographically but differ fundamentally in physics and system design.

### Sister Copernicus Marine regional wave products
- **[BALWAM](../finland/balwam.md)** — Baltic Sea (FMI, Finland)
- **[MEDWAM](../greece/medwam.md)** — Mediterranean Sea (HCMR, Greece)

Together, IBIWAM (IBI), MEDWAM (Med), and BALWAM (Baltic) form the core set of coupled / data-assimilating Copernicus Marine regional wave products, each run by a different national institute.

---

## Notes
- IBIWAM is one of the few operational regional wave systems in open distribution with genuine **two-way coupling to an ocean physics model** — wave outputs feed the IBI NEMO ocean model's subsequent run, not just the other way around. This closer integration is documented in Toledano et al. (2022).
- The domain extends substantially south into Moroccan and North African Atlantic waters, making IBIWAM relevant for users focused on Canary Current dynamics or Mauritanian/Moroccan coastal forecasting — uses that would normally look to a global product.
- The operator pairing of Nologin + CESGA is unusual for operational NWP and oceanographic distribution: Nologin provides the operational suite while CESGA (a public supercomputing centre in Galicia) provides the compute infrastructure. Both are Spanish entities.
- The product uses the same underlying MFWAM code base as Météo-France's global wave product (ECWAM-IFS-47R1), but with separately tuned physics, different resolution, and a different DA configuration specific to the IBI region.
- IBI-V8 (November 2024) includes some of the newest altimeter missions in its DA: SWOT-NADIR (launched 2022, NASA/CNES) and HaiYang-2B (Chinese altimetry mission). This reflects an active effort to ingest the newest available satellite wave observations.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/IBI_ANALYSISFORECAST_WAV_005_005/description
- Product User Manual (PUM): https://documentation.marine.copernicus.eu/PUM/CMEMS-IBI-PUM-005-005.pdf
- Quality Information Document (QUID): https://catalogue.marine.copernicus.eu/documents/QUID/CMEMS-IBI-QUID-005-005.pdf
- Key reference paper: Toledano et al. (2022), "Impacts of an Altimetric Wave Data Assimilation Scheme and Currents-Wave Coupling in an Operational Wave System: The New Copernicus Marine IBI Wave Forecast Service," *J. Mar. Sci. Eng.*, 10(4), 457. https://doi.org/10.3390/jmse10040457
- Nologin: https://nologin.es/
- CESGA: https://www.cesga.es/
- Copernicus Marine Service: https://marine.copernicus.eu/
