# ALADIN Slovakia (ALADIN/SHMÚ)

## What this model is
ALADIN/SHMÚ is the **regional limited-area deterministic numerical weather prediction (NWP) system** operated by the Slovak Hydrometeorological Institute.

It is based on the **ALADIN** modeling system, in the **ALARO Canonical Model Configuration**, and forms the primary source of short-range weather forecasts at SHMÚ. Development is carried out within the **ACCORD** consortium and the **RC LACE** regional cooperation.

---

## Who runs it
- **Organization:** Slovak Hydrometeorological Institute (SHMÚ)
- **Country / region:** Slovakia

---

## What area it covers
- **Coverage:** Slovakia and a large part of Central Europe
- **Domain details:** 625 × 576 grid points at 4.5 km spacing, centred on Slovakia and covering most of Central Europe and the surrounding Mediterranean / Baltic margins

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system / core:** ALADIN (ALARO Canonical Model Configuration)
- **Dynamical formulation:** Hydrostatic (ALADIN spectral limited-area dynamical core)
- **Convection-allowing:** No (4.5 km grid; deep convection is parameterized by the ALARO physics)
- **Code version:** CY46T1_bf07 (export, with bug-fixes)
- **Physics package:** ALARO-1vB
- **Horizontal resolution:** 4.5 km
- **Grid dimensions:** 625 × 576 horizontal grid points
- **Vertical levels:** 63 (an L63 → L87 upgrade is in development; see *Notes*)
- **Time step:** 180 s
- **Forecast length:** Up to **78 hours** in the operational suite (78/72/72/60 h at 00/06/12/18 UTC respectively); the publicly distributed open-data product is provided up to **+72 h** for all four cycles
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC)
- **Temporal output resolution:** 1 hour
- **Cut-off:** Long cut-off (operational); short cut-off LBCs are also used internally
- **Output availability:** Approximately 3–4 hours after the cycle reference time

---

## Data assimilation
- **Data assimilation:** Yes (the **BlendVar** scheme — sequence: bator → e701 → blending → e002 → e131 → e001)
- **Upper-air analysis:** Spectral blending with digital filter initialization (**DFI**) combined with **3D-Var**
- **Surface analysis:** **CANARI** (optimal interpolation)
- **Assimilated observations** (6-hourly cycling):
  - Surface observations from the **OPLACE** RC LACE preprocessing system
  - **TEMP** (radiosonde)
  - **AMDAR** (aircraft)
  - **Mode-S** (aircraft, EHS / MRAR)
  - **METEOSAT HRW** (high-resolution atmospheric motion vectors from SEVIRI)
- **SST treatment:** Relaxation to SST from the LBC0 file (replacing the previous *blendsur* approach, following the configuration used at CHMI)
- **Initialization:** None applied to the analysis itself (DFI is used inside the blending step)

---

## Initial and boundary conditions
- **Initial conditions:** From the ALADIN/SHMÚ BlendVar (blending + 3D-Var) and CANARI analysis
- **Lateral boundary conditions:** **ARPEGE** (Météo-France global model), with both long and short cut-off LBCs available; coupling frequency 3 hours

---

## What it provides
Deterministic forecasts of standard meteorological variables on the model grid, including:
- temperature, humidity, and wind (3D atmosphere; surface and selected vertical levels)
- surface and mean sea-level pressure
- hourly precipitation
- cloud cover and radiation variables
- derived parameters used for warnings, hydrology, and aviation

Outputs are used operationally for **weather forecasting, warnings, aviation, hydrology, and emergency management** in Slovakia.

---

