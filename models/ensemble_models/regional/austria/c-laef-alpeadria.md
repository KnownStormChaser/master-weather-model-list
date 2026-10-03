# C-LAEF AlpeAdria (Convection-Permitting Limited-Area Ensemble Forecasting for the AlpeAdria region)

## What this model is
C-LAEF AlpeAdria is the operational 1 km convection-permitting regional ensemble prediction system for the extended Alpine and Alps–Adriatic region. GeoSphere Austria developed it with ARSO (Slovenia) and DHMZ (Croatia), and the three services operate it jointly. It replaced the 2.5 km **C-LAEF** ensemble in 2026. Its control member is published separately as [C-LAEF AlpeAdria deterministic](../../../nwp_models/regional/austria/c-laef-alpeadria-deterministic.md).

The ensemble quantifies forecast uncertainty for short-range, small-scale and rapidly evolving weather: thunderstorms, heavy precipitation, fog, foehn and local wind effects in complex Alpine terrain. Compared with its predecessor, it uses a grid 2.5× finer and runs **eight full ensemble forecasts per day instead of two**.

---

## Who runs it
- **Organization:** GeoSphere Austria, jointly developed and jointly operated with **ARSO** (Slovenian Environment Agency) and **DHMZ** (Croatian Meteorological and Hydrological Service). GeoSphere Austria publishes the open data.
- **Country / region:** Austria, Slovenia, Croatia

---

## What area it covers
- **Coverage:** Extended Alpine region, the Alps–Adriatic area
- **Domain details:** The native model grid is reported as **1500 × 1320** points at 1.0 km (per the April 2026 ARSO ACCORD poster, confirmation from GeoSphere: TBD). The public dataset is a regular WGS84 / EPSG:4326 lat-lon grid of **1300 × 945 points** (0.0135° lon × 0.009° lat). Grid-point centres span **43.002–51.498 °N, 5.0317–22.5682 °E** (confirmed from the distributed files). This is the same public grid as the deterministic control. The public cut-out does not extend south of 43 °N, even though the native domain was extended southward for the partner services.

---

## Basic details
- **Model type:** Ensemble NWP (convection-permitting)
- **Model system / core:** AROME-based EPS (ALADIN-NH non-hydrostatic spectral dynamical core), code cycle **cy46t1+** (cy48t3bf3 for the 3D-EnVar member), per GeoSphere's April 2026 ACCORD presentation. The operational cycle running since go-live has not yet been confirmed.
- **Dynamical formulation:** Non-hydrostatic, spectral, with quadratic spectral truncation
- **Convection-allowing:** Yes
- **Ensemble size:** 16 perturbed members + 1 control (17 total)
- **Horizontal resolution:** ~1 km native; published on a 0.0135° × 0.009° lat-lon grid
- **Grid dimensions:** 1500 × 1320 native (TBD, see above); 1300 × 945 published
- **Vertical levels:** 90 native levels (per the April 2026 ARSO poster). Member files carry 3D fields on 11 isobaric levels (1000–300 hPa).
- **Model top:** TBD
- **Time step:** 45 s (per the April 2026 ARSO poster)
- **Forecast length:** 60 hours
- **Update frequency / cycles:** 8× daily (00, 03, 06, 09, 12, 15, 18, 21 UTC). The predecessor ran full +60 h forecasts only at 00 and 12 UTC.
- **Temporal output resolution:** 1 hour

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** **3D-Var** combined with **3D-EnVar**, with an incremental analysis update (IAU, 5-minute window) after the 3D-EnVar analysis to reduce spin-up. The 3D-EnVar member assimilates **radar reflectivity** directly, extending the control variable to hydrometeors. Which members use which analysis, and the operational cycling interval, are TBD for the operational configuration.
- **Observations assimilated:** TBD for the operational configuration (radar reflectivity confirmed for the 3D-EnVar member)

---

## Initial and boundary conditions
- **Initial conditions:** Ensemble data assimilation within the C-LAEF AlpeAdria system (see above)
- **Boundary conditions:** TBD. The predecessor used ECMWF IFS ENS for the perturbed members and IFS HRES for the control; the boundary source for AlpeAdria has not been confirmed.

---

## Perturbations and design
- **Ensemble design:** Described in ACCORD material as a **lagged ensemble** running on the ECMWF ATOS HPC facility. The public files label all 17 members with the same nominal run time. How members are lagged internally is TBD.
- **Initial-condition perturbations:** TBD. The predecessor used per-member perturbed 3D-Var analyses from its ensemble data assimilation.
- **Model/physics perturbations:** TBD. The predecessor used a pure Stochastic Parameter Perturbation (SPP) scheme.
- **Surface perturbations:** TBD

---

## What it provides
The publicly distributed dataset (`ensemble-v2-1h-1km`) contains two kinds of files per run. All contents below are confirmed from the files.

