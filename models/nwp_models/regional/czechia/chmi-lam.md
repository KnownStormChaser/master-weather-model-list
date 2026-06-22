# ALADIN (CHMI – Czech Republic)

## What this model is
ALADIN/CHMI is the operational regional limited-area numerical weather prediction (NWP) system run by the Czech Hydrometeorological Institute (CHMI) for short-range forecasting over Czechia and Central Europe.

It is a non-hydrostatic configuration of the **ALADIN system** with **ALARO-1vB physics**, developed and maintained as part of the Regional Cooperation for Limited Area Modeling in Central Europe (**RC LACE**) consortium. The forecast is run on a single Central-European Lambert-projection domain at ~2.3 km and is openly distributed in two forms: the **native Lambert_2.3km** grid and a **CZ_1km** post-processing reprojection (~1 km regular lat-lon raster) covering Czechia and immediate surroundings.

Internally, CHMI also generates **operational biometeorological diagnostics** (mean radiant temperature and Universal Thermal Climate Index) — uncommon among national ALADIN deployments and used in CHMI's biometeorological forecasting and heat-health warning service — though these particular fields are not part of the open-data feed.

---

## Who runs it
- **Organization:** Czech Hydrometeorological Institute (Český hydrometeorologický ústav, ČHMÚ)
- **Country / region:** Czech Republic
- **Consortium:** RC LACE (with ALADIN, HIRLAM, and ECMWF cooperation; now under the wider ACCORD umbrella)

---

## What area it covers
- **Coverage:** Central Europe, with Czechia near the centre
- **Distributed grids:**
  - **Lambert_2.3km** — the **native model grid** ("internal computational domain"); Central Europe on a Lambert projection, 1069 × 853 grid points, ~2.3 km spacing. Includes both surface and pressure-level fields.
  - **CZ_1km** — a **post-processing product**: the same forecast bilinearly reprojected onto a regular geographic-coordinates raster at ~1 km, covering Czechia and immediate surroundings. Surface fields only (no pressure levels), and a slightly reduced parameter set vs. Lambert_2.3km.

---

## Basic details
- **Model type:** Regional deterministic NWP (limited-area)
- **Model system / core:** ALADIN, with **ALARO-1vB** physics package
- **Dynamical formulation:** Non-hydrostatic (NH), spectral, semi-Lagrangian advection, semi-implicit time integration; ICI scheme with 1 iteration
- **Convection-allowing:** Yes (deep convection explicitly resolved at the 2.3 km native resolution)
- **Native horizontal resolution:** ~2.3 km (Lambert projection, 1069 × 853 grid points, linear truncation E539 × 431)
- **Public distribution grids:** Lambert_2.3km (native) and CZ_1km (~1 km regular lat-lon raster, post-processing reprojection over Czechia)
- **Vertical levels:** 87 (mean orography)
- **Time step:** 90 s
- **Forecast length:** Up to **+72 h** for the four publicly distributed cycles (00, 06, 12, 18 UTC), all to +72 h since January 2025 (previously the 18 UTC run went only to +54 h). The intermediate operational cycles (03, 09, 15, 21 UTC) run only to the end of the following day and are **not** publicly distributed — see Update frequency.
- **Update frequency:** **Operationally 8× daily since 10 June 2025** — the four main synoptic cycles (00, 06, 12, 18 UTC, to +72 h) plus four intermediate cycles (03, 09, 15, 21 UTC, to end of the following day). **Only the four main synoptic cycles are published on the open-data portal**; the four intermediate cycles run operationally but are not openly distributed (portal directory structure exposes `{00,06,12,18}` only; confirmed against the live portal, June 2026).
- **Temporal output resolution:** Hourly
- **Cycle / model code version:** ALADIN cycle **48t3mas_op1** (base cycle **cy48t3**, operational since 18 December 2025); previous operational cycles: 46t1as_op1 (2024–2025), 46t1mp (2023–2024), 43t2ag (2022)

---

