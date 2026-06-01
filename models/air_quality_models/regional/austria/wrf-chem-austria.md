# WRF-Chem Air Quality Forecast (GeoSphere Austria)

## What this model is
The GeoSphere Austria air quality forecast is an operational atmospheric-composition system based on the online-coupled chemical transport model **WRF-Chem**. It produces daily 3-day forecasts of key pollutants — ground-level ozone, particulate matter, and nitrogen dioxide — for public-health air quality guidance over Europe and, at higher resolution, Central Europe.

It is distributed as two nested products: an outer **9 km** domain covering Europe (and parts of North Africa and Russia) and an inner **3 km** domain covering Central Europe.

---

## Who runs it
- **Organization:** GeoSphere Austria (formerly ZAMG — Zentralanstalt für Meteorologie und Geodynamik; merged into GeoSphere Austria in 2023)
- **Country / region:** Austria

---

## What area it covers
- **Coverage:** Europe (9 km outer domain) and Central Europe (3 km inner domain)
- **Domain details:**
  - **9 km "Pollutant Forecast for Europe":** covers Europe plus parts of North Africa and Russia. Lambert Conic Conformal (2SP) projection (EPSG:9802); the published bounding box spans roughly 15.59–74.39 °N, −53.73–80.33 °E (the wide longitude range reflects the projected-grid corners rather than the useful coverage area).
  - **3 km "Pollutant Forecast for Central Europe":** covers Central Europe. Lambert Conic Conformal (2SP) projection (EPSG:9802); bounding box roughly 40.91–53.75 °N, 2.86–23.74 °E.

---

## Basic details
- **Model type:** Air quality / atmospheric composition
- **Model system / core:** WRF-Chem (online-coupled meteorology–chemistry chemical transport model)
- **Horizontal resolution:** 9 km (outer Europe domain) and 3 km (inner Central Europe domain)
- **Vertical levels:** ~48 (per GeoSphere HPC documentation for the WRF-Chem domains; not restated for the v2 grids, so treat as approximate)
- **Forecast length:** 72 hours (3 days)
- **Update frequency / cycles:** Published daily. (The operational WRF-Chem system is documented as running twice daily out to +72 h internally; the public datasets are refreshed once per day.)
- **Temporal output resolution:** 1 hour

---

## Meteorological driver
- **Driving NWP model:** WRF (the meteorological component of WRF-Chem). Meteorological and chemical initial and boundary conditions are taken from the ECMWF **Integrated Forecasting System (IFS)**.
- **Coupling:** Online (two-way) — chemistry is computed inline within WRF, the defining feature of WRF-Chem.
- **Update source frequency:** Initial and boundary conditions sourced from ECMWF IFS (exact refresh cadence not documented on the dataset pages).

---

## Chemistry and aerosols
- **Gas-phase chemical mechanism:** Not documented in the public dataset metadata (TBD).
- **Number of chemical species:** Not documented (TBD).
- **Aerosol treatment:** Not documented (TBD).
- **Aerosol components represented:** Not documented (TBD); particulate matter is among the forecast outputs.
- **Heterogeneous/aqueous chemistry:** Not documented (TBD).

