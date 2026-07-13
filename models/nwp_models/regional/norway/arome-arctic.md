# AROME-Arctic

## What this model is
AROME-Arctic is a **high-resolution, convection-permitting regional deterministic numerical weather prediction (NWP) model** operated by the Norwegian Meteorological Institute (MET Norway) over the European Arctic.

It is a HARMONIE-AROME configuration tailored to high-latitude conditions, with particular focus on Arctic boundary layers, sea-ice interactions, and mesoscale weather systems such as polar lows. It is the primary operational short-range forecasting system for Svalbard and surrounding Arctic seas, and also underpins research applications including the Copernicus Arctic Regional Reanalysis (CARRA).

---

## Who runs it
- **Organization:** Norwegian Meteorological Institute (MET Norway)
- **Country / region:** Norway

---

## What area it covers
- **Coverage:** European Arctic — including Svalbard, the Norwegian Sea, the Barents Sea, parts of the Greenland Sea, and adjacent land and marine areas
- **Domain details:** Lambert-projected Arctic domain at 2.5 km grid spacing, **739 × 949 grid points** (verified from the THREDDS OPeNDAP structure), covering roughly **62–88°N, 18°W–80°E** (per the file's `geospatial_*` global attributes); the "blue contour" domain on MET Norway's [domain map](https://github.com/metno/NWPdocs/wiki/AROME-Arctic-dataset#domains) — larger and more northerly than the MEPS Nordic domain.

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system / core:** HARMONIE-AROME (ALADIN-NH non-hydrostatic spectral dynamical core, AROME physics, SURFEX surface scheme); current cycle **cy46** as of 23 September 2025
- **Dynamical formulation:** Non-hydrostatic, spectral, with two-time-level semi-implicit semi-Lagrangian discretization
- **Convection-allowing:** Yes (deep convection explicitly resolved at 2.5 km; shallow convection parameterized)
- **Horizontal resolution:** ~2.5 km
- **Vertical levels:** 65 (lowest level ~12 m above ground)
- **Model top:** 10 hPa (~30 km)
- **Forecast length:**
  - 66 hours for the deterministic control (verified: the `det_vc` product carries 67 hourly steps, +0…+66 h)
  - 61 hours for the time-lagged ensemble members as archived/disseminated (verified from the 2026-07-13 00 UTC `lagged_6_h` product: 62 hourly steps, +0…+61 h)
- **Update frequency / cycles:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 1 hour

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** Upper-air **3D-Var with 3-hourly cycling** (Brousseau et al. 2011), combined with **large-scale mixing**, in which the largest spatial scales of the background field are blended with ECMWF HRES forecasts. Surface variables (2 m temperature, 2 m humidity, snow depth) are assimilated using **optimal interpolation** (Giard and Bazile, 2000).
- **Observations assimilated:** Conventional in-situ observations, scatterometer ocean-surface winds, satellite radiances, atmospheric motion vectors (AMVs), aircraft (including Mode-S EHS), and GPS-RO. Note: relative to MEPS, AROME-Arctic does **not** assimilate radar reflectivity and does **not** ingest MWHS-2 from FengYun-3C.

---

## Initial and boundary conditions
- **Initial conditions:** AROME-Arctic 3D-Var analysis (with ECMWF HRES large-scale blending in the background)
- **Lateral boundary conditions:** ECMWF HRES (IFS) at 1-hour interval
- **Sea surface temperature:** ECMWF
- **Sea ice concentration:** ECMWF
- **Sea-ice surface temperature:** Modelled by the **SICE** 1D thermodynamic sea-ice scheme (Batrak et al. 2018) embedded in SURFEX

---

## What it provides
Deterministic high-resolution Arctic forecasts of:
- temperature (2 m, model levels, pressure levels)
- wind (10 m, model levels, pressure levels)
- precipitation (including precipitation type: drizzle, rain, sleet, snow, freezing drizzle, freezing rain, graupel, hail)
- mean sea level and surface pressure
- humidity
- cloud cover and hydrometeor fields
- surface fluxes and radiation
- sea-ice surface state (via SICE)
- vertical profiles and cross-sections (separate `vc` files)

The model is optimized for polar boundary-layer processes, sea-ice / cold-surface interactions, strong winds, and polar low development.

---

## Data availability
- **Is the data free?** Yes
- **License:** Norwegian Licence for Open Government Data (NLOD) and Creative Commons Attribution 4.0 International (CC BY 4.0). Attribution required: "MET Norway" or equivalent (e.g. "Data from The Norwegian Meteorological Institute" / "Based on data from MET Norway").
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF and NCML (NCML files are aggregations pointing to underlying NetCDF files; accessible via OPeNDAP)
- **Official download location:**
  - AROME-Arctic latest catalogue: https://thredds.met.no/thredds/catalog/aromearcticlatest/latest/catalog.html
  - AROME-Arctic archive: https://thredds.met.no/thredds/catalog/aromearcticarchive/catalog.html

---

## Notes
- **File structure mirrors MEPS:** Since the March 2022 production change, AROME-Arctic file names and structure follow the same convention as [MEPS](./meps.md): `<model>_<form>_<resolution>_<DTG>.<type>`, with `det` for the deterministic control output and `lagged_Xh_*` for ensemble output. Most files in the latest catalogue are NCML-only (accessed via OPeNDAP); some (`*_latest.nc`, `*_vc_*`) are also available as NetCDF.
- **AROME-Arctic EPS (companion ensemble):** AROME-Arctic also runs as an ensemble system distributed alongside the deterministic control. Since the September 2024 update, it produces 6 members per 6 hours (verified: the `lagged_6_h` products carry `ensemble_member = 6`). This entry covers the deterministic configuration; if the AROME-Arctic EPS is documented separately in the future, it would belong under `ensemble_models/regional/norway/`.
- **Polar-low tracking product:** The latest catalogue also carries a gridded polar-low (PL) tracking product derived from the 6-member lagged EPS — `arome_arctic_lagged_6_h_tracking_2_5km_*`, offered as direct NetCDF (`_latest.nc` plus per-cycle files at 00/06/12/18 UTC). It holds two fields on the full 739 × 949 grid, `strike_probability` (long name "PL strike_probability map") and `number_of_tracks` ("Individual PL tracks"), hourly out to +42 h, with no ensemble-member dimension (a probability/count map rather than raw model output). This fits AROME-Arctic's polar-low focus and is distinct from the deterministic and EPS field products.
- **Relationship to MEPS:** AROME-Arctic is closely related to [MEPS](./meps.md) (both are HARMONIE-AROME at 2.5 km), but the two systems are configured differently. AROME-Arctic generally tracks MEPS upgrades after a delay. Documented configuration differences include: no radar reflectivity assimilation in AROME-Arctic; the statistical cloud scheme (`STATNW`) is disabled in AROME-Arctic (it produced higher negative bias in low cloud cover over Svalbard in 500 m experiments); a different surface-layer Richardson number cutoff (`xrimax = 0` in AA vs 0.4 in MEPS); Mode-S EHS settings differ; and AROME-Arctic does not assimilate FengYun-3C MWHS-2.
- **Use in CARRA:** AROME-Arctic provides the operational basis for the Copernicus Arctic Regional Reanalysis (CARRA1 and the upcoming CARRA2), which extends a HARMONIE-AROME-based Arctic reanalysis back to 1985 and is delivered through the Copernicus Climate Data Store.
- **Use in other MET Norway systems:** AROME-Arctic provides atmospheric forcing for [Barents-2.5](https://ocean.met.no/models), MET Norway's coupled ocean and sea-ice ensemble for the Barents Sea and around Svalbard.
- **Relationship to siblings:** [MEPS](./meps.md) (Nordic ensemble counterpart, same model core); [AROME France](../france/arome-france.md), [HARMONIE-AROME Ireland](../ireland/harmonie-arome-ireland.md), and other ACCORD-consortium HARMONIE-AROME / AROME deployments share the same core model with regionally tailored configurations.

---

## Recent version history

### 2025-09-23 — Upgrade to HARMONIE cycle 46 (cy46)
Major surface-physics upgrade: introduction of a multi-layer soil and snow scheme, and explicit representation of vegetation. This is a significant departure from the previous ISBA configuration and is expected to improve representation of land-surface conditions (snow depth and density, soil temperatures, surface energy balance) over Svalbard and other Arctic land areas. See MET Norway's [status note](https://status.met.no/incidents/t53jcy8ld6n4).

### 2024-12-10 — New archive regime
AROME-Arctic adopted the same archive regime introduced for MEPS in October 2023 (one file per member, level type, and time step, with NCML aggregations). See [Updates to MEPS and AROME-Arctic archive and cleaning 2024](https://github.com/metno/NWPdocs/wiki/Updates-to-MEPS-and-AROME%E2%80%90Arctic-archive-and-cleaning-2024).

### 2024-09-24 — AROME-Arctic EPS update
The companion ensemble system increased to 6 members per 6 hours, with a corresponding file-name change.

### 2022-03-15 — Production change
File structure aligned more closely with the MEPS production model (control + lagged ensemble file conventions). See MET Norway's [status note](https://status.met.no/incidents/9c3b4lkqy77r).

### 2021-05-05 — Upgrade to HARMONIE cycle 43 (cy43)
Brought AROME-Arctic in line with the cy43 upgrade to MEPS (March 2021). Several SURFEX parameters added; `atmosphere_convective_inhibition_old` and `specific_convective_available_potential_energy_old` removed in favour of the non-`_old` variants; CIN and CAPE moved between file groups.

### June 2017 — Operational start
AROME-Arctic became operational based on HARMONIE cycle 40h1.1.

---

## Official documentation
- AROME-Arctic dataset wiki: https://github.com/metno/NWPdocs/wiki/AROME-Arctic-dataset
- AROME-Arctic project page: https://www.met.no/en/projects/The-weather-model-AROME-Arctic
- AROME-Arctic technical description: https://www.met.no/en/projects/The-weather-model-AROME-Arctic/about
- Licensing and crediting: https://www.met.no/en/free-meteorological-data/Licensing-and-crediting
- Müller et al. (2017), *Characteristics of a Convective-Scale Weather Forecasting System for the European Arctic*, Mon. Wea. Rev., 145, 4771–4787. https://doi.org/10.1175/MWR-D-17-0194.1
- Køltzow et al. (2019), *An NWP Model Intercomparison of Surface Weather Parameters in the European Arctic during the Year of Polar Prediction Special Observing Period Northern Hemisphere 1*, Wea. Forecasting. https://doi.org/10.1175/WAF-D-19-0003.1
- Bengtsson et al. (2017), *The HARMONIE–AROME Model Configuration in the ALADIN–HIRLAM NWP System*, Mon. Wea. Rev., 145, 1919–1935. https://doi.org/10.1175/MWR-D-16-0417.1
- Batrak et al. (2018), *Implementation of a simple thermodynamic sea ice scheme, SICE version 1.0-38h1, within the ALADIN–HIRLAM NWP system version 38h1*, GMD, 11, 3347–3368. https://doi.org/10.5194/gmd-11-3347-2018
