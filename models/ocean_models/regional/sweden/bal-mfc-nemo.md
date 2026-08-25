# BAL MFC-NEMO (Baltic Sea Physics Analysis and Forecast)

## What this model is
The Baltic Sea Physics Analysis and Forecast is the operational ocean physics product of the Copernicus Marine **Baltic Monitoring and Forecasting Centre (BAL MFC)**, produced at the **Swedish Meteorological and Hydrological Institute (SMHI)**. It is a NEMO 4.2.1 configuration at 1 nautical mile, delivered on the model's **native vertical grid** of 56 levels, with data assimilation by **PDAF** — the only ensemble-filter assimilation in the Copernicus Marine regional physics portfolio apart from TOPAZ5.

The system is not a standalone ocean model. NEMO, the ERGOM biogeochemistry (`BALTICSEA_ANALYSISFORECAST_BGC_003_007`), and the WAM wave model ([BALWAM](../../../wave_models/regional/finland/balwam.md)) run as **one coupled production**, with the physics and biogeochemistry online-coupled through FABM and a two-way field exchange with WAM. The PUM covers the physics and biogeochemistry products together for exactly this reason.

The official Copernicus Marine product identifier is `BALTICSEA_ANALYSISFORECAST_PHY_003_006`. The PUM calls the configuration **BAL MFC-NEMO** and versions the production as **EIS202411**; there is no shorter operational name.

> **The product page description is out of date.** It still describes a 10-day forecast from the 00Z cycle and 6 days from 12Z. Since the **November 2025 upgrade both cycles run 9 days**, each preceded by a 12-hour re-analysis. The same staleness affects the [BALWAM](../../../wave_models/regional/finland/balwam.md) product page and is flagged there too.

*Live-verified 2026-08-25 from Marine Data Store STAC records and ARCO Zarr metadata.*

---

## Who runs it
- **Production Unit:** Swedish Meteorological and Hydrological Institute (**SMHI**), Sweden — the PUM states production is performed at SMHI, and the Copernicus Marine producer of record is "SMHI (Sweden)"
- **Country:** Sweden; domain covers multi-national Baltic and transition waters
- **Programme or coordinating body:** Copernicus Marine Service — Baltic Monitoring and Forecasting Centre (BAL MFC), a consortium whose model configuration is tuned collectively. PUM contributors span SMHI, BSH, and DMI.
- **Model provenance:** ERGOM originates at IOW (Germany) and was extended at BSH; the NEMO configuration is tuned within the BAL MFC consortium
- **Role in any larger system:** supplies lateral boundary conditions to [NEMO-EST](../../regional/estonia/nemo-est.md); supplies ice concentration and surface currents to [BALWAM](../../../wave_models/regional/finland/balwam.md), which returns Stokes drift

---

## What area it covers
- **Coverage:** Baltic Sea, Gulf of Bothnia, Gulf of Finland, Gulf of Riga, Baltic Proper, and the Danish straits / Kattegat transition waters
- **Domain bounds (documented):** 9°E – 30°E, 53°N – 66°N
- **Domain bounds (live-verified):** 9.0416°E – 30.2087°E, 53.0083°N – 65.8910°N
- **Grid dimensions (live-verified):** **763 × 774** (longitude × latitude), regular lat-lon
- **Grid spacing (live-verified):** 0.0277783° longitude × 0.0166658° latitude — the PUM's "1′40″ or 0.028°" and "1′ or 0.017°", i.e. approximately 1 nautical mile
- **Native grid:** staggered Arakawa C-grid. Scalars sit at T-points; **velocity components are de-staggered and interpolated to T-points in the delivered product**, so all variables share one grid.
- **Special masked or excluded regions:** land and fill value **−999**

> **The model domain is larger than the delivered domain.** The NEMO configuration extends across the North Sea — its two open boundaries are at Orkney–Norway and the English Channel — but the product delivered to the Marine Data Store is a **Baltic cutout**. The North Sea portion of the run is not distributed here; it is covered by the NWS MFC products. This is the same relationship the [FMI open-data feed](../../regional/finland/nemo-baltic-fmi.md) has to its own parent run.

