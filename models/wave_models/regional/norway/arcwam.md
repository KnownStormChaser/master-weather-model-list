# ARCWAM (Arctic Ocean Wave Analysis and Forecast)

## What this model is
ARCWAM is an operational regional wave analysis and forecast system for the Arctic Ocean and Nordic Seas, run by the **Norwegian Meteorological Institute (MET Norway)** and distributed through the **Copernicus Marine Service**.

It is a WAM-based spectral wave model covering the Arctic region north of approximately 53°N, with two distinguishing features among operational Arctic wave systems: explicit wave propagation under sea ice using a dimensional-analysis-based source function (Yu et al. 2022), and coupling to the ARC MFC physics and tidal systems for sea ice concentration, sea ice thickness, and surface currents. The system also includes multi-satellite altimetric data assimilation of significant wave height and wind, added in the November 2024 upgrade.

The official Copernicus Marine product identifier is `ARCTIC_ANALYSIS_FORECAST_WAV_002_014`. "ARCWAM" is not an official short name but reflects the product's role as the Arctic counterpart to BALWAM, MEDWAM, and IBIWAM in the Copernicus Marine wave portfolio. MET Norway also operates a separate Nordic Seas WAVEWATCH III wave system distributed directly via their THREDDS server — see the [MET Norway WW3 entry](./met-norway-ww3.md) for that distinct system.

---

## Who runs it
- **Production Unit:** Arctic Monitoring and Forecasting Centre (ARC MFC), operated by MET Norway
- **Country:** Norway
- **Role in Copernicus Marine:** Production Unit for the ARC MFC wave product

---

## What area it covers
- **Coverage:** Arctic Ocean, Nordic Seas, and adjacent regions north of approximately 53°N
- **Domain bounds:** 41.12°N – 89.99°N, 179.98°W – 179.98°E (global in longitude; north of 41.12°N in latitude)
- **Native grid:** Rotated spherical grid at 3 km horizontal resolution
- **Delivered grid:** Polar stereographic projection at 3 km resolution (straight vertical longitude from pole: -45°)
- **Grid dimensions:** 2367 × 2467

---

## Basic details
- **Model type:** Regional deterministic wave model (coupled to ocean/ice, with data assimilation)
- **Core wave model:** WAM Cycle 4.7 (code provided by Arno Behrens, Helmholtz-Zentrum Hereon; modified by MET Norway for wave propagation under sea ice)
- **Horizontal resolution:** 3 km
- **Spectral resolution:** 36 directions × 29 frequencies
- **Forecast length:** Alternating cycles — 5 days (120 hours) from 00 UTC, 10 days (240 hours) from 12 UTC
- **Update frequency:** 2× daily
- **Production cycles:** 00 UTC and 12 UTC (bulletin times 06 UTC and 18 UTC respectively)
- **Target delivery time:** 05:15 UTC (from 12 UTC cycle) and 16:15 UTC (from 00 UTC cycle)
- **Temporal output resolution:** 1-hourly instantaneous
- **Analysis archive:** Rolling archive from 2021-10-01 onward (Copernicus Marine product temporal extent)
- **Bathymetry:** EMODnet Bathymetry and ETOPO1

---

## Forcing and coupling
ARCWAM is forced by ECMWF atmospheric fields and coupled to the ARC MFC operational ocean/ice systems:

- **Atmospheric forcing:** Six-hourly ECMWF 10 m winds (analysis and forecast)
- **Lateral boundary conditions:** Six-hourly wave spectra from ECMWF
- **Sea ice concentration:** Hourly from `ARCTIC_ANALYSISFORECAST_PHY_002_001` (TOPAZ5)
- **Sea ice thickness:** Hourly from `ARCTIC_ANALYSISFORECAST_PHY_002_001` (TOPAZ5)
- **Surface currents:** Hourly from `ARCTIC_ANALYSISFORECAST_PHY_TIDE_002_015` (tidal-resolving Arctic ocean circulation system)