**Ensemble statistics (`ensemble_stats_<run>.nc`):** the **10th, 50th and 90th percentiles** computed from all 17 members, hourly to +60 h. It has **42 fields** (14 parameters × 3 percentiles):
- 2 m temperature and 2 m relative humidity
- 10 m wind (u/v components) and maximum 10 m gust
- Mean sea-level pressure
- Total precipitation, rainfall, snowfall, and snow-level altitude
- Total cloud cover
- CAPE
- Surface global radiation and sunshine duration

Compared with the predecessor's percentile product, this adds 2 m humidity, gusts and MSLP, and drops 2 m min/max temperature.

**Individual members (`ensemble_mem00_<run>.nc` … `ensemble_mem16_<run>.nc`):** all 17 members, each with the same **39 variables** as the deterministic control: 33 surface/single-level fields and 6 pressure-level fields (U, V, T, geopotential, RH, ω) on 11 levels. See [C-LAEF AlpeAdria deterministic](../../../nwp_models/regional/austria/c-laef-alpeadria-deterministic.md) for the full variable list. Raw members were **not** distributed for the predecessor.

**Static field (separate file):** model orography (`orog`, m) on the same grid.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (CF-1.8), float32, gzip-compressed (level 2). Sizes per run: about **7.2 GB** for the statistics file and about **9.2 GB per member file**, so roughly 165 GB for a full 17-member run.
- **Official download location (bulk file listing):**
  https://public.hub.geosphere.at/public/datahub.html?id=ensemble-v2-1h-1km/filelisting
- **API access:** GeoSphere Austria's Dataset API provides programmatic access with subsetting by area, time and parameter, under the dataset ID `ensemble-v2-1h-1km`. **The API serves only the ensemble statistics**; individual members are bulk-download only.
  https://dataset.api.hub.geosphere.at/v1/docs/
- **Dataset landing page:**
  https://data.hub.geosphere.at/dataset/ensemble-v2-1h-1km
- **DOI:** https://doi.org/10.60669/f21y-5007
- **Model orography:**
  https://public.hub.geosphere.at/public/resources/misc/nwp_oro-v2-1km.nc

The file listing keeps only the most recent runs (three at the time of checking); there is no public archive.

---

