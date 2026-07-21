# uEMEP (Norwegian Operational Local Air Quality Forecast)

## What this model is
uEMEP (urban EMEP) is MET Norway's operational high-resolution local air quality forecasting model for Norway. It is a Gaussian-dispersion downscaling of the EMEP MSC-W regional chemical transport model: EMEP MSC-W supplies the regional background, and uEMEP redistributes local emissions (traffic, shipping, heating, industry) to street/neighbourhood scale using its "local fraction" method, without double-counting emissions. It forecasts NO2, PM10, PM2.5 and O3 plus an air quality index (AQI), and powers Norway's public air quality services (luftkvalitet.miljodirektoratet.no and yr.no).

This is a **distinct product** from the EMEP MSC-W member of the [CAMS Regional ensemble](../eu/cams-regional.md): that member is the ~10–20 km European regional CTM distributed via the Copernicus ADS. This entry is the Norway-only, ~1 km operational downscaling distributed through MET Norway's THREDDS server. What CAMS Europe labels as Norway's "EMEP" model is the regional CTM; the national high-resolution forecast is uEMEP.

---

## Who runs it
- **Organization:** MET Norway (Norwegian Meteorological Institute)
- **Country / region:** Norway
- **Public service partners:** Norwegian Environment Agency (Miljødirektoratet), NILU and others operate the downstream "Luftkvalitet i Norge" service
- **Operational since:** 2018

---

## What area it covers
- **Coverage:** Mainland Norway
- **Projection:** UTM zone 33N (transverse Mercator, WGS84; central meridian 15° E, false easting 500 km)
- **Domain (distributed grid):** X −109.5 km to 1129.5 km, Y 6400.5 km to 7979.5 km in UTM33; 1240 × 1580 cells. *Verified from a live file (July 2026).*

---

## Basic details
- **Model type:** Local/urban air quality dispersion downscaling (surface concentrations)
- **Model system / core:** uEMEP (urban EMEP), a Gaussian dispersion model coupled to the EMEP MSC-W chemical transport model via the "local fraction" method (Denby et al., GMD uEMEP_v5, 2020; Mu et al., GMD uEMEP_v6, 2022)
- **Horizontal resolution:** ~1 km distributed grid (1000 m UTM spacing); native uEMEP resolution is much finer (down to ~50 m) — the public grid is aggregated to 1 km
- **Vertical levels:** Surface only (distributed product is surface concentrations)
- **Forecast length:** ~2.5 days — 55 hourly steps; the 00 UTC cycle spans roughly +6 h to +60 h (verified)
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** Hourly

---

