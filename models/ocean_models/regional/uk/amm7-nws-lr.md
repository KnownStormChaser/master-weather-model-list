# AMM7 (North-West European Shelf Ocean Physics — Low Resolution)

## What this model is
The NWS Ocean Physics Low Resolution product is a **regional physical ocean analysis and 7-day forecast for the North-West European Shelf** at ~7 km, run by the **UK Met Office** on the **Atlantic Margin Model 7 km (AMM7)** domain and distributed through the Copernicus Marine North West Shelf MFC.

It is the coarse-resolution sibling of [FOAM-NWSO / AMM15](./amm15-nws.md), but it is **not simply a coarsened AMM15**. Three things distinguish it:

- **The domain is larger.** AMM7 spans 20°W–13°E, 40°N–65°N against AMM15's 16°W–13°E, 46°N–62.75°N — it reaches further west, further north, and considerably further south.
- **It is coupled to biogeochemistry, not to waves.** AMM7 is online-coupled to **ERSEM 22.11** via FABM, producing `NWSHELF_ANALYSISFORECAST_BGC_004_002`. AMM15 is coupled to a wave model instead. The two shelf configurations therefore serve different downstream purposes.
- **It carries a different and larger tidal set** — 16 named constituents against AMM15's 11.

The trade-off is temporal: this product ships **daily means only**. There is no hourly, quarter-hourly, or monthly output, and no separate de-tided dataset.

The official Copernicus Marine product identifier is `NWSHELF_ANALYSISFORECAST_PHY_LR_004_001`. The PUM gives the system name as **AMM7**.

> **The product page description is out of date.** It still describes quarter-hourly, hourly, daily de-tided, and monthly frequencies. The current product has **one temporal resolution: daily mean**, across six single-variable datasets. Live-verified.

*Verified 2026-08-25 from the PUM (Issue 1.0, November 2025), Marine Data Store STAC records, and live ARCO Zarr metadata.*

---

## Who runs it
- **Production Unit:** UK Met Office
- **Country:** United Kingdom
- **Programme or coordinating body:** Copernicus Marine Service — Atlantic European North West Shelf Monitoring and Forecasting Centre (NWS MFC)
- **Role in any larger system:** supplies the physics half of the coupled physics–biogeochemistry system whose BGC output is `NWSHELF_ANALYSISFORECAST_BGC_004_002`; takes its Baltic boundary from the Copernicus Baltic physics product and its Atlantic boundary from a Met Office ORCA12 global forecast

---

## What area it covers
- **Coverage:** North-West European Shelf and adjacent North-East Atlantic
- **Domain bounds (documented):** 20°W – 13°E, 40°N – 65°N
- **Domain bounds (live-verified):** 19.8889°W – 12.9997°E, 40.0667°N – 65.0013°N
- **Grid dimensions (live-verified):** **297 × 375** (longitude × latitude), regular lat-lon
- **Grid spacing (live-verified):** **1/9° longitude (0.111110°) × 1/15° latitude (0.066670°)**, ≈ 7 km — see *Notes* for the PUM's internal contradiction on which axis is which
- **Special masked or excluded regions:**
  - **Baltic:** all grid points in the Baltic and in the Kattegat south of **57.25°N** are masked (the same convention as AMM15)
  - **Atlantic:** the **outermost 10 grid points** are masked — north of 64°18′48″N, west of 18°19′12″W, and south of 40°43′58″N
  - A minimum-depth floor applies in the underlying NEMO configuration — see *Notes*

The domain deliberately extends beyond the continental shelf so the model's boundary region sits in deep water. The focus region is the shelf seas proper: North Sea, Irish Sea, English Channel, Celtic Sea, and Bay of Biscay.

---

