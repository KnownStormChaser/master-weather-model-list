# AROME Austria

## What this model is
AROME Austria (operationally referred to as **AROME-Aut**) is the operational convection-permitting regional deterministic numerical weather prediction model run by GeoSphere Austria over Austria and the surrounding Alpine region.

It is designed for short-range forecasting of small-scale and rapidly evolving weather such as thunderstorms, heavy precipitation, fog, foehn and other local wind effects, with particular strength in mountainous terrain. AROME-Aut is the deterministic member of GeoSphere Austria's broader AROME-based suite, which also includes the regional ensemble system **C-LAEF** (Convection-permitting Limited-Area Ensemble Forecasting) and the higher-resolution rapid-update system **AROME-RUC**.

---

## Who runs it
- **Organization:** GeoSphere Austria (formerly ZAMG — Zentralanstalt für Meteorologie und Geodynamik; merged into GeoSphere Austria in 2023)
- **Country / region:** Austria

---

## What area it covers
- **Coverage:** Austria and surrounding Alpine regions
- **Domain details:** Native model grid is 600 × 432 points centred on the Alpine region (same domain as the operational C-LAEF ensemble). The **public dataset** is regridded to a regular WGS84 / EPSG:4326 lat-lon grid of **594 × 492 points** (0.028° lon × 0.018° lat), bounding box ≈ **42.98–51.82 °N, 5.50–22.10 °E** (confirmed from the distributed NetCDF files)

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system / core:** AROME (ALADIN-NH non-hydrostatic spectral dynamical core), code cycle **cy43t2bf11**
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved at 2.5 km; shallow convection parameterized)
- **Horizontal resolution:** ~2.5 km
- **Grid dimensions:** 600 × 432 grid points
- **Vertical levels:** 90 native model levels (lowest ~5 m above ground; model top ~35 km). The public dataset delivers 3D fields interpolated to **23 isobaric levels** (1000–100 hPa)
- **Forecast length:** 60 hours
- **Update frequency:** 8× daily (00, 03, 06, 09, 12, 15, 18, 21 UTC)
- **Time step:** 60 s
- **Temporal output resolution:** 1 hour (both 2D and 3D fields)
- **Surface scheme:** SURFEX 8.0
- **Orography / physiography:** GMTED2010 / ECOCLIMAP 1

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:** **3D-Var** for upper-air, **optimal interpolation (OI)** for surface; cycled every 3 hours with a ±90 minute assimilation window
- **Background-error covariance:** C-LAEF EDA climatological B-matrix (shared with the C-LAEF ensemble)
- **Observations assimilated:** Conventional surface (SYNOP, SHIP, TAWES), aircraft (Mode-S, AMDAR), radiosonde (BUFR TEMP), atmospheric motion vectors (SEVIRI-HR / GeoWind), ceilometer cloud cover (converted to RH; introduced operationally in the September 2023 C-LAEF upgrade), and ASCAT scatterometer winds. The assimilation system is shared in setup with the C-LAEF control run. *(Note: satellite radiances, weather radar, GNSS-ZTD, GNSS-RO and wind-profiler data are assimilated by the higher-resolution AROME-RUC system, not by AROME-Aut/C-LAEF.)*

---

## Initial and boundary conditions
- **Initial conditions:** 3D-Var (upper-air) + OI (surface)
- **Boundary conditions:** ECMWF IFS HRES, updated hourly

---

## What it provides
Deterministic hourly forecasts to +60 h. The publicly distributed NetCDF (`nwp-v1-1h-2500m`) contains 35 fields.

**Surface / single-level (2D):**
- 2 m temperature (with min/max over the previous forecast hour) and 2 m relative humidity
- 10 m wind (eastward/northward components) and 10 m gusts (u/v components)
- Surface pressure *(surface pressure only — no MSLP field in the public data)*
- Total precipitation, rainfall amount, snowfall amount, surface snow amount, and snow-level altitude
- Cloud cover (total, low, mid, high)
- CAPE, CIN, and Showalter index
- Surface radiation: global (downwelling SW), net shortwave, net longwave, and downwelling longwave
- Sunshine duration
- Surface temperature, surface geopotential (orography), and a coded weather symbol