*(WRF-Chem supports several interchangeable gas-phase mechanisms and aerosol modules; GeoSphere's specific configuration is not stated in the open-data metadata and would need to be confirmed from a GeoSphere technical source.)*

---

## Emissions
- **Anthropogenic inventory:** CAMS emissions inventory ("CAMS emissions cadastre")
- **Biogenic emissions:** Not documented (TBD)
- **Wildfire emissions:** Not documented (TBD)
- **Dust scheme:** Not documented (TBD)
- **Sea salt scheme:** Not documented (TBD)

---

## Data assimilation
- **Assimilates AQ observations:** Not documented; the forecasts are initialized and bounded by ECMWF IFS rather than by an explicit air-quality observation assimilation step described in the public metadata.

---

## What it provides
Daily hourly forecasts (to +72 h) of:
- Ozone (O3)
- Particulate matter (PM)
- Nitrogen dioxide (NO2)

Both the 9 km and 3 km products distribute the same three pollutant parameters.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF
- **Official download locations (bulk file listing):**
  - 9 km (Europe): https://public.hub.geosphere.at/public/datahub.html?id=chem-v2-1h-9km/filelisting
  - 3 km (Central Europe): https://public.hub.geosphere.at/public/datahub.html?id=chem-v2-1h-3km/filelisting
- **API access:** GeoSphere Austria's Dataset API provides programmatic access with subsetting (by area, time, parameter):
  https://dataset.api.hub.geosphere.at/v1/docs/
- **Dataset landing pages:**
  - 9 km: https://data.hub.geosphere.at/en/dataset/chem-v2-1h-9km
  - 3 km: https://data.hub.geosphere.at/en/dataset/chem-v2-1h-3km
- **DOIs:**
  - 9 km (Europe): https://doi.org/10.60669/f4q8-5j13
  - 3 km (Central Europe): https://doi.org/10.60669/1860-g785

Both products are version 2, published 22 January 2025. The 9 km and 3 km datasets share the same access portal, API, and license; users can choose whichever domain/resolution suits their workflow.

---

## Notes
- **Why a separate system from AROME:** GeoSphere Austria runs WRF-Chem for air pollution because the chemistry/air-chemistry functionality it requires is not available in the operational [AROME Austria](../../../nwp_models/regional/austria/arome-austria.md) NWP model. WRF-Chem runs on GeoSphere's operational HPC alongside the AROME suite. It replaced the earlier CAMx photochemical model (which had been coupled to ALADIN/MM5 output).
- **Two nested domains, one system:** The 9 km Europe and 3 km Central Europe products are the outer and inner nests of the same WRF-Chem configuration, documented internally as a two-domain run out to +72 h.
- **Version change (v1 → v2, January 2025):** The current v2 products at 9 km / 3 km replaced the v1 products at 12 km / 4 km (`chem-v1-1h-12km` and `chem-v1-1h-4km`). The published version notes cite changes to the projection (a small shift in the coordinate centre), the spatial coverage (domain), and the horizontal resolution. GeoSphere HPC presentations from 2023 and 2025 still describe the older 12 km (512×432×48) / 4 km (256×223×48) nesting, which corresponds to the v1 configuration.
- **Relationship to siblings:** GeoSphere uses the CAMS emissions inventory, and this system is conceptually related to the European [CAMS regional air quality ensemble](../eu/cams-regional.md) (a multi-model regional AQ ensemble for Europe). The CAMS European products are coarser-resolution, multi-model, and assimilate surface observations; the GeoSphere WRF-Chem products are single-model, higher-resolution, and IFS-driven.
- **Organizational note:** GeoSphere Austria was formed by the 2023 merger of ZAMG with the Geological Survey of Austria; older publications and documentation refer to ZAMG.

---

## Recent version history

### January 2025 — Version 2 (9 km / 3 km)
The public products were re-released as version 2 on a reconfigured nesting: a 9 km Europe outer domain and a 3 km Central Europe inner domain, replacing the v1 12 km / 4 km domains. Changes included the projection (coordinate-centre shift), domain coverage, and horizontal resolution.

### Earlier — Version 1 (12 km / 4 km) and CAMx predecessor
The v1 public products ran on a 12 km outer / 4 km inner WRF-Chem nesting. Before WRF-Chem, GeoSphere/ZAMG used the CAMx photochemical model (coupled to ALADIN or MM5 meteorology) for operational air pollution forecasting.

---

## Official documentation
- Dataset landing page (9 km, Europe): https://data.hub.geosphere.at/en/dataset/chem-v2-1h-9km
- Dataset landing page (3 km, Central Europe): https://data.hub.geosphere.at/en/dataset/chem-v2-1h-3km
- Bulk download (9 km): https://public.hub.geosphere.at/public/datahub.html?id=chem-v2-1h-9km/filelisting
- Bulk download (3 km): https://public.hub.geosphere.at/public/datahub.html?id=chem-v2-1h-3km/filelisting
- Dataset API documentation: https://dataset.api.hub.geosphere.at/v1/docs/
- GeoSphere Austria homepage: https://www.geosphere.at

### Key references
- Grell, G. A., Peckham, S. E., Schmitz, R., McKeen, S. A., Frost, G., Skamarock, W. C., Eder, B. (2005). *Fully coupled "online" chemistry within the WRF model.* Atmospheric Environment, 39(37), 6957–6975. https://doi.org/10.1016/j.atmosenv.2005.04.027 *(the canonical WRF-Chem reference, cited on both GeoSphere dataset pages)*