## Basic details
- **Model type:** Regional shelf-seas ocean physics; deterministic; online-coupled to a biogeochemical model
- **Core ocean model:** **NEMO 3.6**, AMM7 configuration
- **Biogeochemical model:** **ERSEM 22.11**, coupled via **FABM**
- **Sea ice model:** None (shelf-seas domain)
- **System name:** AMM7
- **Horizontal resolution:** ~7 km, on a regular 1/9° longitude × 1/15° latitude grid
- **Vertical levels (delivered):** **24 standard IOC geopotential levels, 0–5000 m** (surface, 3, 10, 15, 20, 30, 50, 75, 100, 125, 150, 200, 250, 300, 400, 500, 600, 750, 1000, 1500, 2000, 3000, 4000, 5000 m) — live-verified. **The surface level is not interpolated**; it is the first model level, 1 m thick where bathymetry exceeds 50 m and thinner where it is shallower.
- **Vertical coordinate (native):** **hybrid S-σ-z, 51 levels almost everywhere** — identical to AMM15 — with a stretching function maintaining near-uniform surface resolution. Native cell thickness ranges from 0.3 m (bathymetry < 50 m) to 99 m (bathymetry > 4000 m). All delivered 3D fields except `bottomT` are interpolated from these 51 native levels to the 24 fixed depths.
- **Forecast length:** **7 days (168 h) plus 2 analysis days**, i.e. T−48 h → T+168 h
- **Update frequency:** Once daily
- **Production cycles:** 00 UTC
- **Target delivery time:** PUM target **12:00 UTC**; actual delivery usually **06:40–10:00 UTC**
- **Temporal output resolution:** **daily mean only** — computed as the mean of 25 hourly instantaneous values from midnight to the following midnight, which removes both diurnal and tidal cycles
- **Archive availability:** 2-year rolling. Live extent begins **2023-09-29** — see *Notes*.
- **Bathymetry source:** **GEBCO 1 arc-minute**, combined with local data supplied by North West-European Shelf Operational Oceanographic System (**NOOS**) partners
- **Initial conditions (system spinup):** the model run started **1 January 2017** from the previous version of the NEMO-ERSEM forecasting system

---

## Forcing
- **Atmospheric forcing:** Met Office Global Unified Model (MetUM), one-way
- **River runoff:** **daily time series for 1991–2017**, carrying discharge plus nutrient loads (nitrate, phosphate, silicate, ammonia), alkalinity (total alkalinity, bioalkalinity, dissolved organic carbon), and oxygen. Built from an updated version of the Lenhart et al. (2010) river dataset combined with a climatology of daily discharge from the Global River Discharge Data Base (Vörösmarty et al. 2000) and data prepared by the Centre for Ecology and Hydrology as used by Young and Holt (2007). **The 2017 series is repeated for all subsequent years**, on the stated grounds that interannual variation is small — so river forcing is effectively frozen at 2017.
- **Lateral boundary conditions:**
  - **Atlantic** (T, S, SSH, barotropic u and v): **Met Office Operational ORCA12 forecast implementation**
  - **Baltic** (T, S, barotropic u and v): **`BALTICSEA_ANALYSISFORECAST_PHY_003_006`**
- **Tidal forcing:** explicit, applied both at the open boundary via **Flather radiation conditions** (Flather 1976) and as an **equilibrium tide** drawn from a tidal model of the North-East Atlantic (Flather 1981). Constituents named in the PUM: **M2, S2, N2, K2, K1, O1, P1, Q1, M4, MS4, L2, T2, S1, 2N2, MU2, NU2** — sixteen, though the PUM says fifteen; see *Notes*.
- **Ice forcing or coupling:** N/A

---

## Coupling
- **Ocean–biogeochemistry:** **one-way online coupling, physics forcing the biogeochemistry, at every model timestep**, via FABM. NEMO 3.6 drives ERSEM 22.11; there is no feedback from biogeochemistry to physics. The physics and BGC products are delivered on an identical horizontal grid and share the same static bathymetry, land-sea mask, and MDT.
- **Ocean–wave:** none. This is the structural difference from [AMM15](./amm15-nws.md), which is two-way coupled to WAVEWATCH III instead.
- **Atmosphere:** one-way MetUM forcing; no coupled atmosphere

---

## Data assimilation
- **DA scheme:** **3D-Var FGAT, NEMOVAR** — the same scheme as AMM15
- **Assimilated observations:**
  - In-situ and satellite (L2/L3) sea surface temperature
  - Satellite sea level anomaly from the CMEMS SL-TAC
  - Temperature and salinity subsurface profiles from GTS and the CMEMS INS-TAC

---

## What it provides

Six single-variable daily-mean datasets, all live-verified at 297 × 375 with 1067 daily timesteps.

| Variable | Standard name | Units | Dimensionality | Dataset |
|---|---|---|---|---|
| `thetao` | `sea_water_potential_temperature` | degrees_C | 3D (24 levels) | `…phy-tem_anfc_7km-3D_P1D-m` |
| `so` | `sea_water_salinity` | 1e-3 | 3D (24 levels) | `…phy-sal_anfc_7km-3D_P1D-m` |
| `uo` / `vo` | `eastward` / `northward_sea_water_velocity` | m s⁻¹ | 3D (24 levels) | `…phy-uv_anfc_7km-3D_P1D-m` |
| `zos` | `sea_surface_height_above_geoid` | m | 2D | `…phy-ssh_anfc_7km-2D_P1D-m` |
| `bottomT` | `sea_water_potential_temperature_at_sea_floor` | degrees_C | 2D | `…phy-bottomt_anfc_7km-2D_P1D-m` |
| `mlotst` | `ocean_mixed_layer_thickness_defined_by_sigma_theta` | m | 2D | `…phy-mld_anfc_7km-2D_P1D-m` |

