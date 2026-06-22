# ALADIN-HR (Croatian Meteorological and Hydrological Service)

## What this model is
ALADIN-HR is the **regional limited-area deterministic numerical weather prediction (NWP) system** operated by the Croatian Meteorological and Hydrological Service (DHMZ).

Since 6 February 2023 it has run operationally as a **two-member deterministic suite**: **ALADIN-HR40** (4 km, hydrostatic, data-assimilating) and **ALADIN-HR20** (2 km, non-hydrostatic), the latter replacing the earlier 2 km **ALADIN-HRDA** wind-only dynamical-adaptation configuration. It is part of the **ALADIN limited-area model system** (developed within the ALADIN consortium and the **RC LACE** regional cooperation, now under the **ACCORD** umbrella) and is used operationally for short-range weather forecasting over Croatia and the Adriatic region. The system is the basis for DHMZ's national warning service, civil-protection and wildfire-prevention products, and a range of specialized partner products (aviation, marine, road and rail traffic, energy, agriculture, water management, nautical tourism).

---

## Who runs it
- **Organization:** Croatian Meteorological and Hydrological Service (DHMZ)
- **Country / region:** Croatia

---

## What area it covers
- **Coverage:** Croatia and surrounding parts of Central Europe / the Adriatic
- **Domain details:**
  - **ALADIN-HR40:** 480 × 432 horizontal grid points at ~4 km spacing, on a Lambert-projection domain centred over Croatia and covering the wider Adriatic / Central European region (assimilation/forecast domain, per the 2024 RC LACE DA status report)
  - **ALADIN-HR20:** 450 × 450 horizontal grid points at ~2 km spacing, nested within the HR40 domain

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area) — a two-member operational suite
- **Model system / core:** ALADIN (ACCORD framework); **ALARO-1** physics package
- **Code version:** **cy43t2_bf10** (operational on the new HPC since February 2023, per the 2024 RC LACE DA status report; the DHMZ national posters give the cycle as "CY43T2")
- **Convection-allowing:** No — deep convection is parameterized via the ALARO physics at both 4 km and 2 km. The 2 km HR20 member sits in the convective grey zone but is not convection-permitting in the AROME sense.
- **Forecast length:** Up to **72 hours** (hourly fields) — both members
- **Update frequency / cycles:** Every **6 hours** (00, 06, 12, 18 UTC), 4 runs/day — both members
- **Temporal output resolution:** Hourly (selected fields)

The suite comprises two nested deterministic members (operational in their current cy43t2 form since **6 February 2023**):

**ALADIN-HR40 (4 km — general fields; the data-assimilating member):**
- Horizontal resolution: ~4 km
- Grid dimensions: 480 × 432
- Vertical levels: **73**
- Dynamical formulation: **Hydrostatic** (ALADIN spectral limited-area core)
- Time step: **150 s**
- Initialization: own 3-hourly 3D-Var + CANARI analysis (see *Data assimilation*)

**ALADIN-HR20 (2 km — high-resolution member; downscales HR40):**
- Horizontal resolution: ~2 km
- Grid dimensions: 450 × 450
- Vertical levels: **87**
- Dynamical formulation: **Non-hydrostatic**
- Time step: **60 s**
- Initialization: **DFI** from the ALADIN-HR40 state — **no independent data assimilation**
- Replaced the earlier **ALADIN-HRDA** 2 km dynamical-adaptation (wind-only) configuration on 6 February 2023

---

## Data assimilation

> Data assimilation is performed in the **ALADIN-HR40** (4 km) member only. **ALADIN-HR20** is initialized by DFI from the HR40 state and carries no independent assimilation cycle.

- **Data assimilation:** Yes (HR40 member)
- **Method / cadence:**
  - **Atmosphere:** **3-hourly 3D-Var** with a **static EDA** B-matrix; **Jk** (large-scale information constraint) and **SCC** (space-consistent coupling) used in the assimilation/initialization
  - **Surface:** **Optimal Interpolation (OI)** via CANARI; CANARI tuning (EXP10 settings + external 9-hour Wp smoothing, "v7.9") was put operational on **22 April 2024** to correct unrealistic annual soil-moisture evolution and forecast jumpiness during summer
