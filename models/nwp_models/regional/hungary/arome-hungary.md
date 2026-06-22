# AROME Hungary

## What this model is
AROME Hungary is the operational **convection-permitting regional deterministic numerical weather prediction (NWP) model** run by HungaroMet (Hungarian Meteorological Service / OMSZ) over the Carpathian Basin.

It is designed for **short-range forecasting and nowcasting** of high-impact convective weather — thunderstorms, squall lines, mesoscale convective systems, heavy precipitation, and rapidly evolving local-scale events. AROME Hungary is one member of the wider AROME / HARMONIE-AROME family operated by national services in the **ACCORD** consortium (formerly the ALADIN and HIRLAM consortia, merged in 2021), with development also coordinated through the **RC LACE** Central European cooperation.

HungaroMet runs three closely related operational systems on the same infrastructure: the **ALARO** 8 km deterministic synoptic-scale model, the **AROME** 2.5 km convection-permitting model documented here, and an **AROME-EPS** 11-member ensemble. Of these three, only AROME is currently distributed on the open data portal.

---

## Who runs it
- **Organization:** HungaroMet (Hungarian Meteorological Service, formerly OMSZ — Országos Meteorológiai Szolgálat)
- **Country / region:** Hungary

---

## What area it covers
- **Coverage:** Hungary and the wider Carpathian Basin (Central Europe)
- **Domain bounds (publicly distributed grid):** **14.3°E – 24.3°E**, **44.9°N – 49.5°N**

---

