# ALARO Belgium (RMI)

## What this model is
ALARO Belgium is the **regional limited-area deterministic numerical weather prediction (NWP) system** operated by the Royal Meteorological Institute of Belgium.

It is built on the **ALADIN** spectral limited-area modelling system using the **ALARO physics package** (currently **ALARO-1**), a configuration designed for *gray-zone* resolutions between mesoscale and convection-permitting scales. Development takes place within the **ACCORD** consortium and the predecessor **ALADIN** consortium.

The dataset documented here — **`alaro_40l`** — is the publicly distributed product: ~4 km horizontal resolution with 40 vertical levels, packaged in GRIB. RMI also runs additional internal configurations (a 4 km / L87 ALARO operational run with longer forecast range and a 1.3 km / L87 non-hydrostatic ALARO/AROME run) that are not part of the public `alaro_40l` distribution.

---

## Who runs it
- **Organization:** Royal Meteorological Institute of Belgium (RMI / KMI / IRM)
- **Country / region:** Belgium

---

## What area it covers
- **Coverage:** Belgium and surrounding parts of Western Europe
- **Domain details:** Lambert conformal conic projection centred near Belgium (central meridian ≈ 4.55°E, latitude of origin ≈ 50.57°N), based on the operational ALARO-Belgium NWP domain
- **Grid dimensions (public dataset):** TBD (the internal 4 km operational ALARO runs on 432 × 432 grid points)

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system / core:** ALADIN (ALARO Canonical Model Configuration)
- **Dynamical formulation:** Hydrostatic (the public 4 km ALARO; RMI's internal 1.3 km ALARO is non-hydrostatic)
- **Convection-allowing:** No (4 km grid; deep convection is parameterized by the ALARO physics, designed specifically for the gray-zone)
- **Code version (internal operational reference):** CY43T2 with ALARO-1 (as documented for RMI's operational ALARO chain in 2021 and as used through CORDEX.be II climate runs in 2024)
- **Physics package:** ALARO-1 — including the modular Multiscale Microphysics and Transport (3MT) deep convection scheme with non-saturated downdraft, the TOUCANS turbulence scheme, and the ACRANEB2 broadband radiation scheme
- **Horizontal resolution:** ~4 km
- **Vertical levels:** 40 (in the public `alaro_40l` distribution; RMI's internal 4 km operational ALARO uses 87 levels)
- **Forecast length:** Up to ~60 hours (TBD for the public distribution specifically; +60 h is the documented range of the internal 4 km operational ALARO chain)
- **Update frequency:** 4× daily (00, 06, 12, 18 UTC) — the most recent 4 runs are kept on the FTP server
- **Temporal output resolution:** TBD

---

## Data assimilation
- **Data assimilation:** Limited (development ongoing as of 2021)
- **Method / cadence:** As of the September 2021 DAsKit progress report, RMI's only operational assimilation in the regional NWP chain was **CANARI-OImain surface DA in AROME-Belgium** with SYNOP. Conventional observations were preprocessed through the local **POP_RMI** Python toolchain to deduplicate and amend BUFR feeds. A 3 h 3D-Var + CANARI-OImain configuration was being tested for AROME-Belgium with AMDAR, TEMP, GNSS, Mode-S and radar reflectivity (DBZH); a 3 h 3D-Var + CANARI configuration was being tested for the 1.3 km ALARO. New work included GNSS ZTD assimilation (~128 stations, three processing centres, 3-hourly cycling), Mode-S EHS monitoring (originally MUAC data via KNMI, with a switch to EMADDC underway), and first radar-reflectivity DA trials using OPERA ODIM-V2.2 HDF5 files from the Belgian radar composite (Jabbeke, Zaventem, Helchteren, Wideumont) plus the French Avesnois radar. Future work flagged at that time included replacing the NMC B-matrix with an AEARP/EDA B-matrix and increasing the assimilation frequency to 1 h for nowcasting. The exact assimilation status of the publicly distributed `alaro_40l` configuration is TBD; it inherits from the 4 km operational ALARO chain, which had no operational upper-air DA as of late 2021. **2024 update (4th ACCORD ASW):** RMI reported an **experimental AROME 3D-Var RUC** (rapid-update cycle) assimilating four observation types — SYNOP, AMDAR, TEMP and **GNSS** — with the analysis performed every 3 h (CANARI for surface + 3D-Var for upper air) and an **Incremental Analysis Update (IAU)** applied on top of the 3D-Var for filtering. The stated outlook was to move to a 1-hourly cycle, add **radar** (reflectivity and radial wind) assimilation, and promote the system to e-suite during summer 2024. This concerns the internal AROME-Belgium (**AR13**) chain; the public 4 km ALARO product (`alaro_40l`, from the AO40 downscaling suite) still carries no operational upper-air DA.

---

## Initial and boundary conditions
- **Initial conditions:** TBD (produced from ARPEGE-coupled fields and any operational ALARO surface analysis)
- **Lateral boundary conditions:** **ARPEGE** (Météo-France global model) for the public 4 km ALARO product. Coupling cadence is documented inconsistently across RMI posters: hourly in the September 2021 status report, **3-hourly** in the 2023 ASW "model versions" description of the local 4 km run, and **hourly** again in the 2024 ASW description and in the ATOS **AO40** suite setup. As of the 2024 ASW poster the operational (ATOS) 4 km ALARO couples to ARPEGE hourly. *(The 3-hourly figure in the 2023 poster's local-suite description is flagged as inconsistent with the surrounding sources.)* RMI's 1.3 km nests are coupled differently on ATOS — to ECMWF HRES — see Notes.

---

## What it provides
Deterministic forecasts of standard meteorological variables, including:
- Temperature, maximum and minimum temperature, dew-point temperature, wet-bulb potential temperature
- 10 m U/V wind components and stationary boundary layer (SBL) gust
- Cloud cover (low / medium / high / total)
- Total precipitation; large-scale and convective rain; large-scale and convective snow
- Relative and specific humidity
- Geopotential and mean sea-level pressure
- Orography
- Freezing level / 0 °C isotherm
- Convective Available Potential Energy (CAPE)
- Vertical velocity

---

## Data availability
- **Is the data free?** Yes
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0); attribution to RMI required. See the open-data portal terms at https://opendata.meteo.be/termsandconditions
- **Is the data downloadable?** Yes
- **Data formats:** GRIB (decoding requires the RMI local concept tables `params227-1.tab` and `params227-228.tab`, available from the documentation page below)
- **Public dataset name:** `alaro_40l`
- **Official download location:**
  https://opendata.meteo.be/ftp/forecasts/alaro_40l/
  (FTP also reachable at `ftp://opendata24-me.oma.be/forecasts/alaro_40l/`; only the latest 4 runs are retained)
- **Web services:** OGC WCS 1.1.0 and WMS 1.3.0 endpoints are also published from the same dataset:
  - WCS GetCapabilities: https://opendata.meteo.be/service/alaro/wcs?service=WCS&version=1.1.0&request=GetCapabilities
  - WMS GetCapabilities: https://opendata.meteo.be/service/alaro/wms?service=WMS&version=1.3.0&request=getCapabilities

---

## Notes
- The dataset name `alaro_40l` refers to the **40 vertical levels** in the publicly distributed product, **not** a horizontal resolution — the model itself runs at ~4 km horizontally. RMI's internal operational ALARO uses 87 vertical levels; the L40 distribution is a public-release subset.
- **Operational infrastructure moved to ECMWF ATOS (Bologna).** RMI's local SGI Rackable cluster (2 × 56 nodes, Xeon E5-2680V3; 2 × 1344 cores) reached end of life. The 2023 ASW poster reports the three operational suites being ported to **ECMWF ATOS** as a Time Critical (tc2) facility, scripted via RMI's **NodeRunner** (ecFlow-based) system; the cy43t2 BATOR code was adapted with Harmonie code to ingest BUFR-SYNOP at ECMWF. By the 2024 ASW poster operations run on ATOS (30+1 nodes), with the 9-year-old local SGI kept only as a **backup** chain (still ARPEGE-coupled) and a possible ECMWF-coupled "mini-EPS" under consideration. The three suites are **AO40** (4 km ALARO), **AO13** (1.3 km ALARO) and **AR13** (1.3 km AROME); the public `alaro_40l` derives from **AO40**. *(In the suite names the digits denote resolution — AO40 = 4.0 km — not to be confused with the `_40l` = 40 vertical levels in the public dataset name.)*
- The publicly distributed `alaro_40l` is the only deterministic NWP product RMI currently distributes openly. Internally, RMI also operates:
  - A **4 km / 432 × 432 / L87 ALARO** operational chain (CY43T2 + ALARO-1; 180 s time step; +60 h forecasts at 00/06/12/18 UTC; ARPEGE-coupled, hourly per the 2024 ASW poster) — the parent of `alaro_40l`. On ATOS this is the **AO40** suite, a downscaling model with hourly ARPEGE LBCs, now using the same re-tuned cy43t2 as the 1.3 km ALARO.
  - A **1.3 km / 600 × 600 / L87 high-resolution non-hydrostatic ALARO** (ISBA surface), running operationally since 2019 with a 45 s time step. On the local SGI it was coupled hourly to the 4 km ALARO and ran to +36 h (2021 sources); the 2023 and 2024 ASW posters document it at **+48 h**, and on ATOS it runs as the **AO13** suite coupled **hourly to ECMWF HRES** with 3-hourly surface DA. A retuned configuration (improved TTE solver, updated roughness-length climatology) was introduced in 2021. *(Grid given as 576 × 576 in 2021 sources vs 600 × 600 in the 2023/2024 posters; forecast range +36 h in 2021 vs +48 h in 2023/2024 — flagged.)*
  - A **1.3 km / 600 × 600 / L87 AROME-Belgium** (45 s time step, +48 h forecasts at 00/06/12/18 UTC), the **AR13** suite on ATOS, coupled **hourly to ECMWF HRES** with 3-hourly surface DA. As of late 2021 it ran with **CANARI-OImain surface DA** only; by the 2024 ASW poster RMI reported an **experimental 3D-Var RUC** adding upper-air assimilation of SYNOP, AMDAR, TEMP and GNSS (3 h CANARI + 3D-Var, with IAU), with an outlook to 1-hourly cycling, radar DA, and e-suite promotion in summer 2024 (see Data assimilation). This resolves the earlier open question about AROME coupling: on the local SGI it was nested in the 4 km ALARO (ARPEGE-driven), whereas the ATOS AR13 suite couples directly to ECMWF HRES. *(Grid given as 576 × 576 in 2021 sources vs 600 × 600 in the 2023/2024 posters.)
  - A **1.3 km / 576 × 576 / L87 AROME-Belgium** (45 s time step, +48 h forecasts at 00/06/12/18 UTC) nested in the 4 km ALARO with hourly coupling. As of late 2021 it ran with **CANARI-OImain surface DA** only (no operational upper-air DA), with 3 h 3D-Var being tested and additional observation types (AMDAR, TEMP, GNSS, Mode-S, radar reflectivities) under monitoring or testing for assimilation. The 2024 RMI Seamless Prediction Programme talk describes a 3 h DA cycle as in operation. Note that the AROME-Belgium operational chain at RMI in 2021 was coupled to the 4 km ALARO (ARPEGE-driven), not directly to IFS as some later RMI presentations describe — coupling was reorganised between then and 2024.
  - The **INCA-BE** nowcasting system (1 km, two parallel versions backed by ALARO and AROME respectively).
  - The **pySTEPS-BE** ensemble nowcast (5-minute time step out to +6 h, 48 members) and the seamless probabilistic system being developed under RMI's *Seamless Prediction Programme* (project IMA).
  - A **1.3 km mini-EPS** combining the high-resolution ALARO and AROME runs, identified in the 2021 DAsKit progress report as part of the *Seamless* and *FULLKOST* projects.
  None of those internal systems is openly distributed under the `alaro_40l` dataset.
- ALARO is also used at RMI for **regional climate** runs over Belgium and Europe (CORDEX.be I, CORDEX.be II, EURO-CORDEX), at resolutions from 25 km down to 1.3 km, again using CY43T2 + ALARO-1. These climate runs are research products distinct from the operational NWP described here.
- For the parent global model, see [ARPEGE Global](../../global/france/arpege-global.md). For other ALARO-based operational deployments in the consortium, see [ALADIN Slovakia](../slovakia/aladin-slovakia.md). For convection-permitting NWP coverage of the same broader region, see [HARMONIE-AROME Netherlands](../netherlands/harmonie-arome-netherlands.md), whose Dutch domain extends well into Belgium.

---

## Recent version history

### 2024 — 3D-Var "in preparation"; first ML-EPS experiments
By the 30 Sep–3 Oct 2024 national poster, the ATOS suites (cy43t2; 4 km +60 h ARPEGE-coupled, 1.3 km +48 h HRES-coupled, 30+1 nodes) ran with surface assimilation and **3D-Var "in preparation."** RMI also reported first exploratory **machine-learning EPS** work aimed at a lightweight ensemble over the Belgian domain — training ECMWF's **Anemoi** framework on the CERRA regional reanalysis and a **Neural-LAM** ALARO emulator on archived local ALARO analyses/forecasts. These remain exploratory and are not part of the public `alaro_40l` product. *(The April 2024 ASW poster had described an experimental AROME 3D-Var RUC with an outlook to e-suite by summer 2024; the autumn "in preparation" wording suggests it had not yet gone operational — flagged.)*

### 2024 — Operational on ATOS; experimental AROME 3D-Var RUC
By the 4th ACCORD ASW the suites were running operationally on ATOS (tc2, 30+1 nodes), with the 9-year-old local SGI retained only as a backup chain (ARPEGE-coupled) and a possible ECMWF-coupled "mini-EPS" under consideration. RMI also reported an experimental AROME 3D-Var rapid-update cycle assimilating SYNOP, AMDAR, TEMP and GNSS (3 h CANARI + 3D-Var, with IAU), targeting a 1-hourly cycle, radar DA, and e-suite promotion during summer 2024.

### 2024 — Seamless Prediction Programme
Work on RMI's *Seamless Prediction Programme* (project IMA) was reported, integrating ALARO and AROME with INCA-BE and pySTEPS-based blending to provide a probabilistic, observation-driven, convection-permitting forecasting system from +5 min out to +24 h. This concerns the broader RMI forecasting suite rather than the public `alaro_40l` dataset specifically.

### 2023 — Operational suites ported to ECMWF ATOS
Facing end-of-life of the local SGI cluster, RMI began moving its three operational suites to ECMWF's ATOS infrastructure in Bologna as a Time Critical facility: **AO40** (4 km ALARO, +60 h, hourly ARPEGE LBCs, downscaling), **AO13** (1.3 km ALARO, +48 h, 3 h surface DA, hourly HRES LBCs) and **AR13** (1.3 km AROME, +48 h, 3 h surface DA, hourly HRES LBCs), all on CY43T2 and scripted with NodeRunner. The cy43t2 BATOR code was adapted with Harmonie code to ingest BUFR-SYNOP data at ECMWF.

### 2021 — Tuned 1.3 km ALARO ("MODC")
A retuned 1.3 km configuration with revised dynamics/physics tunings (provided via cooperation with CHMI), an improved Total Turbulent Energy (TTE) solver, and an updated roughness-length climatology was put into daily use in September 2021. The 2022 ASW poster reports it **became operational in December 2021**. RMSE improvements were seen across mean sea-level pressure, 10 m wind, and 2 m temperature, together with reduced cold (winter) and warm (summer) biases. *(The 2021 national poster described operationalisation as planned "by the end of April," with no year given — flagged as inconsistent with the December 2021 operational date in the 2022 poster.)*

### 2021 — ALARO-1 / CY43T2 operational at RMI
The 4 km operational chain ran CY43T2 with ALARO-1 (3MT with non-saturated downdraft, TOUCANS turbulence, ACRANEB2 radiation) on a 432 × 432 / L87 grid with a 180 s time step, ARPEGE-coupled (hourly as of September 2021) and producing +60 h forecasts at 00, 06, 12 and 18 UTC. A 1.3 km / 576 × 576 / L87 high-resolution ALARO (45 s time step, hourly coupling to the 4 km ALARO, +36 h forecasts) and a 1.3 km / 576 × 576 / L87 AROME with the same dynamical settings (+48 h forecasts, surface assimilation via CANARI-OImain, no operational upper-air DA) ran alongside it.

---

## Official documentation
- RMI open-data dataset documentation (parameter tables): https://opendata.meteo.be/documentation/?dataset=alaro
- RMI open-data portal: https://opendata.meteo.be/
- Dataset metadata (RMI catalogue): https://opendata.meteo.be/geonetwork/srv/eng/catalog.search#/metadata/RMI_DATASET_ALARO
- Dataset metadata (geo.be federal catalogue): https://www.geo.be/catalog/details/RMI_DATASET_ALARO
- ALARO model page (RMI): https://www.meteo.be/en/research-rmi/scope-of-research/numerical-model-alaro/numerieke-weersvoorspellingen
- Public ALARO charts: https://www.meteo.be/en/weather/forecasts/weather-model-alaro

### Key references
- Termonia, P. et al. (2018). *The ALADIN System and its canonical model configurations AROME CY41T1 and ALARO-0 CY40T1.* Geoscientific Model Development, 11, 257–281. DOI: 10.5194/gmd-11-257-2018
- De Troch, R. et al. (2013). *Multiscale performance of the ALARO-0 model for simulating extreme summer precipitation climatology in Belgium.* Journal of Climate, 26, 8895–8915.
- Caluwaerts, S. et al. (2021). *The operational ALADIN-Belgium model.* RMI poster, Belgium National contribution (April 2021).
- Dehmous, I. (2021). *DAsKit progress at RMI (Belgium).* 2021 Joint LACE Data Assimilation & DAsKit Working Days, Ljubljana, 22–24 September 2021.
- Reyniers, M., Van Ginderachter, M., De Cruz, L. et al. (2024). *The Seamless Prediction Programme at the Royal Meteorological Institute of Belgium.* EUMETSAT Integrated Nowcasting Workshop, 23–25 January 2024, Darmstadt, Germany.
- Dewettinck, W. et al. (2024). *State of regional climate modelling in Belgium with the ALARO model.* ACCORD All-Staff Workshop, 2024.
- Caluwaerts, S. et al. (2023). *The operational ALADIN-Belgium model.* RMI national poster, 3rd ACCORD All-Staff Workshop, Tallinn, March 2023.
- Caluwaerts, S. et al. (2024). *The operational ALADIN-Belgium model.* RMI national poster, 4th ACCORD All-Staff Workshop, Norrköping, April 2024.
- Caluwaerts, S. et al. (2022). *The operational ALADIN-Belgium model.* RMI national poster, 2nd ACCORD All-Staff Workshop, April 2022. *(venue TBD)*
- RMI Modelling team (2024). *The operational ALADIN-Belgium models.* RMI national poster, 30 Sep–3 Oct 2024. *(venue/workshop TBD — dates match the 46th EWGLAM / 31st SRNWP meeting)*