- **Assimilation cycle:** 3 h
- **Observation cut-off time:** 1 h 40 min
- **Coupling:** IFS, 1-hourly (lagged in operational cycling) — *see source-conflict note under Initial and boundary conditions*
- **Observations assimilated** (per the 2024 RC LACE DA status report):
  - **Surface:** SYNOP (with 250 additional automatic stations integrated via the METMONIC OBSOUL upgrade — see version history)
  - **Aircraft:** AMDAR, **Mode-S MRAR** (CHMI & SI streams)
  - **Atmospheric motion vectors:** GEOWIND
  - **Radiosondes:** TEMP
  - **Satellite radiances:** MSG SEVIRI
  - **Radar:** **OPERA radar reflectivity** — implemented operationally from the end of 2023; data from the **NIMBUS** production line (comparable performance to ODYSSEY); a combined rain-threshold + observation-error-inflation method (offset 0.35, threshold 0.0; "exp2_3") was selected for operational use, and improves 1 h rain rates at Croatian automatic stations with neutral verification scores against the no-radar-DA baseline.

---

## Initial and boundary conditions

**ALADIN-HR40 (4 km):**
- **Initial conditions:** Local 3D-Var + CANARI analysis on the HR40 domain
- **Lateral boundary conditions:** **ECMWF IFS** (6-hourly lagged in operational cycling)

**ALADIN-HR20 (2 km):**
- **Initial conditions:** **DFI initialization from the ALADIN-HR40 state** (no independent analysis)
- **Lateral boundary conditions:** **ECMWF IFS**, 1-hourly (6-hourly lagged), per the national posters

> **Source conflict — LBC coupling frequency:** The 2024 RC LACE DA status report describes the operational coupling as **IFS 1-hourly** (lagged), which is what the *Data assimilation* section above reflects. The DHMZ national ASW/EWGLAM posters (2023–2026) instead give **"IFS-3h (6-h lagged)" for HR40** and **"IFS 1-h (6-h lagged)" for HR20**. Left unresolved pending confirmation from DHMZ — the difference may simply reflect the 4 km vs 2 km member rather than a genuine discrepancy.

---

## What it provides
Hourly deterministic forecasts of standard meteorological variables out to +72 h, including:
- 2 m temperature, 2 m relative humidity
- 10 m wind (direction, speed, gusts) — including a high-resolution ~2 km product from the non-hydrostatic **ALADIN-HR20** member (which replaced the earlier HRDA wind-only dynamical adaptation in February 2023)
- mean sea-level and surface pressure
- precipitation, cloudiness (low/medium/high)
- 3D atmospheric fields from the surface up to ~15 km above ground level

Specialized post-processed products derived from ALADIN-HR include:
- meteograms (general and Adriatic-specific)
- biometeorological indices (e.g. UTCI thermal-stress)
- statistical / machine-learning post-processing (analog method "HRAN", neighborhood method)
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