The coupling is off-line (one-way): ocean and ice fields influence wave propagation, but waves do not feed back into the physics run during the same cycle.

---

## Wave propagation under sea ice
A distinctive feature of ARCWAM among operational wave systems is explicit representation of wave propagation in ice-infested waters. The system uses a new formulation of wave attenuation by sea ice based on **Yu et al. (2022)**, introduced in the June 2023 upgrade. The sea ice source function is derived from a dimensional analysis scaled by sea ice thickness. Waves are allowed to propagate under the ice subject to frictional dissipation by the ice cover, rather than being suppressed entirely as in many global wave systems.

This is the state-of-the-art formulation described in the product's documentation as one of the refinements compared to the global wave product.

---

## Data assimilation
ARCWAM uses **Optimal Interpolation** for significant wave height and 10 m wind observations from satellite altimetry. DA was added in the November 2024 upgrade (version 1.8), making ARCWAM a relatively recent addition to the set of Copernicus regional wave products with operational DA.

### Assimilated observations (SWH and U10 wind)
- Sentinel-3A
- Sentinel-3B
- Sentinel-6A
- CryoSat-2
- Jason-3
- CFOSAT

Each 12-hourly forecast is preceded by a 12-hour analysis window in which assimilation is performed by OI against multi-satellite altimeter data.

---

## What it provides
Hourly instantaneous fields of integrated wave parameters, partitioned swell fields, maximum crest/trough variables, and supporting ocean/ice context:

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

### Ocean and ice context fields (re-distributed from the physics forcing)
- Sea ice concentration (SIC)
- Sea ice thickness (SIT)
- Surface current speed
- Surface current direction

---

## Data availability
- **Is the data free?** Yes (registration required)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.6 and ACDD-1.3 conventions)
- **Product identifier:** `ARCTIC_ANALYSIS_FORECAST_WAV_002_014`
- **Dataset identifier:** `dataset-wam-arctic-1hr3km-be`
- **File size:** ~1.8 GB per file
- **Official access:** https://data.marine.copernicus.eu/product/ARCTIC_ANALYSIS_FORECAST_WAV_002_014/description
- **DOI:** https://doi.org/10.48670/moi-00002
- **Licence:** Creative Commons Attribution 4.0 International (CC-BY-4.0)

---

## Version history

### November 2024 — Issue 1.8 (current)
- **Data assimilation added:** Optimal Interpolation of SWH and U10 wind from Sentinel-3A/B, Sentinel-6A, CryoSat-2, Jason-3, and CFOSAT
- Ocean currents product changed (now sourced from `ARCTIC_ANALYSISFORECAST_PHY_TIDE_002_015`)
- Spectral frequency resolution updated

### June 2023 — Issue 1.7
- Two new LATERMAR output variables added (VMXL, VCMX — maximum crest height and maximum crest-to-trough height)
- New formulation of wave attenuation by sea ice (Yu et al. 2022)

### February 2022 — Issue 1.6
- Global attribute changes

### April 2021 — Issue 1.5
- Correction of wave direction convention (from/to)

### January 2021 — Issue 1.5
- Inclusion of forecast currents and sea ice as forcing

### September 2019 — Issue 1.4
- Forecast frequency increased to twice daily
- Horizontal resolution increased from 6.25 km to 3 km

### November 2018 — Issue 1.3
- Wave propagation under sea ice introduced
- Current refraction added

### February 2018 — Issue 1.2
- Upgraded to wave V4 product

### December 2016 — Issue 1.0
- Initial version

---

## Relationship to other wave products

### MET Norway's two operational wave systems
MET Norway runs two distinct operational wave systems, both covering Arctic and Nordic waters:

