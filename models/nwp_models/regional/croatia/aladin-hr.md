# ALADIN-HR (Croatian Meteorological and Hydrological Service)

## What this model is
ALADIN-HR is the **regional limited-area deterministic numerical weather prediction (NWP) system** operated by the Croatian Meteorological and Hydrological Service (DHMZ).

It is part of the **ALADIN limited-area model system** (developed within the ALADIN consortium and the **RC LACE** regional cooperation, now under the **ACCORD** umbrella) and is used operationally for short-range weather forecasting over Croatia and the Adriatic region. The system is the basis for DHMZ's national warning service, civil-protection and wildfire-prevention products, and a range of specialized partner products (aviation, marine, road and rail traffic, energy, agriculture, water management, nautical tourism).

---

## Who runs it
- **Organization:** Croatian Meteorological and Hydrological Service (DHMZ)
- **Country / region:** Croatia

---

## What area it covers
- **Coverage:** Croatia and surrounding parts of Central Europe / the Adriatic
- **Domain details:** 480 × 432 horizontal grid points at ~4 km spacing, on a Lambert-projection domain centred over Croatia and covering the wider Adriatic / Central European region (assimilation suite domain, per the 2024 RC LACE DA status report)

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system / core:** ALADIN (ACCORD framework)
- **Code version:** **cy43t2_bf10** (operational on the new HPC since February 2023, per the 2024 RC LACE DA status report)
- **Dynamical formulation:** Hydrostatic (ALADIN spectral limited-area dynamical core)
- **Convection-allowing:** No (4 km grid; deep convection is parameterized)
- **Horizontal resolution:**
  - ~4 km — general meteorological fields (ALADIN-HR4 / "HR40")
  - ~2 km — high-resolution 10 m wind products (terrain-adjusted output; historically produced via high-resolution dynamical adaptation, HRDA)
- **Grid dimensions:** 480 × 432 (4 km assimilation/forecast domain)
- **Vertical levels:** 73
- **Forecast length:** Up to **72 hours** (hourly fields)
- **Update frequency / cycles:** Model run initiated every **6 hours** (00, 06, 12, 18 UTC), per the 2025 DHMZ brochure
- **Temporal output resolution:** Hourly (selected fields)

---

## Data assimilation
- **Data assimilation:** Yes
- **Method / cadence:**
  - **Atmosphere:** **3-hourly 3D-Var** with a **static EDA** B-matrix; **Jk** (large-scale information constraint) and **SCC** (space-consistent coupling) used in the assimilation/initialization
  - **Surface:** **Optimal Interpolation (OI)** via CANARI; CANARI tuning (EXP10 settings + external 9-hour Wp smoothing, "v7.9") was put operational on **22 April 2024** to correct unrealistic annual soil-moisture evolution and forecast jumpiness during summer
- **Assimilation cycle:** 3 h
- **Observation cut-off time:** 1 h 40 min
- **Coupling:** IFS, 1-hourly (lagged in operational cycling)
- **Observations assimilated** (per the 2024 RC LACE DA status report):
  - **Surface:** SYNOP
  - **Aircraft:** AMDAR, **Mode-S MRAR** (CHMI & SI streams)
  - **Atmospheric motion vectors:** GEOWIND
  - **Radiosondes:** TEMP
  - **Satellite radiances:** MSG SEVIRI
  - **Radar:** **OPERA radar reflectivity** — implemented operationally from the end of 2023; data from the **NIMBUS** production line (comparable performance to ODYSSEY); a combined rain-threshold + observation-error-inflation method (offset 0.35, threshold 0.0; "exp2_3") was selected for operational use, and improves 1 h rain rates at Croatian automatic stations with neutral verification scores against the no-radar-DA baseline. Thinning distance in the bator namelist was increased after operational implementation to handle memory issues in screening for spatially widely distributed precipitation patterns.

---

## Initial and boundary conditions
- **Initial conditions:** Local 3D-Var + CANARI analysis on the ALADIN-HR domain
- **Lateral boundary conditions:** **ECMWF IFS**, 1-hourly coupling (lagged in operational cycling)

---

