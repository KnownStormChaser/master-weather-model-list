# HARMONIE (DMI – DINI and IG)

## What this model is
HARMONIE is the **convection-permitting regional deterministic numerical weather prediction (NWP) model** that the Danish Meteorological Institute (DMI) operates for short-range forecasting over Northern Europe, the North Atlantic, Iceland, and Greenland.

DMI runs and distributes two configurations of the same HARMONIE-AROME cycle 43h model:
- **DINI** — Denmark, Iceland, Netherlands, Ireland — covers Northwestern Europe and the surrounding seas. The DINI domain is the shared **UWC-West** production: the same model run is also distributed by KNMI, Met Éireann, and the Icelandic Met Office under their respective open-data platforms.
- **IG** — Iceland and Greenland — DMI-specific configuration covering Iceland, Greenland, and the surrounding North Atlantic and Arctic seas.

Both share the same dynamical core, physics, vertical grid, GRIB encoding, and output structure, and differ mainly in domain extent and forecast length. They are presented together here because DMI documents and distributes them as a single product family.

---

## Who runs it
- **Organization:** Danish Meteorological Institute (DMI)
- **Country / region:** Denmark
- **Operating partnership (DINI domain only):** United Weather Centres-West (UWC-West)
  - DMI (Denmark)
  - KNMI (Royal Netherlands Meteorological Institute)
  - Icelandic Met Office (Veðurstofa Íslands)
  - Met Éireann (Ireland)
- **Computing infrastructure:** UWC-West "Aurora" supercomputer at the Icelandic Met Office data centre in Reykjavík (operational since 2024). DMI owns and operates the supercomputer; IMO operates the site infrastructure.

---

## What area it covers

### DINI configuration
- **Coverage:** Denmark, Iceland, Netherlands, Ireland and the surrounding seas; extends from East Greenland to southern Italy at the corners of the integration domain
- **Approximate domain bounds:** 43°W – 40°E, 37°N – 70°N
- **Native projection:** Lambert conformal conic, false origin 55.5°N / −8°E, single standard parallel 55.5°N, on a sphere of radius 6,371,229 m

### IG configuration
- **Coverage:** Iceland, Greenland, and adjacent Arctic and North Atlantic waters
- **Approximate domain bounds:** 109°W – 37°E, 56°N – 86°N
- **Native projection:** Lambert conformal conic, false origin 72°N / −36°E, single standard parallel 72°N, on a sphere of radius 6,371,229 m

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system / core:** HARMONIE-AROME (ALADIN-NH non-hydrostatic spectral dynamical core, AROME physics, SURFEX surface scheme); cycle **43h**
- **Dynamical formulation:** Non-hydrostatic, spectral, with two-time-level semi-implicit semi-Lagrangian discretization
- **Convection-allowing:** Yes (deep convection explicitly resolved at 2 km; shallow convection parameterized)
- **Horizontal resolution:** ~2 km on the native Lambert grid (both DINI and IG)
- **Vertical levels:** 90 model (hybrid) levels (MF90 — Météo France 90-level definition), terrain-following near the surface and pressure-following higher up
- **Numerical precision:** Forecast step run in single precision (UWC-West cy43 configuration)
- **Model top:** 10 hPa (~30 km, standard HARMONIE-AROME configuration)
- **Forecast length:**
  - DINI: 60 hours from every cycle
  - IG: 72 hours from every cycle
- **Update frequency / cycles:** 8× daily (00, 03, 06, 09, 12, 15, 18, 21 UTC). The model itself runs hourly on the supercomputer, but DMI collects and distributes the deterministic output every third hour.
- **Temporal output resolution:** 1 hour
- **Boundary conditions:** ECMWF IFS. The deterministic IG production and the DINI-EPS control take boundaries from IFS-HRES; the DINI-EPS perturbed members are coupled to IFS-ENS (1+30 LBCs — HRES for the control, ENS members 001–030 for the perturbed members).
---

## Data assimilation
- **Method:** 3D-Var with a three-hour assimilation window and a one-hour cut-off, run as part of the UWC-West cy43 production
- **Observations assimilated:** conventional (SYNOP, ship, aircraft incl. AMDAR/E-AMDAR/TAMDAR, Mode-S EHS via EMADDC, buoy, TEMP), atmospheric motion vectors (geostationary and polar), scatterometer (ASCAT Metop-B/-C), microwave sounders (AMSU-A, MHS, MWHS2 on FY-3D/-3E, ATMS on NPP/NOAA-20/-21), infrared sounders (IASI, CrIS), GPS radio occultation (COSMIC-2, KOMPSAT, Metop, PAZ, Spire, TanDEM, TerraSAR), and radar
- **Surface/ensemble perturbations (DINI-EPS):** EDA, PERTSURF (VEG, CV, Z0, ALB, SST, TS) and SPP

> Source: UWC-West / DMI ACCORD national posters (2025–2026) and UWC-West operational NWP posters. The observation set describes the shared UWC-West DINI production; the IG deterministic configuration runs the same 3D-Var scheme.

---

## Configurations at a glance

