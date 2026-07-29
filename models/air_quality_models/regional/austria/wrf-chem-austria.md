# WRF-Chem Air Quality Forecast (GeoSphere Austria)

## What this model is
The GeoSphere Austria air quality forecast is an operational atmospheric-composition system based on the online-coupled chemical transport model **WRF-Chem**. It produces daily 3-day forecasts of key pollutants — ground-level ozone, particulate matter, and nitrogen dioxide — for public-health air quality guidance over Europe and, at higher resolution, Central Europe.

It is distributed as two nested products: an outer **9 km** domain covering Europe (and parts of North Africa and Russia) and an inner **3 km** domain covering Central Europe. A third dataset — a derived **Air Quality Index** — is computed from the 3 km run and published on the identical grid; it is documented here rather than given its own entry, because it is a post-processing product of this chain rather than a distinct system.

GeoSphere runs a **separate WRF-Chem configuration for Saharan dust transport** on a far larger domain at 0.2°, contributing to the WMO SDS-WAS programme. That is a distinct model chain with its own entry: [WRF-Chem Desert Dust (GeoSphere Austria)](./wrf-chem-dust.md).

---

## Who runs it
- **Organization:** GeoSphere Austria (formerly ZAMG — Zentralanstalt für Meteorologie und Geodynamik; merged into GeoSphere Austria in 2023)
- **Country / region:** Austria

---