**No vertical velocity, no barotropic velocity components, no surface-only SST/SSS/SSC datasets.** All of these exist in the AMM15 product; none is delivered here. The PUM's dataset-details preamble lists "Sea Surface Temperature (SST)", "Sea Surface Salinity (SSS)", and "Sea Surface Current (SSC)" among the fields provided, but no corresponding datasets appear in its own table or in the live catalogue — apparently carried over from the AMM15 PUM. Surface values are available as the top level of the 3D fields.

### Static fields
Distributed as `cmems_mod_nws_phy_anfc_7km-3D_static` in three `--ext--` variants (coords, bathy, mdt): `mask` (land-sea mask), `depth`, `deptho` (bathymetry, `sea_floor_depth_below_geoid`), `mdt` (mean dynamic topography), and `deptho_lev_interp` — the deepest wet grid point **of the interpolated 3D fields**.

> **`bottomT` is output directly from the native model and is not interpolated.** Every other 3D field is interpolated from the 51 native S-σ-z levels to the 24 standard depths; `bottomT` is not. The seabed value and the lowest wet point of the interpolated 3D temperature field **can differ**. Use `deptho_lev_interp` to mask `thetao`, not `bottomT`. This is the same behaviour as AMM15, and the coarser 24-level set makes the gap larger here.

---

## Data availability
- **Is the data free?** Yes, with registration
- **License:** Copernicus Marine Service licence (free after self-registration)
- **Is the data downloadable?** Yes
- **Data formats:** NetCDF-4, CF-1.7; **Zarr** for the ARCO copies
- **Product identifier:** `NWSHELF_ANALYSISFORECAST_PHY_LR_004_001`
- **DOI:** https://doi.org/10.48670/mds-00367 — note the **`mds-` prefix**, not the `moi-` prefix used by older Copernicus Marine products
- **Dataset identifiers** (all tagged `_202511`):
  - `cmems_mod_nws_phy-tem_anfc_7km-3D_P1D-m`
  - `cmems_mod_nws_phy-sal_anfc_7km-3D_P1D-m`
  - `cmems_mod_nws_phy-uv_anfc_7km-3D_P1D-m`
  - `cmems_mod_nws_phy-ssh_anfc_7km-2D_P1D-m`
  - `cmems_mod_nws_phy-bottomt_anfc_7km-2D_P1D-m`
  - `cmems_mod_nws_phy-mld_anfc_7km-2D_P1D-m`
  - `cmems_mod_nws_phy_anfc_7km-3D_static` (`--ext--coords`, `--ext--bathy`, `--ext--mdt`)
- **ARCO endpoints:** `https://s3.waw3-1.cloudferro.com/mdl-arco-time-041/arco/NWSHELF_ANALYSISFORECAST_PHY_LR_004_001/…`
- **Delivery mechanism:** Copernicus Marine Toolbox
- **Dissemination schedule** (all from the 00 UTC run, delivered daily until 12 UTC):

| Type | Temporal coverage |
|---|---|
| Forecast | 0 to +168 h |
| NRT analysis | −24 to 0 h |
| Best estimate analysis | −48 to −24 h |

  Each day the previous day's NRT analysis and 7-day forecast are deleted at the start of the process. Catalogue data from two years before present to T−24 h is taken from the T−48 h to T−24 h block of each run (best estimate).

- **Official access:** https://data.marine.copernicus.eu/product/NWSHELF_ANALYSISFORECAST_PHY_LR_004_001/description

> **Verified 2026-08-25.** Grid dimensions and spacing, the 24-level list, per-dataset variable inventory and dimensionality, dataset version tags, and archive extents confirmed from Marine Data Store STAC records and live ARCO Zarr metadata.

---

## Relationship to other entries

