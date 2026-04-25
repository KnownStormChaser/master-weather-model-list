# RTOFS Global (Real-Time Ocean Forecast System — Global)

## What this model is
The **Real-Time Ocean Forecast System (Global RTOFS)** is NOAA's operational global ocean physics forecast system. It provides nowcasts and forecast guidance for ocean temperature, salinity, currents, sea surface elevation, sea ice concentration, and sea ice thickness out to approximately eight days.

RTOFS is the American counterpart to Copernicus Marine's GLO12 — both are eddy-resolving global ocean physics systems at 1/12° horizontal resolution providing similar variables with comparable forecast ranges. They differ in model core (HYCOM for RTOFS, NEMO for GLO12), sea ice component, data assimilation approach, and operating institution. Together, RTOFS and GLO12 form the two dominant operational global ocean physics forecasts worldwide.

Beyond its standalone role, Global RTOFS serves as the operational ocean initialization for several NOAA coupled prediction systems — notably NOAA's hurricane forecast system ([HAFS](../../../tropical_cyclone_models/hafs.md)), which was upgraded to use RTOFS v2.5 simultaneously with its own v2.1 upgrade in July 2025.

The current version is **RTOFS v2.5**, implemented operationally on July 29, 2025. A proposed v3.0 upgrade would replace HYCOM with MOM6 and CICE4 with CICE6 as part of broader NOAA migration to the Unified Forecast System framework — see the Version history and Upcoming changes sections.

---

## Who runs it
- **Organization:** NOAA / National Centers for Environmental Prediction (NCEP)
- **Country:** United States
- **Developed by:** NOAA Environmental Modeling Center (EMC) and NCEP's Marine Modeling and Analysis Branch (MMAB)
- **Operational implementation:** NCEP Central Operations (NCO)
- **Partnership history:** Earlier RTOFS versions used ocean analysis fields from the U.S. Naval Oceanographic Office (NAVOCEANO); since 2020 RTOFS has run its own in-house 3DVar data assimilation

---

## What area it covers
- **Coverage:** Global ocean
- **Grid:** Global tripolar grid (avoiding the North Pole singularity by using three poles placed over land)
- **Horizontal resolution:** 1/12° (~9 km at mid-latitudes)
- **Vertical levels:** 41 hybrid vertical levels
- **Vertical coordinate:** HYCOM's hybrid coordinate — isopycnal (density-following) in the open stratified ocean, terrain-following (sigma) in shallow regions, and z-level (fixed depth) in the mixed layer. The vertical coordinate adapts dynamically to the water column structure.

---

## Basic details
- **Model type:** Global deterministic ocean physics forecast, coupled to sea ice, with data assimilation
- **Core ocean model:** HYCOM (HYbrid Coordinate Ocean Model)
- **Sea ice model:** CICE v4 (Community Ice CodE)
- **System name:** Global RTOFS
- **Eddy-resolving:** Yes — the 1/12° resolution resolves mesoscale ocean eddies (typically ~50–100 km diameter at mid-latitudes)
- **Forecast length:** 8 days (192 hours), with hindcast going back 1 day from cycle start
- **Surface output frequency:** Hourly (from nowcast through forecast)
- **3D volume output frequency:** Every 6 hours
- **Forecast extent:** 0000Z nowcast out to 196 hours
- **Update frequency:** Once daily, with staggered product delivery throughout the day
- **Cycle time:** 00Z base time, with outputs delivered in batches
- **Completion time:** All products delivered by approximately 17Z
- **Bathymetry source:** Derived from GEBCO and historical hydrographic data; details in HYCOM documentation

---

## Product delivery schedule

Global RTOFS has an unusual delivery schedule compared to atmospheric NWP — the 24-hour cycle produces products in waves throughout the day as different parts of the forecast complete:

- **00Z:** Analysis and analysis post-processing available
- **06Z:** Forecast days 1–4 and post-processing for most of these days
- **12Z:** Forecast days 5–8 and post-processing for these days, plus some remaining post-processing for forecast days 1–4
- **17Z:** Full cycle complete

Users needing specific forecast lead times should be aware that the latter half of the forecast (days 5–8) becomes available later in the day than the early forecast (days 1–4).

---

## Forcing

- **Atmospheric forcing:** GFS (Global Forecast System) from NOAA/NCEP — winds, heat fluxes, precipitation, surface pressure
  - In the proposed v3.0, GDAS/GFS fields will be read by CICE6 directly in NetCDF format (replacing the current binary-format ingestion path used by CICE4)
- **Initial conditions:** Incremental Analysis Update (IAU) applied over 6-hour windows from the 3DVar analysis; each new cycle incorporates the previous 24-hour forecast as the background field
- **River runoff:** Climatological river discharge applied as boundary fluxes at major river mouths
- **Tidal forcing:** Not explicitly represented in the primary operational run (users needing tidal currents should look to regional products)
- **Ice forcing:** Through the coupled CICE v4 component (see Coupling section)