## Data assimilation
- **Surface analysis:** Optimal interpolation (CANARI) using GTS SYNOP and national SYNOP (T2m, RH2m); SST treatment is by relaxation toward the LBC0 (ARPEGE) field; snow analysis with relaxation to climatology suppressed since February 2024 to retain realistic snow amounts. The 3-hourly surface analysis is run with a CHMI-specific local tuning (sun-declination-modulated soil moisture increments, `LISSEW=T` averaging of soil-moisture increments, halved climatology relaxation `RCLIMCA=0.0225`, no snow climatology relaxation `RCLIMSN=0`); these settings live as a local modset rather than common ALADIN code.
- **Upper-air analysis:** **BlendVar** — digital-filter spectral blending (Brožková et al., 2001) at truncation E102 × 81, followed by **3D-Var** (Fischer et al., 2005). Background-error covariance B is sampled from the **AEARP** Ensemble Data Assimilation system (Météo-France), with `REDNMC=0.5` (replacing the earlier spin-up-ensemble approach with `REDNMC=1.7`).
- **Cycling:** Hourly analysis system (VarCan Pack); long cut-off cycle was tightened from 6 h to **3 h** in February 2024
- **Initialisation:** Incremental DFI in the short cut-off production analysis; no DFI in the long cut-off cycle (`±1.5 h` analysis window, VARBC 24-hour cycling)
- **Assimilated observations:** SYNOP (Ps), TEMP (t, q, u, v), AMDAR (t, u, v), **Mode-S** (MRAR data over Czechia and EHS data routed via the KNMI portal, processed under the European EMADDC service since 2022), **SEVIRI** WV channels 2 and 3 (reintroduced September 2024 after a March 2023 – September 2024 withdrawal triggered by the Meteosat-10 / Meteosat-11 swap), HR-AMV, wind profilers, ASCAT
- **Radar reflectivity assimilation:** OPERA volume reflectivity 1D+3D-Var assimilation (Wattrelot et al., 2014), **operational since 28 April 2025** (preceded by the e-suite ALE from February 2025)

---

## Initial and boundary conditions
- **Initial conditions:** Own BlendVar + CANARI analysis
- **Lateral boundary conditions:** **ARPEGE** (Météo-France global model); space-consistent coupling. **⚠ Coupling-interval conflict:** a single source — the 2024 RC LACE DAWD poster — documents a **1-hour** coupling interval, while **nine CHMI ASW / EWGLAM / national posters spanning 2021–2026** all state a **3-hour** coupling interval in the NWP-system box. The weight of evidence favours **3 h**; the lone 1 h figure is treated as a likely DAWD-poster anomaly. Left flagged pending positive confirmation.

---

## What it provides
Deterministic forecasts on the model grid, with the following parameters distributed publicly. The two distributed grids share most surface fields; the **Lambert_2.3km** grid additionally carries pressure-level fields and a few surface variables not present on **CZ_1km**.

**Surface fields (both Lambert_2.3km and CZ_1km):**
- 2 m temperature (incl. hourly maxima/minima at 6 and 18 UTC), dew point, wet-bulb temperature, relative humidity
- 10 m wind speed and direction
- maximum 1-hour wind gust (Lambert: split as U and V components; CZ_1km: combined gust speed)
- mean sea-level pressure, surface temperature, surface geopotential
- low / mid / high / total cloud cover
- minimum 1-hour visibility — split into a cloud/fog-driven value and a precipitation-driven value
- ventilation index
- downward shortwave (global), longwave, and direct shortwave radiation; sunshine duration (all accumulated from forecast start)
- snow water equivalent
- accumulated rainfall, snowfall, and total precipitation
- most-probable and most-dangerous 1-hour precipitation type (numerical index covering drizzle, rain, mixed, freezing rain, sleet, dry/wet snow, graupel, hail, etc., with separate codes for "occasional" vs persistent)
- Most-Unstable CAPE
- lightning flash density (accumulated)

**Lambert_2.3km only:**
- land–sea mask
- convective inhibition (CIN)
- maximum simulated reflectivity in the vertical column (converted to mm/h)
- pressure-level fields on **17 isobaric levels** (1000, 950, 925, 850, 800, 700, 600, 500, 450, 400, 350, 300, 275, 250, 200, 150, 100 hPa): geopotential, temperature, U and V wind, relative humidity, wet-bulb potential temperature, vertical velocity (omega)

Internally CHMI also runs a richer convective-diagnostics package for forecaster use (Storm Relative Helicity, updraft/downdraft helicity tracks, MUCAPE/CIN/moisture-convergence and CAPE/wind-shear combinations) and the biometeorological diagnostics noted above; these are not part of the open-data feed.