- **ARCWAM (this entry)** — WAM Cycle 4.7 at 3 km, distributed via Copernicus Marine, with multi-satellite DA, ocean/ice coupling, and 5/10-day forecasts. Optimized for the full Arctic including ice-covered waters.
- **[MET Norway WW3 (Nordic Seas)](./met-norway-ww3.md)** — WAVEWATCH III at ~4 km, distributed via MET Norway's THREDDS server, no documented DA, 66-hour forecast updated 4× daily. Optimized for short-range Nordic marine forecasting with AROME-MEPS coastal forcing.

These are complementary systems from the same operator, serving different user communities (Copernicus Marine pan-European users vs Norwegian/Nordic operational marine forecast users).

### Copernicus Marine regional wave portfolio
ARCWAM sits alongside the other Copernicus Marine basin-scale regional wave products, each run by a different national institute:
- **[BALWAM](../finland/balwam.md)** — Baltic Sea (FMI, Finland; WAM Cycle 4.7)
- **[MEDWAM](../greece/medwam.md)** — Mediterranean Sea (HCMR, Greece; WAM Cycle 6)
- **[IBIWAM](../spain/ibiwam.md)** — Iberian-Biscay-Irish waters (Nologin/CESGA, Spain; MFWAM)

Together these four systems form the core set of Copernicus Marine regional wave forecasts with multi-satellite data assimilation and coupling to regional physics/ice systems.

### Relationship to the global MFWAM Copernicus product
ARCWAM receives its lateral boundary wave spectra from ECMWF (not from the Copernicus global MFWAM), which is a minor structural difference compared to IBIWAM and MEDWAM — both of which nest inside the global MFWAM product. This reflects ARC MFC's closer integration with ECMWF's operational atmospheric chain.

---

## Notes
- ARCWAM is one of the few operational wave systems with explicit representation of waves propagating under sea ice. This matters for Arctic applications where traditional suppression-based wave-ice treatments either overestimate wave damping in loose ice or fail to capture wave-ice feedback.
- The product's temporal coverage ("Analysis from 2021-10-01") predates the November 2024 DA upgrade by about three years. Users running long retrospective studies should be aware that pre-November-2024 data in the rolling archive was produced without altimetric DA, while post-November-2024 data includes it.
- The Copernicus Marine product documentation lists the product extent as "41.12°N to 89.99°N," while the PUM text refers to "north of 53°N" as the coverage area. The former is the actual grid extent; the latter reflects the practical forecast domain of interest. Users accessing data near the 41–53°N band should be aware that the southern edge of the grid includes some fill/boundary zone and should not be treated as equivalent-quality to core Arctic coverage.
- The forecast-reference documentation notes a historical directional-convention issue (ARC-143) affecting pre-April-2021 data, where attribute definitions and actual directional values were inconsistent. The April 2021 v1.5 update corrected this. Users working with historical data from before that date should consult the Copernicus Marine User Notification Service.
- For sea ice concentration and thickness, the values in ARCWAM files are re-distributed from the ARC MFC physics product and should match that product — users needing authoritative ice fields should cite the physics product rather than the wave product.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/ARCTIC_ANALYSIS_FORECAST_WAV_002_014/description
- Product User Manual (PUM): https://catalogue.marine.copernicus.eu/documents/PUM/CMEMS-ARC-PUM-002-014.pdf
- Quality Information Document (QUID): https://catalogue.marine.copernicus.eu/documents/QUID/CMEMS-ARC-QUID-002-014.pdf
- Synthesis Quality Overview (SQO): https://catalogue.marine.copernicus.eu/documents/SQO/CMEMS-ARC-SQO-002-014.pdf
- Validation dashboard (MET Norway): https://cmems.met.no/ARC-MFC/Wave3kmValidation/index.html
- DOI: https://doi.org/10.48670/moi-00002
- MET Norway: https://www.met.no/
- Copernicus Marine Service: https://marine.copernicus.eu/
- Key reference: Yu et al. (2022), sea ice wave attenuation formulation