| Field                  | DINI                                          | IG                                          |
|------------------------|-----------------------------------------------|---------------------------------------------|
| Coverage               | Denmark, Iceland, Netherlands, Ireland        | Iceland, Greenland                          |
| Forecast length        | 60 h                                          | 72 h                                        |
| Domain bounds          | 43°W – 40°E, 37°N – 70°N                      | 109°W – 37°E, 56°N – 86°N                   |
| Lambert false origin   | 55.5°N / −8°E                                 | 72°N / −36°E                                |
| UWC-West shared        | Yes                                           | No (DMI-specific)                           |
| Sibling distributions  | KNMI ([P1](../netherlands/harmonie-arome-netherlands.md) / [P3](../netherlands/harmonie-knmi.md) / [BES](../netherlands/harmonie-caribbean.md)) and others from the same model chain | None — distributed only by DMI              |

All other model parameters (resolution, vertical levels, cycle, physics, output cadence, GRIB encoding, parameter list) are identical between DINI and IG.

---

## What it provides
Output is distributed in three level-type files for each domain:

- **Surface files (sf)** — surface-scheme outputs and atmosphere-integrated fields, including 2 m temperature and dewpoint, 10 m wind components, wind gusts, total precipitation, snow depth (water equivalent), surface and mean sea level pressure, cloud cover (low/medium/high/total), cloud base and top, mixed-layer depth, CAPE and CIN, visibility, total column water vapour and cloud water/ice, radiation fluxes (short-wave, long-wave, net, downward, global), latent and sensible heat fluxes, momentum fluxes, land-sea mask, and a set of fields at 50 / 100 / 150 / 250 m above ground.
- **Model-level files (ml)** — 3D fields on the 90 hybrid levels: temperature, specific humidity, cloud fraction and condensate, U and V wind components, and others.
- **Pressure-level files (pl)** — selected fields interpolated to 16 standard pressure levels (1000, 950, 925, 900, 850, 800, 700, 600, 500, 400, 300, 250, 200, 150, 100, 50 hPa): temperature, U/V wind, geometric vertical velocity, geopotential, relative humidity, and pseudo-adiabatic potential temperature (the IG pressure-level set is somewhat smaller than DINI's).

HARMONIE-DMI output also provides atmospheric forcing for DMI's downstream marine modelling chain — the **DKSS** storm-surge model and the **WAM** wave model.

---

## Coordinate system and wind components
Both configurations use a Lambert conformal conic projection (different parameters per domain — see "What area it covers" above). The U and V wind components in all output files are defined **relative to the Lambert grid frame**, not true geographic east/north. Software computing wind direction in a different coordinate reference system needs to apply a rotation; DMI's Forecast EDR API does this rotation automatically when `crs=crs84` is requested.

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2 (`gridType: lambert`)
- **Official open-data access:**
  - Registry of Open Data on AWS: https://registry.opendata.aws/dmi-opendata/
  - S3 bucket: `s3://dmi-opendata/` (region `eu-north-1`, anonymous access via `aws s3 ls --no-sign-request s3://dmi-opendata/`)
  - DMI Forecast EDR API: https://www.dmi.dk/friedata/dokumentation/forecast-data-edr-api (collections include `harmonie_dini_sf`, `harmonie_dini_ml`, `harmonie_dini_pl`, `harmonie_ig_sf`, `harmonie_ig_ml`, `harmonie_ig_pl`)
  - DMI Forecast STAC API: https://www.dmi.dk/friedata/dokumentation/forecast-data-stac-api

---

## Forecast availability timing
DMI publishes typical delays between the analysis time of a model run and when the output is available in the open-data APIs:

| Product                                | First file available | Complete model available |
|----------------------------------------|----------------------|--------------------------|
| `harmonie_dini` surface (sf)           | +1h35m               | +2h45m                   |
| `harmonie_dini` model levels (ml)      | +1h35m               | +3h10m                   |
| `harmonie_dini` pressure levels (pl)   | +1h35m               | +2h30m                   |
| `harmonie_ig` surface (sf) and pressure (pl) | +1h25m         | +2h45m                   |
| `harmonie_ig` model levels (ml)        | +1h25m               | +3h15m                   |

Output appears one time-step at a time in chronological order, so users needing only short-range forecast steps can start consuming files well before the run is complete. Surface (sf) files are smaller and arrive first; model-level (ml) files are larger and arrive last.

---

## Notes
- **DINI is operationally a continuous ensemble (DINI-EPS), not a pure deterministic run.** The UWC-West DINI production is a 1+5 continuous EPS updated hourly; DMI's open-data deterministic DINI product is the control member, collected and distributed every third hour. DMI **also distributes DINI-EPS derived products** through the Forecast EDR API — `harmonie_dini_eps_means`, `harmonie_dini_eps_percentiles`, and `harmonie_dini_eps_probabilities`. These ensemble products are out of scope for this deterministic entry and are a candidate for a separate `ensemble_models/regional/denmark/` DINI-EPS entry.
- **Operational vs. public availability — additional DMI domains.** DMI's ACCORD national posters (2025, 2026) show DMI operationally runs several Greenland configurations beyond DINI and IG: three continuously-cycled 750 m domains (TAS/Tasiilaq, SGL/South Greenland, NUUK) and three storm-triggered on-demand domains (DB/Diskobugt, SC/Scoresbysund, QAAN/Qaanaaq). **Only `harmonie_dini_*` and `harmonie_ig_*` appear in the DMI open-data feed**, so these sub-domains are documented here for context but remain out of catalog scope unless a confirmed open feed surfaces.
- **DINI is the same model run KNMI distributes as `harmonie_arome_cy43_p3`.** KNMI's [P3 European dataset](../netherlands/harmonie-knmi.md), the [P1 Dutch dataset](../netherlands/harmonie-arome-netherlands.md), and the DMI DINI distribution are all packaged independently from the same UWC-West cy43 model run on the Aurora supercomputer in Iceland. They differ in distribution grid, retained variable set, parameter encoding, and rotation conventions — but the underlying model state is identical. The Caribbean [BES domain](../netherlands/harmonie-caribbean.md) is a separate KNMI-only configuration sharing the cy43 codebase but not the DINI integration.
- **Sibling UWC-West productions:** Met Éireann distributes its own slice of the same DINI run as part of its [HARMONIE-AROME Ireland](../ireland/harmonie-arome-ireland.md) operational chain (along with the Irish IREPS regional ensemble), and the Icelandic Met Office distributes the run via its own services. These are not duplicate forecasts; they are repackagings of the same UWC-West production.
- **DINI vs MEPS:** Despite being run by a Nordic operator, **DMI's HARMONIE production is part of UWC-West, not MetCoOp.** [MEPS](../norway/meps.md) (MetCoOp: MET Norway, SMHI, FMI, ESTEA, LEGMC) is a separate Nordic HARMONIE-AROME ensemble production that does not include DMI.
- **GRIB edition:** All HARMONIE-DMI output is GRIB2 (`edition = 2`). Parameters are identified by GRIB2 discipline / category / number and `typeOfStatisticalProcessing` codes; full parameter tables for each level type are available in the official documentation.
- **DMI documentation also states that HARMONIE feeds the DKSS storm-surge and WAM wave forecast models.** If those models are documented as separate entries, they should cross-reference HARMONIE-DMI as the meteorological driver.
- **Companion ensemble:** DMI has historically operated **COMEPS** (Continuous Mesoscale EPS), a HARMONIE-AROME-based ensemble system focused on Denmark. If documented separately, COMEPS belongs under `ensemble_models/regional/denmark/`. This entry covers only the deterministic DINI and IG productions.

---

## Recent version history

### Upcoming — Cycle 46 (operational October 2026)
The UWC-West cy43 production is scheduled to be replaced by **HARMONIE-AROME cycle 46h1.1.1** in **October 2026** (configuration approved summer 2025; real-time d-suites running since September 2025). The upgrade keeps the 2 km / 90-level configuration but changes the output encoding — moving from FA to **FullPOS (WMO) GRIB2** — and introduces new physiography and lakes, scale-aware shallow convection, a windfarm parametrization, ECUME6 sea-surface fluxes, in-forecast SST/SIC updates, and assimilation of screen-level T2/RH2 and low-peaking microwave radiances. *Not yet operational as of mid-2026; this entry documents the live cy43 production.*

### 2024 — UWC-West operational go-live (19 March 2024)
UWC-West operational NWP went live on **19 March 2024** on the Aurora HPE supercomputer at the Icelandic Met Office. Pre-operational milestones: the IG deterministic suite ran from September 2023 and DINI-EPS from December 2023. (Distinct from the 20 June 2024 changeover at which the cy43 Aurora run replaced KNMI's legacy Cy40 P3 production — see below.)

### Earlier history (pre-2024)
The lineage of the IG domain traces back to the **IGA** (Iceland-Greenland-Atlantic) configuration documented in DMI's 2016/2017 operational upgrade, when DMI and IMO jointly developed a high-resolution HARMONIE-AROME suite for Iceland and South Greenland, taking advantage of the Reykjavík HPC site. The DINI domain similarly evolved out of DMI's earlier **NEA** (North Europe / Atlantic) configuration. Both domains have since been brought to cycle 43h and are now produced on the UWC-West Aurora system.

---

## Official documentation
- DMI: *Forecast Data Weather Model (HARMONIE) for DINI and IG*
  https://www.dmi.dk/friedata/dokumentation/data/forecast-data-weather-model-harmonie-for-dini-and-ig
- DMI: *Forecast Data Availability*
  https://www.dmi.dk/friedata/dokumentation/data/forecast-data-availability
- DMI: *Free meteorological data — overall documentation*
  https://www.dmi.dk/friedata
- AWS Open Data Registry: *Danish Meteorological Institute (DMI) Open Data Forecasts*
  https://registry.opendata.aws/dmi-opendata/
- DMI Open Data processing guide (Python notebooks):
  https://github.com/dmidk/dmi-opendata-forecastdata-guide
- Bengtsson et al. (2017), *The HARMONIE–AROME Model Configuration in the ALADIN–HIRLAM NWP System*, Mon. Wea. Rev., 145, 1919–1935. https://doi.org/10.1175/MWR-D-16-0417.1
