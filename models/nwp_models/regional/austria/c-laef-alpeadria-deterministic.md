# C-LAEF AlpeAdria (deterministic control)

## What this model is
C-LAEF AlpeAdria deterministic is the **control member** of the [C-LAEF AlpeAdria](../../../ensemble_models/regional/austria/c-laef-alpeadria.md) convection-permitting ensemble, published by GeoSphere Austria as a stand-alone deterministic forecast on a 1 km grid over the extended Alpine region. It is the operational successor to **AROME Austria (AROME-Aut)**, the 2.5 km deterministic AROME run that GeoSphere distributed from 2023 until 2026.

It is designed for short-range forecasting of small-scale and rapidly evolving weather. GeoSphere reports the largest gains over the previous 2.5 km systems for wind, convective precipitation (showers and thunderstorms), fog and high fog in Alpine valleys and basins, precipitation type, and near-surface temperature and humidity.

---

## Who runs it
- **Organization:** GeoSphere Austria, jointly developed and jointly operated with **ARSO** (Slovenian Environment Agency) and **DHMZ** (Croatian Meteorological and Hydrological Service). GeoSphere Austria publishes the open data.
- **Country / region:** Austria, Slovenia, Croatia

---

## What area it covers
- **Coverage:** Extended Alpine region, the Alps–Adriatic area
- **Domain details:** The native model grid is reported as **1500 × 1320** points at 1.0 km (per the April 2026 ARSO ACCORD poster, confirmation from GeoSphere: TBD). The **public dataset** is a regular WGS84 / EPSG:4326 lat-lon grid of **1300 × 945 points** (0.0135° lon × 0.009° lat). Grid-point centres span **43.002–51.498 °N, 5.0317–22.5682 °E** (confirmed from the distributed NetCDF files). Compared with the previous 2.5 km public grid (42.98–51.82 °N, 5.50–22.10 °E), the public domain extends slightly further west and east but less far north. The public cut-out does not extend south of 43 °N, even though the native system was designed with a southward-extended domain for the partner services.

---

## Basic details
- **Model type:** Deterministic NWP (convection-permitting); the control member of a regional ensemble
- **Model system / core:** AROME (ALADIN-NH non-hydrostatic spectral dynamical core), code cycle **cy46t1+** (cy48t3bf3 for the 3D-EnVar configuration), per GeoSphere's April 2026 ACCORD presentation. The operational cycle running since go-live has not yet been confirmed.
- **Dynamical formulation:** Non-hydrostatic, spectral, with quadratic spectral truncation
- **Convection-allowing:** Yes
- **Horizontal resolution:** ~1 km native; published on a 0.0135° × 0.009° lat-lon grid
- **Grid dimensions:** 1500 × 1320 native (TBD, see above); 1300 × 945 published
- **Vertical levels:** 90 native levels (per the April 2026 ARSO poster). The public dataset delivers 3D fields on **11 isobaric levels**: 1000, 950, 925, 900, 850, 800, 700, 600, 500, 400 and 300 hPa (confirmed from the files).
- **Model top:** TBD
- **Time step:** 45 s (per the April 2026 ARSO poster)
- **Forecast length:** 60 hours
- **Update frequency / cycles:** 8× daily (00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Temporal output resolution:** 1 hour (both 2D and 3D fields)

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** The C-LAEF AlpeAdria system combines **3D-Var** and **3D-EnVar** analyses, with an incremental analysis update (IAU, 5-minute window) introduced to reduce spin-up after the 3D-EnVar analysis. The 3D-EnVar configuration supports direct assimilation of **radar reflectivity**, extending the control variable to hydrometeors. Pre-operational ACCORD material described the 3D-EnVar work as a control-member configuration. Whether the published control currently runs 3D-EnVar or 3D-Var, and the operational cycling interval, are TBD.
- **Observations assimilated:** TBD for the operational configuration. Radar reflectivity is assimilated in the 3D-EnVar configuration; the full observation list has not been published for the operational system.

---

## Initial and boundary conditions
- **Initial conditions:** Own analysis, as part of the C-LAEF AlpeAdria ensemble data assimilation (see above)
- **Boundary conditions:** TBD. The predecessor control run was driven by ECMWF IFS HRES; the boundary source for the AlpeAdria control has not been confirmed.

---

## What it provides
Deterministic hourly forecasts to +60 h. The publicly distributed NetCDF (`nwp-v2-1h-1km`) contains **39 variables**: 33 surface/single-level fields and 6 pressure-level fields on 11 levels. All variable lists below are confirmed from the files.

**Surface / single-level (2D):**
- 2 m temperature (with min/max over the last forecast hour) and 2 m relative humidity
- 10 m wind (eastward/northward components), maximum 10 m gust speed, and u/v components of the maximum gust
- Surface pressure and **mean sea-level pressure** (MSLP is new; the previous public dataset had surface pressure only)
- Total precipitation, rainfall, snowfall, surface snow amount, and snow-level altitude
- **Precipitation type** (coded; code list in the GeoSphere help document)
- Cloud cover (total, low, mid, high)
- CAPE, CIN, and Showalter index
- **Lightning density** (flashes km⁻², new)
- Surface radiation: global (downwelling SW), net shortwave, net longwave, and downwelling longwave
- Sunshine duration
- Surface temperature, surface geopotential, and a coded weather symbol

**Upper-air (3D, on 11 isobaric levels, 1000 → 300 hPa):**
- Wind (U, V), temperature, geopotential, relative humidity, and vertical velocity (ω)

**Static field (separate file):** model orography (`orog`, m) on the same 1300 × 945 grid.

*(Precipitation, sunshine and radiation fields are now distributed only as de-accumulated per-interval values; the previous dataset also carried accumulated fields. The public dataset contains no simulated radar reflectivity, cloud-microphysics, or height-above-ground-level fields. Pressure-level output now stops at 300 hPa instead of 100 hPa.)*

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.8), float32, gzip-compressed (level 2). One file per run, about **9.2 GB** each.
- **Official download location (direct file listing):**
  https://public.hub.geosphere.at/public/datahub.html?id=nwp-v2-1h-1km/filelisting