---

## Basic details
- **Model type:** Regional ocean physics + sea ice; deterministic; online-coupled to biogeochemistry, two-way exchange with a wave model
- **Core ocean model:** **NEMO 4.2.1** (upgraded from NEMO 4.0.4 in November 2024)
- **Sea ice model:** NEMO's sea ice component, **reduced from 5 ice thickness categories to 1** in the November 2024 upgrade — explicitly "to better suit the data assimilation which only affects one ice class." An unusual trade: assimilation tractability bought at the cost of the ice thickness distribution.
- **System name:** BAL MFC-NEMO; production version **EIS202411**; coupled system version `NEMO 4.2.1 – ERGOM_v202405`
- **Horizontal resolution:** ~1 nautical mile (~1.85 km)
- **Vertical levels:** **56 levels, 0.50 m – 712.02 m** — delivered on the **model's native vertical grid**, not interpolated to standard depths. Live-verified level list: 0.50, 1.52, 2.55, 3.60, 4.68, 5.80, 6.96, 8.17, 9.45, 10.81, 12.27, 13.86, 15.60, 17.53, 19.70, 22.15, 24.94, 28.15, 31.86, 36.15, 41.13, 46.91, 53.59, 61.28, 70.08, 80.07, 91.31, 103.82, 117.62, 132.67, 148.90, 166.25, 184.60, 203.86, 223.92, 244.66, 265.99, 287.81, 310.05, 332.64, 355.51, 378.61, 401.91, 425.36, 448.93, 472.61, 496.36, 520.19, 544.06, 567.98, 591.94, 615.92, 639.92, 663.94, 687.98, 712.02 m — exactly matching the PUM
- **Vertical coordinate:** z-level on the native NEMO grid
- **Forecast length:** **9 days (216 h) from both cycles**, each preceded by a **12-hour re-analysis**, giving a 228-hour series per production
- **Update frequency:** 2× daily
- **Production cycles:** 00Z and 12Z. The 00Z run starts at 12Z the previous day; the 12Z run starts at 00Z the same day.
- **Target delivery time:** **10 UTC and 22 UTC** (for the 00Z and 12Z analyses respectively)
- **Temporal output resolution:** 15-minute instantaneous (surface), hourly instantaneous, daily mean, de-tided daily mean, monthly mean
- **Archive availability:** documented as a 2-year rolling window; **live datasets currently reach back to 2022-12-02**, i.e. nearly four years — see *Notes*
- **Bathymetry source:** **GEBCO 2014**, at 1 nautical mile, shared by NEMO, ERGOM, and WAM
- **Initial conditions (spin-up):** spin-up from 1 January 2020 using a restart field from `BALTICSEA_MULTIYEAR_PHY_003_011`

---

## Forcing
- **Atmospheric forcing:** **MetCoOp-HARMONIE 2.5 km control member** for the early forecast, then **ECMWF ~11 km deterministic** thereafter. HARMONIE does not cover the full model domain, so the two are blended for area coverage even in the early range. HARMONIE wind forcing is hourly; ECMWF forcing degrades from hourly (67–90 h) to 3-hourly (93–144 h) to 6-hourly for the final three days. *The PUM gives the handover point as both "the first 2.5 days" and "the first 66 hours" — see* Notes.
- **River runoff:** daily real-time discharge, temperature, and nutrients from SMHI's operational **E-HYPE** hydrological model
- **Lateral boundary conditions:** the Copernicus Marine **North West Shelf** physics forecast, cited in the PUM by its legacy identifier `NORTHWESTSHELF_ANALYSIS_FORECAST_PHY_004_013` (catalogue form: `NWSHELF_ANALYSISFORECAST_PHY_004_013`), applied at Orkney–Norway and the English Channel. For the final forecast day(s) several boundary parameters are **held constant** to guarantee timely delivery.
- **Tidal forcing:** tides at the open boundaries come "**from another source**" — the PUM does not name it (**TBD**). Baltic tides are small, but the product ships de-tided datasets, so a tidal signal is present and its origin matters.
- **Ice forcing or coupling:** sea ice modelled internally by NEMO
- **Wave forcing:** **Stokes drift read in from the BAL MFC WAM output** — see *Coupling*

