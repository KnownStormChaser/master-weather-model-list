# WRF-Chem Desert Dust Forecast (GeoSphere Austria)

## What this model is
GeoSphere Austria's operational **desert dust transport forecast**, produced with a dedicated WRF-Chem configuration covering North Africa, the Middle East, and Europe. It forecasts the atmospheric column load of mineral dust out to five days, tracking Saharan and Arabian dust outbreaks as they are lofted and carried northward across the Mediterranean into Central Europe.

Dust matters for Austria specifically because transported Saharan dust is a substantial natural contributor to particulate matter over the Alps — during a strong episode it can account for the majority of the PM measured at high Alpine sites. Under EU air quality reporting rules such outbreaks can be classified as "natural events," so being able to forecast and attribute them has a regulatory as well as a public-health function.

Since 2022 GeoSphere has contributed these forecasts to the WMO **Sand and Dust Storm Warning Advisory and Assessment System (SDS-WAS)**, whose Regional Center for Northern Africa, Middle East and Europe is hosted by AEMET and the Barcelona Supercomputing Center. Forecasts from all participating models, and their evaluation against observations, are published at `dust.aemet.es`.

**This is a separate model chain from the [WRF-Chem air quality forecast](./wrf-chem-austria.md)**, sharing only the WRF-Chem core. It runs on a different domain, a different projection and grid, a longer forecast horizon, and a separate cycle — and publishes a single column-integrated field rather than surface pollutant concentrations.

---

## Who runs it
- **Organization:** GeoSphere Austria (formerly ZAMG — Zentralanstalt für Meteorologie und Geodynamik; merged into GeoSphere Austria in 2023)
- **Country / region:** Austria (operator). The **model domain is not Austrian** — it spans North Africa, the Middle East, and Europe. Filed under `regional/austria/` by operator, per repository convention.
- **Programme affiliation:** Contributing model to the WMO SDS-WAS initiative since 2022; forecasts evaluated at the SDS-WAS Regional Center for Northern Africa, Middle East and Europe (NAMEE) / Barcelona Dust Forecast Center.

---

## What area it covers
- **Coverage:** Equator to Scandinavia — the full Saharan and Arabian dust source regions plus the European receptor region.
- **Domain details (verified from a live file, 2026-07):**
  - **Grid:** 449 (lon) × 324 (lat), regular latitude–longitude
  - **Resolution:** **0.2° × 0.2°** (≈ 22.2 km at the equator)
  - **Bounds:** **0.2 – 64.8 °N, −24.8 – 64.8 °E** (grid-point centres)
  - **CRS:** EPSG:4326, on a **sphere of radius 6 371 000 m** (`inverse_flattening = 0`) — note this is a spherical earth, *not* the WGS84 ellipsoid used by the sibling air quality products. The file's `crs` variable declares the datum as `"undefined"`.
- **Contrast with the sibling AQ system:** the [pollutant forecast](./wrf-chem-austria.md) uses Lambert Conic Conformal (2SP) on WGS84 at 9 km / 3 km over Europe and Central Europe. Nothing about the two grids is interchangeable.

---

## Basic details
- **Model type:** Air quality / atmospheric composition — mineral dust transport
- **Model system / core:** WRF-Chem (online-coupled meteorology–chemistry chemical transport model)
- **Horizontal resolution:** 0.2° (≈ 22 km)
- **Vertical levels:** The **distributed field is column-integrated** — a single 2-D field per timestep with no vertical axis. The model runs a full vertical column internally; only the integral is published.
- **Forecast length:** **120 hours (5 days)** — 121 hourly steps, t+0 … t+120. Note this is substantially longer than the 72 h of the sibling pollutant forecast.
- **Update frequency / cycles:** Daily, **00 UTC** initialization
- **Temporal output resolution:** 1 hour

---

## Meteorological driver
- **Driving NWP model:** WRF (the meteorological component of WRF-Chem), with initial and boundary conditions from the ECMWF **Integrated Forecasting System (IFS)**. *(Not stated on this dataset's page; inherited from the documented configuration of GeoSphere's WRF-Chem suite and from the 2019 evaluation paper. **TBD** — confirm for this specific chain.)*
- **Coupling:** Online (two-way) — chemistry computed inline within WRF.

---

## Chemistry and aerosols
The current operational configuration is **not documented in the open-data metadata (TBD)**. The values below come from Baumann-Stanzer et al. (2019), which evaluates *"the WRF-Chem model set-up, which is run operationally for air quality forecasts in Austria"* — but describes the **2016-era configuration on a 12 km Europe / North Africa domain**, i.e. the v1 generation. Treat as historical context, not as verified current spec:

- **WRF-Chem version:** 3.4.1 *(2016 era)*
- **Gas-phase chemical mechanism:** RADM2 (Stockwell et al., 1990) *(2016 era)*
- **Aerosol treatment:** **MADE/SORGAM** with the dust emission option, including aqueous reactions (Schell et al., 2001) *(2016 era)*
- **PBL scheme:** YSU (Yonsei University) *(2016 era)*
- **Microphysics:** Morrison and Milbrandt *(2016 era)*
- **Land surface:** NOAH *(2016 era)*
- **Radiation:** RRTMG *(2016 era)*
- **Dust scheme:** Online wind-erosion dust emission (the "dust emission option" of MADE/SORGAM) *(2016 era)*

---

## Emissions
- **Dust:** Online, wind-erosion-driven from arid and semi-arid source areas within the domain.
- **Anthropogenic / biogenic:** Not relevant to the published field (which is dust-only), and not documented for this chain. The 2019 paper describes the AQ configuration as using Austrian federal-state inventories combined with TNO and EMEP data, and MEGAN for biogenics — again, 2016 era. **TBD** for the current dust chain.

---

## Data assimilation
- **Assimilates AQ observations:** Not documented; the forecasts are initialized and bounded by ECMWF IFS rather than by an explicit composition-assimilation step described in the public metadata. **TBD.**

---

## What it provides
**One field only.** There is no surface dust concentration, no dust AOD, no size-resolved output, and no PM10 dust fraction in the public dataset.

| Variable (file) | Variable (API) | Long name | Unit (file) | Unit (API) |
|---|---|---|---|---|
| `DUSTcol` | `dust` | Dust load | **kg m⁻²** | **mg m⁻²** |

- **CF standard name:** `atmosphere_mass_content_of_dust_dry_aerosol_particles` — a genuine CF standard name (unlike the AQI variable in the sibling entry).
- **Physical definition.** Baumann-Stanzer et al. (2019) define the WRF-Chem output parameter `DUSTcol` as *the sum of the vertical integral, up to 50 hPa, of the coarse soil-derived aerosols and the coarse soil-derived aerosols in clouds.* So it is a **column mass load including the in-cloud fraction**, integrated to 50 hPa — not a surface concentration and not an optical depth.
- **Observed magnitude** (live file, 2026-07-28 run): 2.3 × 10⁻¹⁷ to 6.9 × 10⁻³ kg m⁻² (0 to ~6850 mg m⁻²), domain mean ~3.1 × 10⁻⁴ kg m⁻². Values of a few hundred mg m⁻² over the Sahara are routine; the low-thousands indicate an active outbreak.

### ⚠ The two access channels use units a factor of 10⁶ apart
This is the single most important thing to know about this dataset. The bulk NetCDF files carry `units = "kg m-2"`; the Dataset API reports `mg m-2`. **Both are correctly labelled and the underlying numbers are the same** — verified by cross-checking four points at a matching valid time, where `file_value × 10⁶ = api_value` to the reported precision (ratio 1.00 at every point). But anyone mixing channels, or assuming the unit carries over, will be off by a million. Always read the unit from the channel you are using.

---

## Data availability
- **Is the data free?** Yes — no account, no registration, no API key.
- **License:** **Creative Commons Attribution 4.0 International (CC BY 4.0)**; attribution to GeoSphere Austria required. *(The CKAN record also carries `"isopen": false` alongside `license_id: CC-BY-4.0` and `"restricted": "False"` — the same portal artifact seen across the GeoSphere hub. CC BY 4.0 is unambiguously open.)*
- **Is the data downloadable?** Yes.
- **Data formats:** NetCDF-4 / HDF5, CF-1.8 conventions. One file per daily run holding all 121 hourly steps. **~70.4 MB per file** (uncompressed; file size is fixed run to run).
- **Time encoding:** `seconds since 1961-01-01` — the same unusual epoch used across the GeoSphere chem family. Decode with a CF-aware reader.
- **File naming:** `chem_dust_<YYYYMMDDHH>.nc`; `<YYYYMMDDHH>` is the 00 UTC run initialization.
- **Bulk download:**
  https://public.hub.geosphere.at/public/datahub.html?id=chem_dust-v1-1h-0p2deg/filelisting
  - Direct raw: `https://public.hub.geosphere.at/datahub/resources/chem_dust-v1-1h-0p2deg/filelisting/chem_dust_<YYYYMMDDHH>.nc`
- **API access:** Yes, with area/time/parameter subsetting —
  `https://dataset.api.hub.geosphere.at/v1/grid/forecast/chem_dust-v1-1h-0p2deg` (geojson, netcdf) and `/timeseries/forecast/chem_dust-v1-1h-0p2deg` (geojson, csv). API parameter name is **`dust`**, not `DUSTcol`.