- **API access:** GeoSphere Austria's Dataset API provides programmatic access with subsetting by area, time and parameter, under the dataset ID `nwp-v2-1h-1km`. The API exposes a reduced parameter list of 16 surface fields; the 3D fields and several 2D fields are available only in the bulk files.
  https://dataset.api.hub.geosphere.at/v1/docs/
- **Dataset landing page:**
  https://data.hub.geosphere.at/dataset/nwp-v2-1h-1km
- **DOI:** https://doi.org/10.60669/rv80-9d61
- **Model orography:**
  https://public.hub.geosphere.at/public/resources/misc/nwp_oro-v2-1km.nc

The file listing keeps only the most recent runs (three at the time of checking); there is no public archive.

---

## Notes
- **Same file as ensemble member 00.** The deterministic file for a given run is identical to `ensemble_mem00_<run>.nc` in the [C-LAEF AlpeAdria ensemble](../../../ensemble_models/regional/austria/c-laef-alpeadria.md) file listing. The two have the same byte size and identical field values, and both carry `ensemble_member = 00` in their global attributes. Users downloading the full ensemble do not need this file separately.
- **Legacy 2.5 km endpoint (until 4 November 2026).** Since about mid-June 2026, the old `nwp-v1-1h-2500m` endpoint has carried C-LAEF AlpeAdria output **interpolated to the previous 2.5 km grid**, using the old parameter set and variable names. GeoSphere documents this as the `nwp-v2-1h-2500m` dataset (DOI [10.60669/jft1-g709](https://doi.org/10.60669/jft1-g709)), served through the v1 endpoint. The legacy landing page still describes the AROME 2.5 km model. GeoSphere has announced that the v1 endpoint shuts down on **4 November 2026**, after which only `nwp-v2-1h-1km` remains.
- **Breaking changes for users migrating from v1** (per GeoSphere's help document and the files themselves):
  - Short names now follow GRIB/ECMWF conventions, for example `2t`, `10u`, `tp` and `msl` instead of `t2m`, `u10m`, `rr_acc`.
  - Some units have changed.
  - Precipitation and radiation are de-accumulated only.
  - Raw files are ordered **north-to-south** instead of south-to-north.
  - Code lists for `pt` and `sy` are documented only in the help PDF and the NetCDF attributes.
- **Grid-attribute quirk:** The files' global attribute `spatial_resolution` still reads `0.028`, a carry-over from the 2.5 km product. The actual coordinate spacing is 0.0135° × 0.009°.
- **AROME-RUC** is a separate GeoSphere 1.2 km rapid-update nowcasting configuration over Austria with hourly cycling. It is not part of this public dataset.
- **Organizational note:** GeoSphere Austria was formed by the 2023 merger of ZAMG with the Geological Survey of Austria; older publications and documentation refer to ZAMG.
- **Partner services:** ARSO and DHMZ co-operate the system alongside their own national suites. See [ALADIN Slovenia](../slovenia/aladin-slovenia.md) and [ALADIN-HR](../croatia/aladin-hr.md).
- **Relationship to the wider AROME family:** C-LAEF AlpeAdria shares its dynamical core and physics with the broader AROME / HARMONIE-AROME family operated by ACCORD-consortium services. See [AROME France](../france/arome-france.md), [AROME-Arctic](../norway/arome-arctic.md), [HARMONIE-AROME Ireland](../ireland/harmonie-arome-ireland.md), and [MEPS](../norway/meps.md) for related deployments.

---

## Recent version history

### 4 November 2026 (scheduled) — Legacy 2.5 km endpoint shutdown
GeoSphere has announced that the `nwp-v1-1h-2500m` endpoint, which currently carries interpolated C-LAEF AlpeAdria data, shuts down on this date.

### 22 September 2026 — Public announcement
GeoSphere Austria formally announced C-LAEF AlpeAdria as operational and available as open data. According to the announcement, the system was first used over summer 2026 to support duty forecasters and warning services while the open-data products were prepared.

### 24 August 2026 — Native 1 km dataset published
The `nwp-v2-1h-1km` dataset (1 km grid, new parameter set) was published on the GeoSphere Data Hub.

### June 2026 — C-LAEF AlpeAdria replaces AROME-Aut on the public feed
The `nwp-v2-1h-2500m` compatibility dataset was created on 15 June 2026 and published on 16 June 2026. From then on, AlpeAdria output interpolated to 2.5 km was delivered through the existing v1 endpoint, replacing the AROME-Aut (cy43t2bf11, 2.5 km / L90) forecasts. The exact operational cut-over date has not been published.

### Predecessor — AROME Austria (AROME-Aut), 2.5 km
- **15 January 2025:** All on-site GeoSphere NWP systems, including AROME-Aut, migrated to the HPE CRAY-XD2000 cluster. This was a hardware change, not a model upgrade.
- **September 2023:** Assimilation of ceilometer-derived cloud cover (converted to relative humidity) was introduced through the shared C-LAEF assimilation framework, improving low-stratus forecasts in autumn and winter.
- **December 2021:** Upgrade from cy40t1 to cy43t2, which included:
  - adapted screening-level diagnostics for 2 m temperature and humidity in the Alps
  - a new B-matrix and REDNMC tuning
  - an orography switch from GTOPO30 to GMTED2010
  - new output parameters: precipitation type, updraft helicity and a weather symbol
- **Configuration at retirement:** 600 × 432 points, 2.5 km, 90 levels, 3-hourly 3D-Var + OI, IFS HRES boundaries, 8 runs/day to +60 h. By 2026, GeoSphere described AROME-Aut as the C-LAEF (2.5 km) control member mirrored on the local HPC.

---

## Official documentation
- GeoSphere Austria Data Hub (deterministic 1 km dataset): https://data.hub.geosphere.at/dataset/nwp-v2-1h-1km
- Direct file listing: https://public.hub.geosphere.at/public/datahub.html?id=nwp-v2-1h-1km/filelisting
- Dataset API documentation: https://dataset.api.hub.geosphere.at/v1/docs/
- Dataset DOI: https://doi.org/10.60669/rv80-9d61
- Migration help document (German), including `pt` / `sy` code lists: https://public.hub.geosphere.at/public/resources/misc/Hilfestellung_DE.pdf
- Model orography: https://public.hub.geosphere.at/public/resources/misc/nwp_oro-v2-1km.nc
- Announcement (22 September 2026): https://www.geosphere.at/en/news-and-events/news/geosphere-austria-a-more-powerful-forecasting-system-for-the-alps-adriatic-region
- Legacy endpoint (until 4 November 2026): https://data.hub.geosphere.at/dataset/nwp-v1-1h-2500m (DOI https://doi.org/10.60669/9zm8-s664); interpolated AlpeAdria compatibility dataset: https://data.hub.geosphere.at/dataset/nwp-v2-1h-2500m
- GeoSphere Austria homepage: https://www.geosphere.at

### Key references
- Meier, F., Schneider, S., Awan, N., Deacu, D., Goger, B., Neduncheran, A., Wastl, C., Weidle, F. (2026). *NWP related activities in Austria.* 6th ACCORD All Staff Workshop, Marrakech, Morocco, 13–17 April 2026.
- ARSO (2026). *NWP activities at ARSO (Slovenia).* Poster, 6th ACCORD All Staff Workshop, Marrakech, Morocco, April 2026.
- Weidle, F., Awan, N., Deacu, D., Neduncheran, A., Meier, F., Schneider, S., Wastl, C., Wittmann, C. (2025). *NWP related activities in AUSTRIA.* 5th ACCORD All Staff Workshop, Zalakaros, Hungary, 31 March – 4 April 2025.
- Weidle, F., Awan, N., Neduncheran, A., Meier, F., Scheffknecht, P., Wastl, C., Wittmann, C. (2024). *NWP related activities in AUSTRIA.* 4th ACCORD All Staff Workshop, Norrköping, Sweden, 15–19 April 2024.
- Wittmann, C., Awan, N., Neduncheran, A., Meier, F., Scheffknecht, P., Wastl, C., Weidle, F. (2023). *NWP related activities in Austria.* 45th EWGLAM & 30th SRNWP Meeting, Reykjavík, Iceland, 25–28 October 2023.
- Wittmann, C., Meier, F., Schneider, S., Weidle, F., Wastl, C., Awan, N., Scheffknecht, P. (2022). *NWP related activities in AUSTRIA.* 2nd ACCORD All Staff Workshop, Ljubljana, 4–8 April 2022.
- Wittmann, C., Meier, F., Schneider, S., Weidle, F., Wastl, C., Awan, N., Scheffknecht, P. (2021). *NWP related activities in AUSTRIA.* 1st ACCORD All Staff Workshop, online, 12–16 April 2021.
- Montmerle, T., Michel, Y., Arbogast, E., Ménétrier, B., Brousseau, P. (2018). *A 3D ensemble variational data assimilation scheme for the limited-area AROME model: Formulation and preliminary results.* Quarterly Journal of the Royal Meteorological Society, 144(716): 2196–2215. https://doi.org/10.1002/qj.3334