---

## Coupling
- **Ocean–biogeochemistry:** ERGOM is **online-coupled** to NEMO through the **FABM** coupler in the TOP module. The two run as a single executable; the PUM documents both products together for this reason.
- **Ocean–wave:** **two-way exchange with WAM**, though not online. NEMO ingests **Stokes drift** from the WAM simulation; WAM ingests **ice concentration and surface currents** from the NEMO production. This is a genuine bidirectional dependency between this product and [BALWAM](../../../wave_models/regional/finland/balwam.md) — see *Relationship to other entries*, where it corrects existing repository text.
- **Ocean–sea ice:** internal to NEMO
- **Atmosphere:** one-way forcing; no coupled atmosphere

---

## Data assimilation
- **DA scheme:** **PDAF** (Parallel Data Assimilation Framework) with the **PDAF_OMI** observation-module infrastructure, running the **LESTKF** filter (Local Error Subspace Transform Kalman Filter). Upgraded to a newer AWI-developed version in November 2023.
- **Update cycle:** at model start of each production cycle — i.e. twice daily
- **Mode:** univariate when reading observations and creating increments, though the first guess and ensemble read several variables
- **Localisation:** **spatially varying localisation radius** implemented for T/S profile assimilation by supplying a 2-D radius field through a NetCDF file, extending PDAF_OMI beyond its native constant-radius support. Sea ice concentration assimilation uses a **constant** radius, chosen during tuning.
- **Quality control:** for temperature, salinity, and SST, observations differing from the first guess by more than 2 (units unstated in the PUM — presumably °C and practical salinity) are **masked before assimilation**. SST increments are **set to zero wherever sea ice is present**.

### Assimilated observations
- **Sea surface temperature:** the Copernicus Marine Baltic L3S product `SST_BAL_SST_L3S_NRT_OBSERVATIONS_010_032` (available 06:30 UTC), daily night-time satellite observations centred around midnight
- **Sea ice concentration:** `SEAICE_BAL_SEAICE_L4_NRT_OBSERVATIONS_011_004`, the FMI Baltic L4 product (available 15 UTC). Added to assimilation in the November 2023 upgrade.
- **In-situ temperature and salinity profiles:** NRT profile data from SMHI databases, aggregated over 24 hours. Added in the November 2022 upgrade.

> **The two daily cycles assimilate different things, and the split is seasonal.** The 00Z run takes sea ice concentration (winter) and T/S profiles from **1 June – 1 December**; the 12Z run takes SST and T/S profiles from **1 December – 1 June**. Outside the ice season only temperature, salinity, and SST are assimilated. This means the observational constraint on any given field depends on both the cycle and the calendar date — worth tracking for verification work.

---

## What it provides

Variable set live-verified across all five time-varying datasets. **Note the delivered variable names differ from the PUM** — see *Notes*.

| Variable | Standard name | Units | 15-min | Hourly | Daily | Monthly | De-tided |
|---|---|---|---|---|---|---|---|
| `thetao` | `sea_water_potential_temperature` | degree_Celsius | — | **3D (56)** | **3D (56)** | **3D (56)** | — |
| `so` | `sea_water_salinity` | 1e-3 | — | **3D (56)** | **3D (56)** | **3D (56)** | — |
| `uo` / `vo` | `eastward` / `northward_sea_water_velocity` | m s⁻¹ | surface | **3D (56)** | **3D (56)** | **3D (56)** | — |
| `wo` | `upward_sea_water_velocity` | m s⁻¹ | — | **3D (56)** | **3D (56)** | **3D (56)** | — |
| `sla` | `sea_surface_height_above_sea_level` | m | ✓ | ✓ | **absent** | ✓ | — |
| `mlotst` | `ocean_mixed_layer_thickness_defined_by_sigma_theta` | m | — | ✓ | ✓ | ✓ | — |
| `bottomT` | `sea_water_potential_temperature_at_sea_floor` | degrees_C | — | ✓ | ✓ | ✓ | — |
| `sob` | `sea_water_salinity_at_sea_floor` | 0.001 | — | ✓ | ✓ | ✓ | — |
| `siconc` | `sea_ice_area_fraction` | 1 | — | ✓ | ✓ | ✓ | — |
| `sithick` | `sea_ice_thickness` | m | — | ✓ | ✓ | ✓ | — |
| `zos_detided` | `sea_surface_height_above_geoid_assuming_no_tide` | m | — | — | — | — | ✓ (ssh) |
| `uos_detided` / `vos_detided` | `eastward` / `northward_sea_water_velocity_assuming_no_tide` | m s⁻¹ | — | — | — | — | ✓ (cur) |