## Notes
- **Member 00 is the deterministic control.** `ensemble_mem00_<run>.nc` is identical to the `nwp-v2-1h-1km` deterministic file for the same run: same byte size, identical field values, and both carry `ensemble_member = 00`. See [C-LAEF AlpeAdria deterministic](../../../nwp_models/regional/austria/c-laef-alpeadria-deterministic.md).
- **Legacy 2.5 km endpoint (until 4 November 2026).** Since about mid-June 2026, the old `ensemble-v1-1h-2500m` endpoint has carried C-LAEF AlpeAdria percentiles **interpolated to the previous 2.5 km grid**, using the old parameter set and a twice-daily update. GeoSphere documents this as the `ensemble-v2-1h-2500m` dataset (DOI [10.60669/c1by-wh34](https://doi.org/10.60669/c1by-wh34)), served through the v1 endpoint. The legacy landing page still describes the 2.5 km C-LAEF. GeoSphere has announced that the v1 endpoint shuts down on **4 November 2026**.
- **Breaking changes for users migrating from v1:**
  - Short names have changed, for example `2t_p50` instead of `t2m_p50`.
  - Some units have changed.
  - Precipitation and radiation are de-accumulated only.
  - Raw files are ordered north-to-south.
  - Fields are float32 instead of scaled 16-bit integers.
- **Grid-attribute quirk:** The files' global attribute `spatial_resolution` still reads `0.028`, carried over from the 2.5 km product. The actual spacing is 0.0135° × 0.009°.
- **Landing-page discrepancy:** The dataset page's parameter keywords omit pressure, but the statistics files and the API both include `msl_p10/p50/p90`.
- **Partner services:** See [ALADIN Slovenia](../../../nwp_models/regional/slovenia/aladin-slovenia.md) and [ALADIN-HR](../../../nwp_models/regional/croatia/aladin-hr.md) for ARSO's and DHMZ's national suites.
- **Relationship to the wider AROME family:** C-LAEF AlpeAdria shares its dynamical core and physics with the broader AROME / HARMONIE-AROME family operated by ACCORD-consortium services. See [AROME France](../../../nwp_models/regional/france/arome-france.md), [MEPS](../../../nwp_models/regional/norway/meps.md), and [AROME-Arctic](../../../nwp_models/regional/norway/arome-arctic.md) for related deployments.
- **Organizational note:** GeoSphere Austria was formed by the 2023 merger of ZAMG with the Geological Survey of Austria; older publications refer to ZAMG.

---

## Recent version history

### 4 November 2026 (scheduled) — Legacy 2.5 km endpoint shutdown
GeoSphere has announced that the `ensemble-v1-1h-2500m` endpoint, which currently carries interpolated C-LAEF AlpeAdria percentiles, shuts down on this date.

### 22 September 2026 — Public announcement
GeoSphere Austria formally announced C-LAEF AlpeAdria as operational and available as open data, after a summer of use by duty forecasters and warning services.

### 24 August 2026 — Native 1 km dataset published
The `ensemble-v2-1h-1km` dataset was published on the GeoSphere Data Hub. It includes, for the first time, all 17 individual members alongside the percentile statistics.

### June 2026 — C-LAEF AlpeAdria replaces C-LAEF on the public feed
The `ensemble-v2-1h-2500m` compatibility dataset was created on 15 June 2026 and published on 16 June 2026. From then on, AlpeAdria percentiles interpolated to 2.5 km were delivered through the existing v1 endpoint, replacing the 2.5 km C-LAEF product. The exact operational cut-over date has not been published. (C-LAEF AlpeAdria is the matured form of the system developed earlier under the name "C-LAEF 1k".)

### Predecessor — C-LAEF, 2.5 km
- **15 January 2025:** The locally mirrored control run moved to GeoSphere's HPE CRAY-XD2000 cluster. The perturbed ensemble remained on ECMWF ATOS.
- **September 2023:** A pure Stochastic Parameter Perturbation (SPP) scheme replaced the hybrid physics-perturbation scheme. Ceilometer cloud cover (converted to RH) was added to the ensemble 3D-Var.
- **December 2021:** Upgrade from cy40t1 to cy43t2, which included:
  - aligning the control with AROME-Aut so it could serve as a backup
  - shortening the assimilation cycle from 6 h to 3 h
  - adding a surface perturbation scheme
- **November 2019:** Operational start on the ECMWF HPC facility, with long +60 h / +48 h runs at 00/12 UTC and short 06/18 UTC runs closing a 6-hour cycle.
- **Configuration at retirement:** 16+1 members, 600 × 432 points, 2.5 km, 90 levels, ensemble 3D-Var + OI, IFS ENS/HRES boundaries. Public output was P10/P50/P90 only, twice daily.
- The ARA Austrian reanalysis ensemble (2.5 km, 10+1 members, 2013–2023) was built on this system. It is a research product and is not cataloged.

---

## Official documentation
- GeoSphere Austria Data Hub (ensemble 1 km dataset): https://data.hub.geosphere.at/dataset/ensemble-v2-1h-1km
- Bulk download (file listing): https://public.hub.geosphere.at/public/datahub.html?id=ensemble-v2-1h-1km/filelisting
- Dataset API documentation: https://dataset.api.hub.geosphere.at/v1/docs/
- Dataset DOI: https://doi.org/10.60669/f21y-5007
- Migration help document (German): https://public.hub.geosphere.at/public/resources/misc/Hilfestellung_DE.pdf
- Model orography: https://public.hub.geosphere.at/public/resources/misc/nwp_oro-v2-1km.nc
- Announcement (22 September 2026): https://www.geosphere.at/en/news-and-events/news/geosphere-austria-a-more-powerful-forecasting-system-for-the-alps-adriatic-region
- Legacy endpoint (until 4 November 2026): https://data.hub.geosphere.at/dataset/ensemble-v1-1h-2500m (DOI https://doi.org/10.60669/1a8r-ve42); interpolated AlpeAdria compatibility dataset: https://data.hub.geosphere.at/dataset/ensemble-v2-1h-2500m
- GeoSphere Austria homepage: https://www.geosphere.at

### Key references
- Meier, F., Schneider, S., Awan, N., Deacu, D., Goger, B., Neduncheran, A., Wastl, C., Weidle, F. (2026). *NWP related activities in Austria.* 6th ACCORD All Staff Workshop, Marrakech, Morocco, 13–17 April 2026.
- ARSO (2026). *NWP activities at ARSO (Slovenia).* Poster, 6th ACCORD All Staff Workshop, Marrakech, Morocco, April 2026.
- Weidle, F., Awan, N., Deacu, D., Neduncheran, A., Meier, F., Schneider, S., Wastl, C., Wittmann, C. (2025). *NWP related activities in AUSTRIA.* 5th ACCORD All Staff Workshop, Zalakaros, Hungary, 31 March – 4 April 2025.
- Wittmann, C., Awan, N., Neduncheran, A., Meier, F., Scheffknecht, P., Wastl, C., Weidle, F. (2023). *NWP related activities in Austria.* 45th EWGLAM & 30th SRNWP Meeting, Reykjavík, Iceland, 25–28 October 2023.
- Wittmann, C., Meier, F., Schneider, S., Weidle, F., Wastl, C., Awan, N., Scheffknecht, P. (2021). *NWP related activities in AUSTRIA.* 1st ACCORD All Staff Workshop, online, 12–16 April 2021.
- Montmerle, T., Michel, Y., Arbogast, E., Ménétrier, B., Brousseau, P. (2018). *A 3D ensemble variational data assimilation scheme for the limited-area AROME model: Formulation and preliminary results.* Quarterly Journal of the Royal Meteorological Society, 144(716): 2196–2215. https://doi.org/10.1002/qj.3334