- **High-resolution sibling:** [FOAM-NWSO / AMM15](./amm15-nws.md) (`NWSHELF_ANALYSISFORECAST_PHY_004_013`), same operator, same NEMO version, same DA scheme, same 51-level native vertical grid, same 10 m depth floor, same dissemination schedule. **They are not interchangeable**, and the differences run deeper than resolution:

  | | **AMM7 (this entry)** | **AMM15** |
  |---|---|---|
  | Domain | 20°W–13°E, 40°N–65°N | 16°W–13°E, 46°N–62.75°N |
  | Grid | 297 × 375 at 1/9° × 1/15° | 958 × 1240 at 1/33° × 1/74° |
  | Delivered levels | 24 | 33 |
  | Coupled to | **ERSEM biogeochemistry** (one-way, online) | **WAVEWATCH III waves** (two-way, OASIS3-MCT) |
  | Tidal constituents | 16 named | 11 named |
  | Temporal output | Daily mean only | Daily mean, hourly, quarter-hourly |
  | Vertical velocity | Not delivered | `wo` delivered |
  | Barotropic velocity | Not delivered | `ubar`, `vbar` delivered |
  | Second channel | Copernicus only | Copernicus **and** Met Office AWS Open Data |

  **AMM7 covers more area and more tidal physics; AMM15 covers it in more detail and more often.** Anyone needing the western Atlantic approaches, Iberian latitudes, or the Norwegian Sea north of 62.75°N must use AMM7 despite its coarser grid.

- **Coupled counterpart:** `NWSHELF_ANALYSISFORECAST_BGC_004_002` (Atlantic - European North West Shelf - Ocean Biogeochemistry Analysis and Forecast, ERSEM 22.11, Met Office). Out of scope for this repository, but this product is the physics half of that coupled system rather than a standalone run.

- **Reciprocal boundary exchange with the Baltic.** Like AMM15, this product takes its Baltic boundary from **[BAL MFC-NEMO](../../regional/sweden/bal-mfc-nemo.md)** (`BALTICSEA_ANALYSISFORECAST_PHY_003_006`). The Baltic PUM in turn names the NWS physics product as *its* western boundary source, though it cites only the `004_013` identifier — **which of the two NWS configurations actually supplies the Baltic boundary is therefore ambiguous** (*TBD*). Both NWS PUMs name the Baltic as a source in the other direction.

- **Parent global model — not FOAM-GC.** The Atlantic boundary comes from the Met Office **Operational ORCA12** forecast (1/12°). [FOAM-GC](../../global/uk/foam-gc.md) is **ORCA025** (1/4°). The actual ORCA12 parent is not currently in this repository — the same gap noted in the AMM15 entry.

- **Programme context:** see [`COPERNICUS.md`](../../../../COPERNICUS.md) for the full Copernicus Marine portfolio.

---

## Version history

The PUM carries a single change-log line and a single record-table issue.

### November 2025 — PUM Issue 1.0
**Re-introduction of the NWS physical analysis and forecasting product by the UK Met Office**, including a 7-day forecast. The record table describes this as the *"initial version after Met Office reintroduction of this product"* — implying the identifier existed previously and lapsed, but the PUM documents nothing before this date.