---

## Data availability
- **Is the data free?** Yes
- **License:** **Creative Commons Attribution 4.0 International (CC BY 4.0)** — per CHMI's open-data FAQ ("You can use ČHMÚ open data free of charge while respecting the Creative Commons BY 4.0 license"; https://en.chmi.cz/web/chmi.cz/-/jak-mohu-pou%C5%BE%C3%ADvat-otev%C5%99en%C3%A1-data-%C4%8Dhm%C3%BA-). Attribution required.
- **Is the data downloadable?** Yes
- **Data formats:** GRIB (distributed as **bzip2-compressed `.grb.bz2`** files; one file per parameter per cycle, containing all forecast steps for that parameter)
- **Official download location:**  
  https://opendata.chmi.cz/meteorology/weather/nwp_aladin/  
  with sub-directories `Lambert_2.3km/{00,06,12,18}/` and `CZ_1km/{00,06,12,18}/`
- **Reference files at the same location:**
  - `Popis_obsahu.xlsx` — content description with one sheet per distributed grid (Lambert, CZ1K), listing parameter codes, physical quantity, units, and the precipitation-type index table (in Czech)
  - `grib2table` and `gribtab` — GRIB code tables for the distributed parameters (consumable by `wgrib2` and `wgrib` respectively)

---

## Notes
- The current high-resolution Lambert_2.3km configuration has been operational since **February 2019**, replacing the previous 4.6 km version with improved resolution, orography, and radiation diagnostics. **CZ_1km is not a separate model integration** — it is a downstream post-processing product produced by reprojecting the same Lambert_2.3km forecast onto a regular ~1 km lat-lon raster over Czechia, with a reduced parameter list (surface only).
- **Biometeorological outputs** (mean radiant temperature, UTCI) and the richer convective-diagnostics package described in the 2023–2025 RC LACE / ACCORD posters are computed operationally inside CHMI for forecaster use (and were the subject of Novák 2021), but are **not** included in the public `opendata.chmi.cz` ALADIN feed.
- **Near-term DA development** documented at the RC LACE Data Assimilation Working Days 2024 (snow-depth status updated per the 2026 ACCORD ASW national poster):
  - **Snow-depth analysis** for ALARO via the ISBA scheme — developed jointly with **Florian Meier (GeoSphere Austria)**. Three assimilation configurations were tuned with the Nelder–Mead simplex method: **FR** (Météo-France-style: Gaussian correlation function, default first-guess QC, no spatial QC, rejects observations above 1500 m), **AT-gauss** (Gaussian correlation function, adapted height-dependent first-guess QC, spatial QC enabled, uses data above 1500 m), and **AT-Mescan** (adapted MESCAN correlation function, first-guess QC with height-dependent observation-error σ, spatial QC enabled, uses data above 1500 m). All three improve snow-depth forecasts over both the no-snow-analysis reference and the default FR setting; the extra snow introduces a short-range screen-level cold/moist bias, to be mitigated by adjusting the grid-box snow fraction before operational implementation. *(Supersedes the earlier 2024 DAWD note on CANARI+ISBA snow DA and the `LAOROFLEXREJSN` rejection-limit test.)*
  - **OPERA Nimbus** reflectivity processing chain validated in parallel against the legacy OIFS chain at CHMI (most stations >95 % availability; Swiss and British radars showed nodata/undetect attribute discrepancies).
  - **Doppler radial-wind** assimilation under development (separate from the volume-reflectivity work).
- **Companion / related ALADIN deployments in the consortium** share much of the same code base and tunings; cross-references to other entries in this repository:
  - [ALADIN Slovakia](../slovakia/aladin-slovakia.md) — RC LACE sister system; SST treatment and L63→L87 cloud retuning developed jointly with CHMI.
  - [ALARO Belgium](../belgium/alaro-belgium.md) — RMI 1.3 km tunings provided via cooperation with CHMI.