> **Sea level is missing from the daily-mean dataset.** `sla` appears in the 15-minute, hourly, and monthly datasets but **not** in `cmems_mod_bal_phy_anfc_P1D-m`. Daily sea level is therefore only available **de-tided**, through the separate `phy-ssh_anfc_detided` dataset. Anyone wanting daily-mean *total* (tide-inclusive) sea level must average the hourly or 15-minute fields themselves. Live-verified; the PUM's statement that the daily dataset contains "daily mean averaged values of the above parameter list" is wrong on this point.

### Static fields
Distributed as three separate files under `cmems_mod_bal_phy_anfc_static`:
- `BAL-MFC_003_006_coordinate.nc` — `e1t`, `e2t`, `e3t` (cell dimensions along X, Y, Z)
- `BAL-MFC_003_006_mask_bathy.nc` — `mask` (1 = sea, 0 = land), `deptho` (bathymetry, `sea_floor_depth_below_geoid`), `deptho_lev` (model level number at sea floor)
- `BAL-MFC_003_006_mdt.nc` — `mdt`, mean dynamic topography (`sea_surface_height_above_geoid`)

**The MDT is not a fixed reference surface.** It is computed as the time mean of SSH at each grid point over a two-year assimilating production run, and is **recalculated at every production upgrade**. Anyone converting `sla` to an absolute sea surface height across an upgrade boundary is combining an anomaly with a reference that has moved.

---

## Data availability
- **Is the data free?** Yes, with registration
- **License:** Copernicus Marine Service licence (free after self-registration)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4 (PUM states CF-1.0); **Zarr** for the ARCO copies
- **Product identifier:** `BALTICSEA_ANALYSISFORECAST_PHY_003_006`
- **DOI:** https://doi.org/10.48670/moi-00010
- **Dataset identifiers:**

| Dataset | Content | Version tag |
|---|---|---|
| `cmems_mod_bal_phy_anfc_PT15M-i` | 15-minute sea level and surface currents | `_202411` |
| `cmems_mod_bal_phy_anfc_PT1H-i` | Hourly instantaneous, 3D | `_202411` |
| `cmems_mod_bal_phy_anfc_P1D-m` | Daily mean, 3D | `_202411` |
| `cmems_mod_bal_phy_anfc_P1M-m` | Monthly mean, 3D | **`_202311`** |
| `cmems_mod_bal_phy-ssh_anfc_detided_P1D-m` | De-tided daily sea level | `_202411` |
| `cmems_mod_bal_phy-cur_anfc_detided_P1D-m` | De-tided daily surface currents | `_202411` |
| `cmems_mod_bal_phy_anfc_static` | Coordinates, bathymetry/mask, MDT (three `--ext--` variants) | `_202411` |

- **File naming:**
  - Hourly: `BAL-NEMO_PHY-{ccyymmddhh}.nc` — **12 hours per file**, timestamp is the file's start time
  - 15-minute: `BAL-NEMO_PHY-15minutes-{ccyymmddhh}.nc` — 48 timestamps (12 hours) per file
  - Daily mean: `BAL-NEMO_PHY-DailyMeans-{ccyymmdd}.nc` — one timestamp
  - Monthly mean: `BAL-NEMO_PHY-MonthlyMeans-{ccyymm}.nc` — one timestamp
  - De-tided: `BAL-NEMO_PHY-detided_ssh-{ccyymmdd}.nc`, `BAL-NEMO_PHY-detided_cur-{ccyymmdd}.nc`