> **The archive predates the documented history by two years.** Live datasets begin **2023-09-29** and carry the `_202511` tag throughout — the *same start date* as the [AMM15 product's](./amm15-nws.md) archive, which is the date its Copernicus identifier switched to an IBI extraction. What system produced the September 2023 → November 2025 portion of this product's archive is **not documented anywhere in the PUM**. Given the AMM15 precedent, an IBI-derived origin is the obvious hypothesis, but it is not stated. **Flagged, not resolved** — and anyone building a multi-year series from this product should treat the November 2025 boundary as a probable discontinuity until clarified.

---

## Notes

- **The PUM contradicts itself on which axis carries which resolution.** The General Information table and the *Horizontal grid* row both say **1/9° longitude × 1/15° latitude**; the Production System Description heading says *"1/15° longitude x 1/9° latitude, regular grid, 297x 375"*. Live data settles it: longitude step **0.111110° = 1/9°**, latitude step **0.066670° = 1/15°**. The General Information table is correct; the heading has the labels swapped. Note that the grid dimensions in that same heading (297 × 375, longitude × latitude) *are* right.

- **Sixteen tidal constituents are named, but the PUM says fifteen.** The list reads M2, S2, N2, K2, K1, O1, P1, Q1, M4, MS4, L2, T2, S1, 2N2, MU2, NU2 — sixteen entries under the heading "15 tidal constituents". Which is wrong, the count or the list, is unresolved (**TBD**). Either way the set differs from AMM15's, which has eleven and includes **MN4** — a constituent absent here — while AMM7 adds L2, T2, S1, 2N2, MU2, and NU2.

- **The dataset table cites the wrong BGC product identifier.** The PUM states that the bathymetry, land-sea mask, and MDT are shared with `NWSHELF_MULTIYEAR_BGC_004_002`. The summary on the same document's first page names the coupled sibling as `NWSHELF_ANALYSISFORECAST_BGC_004_002`, which is the product that actually exists in the catalogue under that number. `MULTIYEAR` in the dataset table appears to be a typo.

- **The dataset preamble lists three variable groups that are not delivered.** SST, SSS, and SSC appear in the *Dataset details* preamble but have no corresponding datasets in the PUM's own table or in the live catalogue — almost certainly carried over from the AMM15 PUM, whose surface-only datasets do exist. Use the top level of the 3D fields instead.

- **Daily means are already quasi-de-tided.** The 25-hour averaging window, running midnight to midnight, removes both diurnal and tidal cycles. This is why there is no separate de-tided dataset — unlike the [Baltic product](../../regional/sweden/bal-mfc-nemo.md), which publishes one, and unlike the pre-2025 configuration of this product implied by the stale catalogue text.

- **Daily means only — a real constraint.** With no hourly or quarter-hourly output, this product cannot resolve tidal currents, storm surge, or any sub-daily signal. The tidal physics is in the model (16 constituents, Flather boundaries, equilibrium tide) but is averaged out before delivery. For sub-daily shelf dynamics, [AMM15](./amm15-nws.md) is the only option — at the cost of the smaller domain.

- **Minimum-depth floor of 10 m, and what it does.** NEMO 3.6 has no wetting-and-drying capability, so every bathymetric grid point shallower than 10 m is reassigned a depth of 10 m to keep the model stable under this domain's large tides. Points are interpolated back to their original depth before delivery, so the floor is invisible in the bathymetry field but present in the dynamics. Tide and surge propagation speed goes as √(gH), so where model depth exceeds reality the propagation is wrong; where the seabed is genuinely exposed the drying process is **absent by design**. The PUM names the **Wadden Sea** as particularly affected and advises against using model fields directly in regions with extensive sub-10 m bathymetry, and against nesting a domain whose boundaries sit near such areas. Wetting and drying is implemented in NEMO 4 and flagged as future development (O'Dea et al. 2020).

- **River forcing is frozen at 2017.** The 1991–2017 daily time series is repeated for every subsequent year. The stated justification is that interannual variation is small, but this means the product carries no response to recent hydrological trends or to individual flood events — a limitation for the biogeochemistry it drives as much as for the physics.

- **The DOI uses the newer `mds-` prefix.** `10.48670/mds-00367`, against the `moi-` prefixes on the older NWS, Baltic, and Arctic products. Worth noting for any repository-wide DOI handling.

---

## Official documentation
- Product page: https://data.marine.copernicus.eu/product/NWSHELF_ANALYSISFORECAST_PHY_LR_004_001/description
- **Product User Manual (Issue 1.0, November 2025):** https://documentation.marine.copernicus.eu/PUM/CMEMS-NWS-PUM-004-001.pdf
- **Quality Information Document (QUID):** https://documentation.marine.copernicus.eu/QUID/CMEMS-NWS-QUID-004-001.pdf
- **DOI:** https://doi.org/10.48670/mds-00367
- UK Met Office: https://www.metoffice.gov.uk/
- Copernicus Marine Service: https://marine.copernicus.eu/

### Key references
- O'Dea, E., Bell, M.J., Coward, A. and Holt, J. (2020). Implementation and assessment of a flux limiter based wetting and drying scheme in NEMO. *Ocean Modelling*, 155. https://doi.org/10.1016/j.ocemod.2020.101708
- Flather, R.A. (1976). A tidal model of the north west European continental shelf. *Mémoires de la Société Royale des Sciences de Liège*, 6, 141–164.
- Flather, R.A. (1981). Results from a model of the north east Atlantic relating to the Norwegian coastal current. *(cited in the PUM as the source of the equilibrium tide)*
- Lenhart, H.J. et al. (2010). Predicting the consequences of nutrient reduction on the eutrophication status of the North Sea. *Journal of Marine Systems*, 81, 148–170.
- Vörösmarty, J., Green, P., Salisbury, J. and Lammers, R.B. (2000). Global water resources: vulnerability from climate change and population growth. *Science*, 289, 284–288. https://doi.org/10.1126/science.289.5477.284
- Young, E.F. and Holt, J.T. (2007). Prediction and analysis of long-term variability of temperature and salinity in the Irish Sea. *J. Geophys. Res.*, 112, C01008. https://doi.org/10.1029/2005JC003386
- Butenschön, M. et al. (2016). ERSEM 15.06: a generic model for marine biogeochemistry and the ecosystem dynamics of the lower trophic levels. *Geoscientific Model Development*, 9, 1293–1339. https://doi.org/10.5194/gmd-9-1293-2016