## Meteorological driver
- **Driving NWP model:** MEPS (MetCoOp Ensemble Prediction System; AROME-based, run jointly by MET Norway and partners)
- **Coupling:** Offline (one-way)
- **Regional chemical background:** EMEP MSC-W (MET Norway's regional CTM). Some MET Norway descriptions also phrase the chain as downscaling CAMS regional output; the operational system is the EMEP MSC-W + uEMEP coupling with MEPS meteorology.

---

## Chemistry and aerosols
- **Regional gas-phase chemistry:** Handled by EMEP MSC-W (EmChem gas-phase mechanism). uEMEP itself is a dispersion downscaling and does not run full gas-phase chemistry; local NO2 is derived from NOx via a simplified/empirical NO2–NOx–O3 relationship applied to the local fraction. *(Regional chemistry details are EMEP MSC-W's; not separately re-verified for this stream.)*
- **Aerosol treatment:** PM handled as PM2.5 / PM10 mass; component-level aerosol chemistry resides in the EMEP MSC-W background (TBD for specifics)
- **Species distributed (verified from file):** NO2, PM10, PM2.5, O3, AQI

---

## Emissions
- **Local (Norwegian) emissions:** Sector-resolved local sources — road traffic (exhaust and non-exhaust), shipping, domestic heating, and industry — distributed with proxy data. These sectors are confirmed as the source-apportionment categories in the `regions` output.
- **Regional/background emissions:** Via EMEP MSC-W (EMEP/CAMS emission inventories)
- **Wildfire / biogenic / other:** TBD (not confirmed for this stream)

---

## Data assimilation
- **Assimilates AQ observations:** Not confirmed for the operational uEMEP forecast. (EMEP MSC-W performs data assimilation in its CAMS configuration, but whether the Norwegian operational uEMEP chain applies observational correction is not documented in the THREDDS metadata — flagged rather than assumed.)

---

## What it provides
**Gridded product** (`uEMEP_delomrade2024_map_*.nc`), 1 km UTM33, hourly:
- NO2, PM10, PM2.5, O3 surface concentrations (µg/m³)
- AQI (air quality index)

**Administrative-unit product with source apportionment** (`uEMEP_regions2024_*.nc`, companion — zonal, not gridded): the same pollutants plus AQI aggregated to 14,101 `grunnkrets` (census tracts) and 1,547 `delområde` (districts), each with local-fraction contributions by sector (traffic exhaust, traffic non-exhaust, shipping, heating, industry), nonlocal fraction, and sea salt fraction. This source attribution is uEMEP's distinguishing capability.

**Station files** (`uEMEP_Norway_station_*.nc`, `uEMEP_meteo_station_*.nc`): point extractions at monitoring/meteorological sites.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 (CC BY 4.0) or Norwegian Licence for Open Government Data (NLOD) 2.0, at the user's choice; credit "MET Norway" as the data source
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF4 (CF-1.6)
- **Access:** MET Norway THREDDS server (HTTP file download and OPeNDAP; WMS also enabled but not intended for operational use)
  - **Catalog:** `https://thredds.met.no/thredds/catalog/data/fou-kl/uEMEP/{YYYY}/{MM}/{DD}/{HH}/catalog.html`
  - **Direct download (fileServer):** `https://thredds.met.no/thredds/fileServer/data/fou-kl/uEMEP/{YYYY}/{MM}/{DD}/{HH}/uEMEP_delomrade2024_map_{YYYYMMDD}_{HH}.nc`
  - **OPeNDAP:** `https://thredds.met.no/thredds/dodsC/data/fou-kl/uEMEP/{YYYY}/{MM}/{DD}/{HH}/{filename}`
  - **Filename convention:** `uEMEP_delomrade2024_map_{YYYYMMDD}_{HH}.nc` (gridded map), `uEMEP_regions2024_{YYYYMMDD}_{HH}.nc` (admin-unit source apportionment). `{HH}` ∈ {00, 06, 12, 18}. The "2024" token is a configuration/emission-year label, not the forecast date.
  - **Archive depth:** Daily archive back to 2019.

---

## Notes
- **Hosting nuance:** The data lives under MET Norway's research (`fou-kl`) THREDDS tree rather than a formal product catalog with a per-dataset landing page. The daily dated archive with four run cycles is unambiguously the operational feed, but there is no KNMI-style metadata landing page — the THREDDS catalog is the access point.
- **Relationship to EMEP / CAMS:** uEMEP is the local downscaling layer; EMEP MSC-W is the regional CTM and the Norwegian member of the [CAMS Regional ensemble](../eu/cams-regional.md). They should not be conflated — CAMS "EMEP" is the ~10–20 km European product; this is the ~1 km national downscaling.
- **Native vs. distributed resolution:** uEMEP runs natively down to ~50 m; the distributed gridded map is aggregated to 1 km. The finest-scale (street-level) detail is reflected in the administrative-unit `regions` product rather than the 1 km raster.
- **Terms of service:** MET Norway asks users not to spawn parallel OPeNDAP sessions or file downloads; for operational reuse they ask to be contacted for priority access.
- **Companion `regions` product:** The source-apportionment file is arguably the more scientifically distinctive output but is administrative-unit (zonal), not gridded — noted here rather than catalogued as a gridded entry.

---

## Official documentation
- THREDDS catalog: https://thredds.met.no/thredds/catalog/data/fou-kl/uEMEP/catalog.html
- MET Norway licensing: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- uEMEP_v5 model description (Denby et al., 2020): https://gmd.copernicus.org/articles/13/6303/2020/
- uEMEP_v6 European downscaling (Mu et al., 2022): https://gmd.copernicus.org/articles/15/449/2022/
- Norwegian air quality service: https://luftkvalitet.miljodirektoratet.no/