- Operational HPC: NEC SX Aurora TSUBASA. Since **10 March 2026** a newer system is operational — **56 nodes**, each with one AMD ROME 7443P CPU (24 cores, 512 GB RAM) + 8× NEC Vector Engine 20B (8 cores, 48 GB each); **1344 VH + 3584 VE cores** total — replacing the earlier 48-node configuration (AMD EPYC 7402; 1152 VH + 3072 VE cores). A NEC LX series cluster (320 Intel Broadwell nodes) was also in use through 2024. Workflow management via ecFlow.
- For the parent global model that supplies LBCs, see [ARPEGE Global](../../global/france/arpege-global.md).

---

## Recent version history

### March 2026 — operational switch to new HPC
Operations moved to a newer NEC SX Aurora TSUBASA (56 nodes; AMD ROME 7443P + 8× NEC Vector Engine 20B per node; 1344 VH + 3584 VE cores) on 10 March 2026, replacing the 48-node AMD EPYC 7402 system.

### December 2025 — cy48t3 model release
The operational model was upgraded to base cycle **cy48t3** (cycle string `48t3mas_op1`, ALARO-1vB) on 18 December 2025.

### September 2025 — rain size distribution reverted to Marshall–Palmer
The Marshall–Palmer rain size distribution was reinstated in place of the Abel & Boutle (2012) distribution (reversing the October 2024 change), together with retuning of microphysics parameters and of cloudiness in the radiation scheme (23 September 2025).

### June 2025 — eight production cycles per day
Four intermediate production cycles (03, 09, 15, 21 UTC, forecasts to the end of the following day) were added to the four main synoptic cycles, bringing operations to 8 runs/day (10 June 2025). **The four intermediate cycles are not published on the open-data portal.**

### April 2025 — OPERA radar reflectivity DA operational
The 1D+3D-Var assimilation of OPERA volume radar reflectivity (Wattrelot et al., 2014) became operational on 28 April 2025, following the February 2025 e-suite (ALE).

### February 2025 — radar reflectivity DA e-suite
The **e-suite ALE** introduced 1D+3D-Var assimilation of OPERA volume radar reflectivity, using the Wattrelot et al. (2014) method with the Abel & Boutle (2012) rain size distribution in the observation operator (consistent with the ALARO microphysics) and a threshold-method + error-inflation treatment to mitigate excessive drying from undetected pixels.

### January 2025 — 18 UTC cycle extended to +72 h
The 18 UTC production cycle was prolonged from +54 h to +72 h, bringing all four daily cycles to the same forecast length.

### October 2024 — Abel & Boutle rain size distribution
The Abel & Boutle (2012) rain size distribution was implemented for simulated reflectivity diagnostics and the radar reflectivity observation operator, replacing the earlier Marshall–Palmer distribution and giving more realistic reflectivities in light rain (lower) and heavy rain (higher).

### September 2024 — SEVIRI water-vapour channels reintroduced
SEVIRI water-vapour channels (channels 2 and 3) were reintroduced into the data-assimilation observation set after an ~18-month withdrawal. SEVIRI had been **withdrawn in March 2023** following the Meteosat-10 / Meteosat-11 instrument exchange, which produced a ~1 K bias with unusually large diurnal variations. Reintroduction used a **cold-start VARBC with increased adaptivity** to absorb the persistent bias signature.

### February 2024 — long cut-off cycle to 3 h, snow / surface assimilation retuning
The long cut-off assimilation cycle was tightened from 6 h to **3 h**. Surface analysis settings were retuned: relaxation to climatology was suppressed for snow (recovering realistic snow amounts), the snow-roughness treatment was switched to `LZ0SNOWH=T` / `RZ0_TO_HEIGHT=0.1` (improving 10 m wind bias over forested snow-covered areas), and the snow-fraction parameter `WCRIN` was raised from 4 to 10 (reducing 2 m cold bias). The Lopez evaporation parameterization and retuned auto-conversions to snow and cloud water were also introduced. A new **wind-gust diagnostic based on TKE at 20 m** was introduced in the same change (per the 2024 national poster; not listed on the ASW 2024 poster). ⚠ *Not confirmed whether the publicly distributed "maximum 1-hour wind gust" parameter now derives from this diagnostic — to verify against the open-data feed.*

### May 2023 — new convective diagnostics
A revised convective-diagnostics package became operational, in cooperation with CHMI's convection-specialist team. New products include storm relative helicity, updraft and downdraft helicity tracks, and combinations of MUCAPE/CIN/moisture convergence and CAPE/wind shear, useful for distinguishing squall lines from supercells and for tornado-environment indicators.