## What area it covers
- **Coverage:** Europe (9 km outer domain) and Central Europe (3 km inner domain)
- **Domain details:** Both domains use the same Lambert Conic Conformal (2SP) projection — standard parallels 30° / 60°, central meridian 13.3 °E, latitude of origin 47.5 °N, WGS84 datum (verified from the files' `lambert_conformal` grid-mapping variable). Files are CF-1.8 NetCDF-4 with 2-D `lat`/`lon` and 1-D projected `x`/`y` coordinates.
  - **9 km "Pollutant Forecast for Europe":** Europe plus parts of North Africa and Russia. Grid **680 × 680** at 9 km (≈ 6111 km span; file label `d01`, outer domain); bounding box roughly 15.59–74.39 °N, −53.73–80.33 °E (the wide longitude range reflects the projected-grid corners rather than the useful coverage area).
  - **3 km "Pollutant Forecast for Central Europe":** Central Europe. Grid **450 × 450** at 3 km (≈ 1347 km span; file label `d02`, inner domain); bounding box roughly 40.91–53.75 °N, 2.86–23.74 °E.
  - **⚠ Portal resource IDs are inverted (verified 2026-07):** the data-hub resource named `chem-v2-1h-9km` actually serves the **3 km** Central Europe files (`chem_d02_*`), and `chem-v2-1h-3km` actually serves the **9 km** Europe files (`chem_d01_*`). GeoSphere's API changelog confirms the intended physical design (Europe = 9 km, Central Europe = 3 km); only the resource-ID labels are reversed. See *Data availability* for the corrected download mapping.

---

## Basic details
- **Model type:** Air quality / atmospheric composition
- **Model system / core:** WRF-Chem (online-coupled meteorology–chemistry chemical transport model)
- **Horizontal resolution:** 9 km (outer Europe domain) and 3 km (inner Central Europe domain)
- **Vertical levels:** The **distributed** fields are surface-only — one "close to the surface (lowest model level)" layer per pollutant (file variables are `*surf`, dimensioned time × y × x with no vertical axis). The full WRF-Chem model runs ~48 levels internally (per GeoSphere HPC documentation); only the lowest level is published.
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
Daily hourly forecasts (73 hourly steps, t+0 … t+72) of four near-surface pollutant concentrations — each the lowest-model-level value, in **kg m⁻³** (note: not µg/m³):
- Nitrogen dioxide — `NO2surf`
- Ozone — `O3surf`
- PM10 — `PM10surf`
- PM2.5 — `PM25surf`

Both domains distribute the same four parameters. (The files split particulate matter into separate PM10 and PM2.5 fields.)

### Derived product — Air Quality Index (`chem_aqi-v1-1d-3km`)
A **daily categorical air quality index** computed from the 3 km run's forecast ozone, nitrogen dioxide, and particulate matter, following the **European Environment Agency (EEA) European Air Quality Index** banding. Distributed on the **identical 3 km `d02` grid** (450 × 450, same LCC 2SP projection, same `x`/`y` bounds, same bbox 40.91–53.75 °N / 2.86–23.74 °E — verified against a live file, 2026-07).

- **Variable:** `aqi` (files) / `aqi` (API), `float32`, dimensioned `time × y × x`, `units = "1"`
- **Values:** integers **1–6** stored as float32, per the file's `flag_value` / `flag_meaning` attributes:

  | Value | Category |
  |---|---|
  | 1 | good |
  | 2 | fair |
  | 3 | moderate |
  | 4 | poor |
  | 5 | very_poor |
  | 6 | extremely_poor |

- **Temporal structure:** **3 daily steps**, valid 00:00 UTC on the run day and the following two days (`P1D`) — *not* hourly. The file's global `freq = 1H` attribute is inherited boilerplate from the hourly chem products and is **wrong for this dataset**; the `1d` in the dataset ID and the actual `time` coordinate are correct.
- **⚠ Title vs coverage:** GeoSphere titles the dataset "Air Quality Index **for Austria**", but the grid is the full 3 km **Central Europe** `d02` domain — roughly 1347 km square, spanning northern Italy to Poland. Austria occupies a small part of it.
- **⚠ Band-label mismatch:** GeoSphere's prose says the categories run from *"very good"* to *"extremely poor"*, but the file's `flag_meaning` gives the lowest band as **`good`**, matching the EEA scale. Trust the file attributes.
- **Aggregation method not documented (TBD).** How the hourly pollutant fields are collapsed into one value per forecast day — daily maximum of the hourly index, daily mean, or worst sub-index over the day — is not stated in the dataset metadata. The EEA index itself is normally the worst sub-index across pollutants, using hourly values for NO2/O3 and 24-hour running means for PM, but GeoSphere's daily reduction step is unconfirmed.
- **Non-CF standard name.** The `aqi` variable carries `standard_name = "air_quality_index"`, which is **not** in the CF standard-name table. Strict CF validators will reject it. The `long_name` also contains a typo (`Air qualiy index`).

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 / HDF5, CF-1.8 conventions. One file per daily run holding all 73 hourly steps and all four pollutants (`NO2surf`, `O3surf`, `PM10surf`, `PM25surf`, kg m⁻³). Time is encoded as "seconds since 1961-01-01" — an unusual epoch; decode with a CF-aware reader.
- **File naming:** `chem_<d0X>_<YYYYMMDDHH>.nc` — `d01` = 9 km Europe (outer), `d02` = 3 km Central Europe (inner); `<YYYYMMDDHH>` is the 00 UTC run initialization.
- **Official download locations (bulk file listing) — ⚠ resource IDs are inverted relative to resolution (verified 2026-07):**
  - **9 km Europe** (files `chem_d01_*`, 9000 m, 680 × 680) is served under the resource named **`chem-v2-1h-3km`**: https://public.hub.geosphere.at/public/datahub.html?id=chem-v2-1h-3km/filelisting
  - **3 km Central Europe** (files `chem_d02_*`, 3000 m, 450 × 450) is served under the resource named **`chem-v2-1h-9km`**: https://public.hub.geosphere.at/public/datahub.html?id=chem-v2-1h-9km/filelisting
  - Direct raw `.nc` base: `https://public.hub.geosphere.at/datahub/resources/<resource-id>/filelisting/<file>.nc`
  - Don't infer resolution from the resource-ID number — confirm via each file's `spatial_resolution` attribute or the projected `x`/`y` spacing.
- **API access:** GeoSphere Austria's Dataset API provides programmatic access with subsetting (by area, time, parameter):
  https://dataset.api.hub.geosphere.at/v1/docs/
- **Dataset landing pages:**
  - 9 km: https://data.hub.geosphere.at/en/dataset/chem-v2-1h-9km
  - 3 km: https://data.hub.geosphere.at/en/dataset/chem-v2-1h-3km
- **DOIs** (per the data-hub resource pages; **domain labels not independently verified** — because the resource IDs are inverted, these DOI-to-domain assignments may likewise be reversed, so confirm before citing):
  - listed for 9 km (Europe): https://doi.org/10.60669/f4q8-5j13
  - listed for 3 km (Central Europe): https://doi.org/10.60669/1860-g785

Both products are version 2, published 22 January 2025. The 9 km and 3 km datasets share the same access portal, API, and license; users can choose whichever domain/resolution suits their workflow.

### Air Quality Index dataset
- **Dataset ID:** `chem_aqi-v1-1d-3km` — resource ID and resolution agree here (unlike the inverted pair above).
- **File naming:** `chem_aqi_<YYYYMMDDHH>.nc`; `<YYYYMMDDHH>` is the 00 UTC run initialization.
- **Format:** NetCDF-4 / HDF5, CF-1.8. One file per daily run holding all 3 daily steps. ~4.08 MB per file.
- **Bulk download:** https://public.hub.geosphere.at/public/datahub.html?id=chem_aqi-v1-1d-3km/filelisting
  - Direct raw: `https://public.hub.geosphere.at/datahub/resources/chem_aqi-v1-1d-3km/filelisting/chem_aqi_<YYYYMMDDHH>.nc`
- **API access:** Yes — `https://dataset.api.hub.geosphere.at/v1/grid/forecast/chem_aqi-v1-1d-3km` and the matching `/timeseries/` route (geojson, netcdf, csv).
- **Landing page:** https://data.hub.geosphere.at/en/dataset/chem_aqi-v1-1d-3km
- **DOI:** https://doi.org/10.60669/9vem-kg63
- **Licence:** CC BY 4.0. Version 1, published 8 June 2026.
- **Latency:** ~**3 h 55 min** after the 00 UTC run (measured: published 03:55 UTC). This is ~30 minutes behind the parent WRF-Chem run (03:25 UTC), consistent with it being a post-processing step.

### ⚠ Retention — the whole chem family keeps only 2 files
Verified 2026-07-29 by enumerating all four buckets: `chem-v2-1h-3km`, `chem-v2-1h-9km`, and `chem_aqi-v1-1d-3km` each held **exactly two objects** — the current and previous day's run. There is **no archive**. Anything you need beyond ~48 hours must be harvested daily.

### Programmatic listing (undocumented but public)
Plain `GET` on a directory path returns `AccessDenied`, but the backing store answers anonymous S3 `ListObjectsV2` on the bucket root:
```bash
curl -s "https://public.hub.geosphere.at/datahub/?list-type=2&max-keys=1000\
&delimiter=/&prefix=resources/chem_aqi-v1-1d-3km/filelisting/"
```
Returns `ListBucketResult` XML (`Key`, `LastModified`, `ETag`, `Size`); bucket `datahub`, key root `resources/`. Substitute any dataset ID in the prefix.

---

## Notes
- **Why a separate system from AROME:** GeoSphere Austria runs WRF-Chem for air pollution because the chemistry/air-chemistry functionality it requires is not available in the operational [AROME Austria](../../../nwp_models/regional/austria/arome-austria.md) NWP model. WRF-Chem runs on GeoSphere's operational HPC alongside the AROME suite. It replaced the earlier CAMx photochemical model (which had been coupled to ALADIN/MM5 output).
- **Two nested domains, one system:** The 9 km Europe and 3 km Central Europe products are the outer and inner nests of the same WRF-Chem configuration, documented internally as a two-domain run out to +72 h.
- **Version change (v1 → v2, January 2025):** The current v2 products at 9 km / 3 km replaced the v1 products at 12 km / 4 km (`chem-v1-1h-12km` and `chem-v1-1h-4km`). The published version notes cite changes to the projection (a small shift in the coordinate centre), the spatial coverage (domain), and the horizontal resolution. GeoSphere HPC presentations from 2023 and 2025 still describe the older 12 km (512×432×48) / 4 km (256×223×48) nesting, which corresponds to the v1 configuration.
- **`calender` typo is family-wide.** Every GeoSphere chem NetCDF — the 9 km and 3 km pollutant files, the AQI file, and the dust file — spells the time-coordinate attribute **`calender`** rather than the CF-mandated `calendar`. Harmless in practice (gregorian is the CF default when the attribute is absent), but strict CF validators will flag it, and code that reads `calendar` explicitly will find nothing. Verified across all four products, 2026-07.
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
- Dataset landing page (Air Quality Index): https://data.hub.geosphere.at/en/dataset/chem_aqi-v1-1d-3km
- Bulk download (AQI): https://public.hub.geosphere.at/public/datahub.html?id=chem_aqi-v1-1d-3km/filelisting
- EEA European Air Quality Index: https://www.eea.europa.eu/en/analysis/maps-and-charts/index
- Dataset API documentation: https://dataset.api.hub.geosphere.at/v1/docs/
- GeoSphere Austria homepage: https://www.geosphere.at

### Key references
- Grell, G. A., Peckham, S. E., Schmitz, R., McKeen, S. A., Frost, G., Skamarock, W. C., Eder, B. (2005). *Fully coupled "online" chemistry within the WRF model.* Atmospheric Environment, 39(37), 6957–6975. https://doi.org/10.1016/j.atmosenv.2005.04.027 *(the canonical WRF-Chem reference, cited on both GeoSphere dataset pages)*