- **File size:** hourly 645 MB; 15-minute surface 71 MB; daily mean 55 MB; monthly mean 55 MB; de-tided SSH 2.3 MB; de-tided currents 4.6 MB
- **Fill / land value:** **−999**
- **Time averaging conventions:** 15-minute instantaneous values centred at hh:00, hh:15, hh:30, hh:45; hourly instantaneous at hh:00; **daily means centred at noon**; monthly means over the calendar month; de-tided values from a Doodson 39-hour filter applied at midnight values
- **Delivery mechanism:** Copernicus Marine Toolbox
- **Official access:** https://data.marine.copernicus.eu/product/BALTICSEA_ANALYSISFORECAST_PHY_003_006/description

> **Verified 2026-08-25.** Grid dimensions and spacing, the 56-level list, per-dataset variable inventory and dimensionality, dataset version tags, and archive extents confirmed from Marine Data Store STAC records and live ARCO Zarr metadata.

---

## Relationship to other entries

- **Coupled counterpart:** [BALWAM](../../../wave_models/regional/finland/balwam.md) (`BALTICSEA_ANALYSISFORECAST_WAV_003_010`) is the wave half of the same BAL MFC production chain.

  > **This corrects existing repository text.** The BALWAM entry and `COPERNICUS.md` both describe the coupling as **one-way from Baltic physics** (currents, sea level, ice into the wave model). The physics PUM states the exchange is **bidirectional**: WAM receives ice concentration and surface currents from NEMO, and **NEMO reads Stokes drift back in from WAM**. It is not online two-way coupling in the OASIS sense — the models exchange fields between runs rather than within a timestep — but it is not one-way either. Both the BALWAM entry and the `COPERNICUS.md` coupling column need updating, and the "Only IBIWAM and AMM15-WW3 have genuine two-way ocean-wave coupling" observation in `COPERNICUS.md` needs qualifying.

- **Biogeochemistry sibling:** `BALTICSEA_ANALYSISFORECAST_BGC_003_007` (ERGOM) is not a separate model run but the same executable — online-coupled through FABM and documented in the same PUM. Out of scope for this repository, but the physics cannot be described as standalone.

- **Parent / boundary source:** takes lateral boundaries from the Copernicus Marine North West Shelf physics forecast (`NWSHELF_ANALYSISFORECAST_PHY_004_013`). The Met Office AWS distribution of that system is documented as [FOAM-NWSO / AMM15](../../regional/uk/amm15-nws.md), whose entry flags the identity with the Copernicus product as unverified — **this PUM naming the Copernicus identifier as its boundary source is corroborating but not confirming evidence**. The PUM also notes the boundary source "will change production system again from November 2025", consistent with the operator change documented in the [AMM15-WW3](../../../wave_models/regional/uk/amm15-ww3-uk.md) entry.

- **Downstream nesting:** [NEMO-EST](../../regional/estonia/nemo-est.md) takes its lateral open-boundary conditions from "the Copernicus Marine operational Baltic Sea model (BAL MFC product)" — this entry. That entry's boundary attribution can now be made specific.

- **Not the same run as the FMI feed.** The [FMI Baltic NEMO open-data feed](../../regional/finland/nemo-baltic-fmi.md) records as **TBD** whether it is the BAL MFC production or an FMI-internal run. This PUM states the BAL MFC production is performed **at SMHI**, which makes the FMI feed a separate run of the same Nemo-Nordic lineage rather than a redistribution of this product. **Partial resolution of that entry's open question** — it does not establish what FMI's configuration is, only that it is not this one.

- **Which to use when:** for full 3D Baltic physics with assimilation, this entry. For registration-free Baltic surface fields, the FMI feed — accepting that it is surface-only, quantized, and three cycles deep. For high-resolution Estonian coastal waters, NEMO-EST.

- **Programme context:** see [`COPERNICUS.md`](../../../../COPERNICUS.md) for the full Copernicus Marine portfolio.

---

## Version history

The PUM carries an unusually complete change log, reproduced and condensed here. Note that dated entries describe the **November upgrade cycle** that BAL MFC runs annually.