### April 2023 — model cycle upgrade to CY46T1mp
The operational model cycle was upgraded to **CY46T1mp** (ALARO-1vB), operational on **19 April 2023** following the **e-suite AKW** technical switch on 27 February 2023.

### May 2022 — prognostic graupel operational
The ALARO microphysics scheme was upgraded with **fully prognostic graupel**, replacing the earlier pseudo-prognostic treatment. A new graupel fall-speed relation (matching ICE3-style behaviour) was introduced together with retuned precipitation-type thresholds for small and big hail. This change also enabled a straightforward lightning-density diagnostic. The change ran as **e-suite AKV from 15 February 2022** and became **operational on 9 May 2022**.

### January 2022 — Mode-S EHS via EMADDC
Mode-S EHS aircraft observations from the previous KNMI/MUAC airspace (Belgium, Netherlands, Luxembourg, Germany) were replaced by **European-wide EMADDC** processing, with shorter assimilation window (1 h, ±30 min around analysis) reflecting the very high data frequency.

### February 2019 — current 2.3 km / L87 non-hydrostatic configuration
The non-hydrostatic 2.3 km, 87-level configuration with ALARO-1vB physics replaced the previous 4.6 km version. The mean radiant temperature diagnostic was added in autumn 2018, followed by the Universal Thermal Climate Index (UTCI) in January 2019.

---

## Official documentation
- CHMI open-data portal (NWP ALADIN): https://opendata.chmi.cz/meteorology/weather/nwp_aladin/
- Novák, M. (2021): *UTCI as the NWP model ALADIN (CHMI) output – first experiences*, Geographia Polonica 94(2), 237–249. https://doi.org/10.7163/GPol.0203
- Bučánek, A., Trojáková, A., Benáček, P. (2018): *Data assimilation activities at CHMI*, RC LACE DAWD + DAsKIT, Bucharest.
- Trojáková, A. et al. (2022): *Numerical Weather Prediction activities at CHMI*, ACCORD All-Staff Workshop poster.
- Brožková, R., Bučánek, A., Mašek, J., Němec, D., Smolíková, P., Trojáková, A. (2023): *Numerical Weather Prediction activities at CHMI*, EWGLAM 2023 poster.
- Brožková, R. et al. (2024): *Numerical Weather Prediction activities at CHMI*, ACCORD All-Staff Workshop 2024 poster.
- Bučánek, A., Trojáková, A., Brožková, R. (2024): *Data assimilation activities CHMI*, RC LACE Data Assimilation Working Days 2024.
- Brožková, R., Bučánek, A., Mašek, J., Němec, D., Šljivić, A., Ševčík, J., Smolíková, P., Trojáková, A. (2025): *Numerical Weather Prediction activities at CHMI*, 5th ACCORD All-Staff Workshop poster, Zalakaros.
- Brožková, R., Bučánek, A., Derková, M., Imrišek, M., Mašek, J., Němec, D., Otruba, A., Šljivić, A., Ševčík, J., Smolíková, P., Trojáková, A. (2026): *Numerical Weather Prediction activities at CHMI*, ACCORD All-Staff Workshop national poster. *(Exact workshop number/venue to confirm.)*

### Key references
- Termonia, P. et al. (2018): *The ALADIN System and its canonical model configurations AROME CY41T1 and ALARO-0 CY40T1*, Geosci. Model Dev. 11, 257–281. https://doi.org/10.5194/gmd-11-257-2018
- Wang, Y. et al. (2018): *27 years of regional cooperation for limited area modelling in Central Europe (RC LACE)*, Bull. Amer. Meteor. Soc. 99, 1415–1432. https://doi.org/10.1175/BAMS-D-16-0321.1
- Wattrelot, E., Caumont, O., Mahfouf, J.-F. (2014): *Operational implementation of the 1D+3D-Var assimilation method of radar reflectivity data in the AROME model*, Mon. Wea. Rev. 142, 1852–1873.
- Abel, S.J., Boutle, I.A. (2012): *An improved representation of the raindrop size distribution for single-moment microphysics schemes*, Q. J. R. Meteorol. Soc. 138, 2151–2162.