## Data availability
- **Is the data free?** Yes (publicly accessible at the SHMÚ open-data portal)
- **License:** **Creative Commons Attribution 4.0 International (CC BY 4.0)**, as stated in the README at the root of the SHMÚ open-data portal (https://opendata.shmu.sk/README.txt). The licence applies to all data published under that portal, including the ALADIN/SHMÚ NWP outputs.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB2
- **Official download location:**  
  https://opendata.shmu.sk/meteorology/weather/nwp/aladin/sk/4.5km/  
  (one file per forecast hour; each file contains all distributed meteorological elements)

---

## Notes
- The current operational configuration runs on the SHMÚ **NEC HPC** system (240 nodes total; Intel Xeon Gold 6230 Scalable Processors, Cascade Lake, Omni-Path, Linux), with ALADIN/SHMÚ using 40 nodes per cycle.
- An upgrade from **L63 to L87** vertical levels is in development at SHMÚ — done in collaboration with CHMI on the cloud parameterization tuning, and motivated in part by improved suitability for GNSS ZTD assimilation. As of February 2024, scorecards comparing the L87 development version against operational L63 showed mixed (broadly neutral) impact, and the upgrade had not yet been declared operational. The L87 development bundle also includes a retuning of the DFI blending and a switch from time-consistent (TCC) to **space-consistent (SCC) coupling**.
- **Near-term DA development** documented at the 5th ACCORD ASW (April 2025) includes operational implementation of **ZTD GNSS** assimilation with **VARBC** (variational bias correction). Earlier plans for **SEVIRI** radiance assimilation are also under way.
- Other ALADIN-based systems are also run at or by SHMÚ but are **not the same product** as the ALADIN/SHMÚ deterministic forecast described here, and are not openly downloadable in the same way:
  - **A-LAEF** — the ACCORD Limited Area Ensemble Forecasting system (ALARO-1vB, multi-physics + surface SPPT, 4.8 km, L60), run operationally at ECMWF under SHMÚ supervision and shared as the common RC LACE ensemble. As of 2025 it was being upgraded to CY46T1 with new ALARO multi-physics.
  - **ALA2e** — a non-hydrostatic 2 km configuration on CY48T3, with ALARO-1vB physics, L87 and a 90 s time step, downscaled from A-LAEF unperturbed control and coupled to ECMWF HRES. It became **operational in May 2024**, primarily feeding the CMAQ chemical-transport model and providing high-resolution forecaster input.
  - **RUC1 / ALA1** — a 1 km test-mode rapid-update-cycle configuration with hourly cycling, L87, 30 s time step, CANARI + 3D-Var. Includes experimental radar reflectivity assimilation.

---

## Recent version history

### 2024 — SST handling change and ongoing L63 → L87 work
SST treatment in ALADIN/SHMÚ (and RUC1) was changed from *blendsur* to **relaxation to SST from LBC0**, following the approach used at CHMI. Experimental work on increasing the vertical levels from L63 to L87 (with retuned cloud parameterization developed jointly with CHMI) continued through 2024–2025 but had not been declared operational.

### November 2023 — Cycle upgrade to CY46T1_bf07
The complete operational upgrade of all configurations (927, 701, Blending, 002, 131, 001) from **CY43T2** to **CY46T1_bf07** was done in November 2023. No principal changes were made to the upper-air observation set or 3D-Var algorithmics in this cycle change.

### April 2023 — 3D-Var (BlendVar) operational
The **BlendVar** scheme — combining the existing spectral blending with **3D-Var** assimilation of SYNOP, AMDAR, AMV and TEMP observations on a 6-hourly cycle — became operational in April 2023, alongside REDNMC and SIGMAO_COEF tuning. This added variational upper-air assimilation to a system that had previously relied on spectral blending alone.

### 2017 — 4.5 km / L63 ALARO-1vB became operational
The current 4.5 km, L63 configuration with **ALARO-1vB** physics replaced the previous 9 km / L37 system, with substantially improved performance in high-impact weather situations. This is the configuration described in Derková et al. (2017).

---

## Official documentation
- SHMÚ — *Model ALADIN — popis* (model description page):  
  https://www.shmu.sk/sk/?page=1016
- Derková, M. et al. (2017): *Recent improvements in the ALADIN/SHMÚ operational system*, Meteorologický časopis, 20, 45–52
- SHMÚ NWP team (2023): *NWP related activities @SHMU*, poster, 45th EWGLAM & 30th SRNWP meetings, Reykjavík, 25–28 September 2023
- SHMÚ NWP team (2024): *NWP related activities in 2023–2024 @SHMU*, poster, 4th ACCORD All-Staff Workshop, Norrköping, 15–19 April 2024
- Derková, M., Imrišek, M., Neštiak, M., Simon, A. (2024): *Data assimilation activities @ SHMU*, RC LACE DA Working Days online, 5–6 September 2024
- SHMÚ NWP team (2025): *NWP related activities in 2024–2025 @SHMU*, poster, 5th ACCORD All-Staff Workshop, Zalakaros, 31 March – 4 April 2025
- Simon, A. et al. (2025): *Severe weather studies using high-resolution forecast applications at SHMU*, 2nd Poster Day of the Slovak Meteorological Society, Bratislava, 13 February 2025