> The two endpoints below are **not advertised on the DHMZ public website**. They were provided directly by DHMZ on request, after the maintainer of this repository contacted them about including DHMZ in a list of free and public weather models. Anyone wanting to use this data is encouraged to contact DHMZ directly (dhmz@dhz.hr) and ask whether they're happy for it to be used for their intended purpose; licensing terms are not stated on the endpoints themselves. The credentials below are the ones DHMZ provided.

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
- Produced from a **2 km horizontal grid spacing** *(these 2 km wind fields are produced by the ALADIN-HR20 member; this provenance is inferred from the February 2023 replacement of HRDA by HR20 and should be confirmed with DHMZ)*
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
- **License:** Not stated on the open-data endpoints. Users are advised to contact DHMZ (dhmz@dhz.hr) for permission and any conditions of re-use.
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
- **Related systems run by / with DHMZ that are *not* part of this deterministic suite and are not described here:** the **A-LAEF** and **C-LAEF (AlpeAdria)** ALARO-based limited-area *ensemble* systems (developed within RC LACE and jointly with GeoSphere Austria; no confirmed open feed at the time of writing), and the **INCA** nowcasting/analysis system installed and configured for the Croatian domain in 2025 (DHMZ + GeoSphere Austria, via the EUMETNET Weather Forecasting Cooperation). INCA is a candidate for separate cataloging under the nowcasting category.
- Other ALADIN/ALARO Central European deployments include [ALADIN Slovakia](../slovakia/aladin-slovakia.md), [ALADIN Slovenia](../slovenia/aladin-slovenia.md), [ALARO Belgium](../belgium/alaro-belgium.md), and the Hungarian and Czech systems documented under their respective country directories.
- DHMZ is a member of the **RC LACE** regional cooperation and the broader **ACCORD** consortium; ALADIN-HR's data assimilation development (radar, all-sky IASI, future MTG-S1 IRS) is coordinated within these frameworks.

---

## Recent version history

> **Note on currency:** The most recent authoritative DHMZ status documentation on file is the **2026 national ASW poster** ("The NWP activities at Croatian Meteorological and Hydrological Service, 2026," Ćorković et al., 2026). It confirms the operational **HR40 + HR20** configuration (cy43t2, 4 km/L73 hydrostatic + 2 km/L87 non-hydrostatic, 4 runs/day, IFS-coupled) unchanged from 2023; the principal new operational change is the METMONIC observation-network upgrade (below).

### 2026 — METMONIC observation network completed
The METMONIC project (Modernisation of the National Weather Observation Network) was completed. The national **OBSOUL** dataset used for data assimilation was significantly upgraded, with **250 new automatic meteorological stations** integrated into the international exchange and into the HR40 DA system. First-guess-departure thresholds (mean and standard deviation) were used to **blacklist** stations poorly represented by the 4 km model (small islands, elevation-mismatched mountain sites). Total observing infrastructure now comprises ~450 automatic systems, meteorological-oceanographic buoys, and the six METMONIC C-band dual-polarized radars (Gradište, Bilogora, Puntijarka, Goli, Debeljak, Uljenje) covering the whole of Croatia.

### 22 April 2024 — Operational CANARI tuning
New CANARI surface-analysis settings (EXP10 + external 9-hour Wp smoothing, internally referred to as "v7.9") were put operational, correcting two previously identified problems linked to surface DA: forecast "jumpiness" during summer and unrealistic annual soil-moisture evolution. The change improves T2m and RH2m verification in both winter and summer periods and the representation of fog / low-cloud cases.

### End of 2023 — OPERA radar reflectivity assimilated operationally
Radar reflectivities from OPERA were successfully implemented in the ALADIN-HR operational chain. Data is sourced from the **NIMBUS** production line, which showed comparable performance to the older ODYSSEY line. A combined rain-threshold and observation-error-inflation method (offset 0.35, threshold 0.0) was selected for operational use after tuning to reduce the "drying" effect in the Bayesian inversion of reflectivity data. Verification showed improved 1 h rain rates at Croatian automatic stations and neutral surface / upper-air scores against the no-radar-DA baseline. Thinning distance in the bator namelist was subsequently increased to address memory issues in screening for spatially widely distributed precipitation patterns.