---

## Coupling

Global RTOFS is **coupled to sea ice** via the CICE v4 (Community Ice CodE) sea ice model:
- **Ocean → ice:** Surface currents, surface temperature, surface salinity passed to the ice model
- **Ice → ocean:** Sea ice fraction modifies surface momentum flux, heat flux, and freshwater flux to the ocean
- **Coupling mode:** Online, at every model timestep

No wave coupling is included in the standalone RTOFS operational run. For applications requiring wave–ocean coupled fields, users typically combine RTOFS outputs with a separate wave model (e.g., GFS-Wave, or a regional WAVEWATCH III configuration).

RTOFS is not atmosphere-coupled — atmospheric forcing is applied as one-way boundary fluxes from GFS rather than as two-way feedback.

---

## Data assimilation

Since 2020, Global RTOFS implements a **multivariate, multi-scale 3DVar data assimilation algorithm** (Cummings and Smedstad, 2014) with a 24-hour update cycle. Increments from the 3DVar analysis are applied to the 24-hour forecast fields using a 6-hour Incremental Analysis Update (IAU).

Prior to 2020, RTOFS did not run its own DA — it used ocean analysis fields provided by the U.S. Naval Oceanographic Office (NAVOCEANO). The 2020 switch to in-house 3DVar brought RTOFS to operational DA parity with GLO12.

### Assimilated observations

**Sea Surface Temperature (SST):**
- METOP-B AVHRR
- JPSS-VIIRS
- In-situ SST from ships, fixed buoys, and drifting buoys

**Sea Surface Salinity (SSS):**
- SMAP (NASA Soil Moisture Active Passive)
- SMOS (ESA Soil Moisture and Ocean Salinity)
- In-situ from buoys

**Temperature and salinity profiles:**
- Argo floats
- Alamo floats
- CTD casts
- Fixed buoys
- Gliders
- XBT (Expendable Bathythermograph)
- TESAC (WMO code for temperature/salinity/current profiles)
- Animal-borne sensors (marine mammal CTD tags)

**Absolute Dynamic Topography (ADT) from altimetry:**
- SARAL/AltiKa
- CryoSat-2
- Jason-3
- Sentinel-3A
- Sentinel-3B
- Sentinel-6A

**Sea ice concentration:**
- SSMI/S (Special Sensor Microwave Imager/Sounder)
- AMSR2 (Advanced Microwave Scanning Radiometer 2)

**Added in v2.5 (July 2025):**
- Velocity observations from drifting buoy trajectories
- Velocity observations from high-frequency (HF) radar coastal installations