### 25 November 2025 — current
- Forecast extended to **9 days from both cycles** (previously 10 days from 00Z, 6 days from 12Z)
- Both productions extended **backwards by 12 hours**, beginning with a 12-hour re-analysis before the forecast start. Stated rationale: re-analysed atmospheric forcing for the first 12 hours, and more observations available at production start.
- **Seven datasets retired** as obsolete: `cmems_mod_bal_phy_anfc_7-10days_PT15M-i`, `…_7-10days_PT1H-i`, `…_7-10days_P1D-m`, `cmems_mod_bal_phy-cur_anfc_detided-7-10days_P1D-m`, `cmems_mod_bal_phy-ssh_anfc_detided-7-10days_P1D-m`, and the two corresponding BGC datasets

### 26 November 2024 — EIS202411
- **NEMO upgraded from v4.0.4 to v4.2.1**
- Ice thickness categories **reduced from 5 to 1** to suit the data assimilation
- Chlorophyll value in the NEMO setup changed to salinity-scaled values
- Bulk formula switched to `ln_ECMWF = .true.` for compatibility with the meteorological forcing; reported to reduce a cold SST bias
- Smagorinsky coefficient changed to a **2-D tuned field** for better SSH and inflow behaviour
- 00Z cycle extended by 4 days to a 10-day forecast; 12Z remained at 6 days

### 29 November 2023
- Data assimilation system upgraded to a newer **AWI-developed** version
- **Sea ice concentration observations added** to assimilation
- Open-boundary input switched to a new dataset from the NWS MFC consortium — *the PUM notes the impact of this change was not tested*

### 29 November 2022
- **Vertical sea water velocity added** to the product
- **Temperature and salinity in-situ profiles added** to assimilation

### 14 December 2021
- Static files added

### 15 December 2020
- Introduced as a new product, superseding the earlier `BALTICSEA_ANALYSIS_FORECAST_PHY_003_006` based on the **HBM** ocean model. The switch from HBM to NEMO is the significant discontinuity in this product's lineage.

---

## Notes

- **Delivered variable names differ from the PUM.** The PUM lists `u0`, `v0`, `w0` for the velocity components and `uo_detided` / `vo_detided` for the de-tided currents. Live files use **`uo`, `vo`, `wo`** and **`uos_detided`, `vos_detided`**. Code written from the PUM will not find these variables.

- **The PUM swaps the de-tided current standard names; the files are correct.** The PUM gives `uo_detided` the standard name `northward_sea_water_velocity_assuming_no_tide` and `vo_detided` the eastward one. Live files pair them correctly (`uos_detided` → eastward, `vos_detided` → northward). **Resolved from data — a documentation error, not a data error.**

- **`sla` is ambiguous, and its standard name is not valid CF.** The variable carries `standard_name = sea_surface_height_above_sea_level`, which is not in the CF standard name table (`sea_surface_height_above_geoid` and `sea_surface_height_above_mean_sea_level` are). Its `long_name` reads "Sea level elevation" while the PUM calls it "Sea level anomalies". Given the separate MDT static file, the reasonable reading is that `sla` is an anomaly requiring `mdt` to reach an absolute height — but **the metadata does not say so unambiguously**, and the de-tided sea level uses a different variable name and a properly geoid-referenced standard name. Treat the reference frame as unresolved and check against tide gauge data before using `sla` quantitatively (**TBD**).

- **`bottomT` is only valid over part of the domain.** Its `long_name` adds "given for depth comprise between 0 and 500m" — a restriction absent from the PUM's variable table. The Baltic's deepest point (Landsort Deep, ~459 m) falls inside this, but the Kattegat–Skagerrak transition and the model's deeper levels to 712 m do not.

- **Salinity units are written two ways in the same file.** `so` carries `units = 1e-3`; `sob` carries `units = 0.001`. Numerically identical, but string-matching on units will fail across the pair.

- **The archive is longer than documented.** The PUM states a rolling 2-year window. Live datasets currently begin **2022-12-02** — nearly four years — while the product-level STAC record claims coverage from 2020-10-01. Three different answers. The 2022-12-02 figure is the one verified from dataset dimensions and is what a user can actually retrieve (**flagged, not resolved**).