### 6 February 2023 — New two-member suite operational on new HPC
The current operational suite went live on the new DHMZ HPC system (Neverin), running model cycle **cy43t2_bf10**:
- **ALADIN-HR40** (4 km / 480 × 432 / L73 / hydrostatic / ALARO-1 / Δt 150 s) became the data-assimilating member, with a 3-hourly 3D-Var + OI cycle, static EDA B-matrix, Jk + SCC, IFS coupling (lagged), 1 h 40 min observation cut-off, and the observation set listed above (SYNOP, AMDAR, Mode-S MRAR CHMI & SI, GEOWIND, TEMP, SEVIRI, OPERA/OIFS reflectivity). This replaced the earlier cy38-based 4 km configuration ("HR44").
- **ALADIN-HR20** (2 km / 450 × 450 / L87 / non-hydrostatic / ALARO-1 / Δt 60 s, DFI-initialized from HR40, IFS LBCs) was introduced as a **full non-hydrostatic forecast member**, replacing the previous 2 km **ALADIN-HRDA** dynamical-adaptation (wind-only) configuration. Verification showed improved 10 m wind and wind-gust forecasts over HRDA, with maximum-wind spatial distributions over the coast (notably under Velebit) corresponding better to observations.

### 2018 (DAWD status) — pre-cy43 ALARO assimilation suite at DHMZ
Earlier ALADIN-HR4 configuration documented at the 2018 LACE DA Working Days used **ALARO-0 / cy38t1** at 4 km / L73 with 3-hourly 3D-Var (NMC B; tests with EDA B), VarBC, REDNMC=1.4, OI surface analysis with MESCAN correlation function, and assimilation of SYNOP (Ps), TEMP (T, q, u, v), AMDAR (T, u, v), AMV, SEVIRI (ch 2, 3) and Mode-S MRAR SI. LBCs from ECMWF (lagged), DFI initialization. Plans at that time included continuing EDA tests, radar DA work, GNSS ZTD tests, Jk testing, and migration to the cy43-series — all of which have since been realized in the current operational suite.

### 2013 (textbook description) — early ALADIN-HR operational suite
The earlier operational chain documented in Tudor et al. (2013) ran ALADIN at 8 km / L37 over a wider Lambert domain, with a 2 km / L15 hydrostatic high-resolution dynamical adaptation (HRDA) for 10 m wind, 72 h forecasts from 00 and 12 UTC ARPEGE-coupled runs, DFI initialization, and a parallel 3D-Var assimilation suite at 4 km / L37 (NMC B) with OI surface analysis. The current 4 km / L73 + 2 km / L87 / cy43t2 / IFS-coupled / radar-DA suite represents the operational descendant of that system, run on substantially upgraded HPC.

---

## Plans (per the 2024 RC LACE DA status report and 2025–2026 national posters)
- Preparation of all-sky code for assimilation of **IASI** data (in the C-LAEF context; observation-error modeling implemented into cy48t3 and tested via a C-LAEF 3D-EnVar member as of 2025–2026 — research, not yet operational in ALADIN-HR)
- Work on assimilation of **IRS data from MTG-S1** (in the C-LAEF context)

> *METMONIC automatic-station integration, previously listed here as a plan, was completed — see version history (2026).*

---

## Official documentation
- DHMZ brochure: *ALADIN-HR — Operational Numerical Weather Prediction* (2025)
- DHMZ national poster series: *The NWP activities at Croatian Meteorological and Hydrological Service* (annual, EWGLAM/ASW; the 2023 edition documents the HR40 + HR20 suite from 6 February 2023, and the 2026 edition (Ćorković et al.) confirms it unchanged)
- Panežić, S., Zajec, A., Stanešić, A. (2024): *DA status Croatia*, Regional Cooperation for Limited Area Modeling in Central Europe (RC LACE), DA Working Days 2024
- Kovačić, T., Stanešić, A. (2018): *Data assimilation status at DHMZ*, RC LACE DA Working Days, 19–21 September 2018
- Tudor, M., Ivatek-Šahdan, S., Stanešić, A., Horvath, K., Bajić, A. (2013): *Forecasting Weather in Croatia Using ALADIN Numerical Weather Prediction Model*, in *Climate Change and Regional/Local Responses*, InTech, http://dx.doi.org/10.5772/55698
- DHMZ contact: dhmz@dhz.hr — https://meteo.hr