### v2.5 DA enhancements
The July 2025 upgrade brought three specific DA improvements:
1. Climatological constraints added to the assimilation of sea surface height (SSH)
2. Updated conversion of DA increments applied to HYCOM (preserving HYCOM's native layer structure better than previous versions)
3. Added assimilation of drifting buoy and HF radar velocity observations

These changes were explicitly targeted at reducing known biases — in particular, the subsurface negative salinity bias in the Caribbean Sea that was a documented weakness in earlier RTOFS versions.

---

## What it provides

### 3D ocean fields (every 6 hours)
- Potential temperature
- Salinity
- Zonal velocity (eastward current component)
- Meridional velocity (northward current component)
- Interface depths (layer interfaces in HYCOM's hybrid coordinate)

### Surface fields (hourly)
- Sea surface temperature
- Sea surface salinity
- Sea surface height
- Surface currents (u and v components)
- Mixed layer depth

### Sea ice fields
- Sea ice concentration (0–1 coverage fraction)
- Sea ice thickness
- Sea ice velocity (u and v components)
- Snow thickness on sea ice

### Analysis vs forecast distinction
- **Nowcast (analysis):** 00Z fields reflecting the assimilated state — the authoritative "best estimate" of conditions at cycle start
- **Forecast fields:** Hours 001–192 representing the model's projection forward
- **Hindcast:** 1 day prior to cycle start, useful for users needing continuity with the previous day's forecast

---

## Data availability

### Distribution channels

Global RTOFS data is publicly available through two primary channels:

**1. NOMADS (NCEP operational distribution):**
- Production: https://nomads.ncep.noaa.gov/pub/data/nccf/com/rtofs/prod/
- Parallel feed: https://nomads.ncep.noaa.gov/pub/data/nccf/com/rtofs/para/
- FTP equivalent: ftp://ftp.ncep.noaa.gov/data/nccf/com/rtofs/prod/

**2. AWS Open Data (NOAA Open Data Dissemination):**
- S3 bucket: `s3://noaa-nws-rtofs-pds/`
- Browser access: https://noaa-nws-rtofs-pds.s3.amazonaws.com/index.html
- AWS CLI (no account required): `aws s3 ls --no-sign-request s3://noaa-nws-rtofs-pds/`
- AWS region: `us-east-1`
- SNS notifications for new data: `arn:aws:sns:us-east-1:709902155096:NewRTOFSObject`

### Data details
- **Is the data free?** Yes
- **Is the data downloadable?** Yes
- **Data formats:**
  - **GRIB2** (standard gridded format, most user-friendly)
  - **NetCDF** (standard gridded format, CF conventions)
  - **HYCOM native binary (`*.a`, `*.b`)** — used by HYCOM community tools; scheduled for removal in v3.0
  - **Compressed bundles (`*.tgz`, `*.tar.gz`, `*.gz`)** — also scheduled for removal in v3.0
- **Current file naming (forecast):** `rtofs_glo.t00z.f{HHH}_{region}_std.grb2`
- **Current file naming (nowcast):** `rtofs_glo.t00z.n024_{region}_std.grb2`
- **Licence:** NOAA public domain — open use, attribution requested
- **Documentation:** https://polar.ncep.noaa.gov/global/

---

## Version history

### July 29, 2025 — RTOFS v2.5 (current)
- Added climatological constraints to SSH assimilation
- Updated conversion of DA increments to better preserve HYCOM layer structure
- Added assimilation of velocity observations from drifting buoys and HF radar
- Targeted outcome: reduce subsurface negative salinity bias in the Caribbean Sea
- **No product changes** — output files and formats remained the same as v2.4
- Deployed simultaneously with [HAFSv2.1](../../../tropical_cyclone_models/hafs.md), which is initialized from RTOFS v2.5

### 2024 — RTOFS v2.4
- Continued refinements to DA and forecast quality
- Public comment period held for the v2.4.0 proposal

### 2020 — Transition to in-house 3DVar DA
- Switched from NAVOCEANO-provided ocean analysis to the in-house multivariate multi-scale 3DVar algorithm (Cummings and Smedstad, 2014)
- First operational version to assimilate observations directly rather than receiving analysis fields from a partner organization
- Described in Garraffo et al. (2020)

### Earlier versions
- Operational RTOFS has existed in various forms since the mid-2000s, with ongoing upgrades to HYCOM version, CICE version, and DA methodology
- For detailed pre-2020 history, consult NCEP's model documentation archives

---

## Upcoming changes: RTOFS v3.0 (proposed, March 2026)

NOAA/NWS issued Public Notice **PNS 26-17** on March 12, 2026, proposing a major architectural upgrade to RTOFS v3.0. The proposal, with a public comment period through April 15, 2026, would implement the following changes:

### Model core replacements
- **HYCOM → MOM6** (Modular Ocean Model 6)
- **CICE v4 → CICE v6**

Both replacements align RTOFS with the broader NOAA **Unified Forecast System (UFS)** component stack, matching the ocean and ice model choices used in other NOAA coupled systems.

### Product changes
- Continued GRIB2 and NetCDF distribution on NOMADS and FTPPRD
- **Discontinued formats:** legacy HYCOM binary archives (`*.a`, `*.b`, `*.B`), compressed bundles (`*.tgz`, `*.tar.gz`, `*.gz`)
- CICE6 will ingest GDAS/GFS forcing fields directly from NetCDF rather than from the binary format used by CICE4

### GRIB2 filename changes
The proposal renames GRIB2 files with cosmetic pattern changes and a new `.grib2` extension:

| Current | Proposed v3.0 |
|---------|---------------|
| `rtofs_glo.t00z.f{HHH}_{region}_std.grb2` | `rtofs_glo.t00z.f{HHH}.{region}.std.grib2` |
| `rtofs_glo.t00z.n024_{region}_std.grb2` | `rtofs_glo.t00z.tm000.{region}.std.grib2` |

Users scraping NOMADS paths or building filename-based indexing should prepare for this change. NOAA's standard service change notification policy provides at least 30 days notice before implementation.

### Status
As of this writing, v3.0 remains a **proposal in public comment**, not yet scheduled for operational implementation. The final Service Change Notice will confirm the implementation date and any modifications based on comments received.

---

## Relationship to other models

### Peer global ocean physics systems
- **[GLO12 (Copernicus Marine Global Ocean Physics)](../../../ocean_models/global/france/glo12.md)** — Mercator Ocean International's operational global ocean forecast. Same 1/12° resolution, similar forecast variables, different model core (NEMO vs HYCOM), different sea ice model, different DA scheme. The two systems are the principal global ocean physics forecasts available operationally.
- **Navy Global HYCOM** — U.S. Navy's operational HYCOM run at FNMOC. RTOFS historically shared analysis fields with the Navy system; both are in the HYCOM family but operated by separate institutions.
- **BLUElink OceanMAPS** — Australian Bureau of Meteorology operational global ocean forecast (1/10° global).

### Upstream dependencies
- **GFS (Global Forecast System):** Provides atmospheric forcing (winds, fluxes, precipitation, pressure)
- **GDAS (Global Data Assimilation System):** Upstream analysis products used for forcing fields

### Downstream consumers
- **[HAFS (Hurricane Analysis and Forecast System)](../../../tropical_cyclone_models/hafs.md):** Uses RTOFS as ocean initial conditions. HAFSv2.1 (July 2025) upgraded to use RTOFS v2.5.
- Various regional ocean downscaling applications use Global RTOFS as boundary conditions.

### AI-based counterparts
The ocean physics AI forecasting space is developing rapidly, with systems like GLONET (Mercator's neural ocean model trained on GLORYS12) emerging in 2024–2025. As of RTOFS v2.5, no fully operational AI counterpart to RTOFS specifically exists, though NOAA has research programs in AI-based ocean forecasting.

---

## Notes
- RTOFS is sometimes referred to simply as "RTOFS" without the "Global" qualifier in NOAA documentation. The full name "Global RTOFS" distinguishes it from legacy regional RTOFS products that have been retired — the operational RTOFS is the global system described here.
- The hybrid vertical coordinate is a distinctive HYCOM feature. It allows the model to adapt its vertical grid to the flow: using isopycnal coordinates (following density surfaces) in the stratified interior, sigma coordinates (following bathymetry) in shallow coastal regions, and z-coordinates (fixed depth) in the well-mixed surface layer. This hybrid approach is one of the key technical differences from NEMO-based systems like GLO12 that use primarily z-level vertical grids.
- The global tripolar grid avoids the singularity at the North Pole that affects standard latitude-longitude grids. The three poles are placed over Canadian land, Russian land, and the South Pole, keeping all grid distortions away from open ocean regions.
- The 24-hour DA cycle (applied with IAU over 6 hours) is longer than atmospheric NWP systems typically use (6-hour cycles for global atmospheric DA). This reflects both the slower evolution of ocean state compared to atmospheric state, and the irregular temporal distribution of ocean observations — Argo floats and altimeters provide sparse data that benefits from longer accumulation windows.
- The forecast range goes out to "196 hours" per the NOMADS description, though standard usage rounds this to 8 days (192 hours). The extra 4 hours extend the last forecast day slightly past 192 h.
- For users with long-term archival or reprocessing needs, RTOFS outputs are not a reanalysis — they are operational forecast archives with the version heterogeneity inherent to operational systems. Users needing a homogeneous long-term dataset should look to dedicated ocean reanalysis products (GLORYS12, HYCOM reanalyses, etc.).

---

## Official documentation
- RTOFS main page: https://polar.ncep.noaa.gov/global/
- HYCOM consortium (model developer): https://www.hycom.org/
- CICE developer (Los Alamos): https://github.com/CICE-Consortium/CICE
- NOMADS RTOFS data: https://nomads.ncep.noaa.gov/pub/data/nccf/com/rtofs/prod/
- AWS Open Data registry: https://registry.opendata.aws/noaa-rtofs/
- NCEP Service Change Notice 25-47 (v2.5 implementation, July 2025): https://www.weather.gov/media/notification/pdf_2025/scn25-47_updated_RTOFS_V.2.5.0_aaa.pdf
- PNS 26-17 (v3.0 proposal, March 2026): https://www.weather.gov/media/notification/pdf_2026/pns26-17_RTOFS_v3.pdf

### Key references
- Cummings, J.A. and Smedstad, O.M. (2013). Variational Data Assimilation for the Global Ocean. In *Data Assimilation for Atmospheric, Oceanic and Hydrologic Applications* (Vol. II), S. Park and L. Xu (eds.), Springer, Chapter 13, 303–343.
- Cummings, J.A. and Smedstad, O.M. (2014). Ocean data impacts in global HYCOM. *Journal of Atmospheric and Oceanic Technology*, 31(8), 1771–1791.
- Garraffo, Z.D., Cummings, J.A., Paturi, S., Hao, Y., Iredell, D., Spindler, T., Balasubramanian, B., Rivin, I., Kim, H-C., Mehra, A. (2020). Real Time Ocean-Sea Ice Coupled Three Dimensional Variational Global Data Assimilative Ocean Forecast System. In *Research Activities in Earth System Modeling*, edited by E. Astakhova, WMO, World Climate Research Program Report No. 6, July 2020.
- Chassignet, E.P., et al. (various years). HYCOM model papers describing the HYCOM development.