## What it provides
Hourly deterministic forecasts of standard meteorological variables out to +72 h, including:
- 2 m temperature, 2 m relative humidity
- 10 m wind (direction, speed, gusts) — including a terrain-adjusted ~2 km product
- mean sea-level and surface pressure
- precipitation, cloudiness (low/medium/high)
- 3D atmospheric fields from the surface up to ~15 km above ground level

Specialized post-processed products derived from ALADIN-HR include:
- meteograms (general and Adriatic-specific)
- biometeorological indices (e.g. UTCI thermal-stress)
- statistical / machine-learning post-processing (analog method, neighborhood method)
- vertical cross-sections (spatial and temporal)

ALADIN-HR output forms the backbone of DHMZ's short-range forecasting system and is used for:
- National weather forecasting and warnings
- Civil protection, rescue, and wildfire-prevention products
- Aviation, maritime, road and rail traffic safety
- Energy (renewable production estimation, grid management, gas transport)
- Agriculture, water management, biometeorology
- Nautical tourism and sailing
- Outdoor event planning and structural wind-load assessment

---

## Publicly available products

> The two endpoints below are **not advertised on the DHMZ public website**. They were provided directly by DHMZ on request, after the maintainer of this repository contacted them about including DHMZ in a list of free and public weather models. Anyone wanting to use this data is encouraged to contact DHMZ directly (dhmz@dhmz.hr) and ask whether they're happy for it to be used for their intended purpose; licensing terms are not stated on the endpoints themselves. The credentials below are the ones DHMZ provided.

### General meteorological data (NetCDF)
- Covers the **entire territory of Croatia**
- Produced from the **00 UTC model run**
- Includes standard meteorological variables
- **Format:** NetCDF

**Access link:**
https://radar.dhz.hr/~aladinhr/opendata

**Username:** `aladinhr`
**Password:** `opendata_aladinhr2`

---

### Sailing and nautical wind products (GRIB)
- High-resolution wind fields at **10 m height**
- Includes **wind components** and **maximum hourly wind speed**
- Produced from a **2 km horizontal grid spacing**
- Covers three Adriatic domains:
  - **ADR** – entire Adriatic Sea
  - **NA** – Northern Adriatic
  - **SA** – Southern Adriatic
- Produced **twice daily** from the **00 and 12 UTC model runs**
- **Format:** GRIB

**Access link:**
https://radar.dhz.hr/~aladinhr/opendata/sailing

**Username:** `aladinhr`
**Password:** `opendata_aladinhr24`

---

## Data availability
- **Is the data free?** Yes (authentication required; credentials provided by DHMZ on request — see note above)
- **License:** Not stated on the open-data endpoints. Users are advised to contact DHMZ (dhmz@dhmz.hr) for permission and any conditions of re-use.
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF and GRIB (the underlying NWP system also produces PNG visualisations and text-tabular forecasts internally, per the 2025 DHMZ brochure)

---

## Operational infrastructure
- **HPC:** Forecasts are produced on the DHMZ high-performance computing system **Neverin** (373 TFLOPS), per the 2025 DHMZ brochure.
- **Archive:** All forecast products are stored in a continuously maintained tape-based automated archive.
- **Distribution:** Forecasts and specialized products are available to users via a DHMZ FTP system.
- **Monitoring:** 24/7/365 on-duty operator monitoring to ensure uninterrupted service and timely product delivery.

---

## Notes
- The publicly accessible endpoints are a **subset** of the full operational ALADIN-HR system. Additional high-resolution and specialized outputs are produced internally or for partner institutions and are not part of this public distribution.
- Other ALADIN/ALARO Central European deployments include [ALADIN Slovakia](../slovakia/aladin-slovakia.md), [ALADIN Slovenia](../slovenia/aladin-slovenia.md), [ALARO Belgium](../belgium/alaro-belgium.md), and the Hungarian and Czech systems documented under their respective country directories.
- DHMZ is a member of the **RC LACE** regional cooperation and the broader **ACCORD** consortium; ALADIN-HR's data assimilation development (radar, all-sky IASI, future MTG-S1 IRS) is coordinated within these frameworks.

---

## Recent version history