- **Dataset landing page:** https://data.hub.geosphere.at/en/dataset/chem_dust-v1-1h-0p2deg
- **DOI:** https://doi.org/10.60669/metr-z617
- **Version:** 1, published 8 June 2026.
- **Latency:** ~**4 h 25 min** after the 00 UTC run (measured: published 04:25 UTC, twice consecutively). This is a full hour behind the sibling pollutant forecast (03:25 UTC) — a separate run, not a shared one.
- **⚠ Archive depth: 2 files.** Verified 2026-07-29 by full bucket enumeration — the store held exactly the current and previous day's run. **There is no archive.** Harvest daily or lose it.
- **Programmatic listing:** the browse UI fronts an S3-compatible store; directory `GET` returns `AccessDenied`, but anonymous `ListObjectsV2` works:
```bash
  curl -s "https://public.hub.geosphere.at/datahub/?list-type=2&max-keys=1000\
  &delimiter=/&prefix=resources/chem_dust-v1-1h-0p2deg/filelisting/"
```

---

## Notes
- **Distinct chain, not a domain of the AQ system.** Unlike the 9 km and 3 km pollutant products — which are the outer and inner nests of one run — this dust forecast has its own domain, projection, grid geometry, forecast length, cycle time, and output field. Documented separately for that reason. Both use WRF-Chem; that is the extent of the overlap.
- **⚠ API reports the wrong spatial resolution.** The Dataset API metadata gives `spatial_resolution_m: 2200` for this dataset. The true spacing is **0.2° ≈ 22 236 m** — an order of magnitude larger, and the value the NetCDF files themselves carry (`spatial_resolution = 22235.5`). Don't size buffers or reprojections off the API figure.
- **⚠ Dangling CF bounds references.** The `lon` and `lat` coordinate variables declare `bounds = "lon_bnds"` and `bounds = "lat_bnds"`, but **neither variable exists in the file**. Strict CF checkers will fail this, and some readers may error when resolving cell bounds. Compute bounds from the regular 0.2° spacing instead.
- **`calender` typo.** The time coordinate carries `calender = "gregorian"` rather than the CF-mandated `calendar` — consistent with the rest of the GeoSphere chem family. Harmless (gregorian is the CF default) but non-compliant.
- **API truncates past timesteps.** The bulk file contains the complete run from t+0. The API serves only timesteps from approximately the current time forward, so an API request mid-day returns a shortened forecast. Use the bulk file if you need the full 121 steps or the analysis time.
- **Not a full dust product.** SDS-WAS partner models typically publish dust optical depth, surface concentration, and size-resolved load. GeoSphere publishes only the column mass integral. For a richer open dust product see [CAMS Global](../../global/eu/cams-global.md) (0.4°, twice daily, multiple aerosol species) or the SDS-WAS multi-model comparison at `dust.aemet.es`.
- **Relationship to siblings:** the desert dust that this model tracks is a physical contributor to the PM10 and PM2.5 fields in the [pollutant forecast](./wrf-chem-austria.md). Whether the operational AQ chain ingests or shares dust fields with this chain is **not documented (TBD)**.
- **Organizational note:** GeoSphere Austria was formed by the 2023 merger of ZAMG with the Geological Survey of Austria; older publications, including the key evaluation paper below, refer to ZAMG.

---

## Official documentation
- Dataset landing page: https://data.hub.geosphere.at/en/dataset/chem_dust-v1-1h-0p2deg
- Bulk download: https://public.hub.geosphere.at/public/datahub.html?id=chem_dust-v1-1h-0p2deg/filelisting
- Dataset API documentation: https://dataset.api.hub.geosphere.at/v1/docs/
- GeoSphere desert dust product page: https://www.geosphere.at/en/maps/health-weather/desert-dust
- DOI: https://doi.org/10.60669/metr-z617
- WMO SDS-WAS: https://community.wmo.int/en/activity-areas/gaw/science-for-services/sds-was
- SDS-WAS model intercomparison and evaluation (AEMET / Barcelona): https://dust.aemet.es/
- SDS-WAS participating models: https://sds-was.aemet.es/forecast-products/models

### Key references
- Baumann-Stanzer, K., Greilinger, M., Kasper-Giebl, A., Flandorfer, C., Hieden, A., Lotteraner, C., Ortner, M., Vergeiner, J., Schauer, G., Piringer, M. (2019). *Evaluation of WRF-Chem Model Forecasts of a Prolonged Saharan Dust Episode over the Eastern Alps.* Aerosol and Air Quality Research, 19: 1226–1240. https://doi.org/10.4209/aaqr.2018.03.0116 *(evaluates the operational Austrian WRF-Chem setup against Alpine PM observations, and gives the definition of the `DUSTcol` parameter used in this dataset)*
- Grell, G. A., Peckham, S. E., Schmitz, R., McKeen, S. A., Frost, G., Skamarock, W. C., Eder, B. (2005). *Fully coupled "online" chemistry within the WRF model.* Atmospheric Environment, 39(37), 6957–6975. https://doi.org/10.1016/j.atmosenv.2005.04.027
- Schell, B., Ackermann, I. J., Hass, H., Binkowski, F. S., Ebel, A. (2001). *Modeling the formation of secondary organic aerosol within a comprehensive air quality model system.* Journal of Geophysical Research, 106(D22), 28275–28293. https://doi.org/10.1029/2001JD000384 *(MADE/SORGAM aerosol module)*