**Upper-air (3D, on 23 isobaric levels, 1000 → 100 hPa):**
- Wind (U, V), temperature, geopotential, relative humidity, and vertical velocity (ω)

*(The public dataset contains no simulated radar reflectivity, cloud-microphysics, or height-above-ground-level fields.)*

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF
- **Official download location (direct file listing):**
  https://public.hub.geosphere.at/public/datahub.html?id=nwp-v1-1h-2500m/filelisting
- **API access:** GeoSphere Austria's Dataset API provides programmatic access to the same data with subsetting (by area, time, parameter):
  https://dataset.api.hub.geosphere.at/v1/docs/
- **Dataset landing page:**
  https://data.hub.geosphere.at/dataset/nwp-v1-1h-2500m

GeoSphere Austria publishes the dataset as `nwp-v1-1h-2500m` (1-hour output, 2500 m resolution). Both the direct download portal and the API serve identical data; users can choose whichever access pattern suits their workflow.

---

## Notes
- **Current framing (2026):** As of the 6th ACCORD ASW (April 2026), GeoSphere describes its operational AROME suite as two configurations — C-LAEF (the 2.5 km / L90 16+1 ensemble on ECMWF ATOS, with its control run mirrored on GeoSphere's local HPC) and the 1.2 km AROME-RUC nowcasting system. The deterministic "AROME-Aut" run is this mirrored C-LAEF control; the standalone "AROME-Aut" label is no longer used separately in the most recent documentation, though the configuration is unchanged (cy43t2bf11+).
- AROME-Aut and the **C-LAEF control run** are configured identically but run on different HPC platforms, so the C-LAEF control can act as an operational backup for AROME-Aut. C-LAEF is the 16+1-member regional ensemble companion (same 2.5 km / L90 setup), running on the ECMWF ATOS HPC system. **AROME-RUC** is a separate higher-resolution (1.2 km) rapid-update configuration covering Austria with hourly cycling.
- Hardware: AROME-Aut and AROME-RUC run on GeoSphere Austria's **HPE CRAY-XD2000** cluster (100 nodes, AMD Genoa, Slingshot 11 interconnect, Lustre 200 TB), operational for all NWP systems since **15 January 2025**. The previous HPE Apollo 8600 system was retired at that point. The C-LAEF control run continues to operate on the ECMWF ATOS XH2000.
- Organizational note: GeoSphere Austria was formed by the 2023 merger of ZAMG with the Geological Survey of Austria; older publications and documentation refer to ZAMG.
- A high-resolution Austrian regional reanalysis ensemble (**ARA**), built on the C-LAEF system at 2.5 km / L90 with 10+1 members and 3-hourly cycling, has been developed at GeoSphere Austria targeting the period 2013–2023, with ERA5 providing coupling fields. ARA is a research/reanalysis product distinct from this operational forecast entry.
- **Relationship to the wider AROME family:** AROME-Aut shares the same dynamical core and physics packages as the broader AROME / HARMONIE-AROME family operated by ACCORD-consortium services. See [AROME France](../france/arome-france.md), [AROME-Arctic](../norway/arome-arctic.md), [HARMONIE-AROME Ireland](../ireland/harmonie-arome-ireland.md), and [MEPS](../norway/meps.md) for closely related deployments.

---

## Recent version history

### Forward look — C-LAEF AlpeAdria (1 km, successor to "C-LAEF 1k")
The "C-LAEF 1k" development has matured into **C-LAEF AlpeAdria**, a 1 km ensemble developed jointly with ARSO (Slovenia) and DHMZ (Croatia), expected to replace operational C-LAEF — and thus the configuration mirrored as the AROME-Aut deterministic control — during summer 2026. It runs on ECMWF ATOS in lagged mode with quadratic spectral truncation, uses newer cycles (cy46t1+ / cy48t3bf3 for the EnVar member), and includes a 3D-EnVar member supporting direct reflectivity assimilation (extending the control variable to hydrometeors rather than the 1D+3D-Var Bayesian approach).

### 2025-01-15 — Migration to new HPC
All GeoSphere Austria operational NWP systems (AROME-Aut, AROME-RUC, and other on-site components) migrated to the new **HPE CRAY-XD2000** cluster. AROME-Aut continues to run with the cy43t2bf11 setup; the migration was a hardware change rather than a model upgrade.

### 2023 (September) — Shared assimilation upgrades with C-LAEF
The September 2023 C-LAEF upgrade introduced changes that AROME-Aut shares through its common assimilation framework, most notably the assimilation of **ceilometer-derived cloud cover** (converted to relative humidity), which improves low-stratus forecasts in autumn and winter. The same upgrade replaced the hybrid model-physics perturbation scheme in C-LAEF with a pure stochastic parameter perturbation (SPP) scheme — this change applies to the ensemble and not to the deterministic AROME-Aut.

### December 2021 — Major upgrade to cycle cy43t2
A major upgrade migrated AROME-Aut (together with C-LAEF and AROME-RUC) from cy40t1 to cy43t2. Beyond the cycle change, the AROME-Aut deterministic configuration received:
- Adapted screening-level diagnostics (for the `LCANOPY=.T.` case) to improve 2 m temperature and relative-humidity performance in the Alpine region
- A modified 3D-Var setup (new B-matrix, REDNMC tuning)
- A switch of orography input from GTOPO30 to GMTED2010
- Additional forecast parameters: precipitation type, updraft helicity, and a weather-symbol code

Prior to this upgrade, AROME-Aut ran cy40t1 with CANARI/OI + 3D-Var assimilation on the HPE Apollo 8600. (The GTOPO30 → GMTED2010 switch here is the origin of the orography setting recorded above.)

---

## Official documentation
- GeoSphere Austria data hub (NWP 2.5 km dataset): https://data.hub.geosphere.at/dataset/nwp-v1-1h-2500m
- Direct file listing: https://public.hub.geosphere.at/public/datahub.html?id=nwp-v1-1h-2500m/filelisting
- Dataset API documentation: https://dataset.api.hub.geosphere.at/v1/docs/
- GeoSphere Austria homepage: https://www.geosphere.at

### Key references
- Meier, F., Schneider, S., Awan, N., Deacu, D., Goger, B., Neduncheran, A., Wastl, C., Weidle, F. (2026). *NWP related activities in Austria.* 6th ACCORD All Staff Workshop, Marrakech, Morocco, 13–17 April 2026.
- Wittmann, C., Awan, N., Neduncheran, A., Meier, F., Scheffknecht, P., Wastl, C., Weidle, F. (2023). *NWP related activities in Austria.* 45th EWGLAM & 30th SRNWP Meeting, Reykjavík, Iceland, 25–28 October 2023.
- Wittmann, C., Meier, F., Schneider, S., Weidle, F., Wastl, C., Awan, N., Scheffknecht, P. (2022). *NWP related activities in AUSTRIA.* 2nd ACCORD All Staff Workshop, Ljubljana, 4–8 April 2022.
- Wittmann, C., Meier, F., Schneider, S., Weidle, F., Wastl, C., Awan, N., Scheffknecht, P. (2021). *NWP related activities in AUSTRIA.* 1st ACCORD All Staff Workshop, online, 12–16 April 2021.
- Weidle, F., Awan, N., Neduncheran, A., Meier, F., Scheffknecht, P., Wastl, C., Wittmann, C. (2024). *NWP related activities in AUSTRIA.* 4th ACCORD All Staff Workshop, Norrköping, Sweden, 15–19 April 2024.
- Weidle, F., Awan, N., Deacu, D., Neduncheran, A., Meier, F., Schneider, S., Wastl, C., Wittmann, C. (2025). *NWP related activities in AUSTRIA.* 5th ACCORD All Staff Workshop, Zalakaros, Hungary, 31 March – 4 April 2025.
- Montmerle, T., Michel, Y., Arbogast, E., Ménétrier, B., Brousseau, P. (2018). *A 3D ensemble variational data assimilation scheme for the limited-area AROME model: Formulation and preliminary results.* Quarterly Journal of the Royal Meteorological Society, 144(716): 2196–2215. https://doi.org/10.1002/qj.3334