## Basic details
- **Model type:** Regional deterministic NWP (convection-permitting)
- **Model system / core:** AROME (ALADIN-NH dynamical core, with Meso-NH–derived physics)
- **Code version:** cy46t1_bf07 (operational since the end of February 2025, per HungaroMet's 2025 ACCORD/RC LACE poster; replaced cy43t2_bf11 — see *Recent version history*)
- **Dynamical formulation:** Non-hydrostatic, spectral, with semi-Lagrangian advection and semi-implicit time integration
- **Convection-allowing:** Yes (deep convection explicitly resolved at 2.5 km; only shallow convection parameterized)
- **Horizontal resolution:** ~2.5 km, distributed on a 0.025° × 0.025° regular latitude–longitude grid
- **Vertical levels:** 60 (per HungaroMet's 2024 LACE status report, the 2025 and 2026 ACCORD posters, and the HungaroMet modelling activities page; the open-data PDF lists 59, an apparent inconsistency in HungaroMet documentation)
- **Model top:** ~2.7 hPa (~23 km)
- **Time step:** 60 s
- **Forecast length:** Up to 48 hours (publicly distributed product); HungaroMet's full operational suite produces 48 h or 36 h depending on cycle
- **Update frequency:** **4× daily on the open data portal (00/06/12/18 UTC)**; HungaroMet's internal operational AROME runs **8× daily** (00/06/12/18 UTC out to 48 h plus 03/09/15/21 UTC out to 36 h)
- **Temporal output resolution:** 1 hour
- **Workflow environment:** ECFLOW

---

## Data assimilation
- **Data assimilation:** Yes — AROME runs its own native high-resolution analysis (operational at HungaroMet since March 2013)
- **Atmospheric analysis:** **3D-Var** with a static AROME-EDA-based B-matrix
- **Surface analysis:** **SEKF** (Simplified Extended Kalman Filter)
- **Cycle:** 3-hour assimilation cycle
- **Initialization:** Space-consistent coupling; **no digital filter initialization (DFI)**
- **Cycled fields:** Hydrometeors and snow are cycled within the assimilation; lake temperature is initialized from in-situ measurements at **Lake Balaton** (per the 2025 ACCORD/RC LACE poster)
- **Snow-depth assimilation (operational March 2026):** AROME/HU now assimilates **SYNOP snow-depth observations** via CANARI, replacing the prior approach of initializing snow from the 3-hour first guess (which tended to retain snow unrealistically long). Hungarian snow depth is measured only at 06 UTC; the e-suite (November 2025 – March 2026) used the **1-layer D95 snow scheme** with three prognostic variables (snow-water equivalent, snow density, snow albedo), an altitude-dependent quality check (snow obs above 1500 m a.s.l. considered; spatial check disabled because it removed valid SYNOP data; negative values assimilated as zero snow depth), and a CANARI-flag bugfix from Trojáková, Ševčík and Meier. It improved snow-depth analyses and forecasts (more realistic, faster melting) as well as 2 m temperature and dewpoint, and was introduced into **every AROME/HU configuration (deterministic, EPS, RUC) in March 2026**.
- **Under development (not operational):** radar-reflectivity assimilation is being tested in AROME/HU (thinning-method optimisation using the 5 Hungarian radars, following a 2025 collaboration with CHMI on ALARO/CZ), as is assimilation of additional LACE-shared (non-GTS) SYNOP data at 2.5 km/L60 (3-hourly) and 1.3 km/L90 (1-hourly, i.e. AROME-RUC). The extra-SYNOP experiments roughly doubled the assimilated SYNOP count but gave mixed impact (neutral-to-positive at lower resolution, with some degradation); work continues, including blacklist reconsideration.
- **Observations assimilated (as of the 2026 ACCORD poster):** SYNOP (u, v, T, RH, z, **snow depth** — added operationally in March 2026; see below and version history), TEMP (u, v, T, q), AMDAR (u, v, T, q), Mode-S MRAR (u, v, T), GNSS-ZTD (IWV), and AMV / HRWind (u, v). Observations are sourced via the **OPLACE** RC LACE pre-processing service. *(The 2026 poster drops the "Slovenian and Czech" qualifier that earlier accompanied Mode-S MRAR; whether this reflects an expanded source set or a de-specification is unclear — flag for verification.)*

---

## Initial and boundary conditions
- **Initial conditions:** AROME 3D-Var + SEKF analysis (atmosphere + surface)
- **Lateral boundary conditions:** **ECMWF HRES** (IFS), 1-hourly coupling frequency, delivered via the Internet
  - **Backup LBCs:** Météo-France **ARPEGE** (per the 2025 ACCORD/RC LACE poster)
  - Time-lagged coupling for the production forecast
  - Mixed coupling in the data assimilation cycle

---

## What it provides
Deterministic forecasts of a wide range of atmospheric fields, distributed as one variable per file. The full parameter list is documented in the official dataset description (PDF linked below); a summary follows.

**2-D surface fields:**
- 2 m temperature, dewpoint, relative humidity, daily min/max
- 10 m U/V wind components and wind gusts
- Surface pressure, mean sea-level pressure
- Total cloud cover
- Total instantaneous precipitation, accumulated precipitation, total snow + graupel, total rainfall, snow mass, snow depth
- Surface temperature, accumulated short-wave surface radiation
- Planetary boundary layer height
- Convective available potential energy (CAPE), convective condensation level (CCL)
- Surface geopotential and land–sea mask (first time step only)

**3-D fields on standard pressure levels (1000, 850, 700, 500 hPa):**
- U/V wind, temperature, geopotential, relative humidity

**3-D fields on height levels (100 m above surface):**
- U/V wind, temperature, relative humidity (planetary boundary layer)

---

## Data availability
- **Is the data free?** Yes
- **License:** HungaroMet Open Data Portal terms (free use without modification; attribution required as *"Database: Meteorological Database, HungaroMet Nonprofit Zrt."*; modifications require written consent, with example attribution forms provided in the General Terms of Use). Warnings, alerts, and aviation forecasts may only be redistributed unmodified.
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF (one variable per file, gzip-compressed inside ZIP archives)
- **Official download location:**
  https://odp.met.hu/weather/nwp/AROME/nc/
- **File naming conventions** (from the dataset description):
  - 2-D fields: `AROME-<variable>-<YYYYMMDD>_<HHmm>+<TTTtt>.nc.zip`
  - 3-D pressure-level fields: `AROME-<variable>_p<pressure_level>-<YYYYMMDD>_<HHmm>+<TTTtt>.nc.zip`
  - 3-D height-level fields: `AROME-<variable>_h<height_level>-<YYYYMMDD>_<HHmm>+<TTTtt>.nc.zip`

---

## Notes
- **Public subset vs full operational suite:** HungaroMet runs AROME **8× daily** — the four main synoptic cycles (00/06/12/18 UTC) out to 48 h plus four intermediate cycles (03/09/15/21 UTC) out to 36 h. The open data portal distributes **only the four main 00/06/12/18 UTC cycles, out to +48 h, at hourly output resolution**. Internally HungaroMet also generates **15-minute outputs** for commercial users and the hail-prevention system; these higher-frequency products are not published on ODP. Users requiring more frequent cycles, the longer cycles, the 15-minute fields, or other internal products should contact HungaroMet directly.
- **Screen-level diagnostics:** Screen-level (2 m / 10 m) variables are diagnosed using a surface boundary-layer (SBL) scheme over both nature and sea tiles (per the 2025 ACCORD/RC LACE poster).
- **Sibling: ALADIN/HU — "ALARO" (8 km, deterministic, not publicly distributed):** HungaroMet's synoptic-scale model is an 8 km / L49 ALARO-physics model, hydrostatic, with parameterized deep convection — covering most of continental Europe. The 2025 ACCORD/RC LACE poster labels it **cy40t1 (ALARO-v1b physics)** and uses the system name **ALADIN/HU**; the 2024 LACE report gave the branch as cy40t1_bf05. It runs **4× daily (00/06/12/18 UTC) out to 60/48/60/36 h**, coupled to ECMWF HRES at 3-hour frequency, with 3D-Var (upper air) + optimal interpolation (surface) on a **6-hour cycle**, downscaled ensemble background-error covariances, and digital filter initialization. Assimilated observations (2025): SYNOP, SYNOP-SHIP, TEMP, AMDAR, ATOVS (AMSU, MHS radiances), MSG/GEOWIND AMV, and MSG/SEVIRI radiances. ALADIN/HU is not published on ODP. (HungaroMet documentation uses "ALADIN/HU" and "ALARO" interchangeably, reflecting the ALADIN dynamical core with ALARO physics.)
- **Sibling: AROME-EPS (operational, not publicly distributed):** HungaroMet operates a regional ensemble using the same AROME core: **10+1 members** (10 perturbed + 1 control; "11 members" in earlier wording), 2.5 km / L60, 2 forecast runs/day (00 and 12 UTC) out to 48 h, with local perturbations from a **3-hourly ensemble data assimilation** (same observation set as deterministic AROME), coupled to **ECMWF ENS** with hourly LBCs from the **18/06 UTC ENS** runs. The 2025 and 2026 posters both state the resolution and physics are "as in AROME/HU" but give no explicit cycle label for the ensemble; the 2024 LACE report listed cy43t2_bf11, so whether AROME-EPS tracks the deterministic cy46t1_bf07 is **still not explicitly confirmed**. The new **SYNOP snow-depth assimilation** was extended to the EPS configuration in March 2026 (see *Data assimilation*). Stochastically Perturbed Parametrizations (SPP) were under development with operational introduction targeted for Q2–Q3 2025. AROME-EPS is not currently published on ODP; should it become available, it would belong under regional ensemble models.
- **Sibling: AROME-RUC (e-suite, experimental):** HungaroMet is developing **AROME-RUC**, a 1.3 km / L90 / cy43t2_bf11 rapid-update configuration on approximately the same domain as operational AROME, with a 1-hour DA cycle (30-minute cut-off) and 8 runs/day out to 12 h (extended on 27 August 2024 to 30 h at 00 UTC and 24 h at 06 UTC). Same observation set as AROME-OPER, including GNSS-ZTD with an expanded whitelist. The 2025 ACCORD/RC LACE poster reports testing of **EMADDC Mode-S EHS (Enhanced Surveillance) wind/temperature assimilation** in AROME-RUC (June 2024 experiments), with early signals in high-level winds and precipitation but no clear operational benefit yet. The 2026 poster reports that the new **SYNOP snow-depth assimilation** was extended to the RUC configuration in March 2026, and that the extra-(non-GTS)-SYNOP experiments were run in this 1.3 km/L90 1-hourly RUC setup alongside the 2.5 km deterministic one. This system is analogous to the AROME-RUC configuration at GeoSphere Austria.
- **Relationship to siblings in the AROME / HARMONIE-AROME family:** AROME Hungary shares the same dynamical core and physics packages as other ACCORD-consortium AROME / HARMONIE-AROME deployments. See [AROME France](../france/arome-france.md), [AROME Austria](../austria/arome-austria.md), [AROME-Arctic](../norway/arome-arctic.md), [HARMONIE-AROME Ireland](../ireland/harmonie-arome-ireland.md), and [MEPS](../norway/meps.md) for closely related systems. Other ALADIN/ALARO Central European deployments include [ALADIN Slovakia](../slovakia/aladin-slovakia.md), [ALADIN-HR (Croatia)](../croatia/aladin-hr.md), and [ALARO Belgium](../belgium/alaro-belgium.md).
- **RC LACE / OPLACE:** HungaroMet leads the **OPLACE** observation pre-processing service for the RC LACE cooperation, providing pre-processed observation streams (in OPLACE obsoul format) for data assimilation across partner services.
- **Computer system:** Operational NWP runs on an **HPE Apollo 6000** cluster (22 nodes × 2 × 20-core 2.2 GHz Intel Xeon E5-2698, 128 GB RAM/node). LBCs are fetched from ECMWF (IFS/HRES) over the Internet, with Météo-France **ARPEGE** as backup.

---

## Recent version history

> **Note on currency:** The most recent authoritative HungaroMet status documentation on file is the **2026 ACCORD poster** ("NWP activities at the Hungarian Meteorological Service," Szépszó et al., 2026). It confirms the operational AROME/HU configuration (cy46t1_bf07, 2.5 km, L60, 8 runs/day, ECMWF-HRES 1 h coupling, 3D-Var + SEKF on a 3 h cycle) unchanged from 2025; the principal new operational change is SYNOP snow-depth assimilation (below).

### March 2026 — SYNOP snow-depth assimilation operational
Following an e-suite run from November 2025 to March 2026, assimilation of **SYNOP snow-depth observations** (via CANARI, 1-layer D95 snow scheme with snow-water equivalent, density and albedo as prognostic variables, altitude-dependent quality control) was introduced operationally into **every AROME/HU configuration** (deterministic, EPS, RUC). It replaced the prior first-guess snow initialization — which retained snow unrealistically long — and improved snow-depth analyses/forecasts as well as 2 m temperature and dewpoint. Radar-reflectivity assimilation and assimilation of additional LACE-shared SYNOP data were under test but not yet operational.

### End of February 2025 — CY46T1 (cy46t1_bf07) operational
The 2025 ACCORD/RC LACE poster confirms **cy46t1_bf07 became operational in AROME/HU at the end of February 2025**, alongside the operational introduction of a reduced ECOCLIMAP-II town-fraction PGD modification (FTOWN_MIN threshold) addressing summer warm biases in 2 m temperature. This realized the upgrade the 2024 LACE report had scheduled for February 2025 (CY46T1 verification during 2024 had shown only minor differences from cy43t2 in 2 m temperature scores and precipitation case studies).

### 16 January 2024 — GNSS-ZTD assimilation in fine-resolution AROME (e-suite)
GNSS Zenith Total Delay assimilation was activated in the fine-resolution AROME e-suite (AROME-RUC, 1.3 km) using the same whitelist as operational AROME, following a bug-fix in the PREGPSSOL preprocessor (1-hourly cycling). Improvements in 2 m relative humidity scores were demonstrated against the operational AROME 2.5 km baseline.

### June 2023 — GNSS-ZTD whitelist update (BMEG network operational)
The SGO1 GNSS-ZTD network's data quality dropped markedly in 2023 and was degrading forecasts; SGO1 was dropped from assimilation in April 2023. The Budapest University of Technology and Economics **BMEG** network (E-GVAP, comparable coverage to SGO1) came online in February 2023, and a new whitelist (30/34/40/11 stations from BMEG/WUEL/GF1R/ASI) became operational in **AROME/HU in June 2023**, with mainly neutral-to-positive impact and reduced early-forecast precipitation overestimation. (Source: 2023 EWGLAM/SRNWP poster.)

### March 2023 — AROME-EPS EDA operational
Ensemble Data Assimilation became operational in **AROME-EPS** in **March 2023**, introducing local perturbations (3-hourly EDA) in place of pure ECMWF-ENS downscaling and improving initial conditions for the ensemble. (Source: 2023 ACCORD poster, which states "EDA is introduced to the operational AROME-EPS in March 2023.")

### February 2023 — AMV / HRWind assimilation operational in AROME/HU
EUMETSAT AMV and NWCSAF HRWind data (with extra observations admitted between 350 and 800 hPa relative to the original ALADIN/HU blacklist) were **included in the operational AROME/HU assimilation flow from February 2023**, after cy40 and cy43t2_bf11 experiments showed mostly neutral-to-slightly-positive impact. This is the operational origin of the **AMV / HRWind** entries in the assimilated-observations list (earlier 2020–2022 posters list neither for AROME/HU). (Source: 2023 ACCORD poster.)

### June 2022 — SEKF surface analysis operational
AROME/HU's surface data assimilation was switched from **optimal interpolation (OI-main) to the Simplified Extended Kalman Filter (SEKF)** in **June 2022**. An AROME-TEST e-suite had run in parallel from November 2021 to June 2022; SEKF improved nocturnal 2 m temperature/dewpoint errors and reduced over-prediction of daily precipitation above ~7 mm. This is the operational origin of the entry's current "Surface analysis: SEKF." (Sources: 2022 EWGLAM/SRNWP poster — "SEKF was introduced in the operational AROME/HU in June"; 2022 ACCORD poster shows the e-suite still parallel to an OI-main operational run.)

### 2021 — Code-base label upgrade
The ODP dataset description records a 2021 transition from cy40t1 to a cy43-series label. The exact branch label given in the ODP PDF (cy43t3) does not match the cy43t2_bf11 label in HungaroMet's 2024 LACE status report; the LACE document is more authoritative for the running configuration.

### 2019 — AROME cy40t1 model version
First version-tagged release documented in the ODP open-data dataset description.

### March 2013 — AROME 3D-Var operational
HungaroMet's native AROME 3D-Var atmospheric assimilation became operational, initially using conventional observations (surface, radiosonde, aircraft) with a 3-hour assimilation cycle.

---

## Official documentation
- HungaroMet AROME page (English): https://www.met.hu/en/idojaras/elorejelzes/modellek/AROME/
- HungaroMet open data portal (root): https://odp.met.hu/
- AROME public dataset description (PDF): https://odp.met.hu/weather/nwp/AROME/Description_shortrange_forecast-AROME-en.pdf
- HungaroMet Open Data Portal General Terms of Use (PDF): https://odp.met.hu/ (linked from the ODP root)
- HungaroMet regional modeling activities (Hungarian): https://www.met.hu/rolunk/tevekenysegek/idojarasmodellezes/modellek/
- HungaroMet data assimilation page (Hungarian): https://www.met.hu/rolunk/tevekenysegek/idojarasmodellezes/adatasszimilacio/
- HungaroMet physical parameterizations page (Hungarian): https://www.met.hu/rolunk/tevekenysegek/idojarasmodellezes/parametrizacio/
- ACCORD consortium: https://www.umr-cnrm.fr/accord/
- RC LACE Central European cooperation: http://www.rclace.eu/

### Key references
- Szépszó, G., Kardos-Várkonyi, A., Lancz, D., Magyar, L., Szalontainé Gáspár, N., Tóth, B., Tóth, H. (2026). *NWP activities at the Hungarian Meteorological Service.* ACCORD poster, 6th ACCORD All-Staff Workshop, Marrakech, Morocco, April 2026. *(Workshop number/venue inferred from the parallel SHMÚ 2026 poster, which states 6th ACCORD ASW, 13–17 April 2026, Marrakech; confirm.)*
- Szépszó, G., Elek, P., Jávorné Radnóczi, K., Lancz, D., Tóth, B., Tóth, H. (2025). *NWP activities at the Hungarian Meteorological Service.* ACCORD / RC LACE poster.
- Duics-Korosecz, L., Elek, P., Homonnai, V., Lancz, D., Nagy, G., Jávorné Radnóczi, K., Tóth, H. (2024). *NWP developments in Hungary in 2024.* RC LACE annual status presentation (online DA Working Days).
- Szintai, B., Szűcs, M., Randriamampianina, R., Kullmann, L. (2015). *Application of the AROME non-hydrostatic model at the Hungarian Meteorological Service: physical parametrizations and ensemble forecasting.* Időjárás, **119**, 241–265.
- Homonnai, V., Jávorné Radnóczi, K., Kocsis, Zs., Szépszó, G., Tóth, H. (2021). *NWP at the Hungarian Meteorological Service.* 1st ACCORD All-Staff Workshop, online, 12–16 April 2021.
- Lancz, D., Jávorné Radnóczi, K., Szanyi, K., Szépszó, G., Szintai, B., Tóth, B., Tóth, H., Várkonyi, A. (2022). *NWP activities at the Hungarian Meteorological Service.* 2nd ACCORD All-Staff Workshop, Ljubljana, 4–8 April 2022.
- Szépszó, G., Baran, Á., Baran, S., Jávorné Radnóczi, K., Kardos-Várkonyi, A., Kocsis, Zs., Lancz, D., Szintai, B., Tóth, B., Tóth, G., Tóth, H. (2023). *NWP activities at the Hungarian Meteorological Service.* 3rd ACCORD All-Staff Workshop, Tallinn, 27–31 March 2023.
- Szépszó, G., Jávorné Radnóczi, K., Kardos-Várkonyi, A., Lancz, D., Szintai, B., Tóth, G., Tóth, H. (2024). *NWP activities at the Hungarian Meteorological Service.* 4th ACCORD All-Staff Workshop, Norrköping, Sweden, 15–19 April 2024.
- Homonnai, V., Jávorné Radnóczi, K., Kocsis, Zs., Szanyi, K., Szépszó, G., Szintai, B., Tóth, B., Tóth, G., Tóth, H. (2020). *NWP at the Hungarian Meteorological Service.* EWGLAM/SRNWP meeting, 2020 (online).
- Baran, Á., Baran, S., Homonnai, V., Kocsis, Zs., Kornyik, M., Szépszó, G., Szintai, B., Tóth, H. (2021). *NWP activities at the Hungarian Meteorological Service.* EWGLAM/SRNWP meeting, 2021.
- Lancz, D., Jávorné Radnóczi, K., Szanyi, K., Szépszó, G., Szintai, B., Tóth, B., Tóth, G., Tóth, H., Várkonyi, A. (2022). *NWP activities at the Hungarian Meteorological Service.* EWGLAM/SRNWP meeting, 2022.
- Szépszó, G., Jávorné Radnóczi, K., Kardos-Várkonyi, A., Lancz, D., Szintai, B., Tóth, B., Tóth, G., Tóth, H. (2023). *NWP activities at the Hungarian Meteorological Service.* 45th EWGLAM & 30th SRNWP Meeting, Reykjavík, Iceland, 25–28 October 2023.
- Szépszó, G., Elek, P., Homonnai, V., Lancz, D., Szintai, B., Tóth, H. (2024). *NWP activities at the Hungarian Meteorological Service.* RC LACE national presentation.
- Szépszó, G., Kardos-Várkonyi, A., Lancz, D., Tóth, H. (2025). *NWP activities at the Hungarian Meteorological Service.* RC LACE national presentation.

### Contact
- Open data technical contact: odp@met.hu
- Licensing / consent for modifications: service@met.hu