### 22 April 2024 — Operational CANARI tuning
New CANARI surface-analysis settings (EXP10 + external 9-hour Wp smoothing, internally referred to as "v7.9") were put operational, correcting two previously identified problems linked to surface DA: forecast "jumpiness" during summer and unrealistic annual soil-moisture evolution. The change improves T2m and RH2m verification in both winter and summer periods and the representation of fog / low-cloud cases.

### End of 2023 — OPERA radar reflectivity assimilated operationally
Radar reflectivities from OPERA were successfully implemented in the ALADIN-HR operational chain. Data is sourced from the **NIMBUS** production line, which showed comparable performance to the older ODYSSEY line. A combined rain-threshold and observation-error-inflation method (offset 0.35, threshold 0.0) was selected for operational use after tuning to reduce the "drying" effect in the Bayesian inversion of reflectivity data. Verification showed improved 1 h rain rates at Croatian automatic stations and neutral surface / upper-air scores against the no-radar-DA baseline. Thinning distance in the bator namelist was subsequently increased to address memory issues in screening for spatially widely distributed precipitation patterns.

### February 2023 — DA suite operational on new HPC
The ALADIN-HR data-assimilation suite became operational on the new DHMZ HPC system (Neverin) running model cycle **cy43t2_bf10** on a 480 × 432 / 4.0 km / L73 domain, with a 3-hourly 3D-Var + OI cycle, static EDA B-matrix, Jk + SCC, IFS 1-hourly coupling (lagged), 1 h 40 min observation cut-off, and the observation set listed above (SYNOP, AMDAR, Mode-S MRAR CHMI & SI, GEOWIND, TEMP, SEVIRI, OPERA/OIFS reflectivity).

### 2018 (DAWD status) — pre-cy43 ALARO assimilation suite at DHMZ
Earlier ALADIN-HR4 configuration documented at the 2018 LACE DA Working Days used **ALARO-0 / cy38t1** at 4 km / L73 with 3-hourly 3D-Var (NMC B; tests with EDA B), VarBC, REDNMC=1.4, OI surface analysis with MESCAN correlation function, and assimilation of SYNOP (Ps), TEMP (T, q, u, v), AMDAR (T, u, v), AMV, SEVIRI (ch 2, 3) and Mode-S MRAR SI. LBCs from ECMWF (lagged), DFI initialization. Plans at that time included continuing EDA tests, radar DA work, GNSS ZTD tests, Jk testing, and migration to the cy43-series — all of which have since been realized in the current operational suite.

### 2013 (textbook description) — early ALADIN-HR operational suite
The earlier operational chain documented in Tudor et al. (2013) ran ALADIN at 8 km / L37 over a wider Lambert domain, with a 2 km / L15 hydrostatic high-resolution dynamical adaptation (HRDA) for 10 m wind, 72 h forecasts from 00 and 12 UTC ARPEGE-coupled runs, DFI initialization, and a parallel 3D-Var assimilation suite at 4 km / L37 (NMC B) with OI surface analysis. The current 4 km / L73 / cy43t2 / IFS-coupled / radar-DA configuration represents the operational descendant of that system, run on substantially upgraded HPC.

---

## Plans (2025, per the 2024 RC LACE DA status report)
- Include new automatic stations from the **METMONIC** project in the DA system (local upgrade)
- Preparation of all-sky code for assimilation of **IASI** data (in the C-LAEF context)
- Work on assimilation of **IRS data from MTG-S1** (in the C-LAEF context)

---

## Official documentation
- DHMZ brochure: *ALADIN-HR — Operational Numerical Weather Prediction* (2025)
- Panežić, S., Zajec, A., Stanešić, A. (2024): *DA status Croatia*, Regional Cooperation for Limited Area Modeling in Central Europe (RC LACE), DA Working Days 2024
- Kovačić, T., Stanešić, A. (2018): *Data assimilation status at DHMZ*, RC LACE DA Working Days, 19–21 September 2018
- Tudor, M., Ivatek-Šahdan, S., Stanešić, A., Horvath, K., Bajić, A. (2013): *Forecasting Weather in Croatia Using ALADIN Numerical Weather Prediction Model*, in *Climate Change and Regional/Local Responses*, InTech, http://dx.doi.org/10.5772/55698
- DHMZ contact: dhmz@dhmz.hr — https://meteo.hr
