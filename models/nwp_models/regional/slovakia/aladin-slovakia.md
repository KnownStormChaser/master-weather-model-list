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
- **Vertical levels:** 87 (the L63 → L87 upgrade is operational; first shown operational in the **47th EWGLAM 2025 poster** — September 2025 — having still been L63 at the 5th ACCORD ASW / April 2025, so the change went operational between April and September 2025. See *Notes* and version history.)
- **Time step:** 180 s
- **Forecast length:** Up to **102 hours** in the operational suite since mid-2025 (first shown in the 47th EWGLAM 2025 poster) — **102/72/72/72 h** at 00/06/12/18 UTC respectively (previously 78/72/72/60 h, i.e. the 00 UTC run extended from +78 h to +102 h and the 18 UTC run from +60 h to +72 h). The publicly distributed open-data product mirrors this: the **00 UTC cycle now runs to +102 h** on the open-data portal (confirmed against the live URL, June 2026), with the other three cycles at +72 h.
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
- The upgrade from **L63 to L87** vertical levels — developed in collaboration with CHMI on the cloud-parameterization tuning, and motivated in part by improved suitability for GNSS ZTD assimilation — is **operational**. The first source showing it operationally is the **47th EWGLAM 2025 poster** (Norrköping, September 2025), which lists ALADIN/SHMÚ at 87 levels; the **5th ACCORD ASW** (April 2025) still showed L63, so the change went operational **between April and September 2025**. As of February 2024 the L87 development version had shown broadly neutral scores against operational L63. The L87 bundle also included retuning of the DFI blending step and the 3D-Var REDNMC coefficients, and a switch from time-consistent (TCC) to **space-consistent (SCC) coupling**.
- **DA development:** the 5th ACCORD ASW (April 2025) reported operational **ZTD GNSS** assimilation with **VARBC** (variational bias correction); **SEVIRI** radiance assimilation was under way. The 2026 ASW poster lists **radar** and **SEVIRI** data assimilation among current R&D highlights — radar-reflectivity DA is being trialled in the 1 km RUC (the **ALA1** variant; see below) rather than in the deterministic ALADIN/SHMÚ suite.
- Other ALADIN-based systems are also run at or by SHMÚ but are **not the same product** as the ALADIN/SHMÚ deterministic forecast described here, and are not openly downloadable in the same way:
  - **A-LAEF** — the ACCORD Limited Area Ensemble Forecasting system (ALARO-1vB, multi-physics + surface SPPT; 4.8 km, 1250 × 750, L60, 180 s), run operationally at ECMWF (Atos Sequana XH2000, 85 nodes) and shared as the common RC LACE ensemble. Per the 2026 ASW poster the operational version is **CY40T1bf07+** with ESDA (ensemble surface DA by CANARI) + DFI spectral blending, ECMWF-ENS-coupled (6 h, time-lagged), running 00 and 12 UTC to +72 h hourly. A **CY46T1+ e-suite** (control member running since 12 June 2025; new multiphysics + stochastic physics, inline GRIB2 fullpos) has been prepared but is not yet operational — operational implementation will probably be handled by the **IMGW** team. Following the departure of its principal developer from SHMÚ in 2025, A-LAEF emergency maintenance has since **January 2026** been shared on a weekly rotation by an RC LACE team spanning five countries (CZ, HR, PL, SI, SK).
  - **ALA2E** — a non-hydrostatic 2 km configuration (512 × 384, L87, 90 s) on CY48T3 with ALARO-1vB physics, initialised by downscaling the A-LAEF control with DFI and coupled to ECMWF (3 h, time-lagged). **Operational since May 2024**; runs 00 and 12 UTC to +84 h with **30-minute output frequency** (per the 2026 poster's operational highlights). Primarily feeds the CMAQ chemical-transport model and provides high-resolution forecaster input.
  - **RUC1 / ALA1** — a 1 km **test-mode** rapid-update cycle (1024 × 768, L87, 30 s) on CY46T1bf07, ARPEGE-coupled (1 h, time-lagged, SCC) with CANARI surface DA + 3D-Var upper-air and DFI initialisation, on 60 nodes; hourly cycling to +12 h (extended to +30 h at 00 UTC). **ALA1** is the variant that adds **OPERA radar-reflectivity assimilation** (RUC1 is the no-radar baseline); the 2026 poster shows ALA1's radar DA producing sharper, more localised wind-field structure than RUC1 in a Bratislava convective case. RUC1 is also being adapted for a global-radiation nowcasting project (APVV). Planned development: upgrade to **CY48T3**, AI/ML nowcasting, single precision, and severe-weather diagnostics.
- **New diagnostic outputs (L87 bundle):** the 47th EWGLAM 2025 poster reports that new parameters were added to ALADIN/SHMÚ outputs alongside the L87 upgrade, including **simulated METEOSAT satellite imagery**. The poster states these were "added to outputs" but does not say whether they reach the public open-data GRIB2 feed or only internal forecaster products — **flag: verify against the portal before listing under *What it provides***.
- **CANARI deep-soil-wetness smoothing — tested, not operational:** the 47th EWGLAM 2025 poster notes that smoothing of the deep soil wetness (PROFRESERV.EAU) in CANARI was tested for ALADIN/SHMÚ but **not implemented operationally**. (Related smoothing experiments in the same and later posters appear in the RUC1 columns; those belong to the RUC1/ALA1 development line, not the operational deterministic suite — don't conflate.)

---

## Recent version history

### Mid-2025 — L87 operational and 00 UTC run extended to +102 h
The long-developed **L63 → L87** vertical-level upgrade became operational and the operational forecast ranges changed to **102/72/72/72 h** at 00/06/12/18 UTC (the 00 UTC run extended from +78 h to +102 h, the 18 UTC run from +60 h to +72 h). The first source showing this operationally is the **47th EWGLAM & 32nd SRNWP poster** (Norrköping, 22–25 September 2025), whose systems table lists ALADIN/SHMÚ at 87 levels / 102-72-72-72 and names "Upgrade of ALADIN/SHMÚ to 87 vertical levels" as an operational highlight. The **5th ACCORD ASW** (Zalakaros, 31 March – 4 April 2025) still showed L63 / 78-72-72-60, placing the change **between April and September 2025**. The bundle also included retuning of the DFI blending step and the 3D-Var REDNMC coefficients and a switch from TCC to **space-consistent (SCC) coupling**. The model remained on CY46T1_bf07 / ALARO-1vB at 4.5 km on the 625 × 576 grid (confirmed unchanged at the 6th ACCORD ASW, April 2026).

### 2024 — SST handling change and ongoing L63 → L87 work
SST treatment in ALADIN/SHMÚ (and RUC1) was changed from *blendsur* to **relaxation to SST from LBC0**, following the approach used at CHMI. Experimental work on increasing the vertical levels from L63 to L87 (with retuned cloud parameterization developed jointly with CHMI) continued through 2024–2025 but had not been declared operational.

### November 2023 — Cycle upgrade to CY46T1_bf07
The complete operational upgrade of all configurations (927, 701, Blending, 002, 131, 001) from **CY43T2** to **CY46T1_bf07** was done in November 2023. No principal changes were made to the upper-air observation set or 3D-Var algorithmics in this cycle change.

### April 2023 — 3D-Var (BlendVar) operational
The **BlendVar** scheme — combining the existing spectral blending with **3D-Var** assimilation of SYNOP, AMDAR, AMV and TEMP observations on a 6-hourly cycle — became operational in April 2023, alongside REDNMC and SIGMAO_COEF tuning. This added variational upper-air assimilation to a system that had previously relied on spectral blending alone.

### 22 March 2022 — Migration to HPC3 (NEC)
The operational suite migrated from **HPC2** (IBM Flex System p460) to **HPC3** — the current **NEC HPC1804Ri 2** system (240 nodes, Intel Xeon Gold 6230 / Cascade Lake, Omni-Path, Linux) — operational since 22 March 2022 (2nd ACCORD ASW poster).

### Late 2020 / early 2021 — Deterministic cycle CY40T1 → CY43T2bf11
The operational deterministic cycle moved from **CY40T1bf07_export** to **CY43T2bf11**. The 42nd EWGLAM poster (September–October 2020) still listed CY40T1bf07_export as operational with CY43T2bf11 "experimental … ready to be implemented operationally in the nearest future"; the 1st ACCORD ASW poster (April 2021) lists CY43T2bf11 as the operational deterministic cycle — placing the switch between October 2020 and April 2021.

### 2017 — 4.5 km / L63 ALARO-1vB became operational
The current 4.5 km, L63 configuration with **ALARO-1vB** physics replaced the previous 9 km / L37 system, with substantially improved performance in high-impact weather situations. This is the configuration described in Derková et al. (2017).

---

## Official documentation
- SHMÚ — *Model ALADIN — popis* (model description page):  
  https://www.shmu.sk/sk/?page=1016
- Derková, M. et al. (2017): *Recent improvements in the ALADIN/SHMÚ operational system*, Meteorologický časopis, 20, 45–52
- SHMÚ NWP team (2020): *NWP related activities @SHMU*, poster, 42nd EWGLAM & 27th SRNWP Meetings, videoconference, 28 September – 2 October 2020
- SHMÚ NWP team (2021): *NWP related activities @SHMU*, poster, 43rd EWGLAM & 28th SRNWP meetings, videoconference, 27 September – 1 October 2021
- SHMÚ NWP team (2022): *NWP related activities @SHMU*, poster, 44th EWGLAM & 29th SRNWP meetings, Brussels, 26–29 September 2022
- SHMÚ NWP team (2023): *NWP related activities @SHMU*, poster, 45th EWGLAM & 30th SRNWP meetings, Reykjavík, 25–28 September 2023
- SHMÚ NWP team (2024): *NWP related activities in 2023–2024 @SHMU*, poster, 4th ACCORD All-Staff Workshop, Norrköping, 15–19 April 2024
- Derková, M., Imrišek, M., Neštiak, M., Simon, A. (2024): *Data assimilation activities @ SHMU*, RC LACE DA Working Days online, 5–6 September 2024
- SHMÚ NWP team (2024): *Development of NWP models at SHMU in 2023/2024*, poster, 46th EWGLAM & 31st SRNWP Meeting, Prague, 30 September – 3 October 2024
- SHMÚ NWP team (2025): *NWP related activities in 2024–2025 @SHMU*, poster, 5th ACCORD All-Staff Workshop, Zalakaros, 31 March – 4 April 2025
- Simon, A. et al. (2025): *Severe weather studies using high-resolution forecast applications at SHMU*, 2nd Poster Day of the Slovak Meteorological Society, Bratislava, 13 February 2025
- SHMÚ NWP team (2025): *Development of NWP models at SHMU in 2024/2025*, poster, 47th EWGLAM & 32nd SRNWP Meeting, Norrköping, 22–25 September 2025
- SHMÚ NWP team (2026): *NWP related activities in 2025–2026 @SHMU*, poster, 6th ACCORD All-Staff Workshop, Marrakech, Morocco, 13–17 April 2026