- **The monthly dataset was not reversioned at the November 2024 upgrade.** It carries `_202311` while every other dataset carries `_202411`. Whether this means the monthly means are still produced by the older NEMO 4.0.4 chain, or simply that the version tag was not bumped, is unclear (**TBD**). Its coverage also starts 2021-11-01, earlier than the sub-monthly datasets.

- **De-tided datasets are one day shorter, by construction.** The Doodson 39-hour centred filter costs the final forecast day; the PUM says de-tided values are available for "forecast length minus one day". Live-verified: de-tided datasets end 2026-08-31 where the others reach 2026-09-02.

- **The atmospheric forcing handover point is stated two ways.** The PUM says HARMONIE covers "the first 2.5 days" in two places and "the first 66 hours" in another. 2.5 days is 60 hours. The 66-hour figure is consistent with the adjacent statement that ECMWF hourly winds begin at forecast hour 67, so **66 h is the more likely value** — but the discrepancy is unresolved.

- **The wind-forcing schedule in the PUM is written about WAM.** The passage describing the degradation from hourly to 3-hourly to 6-hourly ECMWF winds says "WAM uses hourly wind forcing from ECMWF". Since NEMO and WAM share the production chain the schedule presumably applies to both, but the PUM only states it for the wave model (**TBD**).

- **Boundary tides come from an unnamed source.** The PUM says tides are included at the open boundaries "from another source" without identifying it, and that several boundary parameters are held constant for the final forecast days to protect delivery timing. Both matter for anyone using the de-tided products or interpreting late-forecast sea level (**TBD**).

- **Reducing the ice model to a single thickness category is consequential.** It was done to match an assimilation scheme that only updates one class, but it removes the sub-grid ice thickness distribution. For ice-thickness applications this is a meaningful limitation, and it is a step backwards from the previous 5-category configuration.

- **The product page's forecast-length description is stale.** It describes the pre-November-2025 configuration (10 days from 00Z, 6 days from 12Z). The same staleness affects the BALWAM product page, already flagged in that entry — a recurring pattern in BAL MFC catalogue text worth checking whenever these products are revisited.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/BALTICSEA_ANALYSISFORECAST_PHY_003_006/description
- **Product User Manual (covers both PHY 003_006 and BGC 003_007), Issue 1.3, November 2025:** https://documentation.marine.copernicus.eu/PUM/CMEMS-BAL-PUM-003-006-007.pdf
- Quality Information Document (QUID): https://documentation.marine.copernicus.eu/QUID/CMEMS-BAL-QUID-003-006.pdf
- Synthesis Quality Overview (SQO): https://documentation.marine.copernicus.eu/SQO/CMEMS-BAL-SQO-003-006.pdf
- DOI: https://doi.org/10.48670/moi-00010
- SMHI: https://www.smhi.se/
- Copernicus Marine Service: https://marine.copernicus.eu/

### Key references
- Bruggeman, J. and Bolding, K. (2014). A general framework for aquatic biogeochemical models. *Environmental Modelling & Software*, 61, 249–265. https://doi.org/10.1016/j.envsoft.2014.04.002
- Kärnä, T. et al. (2021). Nemo-Nordic 2.0: operational marine forecast model for the Baltic Sea. *Geoscientific Model Development*, 14, 5731–5749. https://doi.org/10.5194/gmd-14-5731-2021
- Hordoir, R. et al. (2019). Nemo-Nordic 1.0: a NEMO-based ocean model for the Baltic and North seas. *Geoscientific Model Development*, 12, 363–386. https://doi.org/10.5194/gmd-12-363-2019
- de Boyer Montégut, C. et al. (2004). Mixed layer depth over the global ocean. *J. Geophys. Res.*, 109, C12003. *(basis for the `mlotst` definition used here)*
- Neumann, T. et al. (2021). *(ERGOM model description — cited in the PUM)*
- Nerger, L. and Hiller, W. (2013). Software for ensemble-based data assimilation systems — implementation strategies and scalability. *Computers & Geosciences*, 55, 110–118. *(PDAF)*
